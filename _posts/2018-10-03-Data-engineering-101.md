---
title: Data engineering 101
key: 20181003
tags: Data
---

## Postgres SQL
Becoming a data engineer with [DATAQUEST](https://www.dataquest.io/m/245/intro-to-postgres).

Postgres server. In Python, there is an open source library called psycopg2 that implements the Postgres protocol to connect to our Postgres server. 

### psycopg2 
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

* INSERT
```python
with open('user_accounts.csv') as f:
    reader = csv.reader(f)
    next(reader)
    rows = [row for row in reader]

conn = psycopg2.connect("dbname=dq user=dq")
cur = conn.cursor()
for row in rows:
    cur.execute("INSERT INTO users VALUES (%s, %s, %s, %s)", row)
conn.commit()

cur.execute('SELECT * FROM users')
users = cur.fetchall()
conn.close()
```

* COPY FROM
```python
conn = psycopg2.connect('dbname=dq user=dq')
cur = conn.cursor()

with open('user_accounts.csv', 'r') as f:
    next(f)
    cur.copy_from(f, 'users', sep=',')
conn.commit()

cur.execute('SELECT * FROM users')
users = cur.fetchall()
conn.close()
```

### SQLAlchemy

#### filter & filter_by
```python
# filter_by is used for simple queries on the column names using regular kwargs, like

db.users.filter_by(name='Joe')

# The same can be accomplished with filter, not using kwargs, 
# but instead using the '==' equality operator, 
# which has been overloaded on the db.users.name object:

db.users.filter(db.users.name=='Joe')

# You can also write more powerful queries using filter, such as expressions like:

db.users.filter(or_(db.users.name=='Ryan', db.users.country=='England'))
```

## Flask


## Marshmallow

### Serializing Objects (“Dumping”)

### Deserializing Objects (“Loading”)

## SQL TABLE

```SQL
mysql -u root -p  # mysql -D 所选择的数据库名 -h 主机名 -u 用户名 -p

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.00 sec)

mysql> use test; # use 数据库名;
Database changed

# 创建 table
# CREATE TABLE table_name (column_name column_type);
mysql> CREATE TABLE IF NOT EXISTS `gocrz_tbl`(
    ->    `gocrz_id` INT UNSIGNED AUTO_INCREMENT,
    ->    `gocrz_title` VARCHAR(100) NOT NULL,
    ->    `gocrz_dj` VARCHAR(40) NOT NULL,
    ->    `the_date` DATE,
    ->    PRIMARY KEY ( `gocrz_id` )
    -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.36 sec)

# 插入数据
mysql> insert into gocrz_tbl
    -> (gocrz_id, gocrz_title, gocrz_dj)
    -> values
    -> ('111', 'tiesto', NOW())
    -> ;
Query OK, 1 row affected (0.06 sec)

# 查询
mysql> select * from gocrz_tbl;
+----------+-------------+---------------------+----------+
| gocrz_id | gocrz_title | gocrz_dj            | the_date |
+----------+-------------+---------------------+----------+
|      111 | tiesto      | 2018-09-27 14:59:47 | NULL     |
+----------+-------------+---------------------+----------+
1 row in set (0.00 sec)
```