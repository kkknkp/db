
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
   - Общее количество обработанных транзакций: 12,232
   - Средняя задержка: 48.893 мс
   - Время установления соединения: 312.030 мс
   - Транзакции в секунду (TPS): 204.530 
