---
author: babinho
comments: true
date: 2012-10-24 20:39:06
layout: post
slug: limit-and-offset-in-oracle
title: Limit and offset in Oracle
wordpress_id: 159
categories:
- Oracle
tags:
- oracle
---

If you are considering about using some kind of pagination like [will_paginate](https://github.com/mislav/will_paginate) or some pagination of your own flavor, and you are stuck using Oracle i have a solution for you. Oracle is known not to have limit and offset helpers for the select statements. Those two pieces of code that are missing are helping us to paginate properly. Yeas, you can use rownum, and i am using it too, but we have to encapsulate our select a few times, so it won't make trouble for us(bad data, not ordering properly or something).

I have already made a patch for [arel](https://github.com/rails/arel), so it's embedded in Rails, if you really have use Oracle as your database.

Let's say that we want to list all the employees from scott.emp table, and order them by empno descending and fetch the records from  rows of that data 41 to 50.

```sql
SELECT * FROM (
  SELECT raw_sql_.*, rownum raw_rnum_
    FROM (select * from scott.emp order by empno DESC) raw_sql_ )
    WHERE raw_rnum_ between 41 and 50
```

Yes, i know it looks stupid, but it works, and until Oracle implements limit and offset into it, sadly, this is the only way.
