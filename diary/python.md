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

### Making pyodbc fast, with a few trics
Pyodbc is painfully slow for large inserts. But if you twaeak it a little and removes auto commit and do the inserts in batches, it becomes very fast. This little demonstrantion inserts 1.5 million rows in just 30 seconds. With "normal" code, as you would just make it, it takes more than half an hour.
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
        )
        cursor = cecnxn.cursor()
        cursor.fast_executemany = True
        cursor.executemany(
            "insert into dbo.speed_test (id, name, age,dato ) values (?,?,?,?)", chunk
        )
        cursor.commit()
        cursor.close()

    print(f"Ending insert: {datetime.datetime.now()}")


if __name__ == "__main__":
    main(1500000)
```