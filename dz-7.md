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
 Limit  (cost=329965.00..329965.02 rows=10 width=56) (actual time=4701.377..4701
.657 rows=10 loops=1)
   ->  Sort  (cost=329965.00..330322.88 rows=143155 width=56) (actual time=4677.
956..4678.233 rows=10 loops=1)
         Sort Key: r.startdate
         Sort Method: top-N heapsort  Memory: 25kB
         ->  Group  (cost=324366.26..326871.47 rows=143155 width=56) (actual tim
e=4437.265..4608.845 rows=144000 loops=1)
               Group Key: r.id, (((bs.city || ', '::text) || bs.name)), (count(t
.id)), (count(s_1.id))
               ->  Sort  (cost=324366.26..324724.14 rows=143155 width=56) (actua
l time=4437.217..4494.939 rows=144000 loops=1)
                     Sort Key: r.id, (((bs.city || ', '::text) || bs.name)), (co
unt(t.id)), (count(s_1.id))
                     Sort Method: external merge  Disk: 7880kB
                     ->  Hash Join  (cost=257234.75..307214.02 rows=143155 width
=56) (actual time=2896.000..4302.592 rows=144000 loops=1)
:


```
