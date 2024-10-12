Открытие двух сессий

```
$ winpty docker exec -it db-postgres-1 psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg120+1))
Type "help" for help.
$ winpty docker exec -it db-postgres-1 psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg120+1))
Type "help" for help.
```
Создание таблицы 
```sql
CREATE TABLE accounts (
    id integer PRIMARY KEY,
    amount numeric
);

```
Заполнение талицы
```sql
INSERT INTO accounts (id, amount) VALUES (1, 100.00), (2, 200.00);

```
Терминал 1 
```sql
BEGIN;
UPDATE accounts SET amount = amount + 50 WHERE id = 1;

UPDATE accounts SET amount = amount + 50 WHERE id = 2;


```
Теринал 2

```sql
BEGIN;
UPDATE accounts SET amount = amount + 50 WHERE id = 2;

UPDATE accounts SET amount = amount + 50 WHERE id = 1;  


```

Дедлок

```sql
ERROR:  deadlock detected
DETAIL:  Process 60 waits for ShareLock on transaction 765; blocked by process 4
0.
Process 40 waits for ShareLock on transaction 766; blocked by process 60.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,1) in relation "accounts"
```
