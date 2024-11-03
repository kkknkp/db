```sql
$ docker build -t my-postgres-image .
$ docker run -p 5432:5432 --rm -e POSTGRES_PASSWORD=postgres my-postgres-image5
```


Создание базы
```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    customer_id INT NOT NULL
);


```
Функция выбора трети года
```aql
CREATE OR REPLACE FUNCTION get_third_year_order(date_input DATE)
RETURNS INTEGER AS $$
BEGIN
    RETURN CASE 
        WHEN date_input IS NULL THEN NULL
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 1 AND 4 THEN 1
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 5 AND 8 THEN 2
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 9 AND 12 THEN 3
    END;
END;
$$ LANGUAGE plpgsql;   ----- переделала


```
Математическая функция
```aql

CREATE OR REPLACE FUNCTION get_third(date_input DATE)   ------переделала
RETURNS INTEGER AS $$
BEGIN
    RETURN CASE 
        WHEN date_input IS NULL THEN NULL 
        ELSE (EXTRACT(MONTH FROM date_input) - 1) / 4 + 1
    END;
END;
$$ LANGUAGE plpgsql;


```

```sql
проверка скорости выполнения    -----переделала

EXPLAIN ANALYZE
SELECT * FROM sales WHERE amount > 100;

```
 Seq Scan on sales  (cost=0.00..29.62 rows=523 width=24) (actual time=0.006..0.0
06 rows=0 loops=1)
   Filter: (amount > '100'::numeric)
 Planning Time: 0.471 ms
 Execution Time: 0.020 ms


Сделать селект

```sql
postgres=# SELECT id, sale_date, amount, get_third_year_order_case(sale_date) AS   ------переделала
 third_order_case
postgres-# FROM sales;
 id | sale_date  | amount | third_order_case
----+------------+--------+------------------
  6 | 2023-01-15 | 150.00 |                1
  7 | 2023-05-22 | 200.00 |                2
  8 | 2023-09-10 | 250.00 |                3
  9 | 2023-12-05 | 300.00 |                3
(4 rows)
postgres=# SELECT id, sale_date, amount, get_third_year_order_math(sale_date) AS
 third_order_math
postgres-# FROM sales;
 id | sale_date  | amount | third_order_math
----+------------+--------+------------------
  6 | 2023-01-15 | 150.00 |                1
  7 | 2023-05-22 | 200.00 |                2
  8 | 2023-09-10 | 250.00 |                3
  9 | 2023-12-05 | 300.00 |                4
(4 rows)

