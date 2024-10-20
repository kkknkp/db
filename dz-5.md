
Инициализация базы данных для теста:
```sql
   sudo -u postgres pgbench -i -s 10 postgres
```

Запуск теста нагрузки:
```sql

   sudo -u postgres pgbench -c 10 -j 2 -T 60 postgres

```
https://github.com/kkknkp/db/blob/main/2024-10-20_18-50-49.png
