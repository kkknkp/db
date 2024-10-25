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
CREATE OR REPLACE FUNCTION get_quarter_order(date_input DATE)
RETURNS INTEGER AS $$
BEGIN
    RETURN CASE 
        WHEN date_input IS NULL THEN NULL -- предусмотреть NULL на входе
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 1 AND 3 THEN 1
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 4 AND 6 THEN 2
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 7 AND 9 THEN 3
        WHEN EXTRACT(MONTH FROM date_input) BETWEEN 10 AND 12 THEN 4
    END;
END;
$$ LANGUAGE plpgsql;

```
Математическая функция
```aql

CREATE OR REPLACE FUNCTION get_quarter_order_math(date_input DATE)
RETURNS INTEGER AS $$
BEGIN
    RETURN CASE 
        WHEN date_input IS NULL THEN NULL 
        ELSE (EXTRACT(MONTH FROM date_input) - 1) / 3 + 1
    END;
END;
$$ LANGUAGE plpgsql;

```

```sql
проверка скорости выполнения

EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE total_amount > 500; 

```
"Seq Scan on orders  (cost=0.00..28.12 rows=483 width=28) (actual time=0.004..0.005 rows=0 loops=1)"
"  Filter: (total_amount > '500'::numeric)"
"Planning Time: 0.157 ms"
"Execution Time: 0.012 ms"

Сделать селект

```sql
sqlSELECT 
    o.order_id,
    o.order_date,
    o.total_amount,
    get_quarter_order(o.order_date) AS quarter,
    get_quarter_order_math(o.order_date) AS quarter_math
FROM orders o;


1	"2023-01-15"	100.00	1	1
2	"2023-02-20"	150.50	1	1
3	"2023-03-10"	200.00	1	2
4	"2023-04-05"	250.75	2	2
5	"2023-05-12"	300.00	2	2
6	"2023-01-15"	100.00	1	1
7	"2023-02-20"	150.50	1	1
8	"2023-03-10"	200.00	1	2
9	"2023-04-05"	250.75	2	2
10	"2023-05-12"	300.00	2	2
11	"2023-06-01"	120.00	2	3
12	"2023-06-15"	400.00	2	3
13	"2023-07-20"	250.00	3	3
14	"2023-08-10"	320.50	3	3
15	"2023-09-01"	180.25	3	4
16	"2023-09-15"	210.00	3	4
17	"2023-10-10"	350.00	4	4
18	"2023-11-05"	480.80	4	4
19	"2023-11-20"	400.00	4	4
20	"2023-12-15"	150.00	4	5
21	"2023-12-20"	275.50	4	5

```
