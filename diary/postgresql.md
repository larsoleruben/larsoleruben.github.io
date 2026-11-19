---
layout: page
title: Postgresql SQL
permalink: /diary/postgresql
---

## Enable pg_stat_statements (the main tool)
```sql
shared_preload_libraries = 'pg_stat_statements'   -- requires restart
CREATE EXTENSION pg_stat_statements;
```

## Find the heaviest queries
```
-- Top queries by total time
SELECT query, calls, round(total_exec_time::numeric, 1) AS total_ms,
       round(mean_exec_time::numeric, 1)  AS avg_ms,
       round(max_exec_time::numeric, 1)   AS max_ms,
       rows
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 20;
```
## See what's running right now
```
SELECT pid, now() - query_start AS duration, state, wait_event_type, query
FROM pg_stat_activity
WHERE state <> 'idle'
ORDER BY query_start;
```

## Analyze a specific slow query
```
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;
```
## sizes per table
```
SELECT relname AS table_name,
       pg_size_pretty(pg_total_relation_size(relid)) AS total_size,
       pg_size_pretty(pg_relation_size(relid))        AS table_only,
       pg_size_pretty(pg_indexes_size(relid))         AS indexes,
       pg_size_pretty(pg_total_relation_size(relid) - pg_indexes_size(relid) - pg_relation_size(relid)) AS toast
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;
```
## Top 20 largest, with row counts
```
SELECT schemaname, relname AS table, n_live_tup AS rows,
       pg_size_pretty(pg_total_relation_size(relid)) AS total_size
FROM pg_stat_user_tables
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 20;
```
## Database total
```
SELECT pg_size_pretty(pg_database_size(current_database()));
```
## Disk usage per column (approximate)
```
SELECT pg_size_pretty(pg_total_relation_size('my_table'));      -- whole table
SELECT pg_column_size(my_column) FROM my_table LIMIT 1;         -- one row's column
SELECT pg_size_pretty(sum(pg_column_size(my_column))) FROM my_table;  -- whole column
```
## Table bloat (most common culprit)
Lots of UPDATEs/DELETEs leave dead rows that COUNT(*) must scan through:  
```
SELECT n_live_tup, n_dead_tup,
       round(n_dead_tup::numeric / nullif(n_live_tup, 0), 2) AS dead_ratio,
       last_autovacuum
FROM pg_stat_user_tables
WHERE relname = 'your_table';
```
If n_dead_tup is a large fraction of n_live_tup, run:  
```
VACUUM (VERBOSE, ANALYZE) your_table;
-- or, if badly bloated:
VACUUM FULL your_table;  -- rewrites table, takes exclusive lock — do off-hours
```
## Check what the plan actually is
```
EXPLAIN (ANALYZE, BUFFERS) SELECT count(*) FROM your_table;
```
## Slow storage or resource starvation
```
SELECT pg_size_pretty(pg_total_relation_size('your_table'));
```
## Locking — is it actually waiting, not scanning?
```
SELECT pid, wait_event_type, wait_event, state, now() - query_start AS duration
FROM pg_stat_activity
WHERE query ILIKE '%count%';
```
## If dead tuples are high, this usually fixes it completely:
```
VACUUM (ANALYZE) your_table;
```
