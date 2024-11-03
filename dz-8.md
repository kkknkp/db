Создание таблицы с 1млн документов jsonb

```sql
CREATE TABLE jsonb_data (
    id SERIAL PRIMARY KEY,
    data JSONB
);

DO $$
DECLARE 
    i INT;
BEGIN
    FOR i IN 1..1000000 LOOP
        INSERT INTO jsonb_data (data) 
        VALUES (jsonb_build_object('field1', i, 'field2', 'some_value', 'field3', i * 10));
    END LOOP;
END $$;
```

Создание индекса

```sql
CREATE INDEX idx_jsonb_field1 ON jsonb_data USING GIN ((data->'field1'));

```
Обновить 1 из полей в json

```sq
lUPDATE jsonb_data 
SET data = jsonb_set(data, '{field2}', '"updated_value"') 
WHERE id = 1;
UPDATE 1

```
Убедиться в блоатинге TOAST
```sql
SELECT 
    pg_size_pretty(pg_total_relation_size('jsonb_data')) AS total_size,
    pg_size_pretty(pg_relation_size('jsonb_data')) AS table_size,
    pg_size_pretty(pg_relation_size('jsonb_data') - pg_total_relation_size('jsonb_data')) AS toast_size;

```
```
 total_size | table_size | toast_size
------------+------------+------------
 185 MB     | 112 MB     | -73 MB
```

 Избавление от блаутинга
```sql
VACUUM jsonb_data;
REINDEX TABLE jsonb_data;
ANALYZE jsonb_data;
    
```
 Проверка 
 
```sql
SELECT 
    pg_size_pretty(pg_total_relation_size('jsonb_data')) AS total_size,
    pg_size_pretty(pg_relation_size('jsonb_data')) AS table_size,
    pg_size_pretty(pg_relation_size('jsonb_data') - pg_total_relation_size('jsonb_data')) AS toast_size;
```
```
 total_size | table_size | toast_size
------------+------------+------------
 112 MB     | 112 MB     | -56 kB
```

 Блоатинг TOAST применен и система освободила место в хранилище.
