```
$ winpty docker exec -it db-postgres-1 psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg120+1))
Type "help" for help.
$ winpty docker exec -it db-postgres-1 psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg120+1))
Type "help" for help.
```

```sql
CREATE TABLE accounts (
    id integer PRIMARY KEY,
    amount numeric
);

```
```sql
INSERT INTO accounts (id, amount) VALUES (1, 100.00), (2, 200.00);

```
```sql
ERROR:  deadlock detected
DETAIL:  Process 60 waits for ShareLock on transaction 765; blocked by process 4
0.
Process 40 waits for ShareLock on transaction 766; blocked by process 60.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,1) in relation "accounts"
```
