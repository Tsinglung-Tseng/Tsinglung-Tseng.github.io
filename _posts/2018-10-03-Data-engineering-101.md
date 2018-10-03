---
title: Data engineering 101
key: 20181003
tags: Data
---

## Postgres SQL
Becoming a data engineer with [DATAQUEST](https://www.dataquest.io/m/245/intro-to-postgres).

Postgres server. In Python, there is an open source library called psycopg2 that implements the Postgres protocol to connect to our Postgres server. 

* 用 pyscopg2 连接 postgres SQL server
```python
import psycopg2
conn = psycopg2.connect("dbname=dq user=dq")
print(conn)
conn.close()
```
* Open a connection
```python
conn = psycopg2.connect("dbname=dq user=dq")
```

* Using the Cursor object
```python
cur = conn.cursor()
cur.execute('SELECT * FROM notes')
```

* To get the returned value
```python
notes = cur.fetchall()      # fetchone() or fetchall()
```

* insert
