
PRE PROCESSING - 

sqlite> select concat(first_name,' ', last_name)as fullName from worker;
fullName
Amit Sharma
Amitabh Singh
Arjun Rao
Kiran Patel
Suresh Nair
Anita Iyer
Rohit Mehra
Priya Gupta
Vikram Singh
Meena Chopra


UPPER / LOWER CASE OUTPUT- 

sqlite> SELECT UPPER(first_name) AS upper_title
FROM worker_clone;
upper_title
AMIT
AMITABH
ARJUN
KIRAN
SURESH
ANITA
ROHIT
PRIYA
VIKRAM
MEENA
sqlite> 



sqlite> select department,count(worker_id) as worker_count from worker group by
 department order by worker_count desc;
DEPARTMENT|worker_count
Admin|4
HR|2
Finance|2
Account|2
sqlite> 

DATES,TIME , YEAR AND MONTHS - 

select * from worker where year(joining_date) = 2021 and month(joining_date) = 02;
--only work in sql not in sqlite

DATE - 
sqlite> SELECT date('now') AS current_date;
2025-09-07
sqlite> 


TIME - 
sqlite> SELECT time('now') AS current_time;
20:02:28
sqlite> 


JOINS --


sqlite> select w.worker_id, w.first_name, t.worker_title from worker w join tit
le t on w.worker_id = t.worker_ref_id where t.worker_title like '%manager%';

WORKER_ID|FIRST_NAME|WORKER_TITLE
1|Amit|Manager
2|Amitabh|Sr. Manager
4|Kiran|Asst. Manager
6|Anita|Manager
10|Meena|Finance Manager
sqlite> 





sqlite> select t.worker_title , count(*) as no_of_worker from title t group by t. worker_title having no_of_worker > 1;
WORKER_TITLE|no_of_worker
Executive|2
Lead|2
Manager|2
sqlite> 


EXPRESSION - 
CAN ALSO - MOD(WORKER_ID , 2) != 0;


sqlite> select * from worker w where w.worker_id%2 != 0;
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
1|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
3|Arjun|Rao|200000|2021-04-20 09:15:00|Account
5|Suresh|Nair|150000|2022-08-17 14:00:00|Finance
7|Rohit|Mehra|300000|2017-01-10 09:00:00|Account
9|Vikram|Singh|80000|2019-06-18 09:00:00|HR
sqlite> 





CLONE A table by cloning its schema ands its data **

1️⃣ Using CREATE TABLE AS (copies structure + data)

CREATE TABLE worker_clone AS
SELECT *
FROM worker
WHERE 0;

    WHERE 0 ensures no data is copied, only the structure.

    This will create worker_clone with the same columns as worker

sqlite> insert into worker_clone select * from worker;


INTERSECTION - 

sqlite> select worker.* from worker inner join worker_clone using(worker_id);
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
1|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
2|Amitabh|Singh|500000|2014-02-20 09:00:00|Admin
3|Arjun|Rao|200000|2021-04-20 09:15:00|Account
4|Kiran|Patel|320000|2021-12-01 09:00:00|Admin
5|Suresh|Nair|150000|2022-08-17 14:00:00|Finance
6|Anita|Iyer|400000|2023-05-10 09:00:00|HR
7|Rohit|Mehra|300000|2017-01-10 09:00:00|Account
8|Priya|Gupta|250000|2018-11-05 11:00:00|Admin
9|Vikram|Singh|80000|2019-06-18 09:00:00|HR
10|Meena|Chopra|500000|2020-09-12 10:00:00|Finance
sqlite> 

IS NULL - 

sqlite> select * from worker w left join worker_clone c  on w.worker_id = c.worker_id where c.worker_id is null;


SET OPERATION - 

intersection 

sqlite> select * from worker w left join worker_clone c  on w.worker_id = c.worker_id where c.worker_id is null;


