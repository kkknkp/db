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
