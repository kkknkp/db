```sql
CREATE TABLE my_table (
    id SERIAL PRIMARY KEY,
    text_field TEXT
);
```

```sql
INSERT INTO my_table (text_field)
SELECT md5(random()::text)
FROM generate_series(1, 1000000);
```

```sql
 SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 87 MB
(1 row)
```

```sql
 DO $$
BEGIN
    FOR i IN 1..5 LOOP
        UPDATE my_table SET text_field = text_field || 'A';
    END LOOP;
END $$;
```


```sql
 SELECT n_dead_tup, last_vacuum, last_autovacuum
postgres-# FROM pg_stat_all_tables
postgres-# WHERE relname = 'my_table';
 n_dead_tup | last_vacuum |        last_autovacuum
------------+-------------+-------------------------------
    5000000 |             | 2024-10-11 18:39:23.218691+00
(1 row)
```


```sql
 SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 524 MB
(1 row)
```

```sql
 ALTER TABLE my_table SET (autovacuum_enabled = false);
ALTER TABLE


SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1008 MB
(1 row)
```


```sql
DO $$
BEGIN
    FOR i IN 1..10 LOOP
        UPDATE my_table SET text_field = text_field || 'D';
        RAISE NOTICE 'Step %', i;
    END LOOP;
END $$;
```


```sql
SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1392 MB
(1 row)
```


