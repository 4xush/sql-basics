top nth salary - 

sqlite> select * from worker order by salary desc limit = 5;
Parse error: near "=": syntax error
  select * from worker order by salary desc limit = 5;
                                    error here ---^
sqlite> select * from worker order by salary desc limit 5;
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
2|Amitabh|Singh|500000|2014-02-20 09:00:00|Admin
10|Meena|Chopra|500000|2020-09-12 10:00:00|Finance
1|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
6|Anita|Iyer|400000|2023-05-10 09:00:00|HR
4|Kiran|Patel|320000|2021-12-01 09:00:00|Admin
sqlite> 


nth highest salary -- 
use offset = n-1 

SELECT *
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 4;

