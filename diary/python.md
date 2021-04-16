---
layout: page
title: Python
permalink: /diary/python
---

### Using weaitress WSGI to launch a flask app:
```cmd
waitress-serve --port=8000 --host=0.0.0.0 --call 'mymodule:create_app'
```
### Using List comprehensions to unpack a curser to an array of dicts for each row
```python
results_proxy = cursor.execute('some sql', ['some parfams'])
result = [dict(zip([key[0] for key in cursor.description], row)) for row in results_proxy]
```

### Making pyodbc fast, with a few simple tricks
Pyodbc is painfully slow for large inserts. But if you tweak it a little like remove auto commit and do the inserts in batches, it becomes very fast. This little demonstrantion inserts 1.5 million rows in just app 30 seconds. With "normal" code, as you would just make it, it takes more than half an hour. And no, you don't need the file, just make the list of lists. And remeber if you make the chunks too big, you force the database to hande very large ["redo logs"](https://docs.oracle.com/cd/B28359_01/server.111/b28310/onlineredo001.htm#ADMIN11302) or ["transaction logs"](https://docs.oracle.com/cd/B28359_01/server.111/b28310/onlineredo001.htm#ADMIN11302) (that is the name in sqlserver as far as I remember)
```python
import pyodbc
import datetime
import csv


def chunk_generator(reader, chunksize=10000):
    chunk = []
    for i, line in enumerate(reader):
        if i % chunksize == 0 and i > 0:
            yield chunk
            del chunk[:]
        chunk.append(line)
    yield chunk


def main(file_length):
    print(f"Starting test og {file_length}")
    with open("testfile.csv", "w") as testfile:
        for x in range(file_length):
            testfile.write(f"{x},lars,60,{datetime.datetime.now()}\n")

    print("open large CSV file")
    csvReader = csv.reader(open("testfile.csv", newline=""), delimiter=",")

    print(f"Starting insert: {datetime.datetime.now()}")

    for chunk in chunk_generator(csvReader):
        cecnxn = pyodbc.connect(
            """DRIVER={ODBC Driver 17 for SQL Server};SERVER=127.0.0.1,1400;DATABASE=obs-db-gold-local;UID=sa;PWD=P@ssw0rd!;autocommit=False"""
            # autocommit=False is VERY important
        )
        cursor = cecnxn.cursor()
        cursor.fast_executemany = True
        # No named parameters
        cursor.executemany(
            "insert into dbo.speed_test (id, name, age,dato ) values (?,?,?,?)", chunk
        )
        cursor.commit()
        cursor.close()

    print(f"Ending insert: {datetime.datetime.now()}")


if __name__ == "__main__":
    main(1500000)
```