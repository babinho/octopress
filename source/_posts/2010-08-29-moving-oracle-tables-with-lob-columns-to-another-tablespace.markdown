---
author: babinho
comments: true
date: 2010-08-29 20:00:00
layout: post
slug: moving-oracle-tables-with-lob-columns-to-another-tablespace
title: Moving oracle tables with lob columns to another tablespace
wordpress_id: 6
categories:
- Oracle
---

I have encountered a problem with oracle database that it does not reuse lob data. So, if you have 5000 rows of some lob data, and you delete them from the table, then insert the same 5000 rows, you will have 10000 rows of consumed space. That could be done easily with shrinking the table but the default tablespace segment space management (SSM) on oracle 10g is manual, so it does not allow row movement and table shrinking.

First, you create a tablespace with ASSM, that is automatic segment space management:
```sql
    CREATE TABLESPACE NEW_TABLESPACE DATAFILE
    '/home/oracle/product/10.2.0/db_1/dbs/new_tablespace.dbf' SIZE 16109M AUTOEXTEND ON NEXT
    1000M MAXSIZE UNLIMITED
    LOGGING
    ONLINE
    PERMANENT
    EXTENT MANAGEMENT LOCAL AUTOALLOCATE
    BLOCKSIZE 8K
    SEGMENT SPACE MANAGEMENT AUTO
    FLASHBACK ON;
```
Now we can proceed with table movement and after that lob_segment movement. There are some steps we should follow:

1. Move table
2. Rebuild indexes (and move them to the new tablespace if you wish so)
3. Move the lob segment
4. Enable row movement
5. Shrink the table and lob segment


Here is the sql needed, of course, you will need to change and/or add indexes that reside on the current table.
```sql
    --moving the table to the new tablespace
    alter table table_name move tablespace new_tablespace;

    --rebuilding the index(es) and moving them to the new tablespace
    alter index index_name rebuild tablespace new_tablespace;

    --moving the lobsegment(s) to the new tablespace
    alter table table_name move lob (lob_column) store as
    (tablespace new_tablespace);

    --shrinking the table and lobsegment
    alter table table_name enable row movement;
    alter table table_name shrink space cascade;
    alter table table_name modify lob (lob_column) (shrink space);
```
Table and the lob segment will shrink during migration, but just in case you need further space shrinkage i have included the scripts needed.
