Проверка скорости выполнения сложного запроса
```sql
EXPLAIN ANALYZE WITH all_place AS (
    SELECT count(s.id) as all_place, s.fkbus as fkbus
    FROM book.seat s
    group by s.fkbus
),
order_place AS (
    SELECT count(t.id) as order_place, t.fkride
    FROM book.tickets t
    group by t.fkride
)
SELECT r.id, r.startdate as depart_date, bs.city || ', ' || bs.name as busstation,  
      t.order_place, st.all_place
FROM book.ride r
JOIN book.schedule as s
      on r.fkschedule = s.id
JOIN book.busroute br
      on s.fkroute = br.id
JOIN book.busstation bs
      on br.fkbusstationfrom = bs.id
JOIN order_place t
      on t.fkride = r.id
JOIN all_place st
      on r.fkbus = st.fkbus
GROUP BY r.id, r.startdate, bs.city || ', ' || bs.name, t.order_place,st.all_place
ORDER BY r.startdate
limit 10;

```

```sql
   QUERY PLAN

--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
---------------------------------------------
 Planning Time: 2.411 ms
 JIT:
   Functions: 84
   Options: Inlining false, Optimization false, Expressions true, Deforming true
   Timing: Generation 4.954 ms, Inlining 0.000 ms, Optimization 2.715 ms, Emissi
on 48.443 ms, Total 56.111 ms
 Execution Time: 4724.802 ms


```
Добавить индексы
```sql

CREATE INDEX idx_busroute_fkbusstationfrom ON book.busroute (fkbusstationfrom);

CREATE INDEX idx_busroute_fkbusstationto ON book.busroute (fkbusstationto);

CREATE INDEX idx_ride_fkbus ON book.ride (fkbus);

CREATE INDEX idx_ride_fkschedule ON book.ride (fkschedule);

CREATE INDEX idx_schedule_fkroute ON book.schedule (fkroute);

CREATE INDEX idx_seat_fkbus ON book.seat (fkbus);

CREATE INDEX idx_seat_fkseatcategory ON book.seat (fkseatcategory);

CREATE INDEX idx_tickets_fkride ON book.tickets (fkride);

CREATE INDEX idx_tickets_fkseat ON book.tickets (fkseat);


```
