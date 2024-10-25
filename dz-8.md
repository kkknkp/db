```sql
$ docker build -t my-postgres-image .
$ docker run -p 5432:5432 --rm -e POSTGRES_PASSWORD=postgres my-postgres-image5
```


Создание базы
```sql
CREATE TABLE sales (
    sales_id SERIAL PRIMARY KEY,
    sale_date DATE NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    product_id INT NOT NULL
);

```
