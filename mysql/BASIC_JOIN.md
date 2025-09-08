JOIN -

apply when there is common attribute in table

Select c.\* , o.order_name -> Select all column of customer table and only order_name column from order table;

INNER JOIN - ONLY ONE COMMON COLUMN VALUE'S ROW

MariaDB [punisher]> select w.\* , t.worker_title from worker w inner join title t on w.worker_id = t.worker_ref_id;
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE | DEPARTMENT | worker_title |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Sr. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Asst. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | HR Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Finance Manager |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+

here row with worker_id 1 is not present in title table so it is not shown in result;

LEFT JOIN - ALL ROW OF LEFT TABLE + COMMON COLUMN VALUE'S ROW

MariaDB [punisher]> select w.\* , t.worker_title from worker w left join title t on w.worker_id = t.worker_ref_id;

+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE | DEPARTMENT | worker_title |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | NULL |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Sr. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Asst. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | HR Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Finance Manager |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+

here all row of left table(worker) is shown and row with worker_id 1 is shown with NULL value in worker_title column;

RIGHT JOIN - ALL ROW OF RIGHT TABLE + COMMON COLUMN VALUE'S ROW

MariaDB [punisher]> select w.\* , t.worker_title from worker w right join title t on w.worker_id = t.
worker_ref_id;
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE | DEPARTMENT | worker_title |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Sr. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Asst. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | HR Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Finance Manager |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
9 rows in set (0.000 sec)

FULL JOIN - ALL ROW OF BOTH TABLE + COMMON COLUMN VALUE'S ROW
MariaDB [punisher]> select w._ , t.worker_title from worker w full join title t on w.worker_id = t.worker_ref_id;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'full join title t on w.worker_id = t.worker_ref_id' at line 1  
MariaDB [punisher]> select w._ , t.worker_title from worker w left join title t on w.worker_id = t.worker_ref_id
-> union
-> select w.\* , t.worker_title from worker w right join title t on w.worker_id = t.worker_ref_id;

    +-----------+------------+-----------+--------+---------------------+------------+-----------------+

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE | DEPARTMENT | worker_title |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | NULL |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Sr. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Asst. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | HR Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Finance Manager |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+

CARTISIAN JOIN - ALL ROW OF BOTH TABLE
MariaDB [punisher]> select w.\* , t.worker_title from worker w , title t

result in 90 rows because 10\*9 = 90;

+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE | DEPARTMENT | worker_title |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Sr. Manager |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Lead |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Asst. Manager |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Executive |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Manager |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Lead |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Executive |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | HR Executive |
| 1 | Amit | Sharma | 450000 | 2015-03-15 09:00:00 | Admin | Finance Manager |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Sr. Manager |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Lead |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Asst. Manager |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Executive |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Manager |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Lead |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Executive |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | HR Executive |
| 2 | Amitabh | Singh | 500000 | 2014-02-20 09:00:00 | Admin | Finance Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Sr. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Asst. Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Executive |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Manager |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Lead |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Executive |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | HR Executive |
| 3 | Arjun | Rao | 200000 | 2021-04-20 09:15:00 | Account | Finance Manager |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Sr. Manager |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Asst. Manager |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Executive |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Manager |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Lead |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Executive |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | HR Executive |
| 4 | Kiran | Patel | 320000 | 2021-12-01 09:00:00 | Admin | Finance Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Sr. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Lead |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Asst. Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Manager |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Lead |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Executive |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | HR Executive |
| 5 | Suresh | Nair | 150000 | 2022-08-17 14:00:00 | Finance | Finance Manager |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Sr. Manager |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Lead |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Asst. Manager |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Manager |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Lead |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | HR Executive |
| 6 | Anita | Iyer | 400000 | 2023-05-10 09:00:00 | HR | Finance Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Sr. Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Asst. Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Executive |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Manager |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Lead |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Executive |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | HR Executive |
| 7 | Rohit | Mehra | 300000 | 2017-01-10 09:00:00 | Account | Finance Manager |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Sr. Manager |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Asst. Manager |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Manager |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Lead |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Executive |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | HR Executive |
| 8 | Priya | Gupta | 250000 | 2018-11-05 11:00:00 | Admin | Finance Manager |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Sr. Manager |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Lead |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Asst. Manager |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Manager |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Lead |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | HR Executive |
| 9 | Vikram | Singh | 80000 | 2019-06-18 09:00:00 | HR | Finance Manager |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Sr. Manager |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Lead |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Asst. Manager |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Manager |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Lead |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | HR Executive |
| 10 | Meena | Chopra | 500000 | 2020-09-12 10:00:00 | Finance | Finance Manager |
+-----------+------------+-----------+--------+---------------------+------------+-----------------+

CROSS JOIN - ALL ROW OF BOTH TABLE
MariaDB [punisher]> select w.\* , t.worker_title from worker w cross join title t;
