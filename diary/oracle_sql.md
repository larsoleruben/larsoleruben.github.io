---
layout: page
title: Sql (for oracle)
permalink: /diary/sql-oracle
---

## How to get a correct time zone info from a timestamp with no tz info

```SQL 
to_char((from_tz(cast(PERIOD_START_DTM as timestamp),'UTC') AT TIME ZONE 'Europe/Berlin'),  'YYYY-MM-DD HH24:MI:SS TZH:TZM')
```

## Format of time and date in Oracle

|Parameter|	Explanation|
|---------|------------|
|YEAR|	Year, spelled out|
|YYYY|	4-digit year|
|YYY||
|YY||
|Y|	Last 3, 2, or 1 digit(s) of year.|
|IYY||
|IY||
|I|	Last 3, 2, or 1 digit(s) of ISO year.|
|IYYY|	4-digit year based on the ISO standard|
|RRRR|	Accepts a 2-digit year and returns a 4-digit year.|
|A| value between 0-49 will return a 20xx year.|
|A| value between 50-99 will return a 19xx year.|
|Q|	Quarter of year (1, 2, 3, 4; JAN-MAR = 1).|
|MM|	Month (01-12; JAN = 01).|
|MON|	Abbreviated name of month.|
|MONTH|	Name of month, padded with blanks to length of 9 characters.|
|RM|	Roman numeral month (I-XII; JAN = I).|
|WW|	Week of year (1-53) where week 1 starts on the first day of the year and ||continues| to the seventh day of the year.|
|W|	Week of month (1-5) where week 1 starts on the first day of the month and ends ||on| the seventh.|
|IW|	Week of year (1-52 or 1-53) based on the ISO standard.|
|D|	Day of week (1-7).|
|DAY|	Name of day.|
|DD|	Day of month (1-31).|
|DDD|	Day of year (1-366).|
|DY|	Abbreviated name of day.|
|J|	Julian day; the number of days since January 1, 4712 BC.|
|HH|	Hour of day (1-12).|
|HH12|	Hour of day (1-12).|
|HH24|	Hour of day (0-23).|
|MI|	Minute (0-59).|
|SS|	Second (0-59).|
|SSSSS|	Seconds past midnight (0-86399).|
|AM|, A.M., PM, or P.M.	Meridian indicator|
|AD| or A.D	AD indicator|
|BC| or B.C.	BC indicator|
|TZD|	Daylight savings information. For example, 'PST'|
|TZH|	Time zone hour.|
|TZM|	Time zone minute.|
|TZR|	Time zone region.|
