
Инициализация базы данных для теста:
```sql
   sudo -u postgres pgbench -i -s 10 postgres
```

Запуск теста нагрузки:
```sql

   sudo -u postgres pgbench -c 10 -j 2 -T 60 postgres

```
https://github.com/kkknkp/db/blob/main/2024-10-20_18-50-49.png


Запуск теста нагрузки на реплике:
```sql

 sudo -u postgres pgbench -c 10 -j 2 -T 60 -h 127.0.0.1 -p 5433 postgres

```

https://github.com/kkknkp/db/blob/main/2024-10-20_19-00-17.png

Результаты теста:
tcp:318.842905
tcp:231.717289



```sql 
$ winpty docker exec -it pg_test bash -c "PGUSER=postgres pgbench -i -s 10 postgres"
dropping old tables...
NOTICE:  table "pgbench_accounts" does not exist, skipping
NOTICE:  table "pgbench_branches" does not exist, skipping
NOTICE:  table "pgbench_history" does not exist, skipping
NOTICE:  table "pgbench_tellers" does not exist, skipping
creating tables...
generating data (client-side)...
1000000 of 1000000 tuples (100%) done (elapsed 3.32 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 4.58 s (drop tables 0.02 s, create tables 0.06 s, client-side generate 3
.39 s, vacuum 0.30 s, primary keys 0.81 s).

```

```sql
$ winpty docker exec -it pg_test bash -c "PGUSER=postgres pgbench -c 10 -j 2 -T 60 postgres"
pgbench (16.4 (Debian 16.4-1.pgdg120+2))
starting vacuum...end.
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 10
query mode: simple
number of clients: 10
number of threads: 2
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 48232
number of failed transactions: 0 (0.000%)
latency average = 12.440 ms
initial connection time = 36.209 ms
tps = 803.850282 (without initial connection time)

```
