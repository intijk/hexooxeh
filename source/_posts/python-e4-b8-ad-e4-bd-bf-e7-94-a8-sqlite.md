---
title: python 中使用 sqlite
tags:
  - python
  - sqlite
id: 344
categories:
  - 其它
date: 2011-02-16 18:52:19
---

[python]
import sqlite3
con = sqlite3.connect(“/tmp/dbfile”)
cur = con.cursor()
cur.execute(“create table test(x)”)
cur.executemany(“insert into test(x) values (?)”, [(&quot;a&quot;,), (&quot;b&quot;,)])
cur.execute(“select x from test order by x collate reverse”)
for row in cur:
    print row
con.close()
[/python]