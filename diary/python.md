---
layout: page
title: Python
permalink: /diary/python
---

## Using "extra-index-url" and not having to deal with azure errors
Just use the URL as argument for pip like this:
```bash
pip install -r requirements.txt --extra-index-url https://user@domain.com:your_personal_access_token@url_to_private_packages/
```

## When your pyodbc on you MACOS stops working after an update and you have no idea why
This error is not comming anymore. This is horrifying scenario. You are developing in Python on a mac and everything you run needs acces to a database. And out of nowhere, your PYODBC package can't load the driver. You see errors like this:
```bash
isql -v -k '<your connection string>'
[08001][Microsoft][ODBC Driver 17 for SQL Server]SSL Provider: [OpenSSL library could not be loaded, make sure OpenSSL 1.0 or 1.1 is installed]
[08001][Microsoft][ODBC Driver 17 for SQL Server]Client unable to establish connection (0) (SQLDriverConnect)
```
And you have no idea why. Well that is a build in error from Microsoft, which is only present in python.
And here is what you should do, to get it working again:
```bash
rm -rf $(brew --prefix)/opt/openssl
version=$(ls $(brew --prefix)/Cellar/openssl@1.1 | grep "1.1")
ln -s $(brew --prefix)/Cellar/openssl@1.1/$version $(brew --prefix)/opt/openssl
```
Want to know more about that annoying failure and bug go to [here](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/known-issues-in-this-version-of-the-driver?view=sql-server-ver15#connectivity)
Also they say it has been fixed in driver 17.9+, but I installed 18 and had exactly the same problem.
And how to [install](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/install-microsoft-odbc-driver-sql-server-macos?view=sql-server-ver15) the drivers!
## Making a virtual environment
```bash
python3 -m venv /path/to/new/virtual/environment
```

## Where is my pip.conf?
Find the places where pip will look for a conf (PS in virtual env its in the root of the wirtual env)
```bash
pip config list -v
```

## Using weaitress WSGI to launch a flask app:
```cmd
waitress-serve --port=8000 --host=0.0.0.0 --call 'mymodule:create_app'
```
## Using List comprehensions to unpack a curser to an array of dicts for each row
```python
results_proxy = cursor.execute('some sql', ['some params'])
result = [dict(zip([key[0] for key in cursor.description], row)) for row in results_proxy]
```

## Making pyodbc fast, with a few simple tricks
Pyodbc is painfully slow for large inserts. But if you tweak it a little like remove auto commit and do the inserts in batches, it becomes very fast. This little demonstration inserts 1.5 million rows in just app 30 seconds. With "normal" code, as you would just make it, it takes more than half an hour. And no, you don't need the file, just make the list of lists. And remember if you make the chunks too big, you force the database to handle very large ["redo logs"](https://docs.oracle.com/cd/B28359_01/server.111/b28310/onlineredo001.htm#ADMIN11302) (oracle) or ["transaction logs"](https://docs.oracle.com/cd/B28359_01/server.111/b28310/onlineredo001.htm#ADMIN11302) (sqlserver)
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

## How to clean you python installation for all pakages

```bash
pip freeze | xargs pip uninstall -y
```

## Understanding the Azure Service Bus Lock

I have made a quick [GIST](https://gist.github.com/larsoleruben/44ad1a58007e388010498811aad4d511) for this. The concept is a bit hard to understand, but is actually quite simpel

## Python Packaging User Guide

This is [a very comprehensive list of using packages and installing them](https://packaging.python.org/en/latest/tutorials/installing-packages/#installing-from-a-local-src-tree).  
To install from a local src tree:
```bash
pip install -e <path> 
```
The -e means editable and can be omitted, if you are not editing the package you are installing.
