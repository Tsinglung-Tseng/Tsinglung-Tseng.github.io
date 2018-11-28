---
title: Data engineering 101
key: 20181003
tags: Data
---

## Concepts

### ORM
对象关系映射

SQLAlchemy is a ORM, psycopg2 is a database driver. These are completely different things: SQLAlchemy generates SQL statements and psycopg2 sends SQL statements to the database. SQLAlchemy depends on psycopg2 or other database drivers to communicate with the database.

### Session
* transaction scope and session scope

一个 session 同时只能处理一个 transaction, 一个 session 可以依次处理多个 transaction.

为了保持本地事务与数据库的一致性。A common choice is to consider session scope (会话范围) as seperate from transcation scope.

web 应用中的 request 就可以被认为是一个分离的 session 和 transcation.

```python
### this is a **better** (but not the only) way to do it ###

class ThingOne(object):
    def go(self, session):
        session.query(FooBar).update({"x": 5})

class ThingTwo(object):
    def go(self, session):
        session.query(Widget).update({"q": 18})

def run_my_program():
    session = Session()
    try:
        ThingOne().go(session)
        ThingTwo().go(session)

        session.commit()
    except:
        session.rollback()
        raise
    finally:
        session.close()

### another way (but again *not the only way*) to do it ###

from contextlib import contextmanager

@contextmanager
def session_scope():
    """Provide a transactional scope around a series of operations."""
    session = Session()
    try:
        yield session
        session.commit()
    except:
        session.rollback()
        raise
    finally:
        session.close()


def run_my_program():
    with session_scope() as session:
        ThingOne().go(session)
        ThingTwo().go(session)
```

Session 不是 cache. 

## Postgres SQL
Becoming a data engineer with [DATAQUEST](https://www.dataquest.io/m/245/intro-to-postgres).

Postgres server. In Python, there is an open source library called psycopg2 that implements the Postgres protocol to connect to our Postgres server. 

### Use IN instead of ANY in PostgreSQL
or how we reduce sql query from 150 seconds to 60 ms.

we have chain of functions that return array of records ids, and we use ANY operator - and query was very slow on large data sets.

```SQL
SELECT "users".* FROM "users" WHERE "users"."id" = ANY(get_involved_user_ids_in_projects_for_user_id(3))

-- replacing = ANY(array) with IN(SELECT(UNNEST(array))), just like below

SELECT "users".* FROM "users"  WHERE "users"."id" 
IN (select(unnest(get_involved_user_ids_in_projects_for_user_id(3))))
```

reduce query time from 150 seconds to 3 seconds :-) . replacing all ANY statements to IN SELECT UNNEST in function itself - get us to 60 ms :-)

这里涉及到了一个问题：

### Function Volatility Categories

> Every function has a volatility classification, with the possibilities being **VOLATILE**, **STABLE**, or **IMMUTABLE**. VOLATILE is the default if the CREATE FUNCTION command does not specify a category. The volatility category is a promise to the optimizer about the behavior of the function:

* **volatile**, can return different results on successive calls with the same arguments. The optimizer makes no assumptions about the behavior of such functions. Will re-evaluate the function at every row where its value is needed.

* **STABLE**, cannot modify the database and is guaranteed to return the same results given the same arguments for all rows **within** a single statement. Allows the optimizer to optimize multiple calls of the function to a single call. 

* **IMMUTABLE**, cannot modify the database and is guaranteed to return the same results given the same arguments forever.

有可能返回不同值的函数，必须标记 volatile 以防止被优化。
Any function with side-effects must be labeled VOLATILE, so that calls to it cannot be optimized away. Even a function with no side-effects needs to be labeled VOLATILE if its value can change within a single query; some examples are random(), currval(), timeofday().

对于 stable 和 immutable。planning 时执行，还是 query 时执行并无所谓。But there is a big difference if the plan is saved and reused later. 

There is a second important property determined by the volatility category, namely the visibility of any data changes that have been made by the SQL command that is calling the function. A VOLATILE function will see such changes, a STABLE or IMMUTABLE function will not. 

This behavior is implemented using the snapshotting behavior of MVCC: STABLE and IMMUTABLE functions use a snapshot established as of the **start** of the calling query, whereas VOLATILE functions obtain a **fresh** snapshot at the start of each query they execute.

Because of this snapshotting behavior, a function containing only SELECT commands can safely be marked STABLE, even if it selects from tables that might be undergoing modifications by concurrent queries. PG SQL 会使用 query 开始时的快照执行 stable function 的所有操作。

The same snapshotting behavior is used for SELECT commands within IMMUTABLE functions. It is generally unwise to select from database tables within an IMMUTABLE function at all, since the immutability will be broken if the table contents ever change. However, PostgreSQL does not enforce that you do not do that.


[PG优化器](http://mysql.taobao.org/monthly/2016/09/07/)

### DDL

When issued, a pre-determined order of operations is invoked, and DDL to create each table is created unconditionally including all constraints and other objects associated with it. For more complex scenarios where database-specific DDL is required, SQLAlchemy offers two techniques which can be used to add any DDL based on any condition, either accompanying the standard generation of tables or by itself.

DDL 构造可以被数据库的事件触发。(The DDL construct introduced previously also has the ability to be invoked conditionally based on inspection of the database. )

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

API server

## Marshmallow

序列化 & 反序列化，Serializing Objects (“Dumping”)，Deserializing Objects (“Loading”)

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