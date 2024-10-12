Создание таблицы и заполнение ее данными 

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
Просмотр размера файла с таблицей

```sql
 SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 87 MB
(1 row)
```
Обновление всех строк 5 раз

```sql
 DO $$
BEGIN
    FOR i IN 1..5 LOOP
        UPDATE my_table SET text_field = text_field || 'A';
    END LOOP;
END $$;
```
Просмотр количества мертвых строк

```sql
 SELECT n_dead_tup, last_vacuum, last_autovacuum
postgres-# FROM pg_stat_all_tables
postgres-# WHERE relname = 'my_table';
 n_dead_tup | last_vacuum |        last_autovacuum
------------+-------------+-------------------------------
    5000000 |             | 2024-10-11 18:39:23.218691+00
(1 row)
```

Ожидание и проверка авто-вакуума
```sql
SELECT n_dead_tup FROM pg_stat_all_tables WHERE relname = 'my_table';
```
Обновление всех строк 5 раз

```sql
DO $$
BEGIN
    FOR i IN 1..5 LOOP
        UPDATE my_table SET text_field = text_field || 'B';
    END LOOP;
END $$;
```
Просмотр нового размера файла с таблицей

```sql
 SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 524 MB
(1 row)
```
Отключение авто-вакуума для таблицы
```sql
 ALTER TABLE my_table SET (autovacuum_enabled = false);
ALTER TABLE
```

```sql
SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1008 MB
(1 row)
```

Обновление всех строк 10 раз
```sql
DO $$
BEGIN
    FOR i IN 1..10 LOOP
        UPDATE my_table SET text_field = text_field || 'D';
        RAISE NOTICE 'Step %', i;
    END LOOP;
END $$;
```
Просмотр размера файла с таблицей снова

```sql
SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1392 MB
(1 row)
```




Включение авто-вакуума

```sql
ALTER TABLE my_table SET (autovacuum_enabled = true);
```

Дополнительная анонимная процедура

```sql
DO $$
BEGIN
    FOR i IN 1..10 LOOP
        UPDATE my_table SET text_field = text_field || 'D';
        RAISE NOTICE 'Step %', i;
    END LOOP;
END $$;
```
На этапе 9 размер файла может увеличиваться, несмотря на обновление строк. При обновлении строк в PostgreSQL создаются новые версии строк, а старые версии остаются в таблице и становятся мертвыми. Поскольку авто-вакуум был отключен, эти мертвые строки не удаляются, что увеличивает общий размер файла. После выполнения авто-вакуума мертвые строки были бы убраны, и размер файла уменьшился.


```sql
postgres=# ALTER TABLE my_table SET (autovacuum_enabled = true);
ALTER TABLE
postgres=# SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1662 MB
(1 row)

postgres=# DO $$
BEGIN
    FOR i IN 1..10 LOOP
        UPDATE my_table SET text_field = text_field || 'D';
        RAISE NOTICE 'Step %', i;
    END LOOP;
END $$;
NOTICE:  Step 1
NOTICE:  Step 2
NOTICE:  Step 3
NOTICE:  Step 4
NOTICE:  Step 5
NOTICE:  Step 6
NOTICE:  Step 7
NOTICE:  Step 8
NOTICE:  Step 9
NOTICE:  Step 10
DO
postgres=# SELECT pg_size_pretty(pg_total_relation_size('my_table'));
 pg_size_pretty
----------------
 1662 MB
(1 row)

```
