---
layout: post
title: "Python script update mysql database"
description: "Python script update mysql database"
tags: [python]
---

### I use this script to update Racktables databases value 

```python
import MySQLdb
import xlrd
import time
import sys
reload(sys)
sys.setdefaultencoding("utf-8")

def get_table():
    FILE_NAME = 'owner.xls'
    data = xlrd.open_workbook(FILE_NAME)
    table = data.sheets()[0]
    return table

def insert_by_many(table):
    nrows = table.nrows
    param=[]
    for i in xrange(1,nrows):
        param.append([table.cell(i, 0).value, table.cell(i, 1).value])
        print param
    try:
        sql = 'UPDATE AttributeValue JOIN Object ON AttributeValue.object_id = Object.id SET AttributeValue.string_value = %s WHERE AttributeValue.attr_id=14 and Object.name = %s'
        cur.executemany(sql, param)
        conn.commit()
    except Exception as e:
        print e
        conn.rollback()
    print '[insert_by_many executemany] total:',nrows-1


conn = MySQLdb.connect(host="127.0.0.1", port=3306, user="root", passwd="password", db="racktables")
cur = conn.cursor()

table = get_table()


start = time.clock()
insert_by_many(table)
end = time.clock()
print '[insert_by_many executemany] Time Usage:',end-start

if cur:
    cur.close()
if conn:
    conn.close()

```
