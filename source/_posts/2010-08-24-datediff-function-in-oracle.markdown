---
author: babinho
comments: true
date: 2010-08-24 18:30:00
layout: post
slug: datediff-function-in-oracle
title: Datediff function in Oracle
wordpress_id: 7
categories:
- Oracle
---

As i am working on Oracle database at my day job, i have found something that is by default on **sqlserver** but not in **oracle**. That is the **datediff** function that given the input of two dates and an identifier like day, month or year returns integer with the requested number.

So to cut the long story short, i have written my own **datediff** function for oracle use. You can request more return types in the comments, and i will be glad to add them. Here is the code:

```sql
create or replace function datediff(v_type VARCHAR2, date_from DATE, date_to DATE) return integer as
v_diff INTEGER;
begin
v_diff :=
  case upper(v_type)
    when 'MONTH' then
      months_between(date_to, date_from)
    when 'DAY' then
      date_to - date_from
    when 'YEAR' then
      trunc(months_between(date_to, date_from)/12)
    when 'HH' then
    (date_to - date_from) * 24
    when 'MM' then
    (date_to - date_from) * 24 * 60
    when 'SS' then
    (date_to - date_from) * 24 * 60 * 60
  end;
return abs(v_diff);
end;
```

As you can see, the type parameters are: MONTH for difference in months, DAY for difference in days and YEAR for difference in years respectively.

I hope oracle will see the light and include this function in any of the future releases.

UPDATE: As my friend [LILkillaBEE](http://blog.sibinj.com) has mentioned that he could be in a need of hour/minute/second difference, i have added that too, so, this should be the final implementation.

New parameters are HH for hour, MM for minute and SS for seconds difference.
