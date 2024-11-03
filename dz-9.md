```sql
SELECT 
    e.id AS employee_id,
    e.first_name,
    e.last_name,
    s.amount,
    s.from_date,
    COALESCE(s.amount - LAG(s.amount) OVER (PARTITION BY s.fk_employee ORDER BY s.from_date), 0) AS salary_increase,
    g.value AS grade_value,
    tn.title AS title 
FROM 
    employee e
JOIN 
    salary s ON e.id = s.fk_employee
JOIN 
    grade g ON s.fk_grade = g.id
LEFT JOIN 
    title t ON t.fk_employee = s.fk_employee 
              AND s.from_date >= t.from_date 
              AND (t.to_date IS NULL OR t.to_date >= s.from_date)
LEFT JOIN 
    title_name tn ON t.fk_titlename = tn.id  
ORDER BY 
    e.id, s.from_date;


```

```


| employee_id | first_name | last_name | amount | from_date  | salary_increase | grade_value | title            |
| ----------- | ---------- | --------- | ------ | ---------- | --------------- | ----------- | ---------------- |
| 1           | Eugene     | Aristov   | 100000 | 2024-01-01 | 0               | junior      | manager          |
| 1           | Eugene     | Aristov   | 200000 | 2024-02-01 | 100000          | middle      | manager          |
| 1           | Eugene     | Aristov   | 300000 | 2024-03-01 | 100000          | senoir      | manager          |
| 1           | Eugene     | Aristov   | 300000 | 2024-03-01 | 0               | senoir      | vice president   |
| 2           | Ivan       | Ivanov    | 200000 | 2023-01-01 | 0               | middle      | teamlead         |
| 3           | Petr       | Petrov    | 200000 | 2024-03-01 | 0               | middle      | python developer |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/eQ8zNtAFY88i8nB4GRB65V/0)


```
