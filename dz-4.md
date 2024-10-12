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
