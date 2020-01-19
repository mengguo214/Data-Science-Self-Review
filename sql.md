
DATABASE DESIGN
==================

Some of the explanations are from https://www.w3schools.com/sql/default.asp

<!-- more -->
RELATIONAL DATABASE DESIGN
==================

First normal form
------------------
A relation is in first normal form if and only if the domain of each attribute contains only atomic (indivisible) values, and the value of each attribute contains only a single value from that domain
每个属性不可再分。

Second normal form
------------------
A relation is in the second normal form if it fulfills the following two requirements:
  (1) It is in first normal form.
  (2)It does not have any non-prime attribute that is functionally dependent on any proper subset of any candidate key of the relation. A non-prime attribute of a relation is an attribute that is not a part of any candidate key of the relation.
消除非主属性对key的部分函数依赖。

Steps:
 1. find all keys
 2. based on keys found, determine prime attributes
 3. identify non-prime attributes
 4. check whether there is partial dependent relationship between non-prime attributes and keys

Third normal form
------------------
Third normal form (3NF) is a normal form that is used in normalizing a database design to reduce the duplication of data and ensure referential integrity by ensuring that:
  (1) The entity is in second normal form.
  (2) No non-prime (non-key) attribute is transitively dependent on any key i.e. no non-prime attribute depends on other non-prime attributes. All the non-prime attributes must depend only on the keys.
不保留非主属性对传递函数依赖
比如学号->系名->系主任，系主任就不能保留

SQL COMMANDS
==================

Two types of commands:
1. Data Manipulation
2. Data Definition

Data Definition
------------------
Relational Data Structures:


tables,


indexes,


views, help us to focus data for a particular use


schemas, overall structure


(1) Create
------------------
```
CREATE TABLE staff(
  id INTEGER
  last_name VARCHAR(30),
  department_name VARCHAR(25),
  start_date DATE,
  PRIMARY KEY (id)
  )
```
(2) Create Index
------------------
```
CREATE INDEX idx_staff_last_name
ON staff
USING (last_name)
```

(3) CREATE VIEW
```
CREATE VIEW staff_div AS
SELECT
s.id
cd.company_division
FROM staff s
LEFT JOIN company_divisions cd
ON ...
```

(4) INSERT INTO
------------------
加一行中的指定列：

```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

从其他表中选出后插入
```
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```
(5) UPDATE
------------------

当没有where condition 所有都会被update

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

(6) DELETE
------------------

Delete a row satisfies the where clause

`DELETE FROM table_name WHERE condition;``



DATA MANIPULATION
------------------

(1) SELECT
------------------

```
SELECT TOP number|percent DISTINCT column1
FROM table_name
WHERE condition
ORDER BY column2 ASC|DESC;

```

```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```

*Logic Query Processing:
FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY*

*The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.*

 *Operator in WHERE clause:
= < <= > >= <>(sometimes != or NOT...=) BETWEEN a AND b, IN, LIKE*

```
SELECT * FROM table_name
WHERE column1 IN ('A','B');
```

```
SELECT * FROM table_name
WHERE column1 IN (SELECT column1 FROM table_name);
```



(2) ANY and ALL
------------------
The ANY and ALL operators are used with a WHERE or HAVING clause.

The ANY operator returns true if any of the subquery values meet the condition.

The ALL operator returns true if all of the subquery values meet the condition.
全部满足才输出


(3) AS
------------------
```
SELECT CustomerName, Address + ', ' + PostalCode + ' ' + City + ', ' + Country AS Address
FROM Customers;
```

OR IN MYSQL:

```
SELECT CustomerName, CONCAT(Address,', ',PostalCode,', ',City,', ',Country) AS Address
FROM Customers;
```

IN PostgreSQL
```
SELECT Address || ', ' || PostalCode || ' ' || City || ', ' || Country AS Address
FROM Customers;
```

(4) CASE WHEN
------------------
The CASE statement goes through conditions and returns a value when the first condition is met (like an IF-THEN-ELSE statement). So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the ELSE clause.

If there is no ELSE part and no conditions are true, it returns NULL.

```
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN "The quantity is greater than 30"
    WHEN Quantity = 30 THEN "The quantity is 30"
    ELSE "The quantity is under 30"
END AS QuantityText
FROM OrderDetails;
```
```
SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);
```
活用case when 可以大大提高query效率和可读性： case when + aggregation
```sum(case when ... then .... else ...end) as
count(case when ... then .... else ...end) as
```
一次join算好几个metrics，大大提高效率和可读性




(5) Cast()

transfer types

```
CAST('2019-01-01' AS DATE)
```

(6) EXISTS
------------------
The EXISTS operator is used to test for the existence of any record in a subquery.

The EXISTS operator returns true if the subquery returns one or more records.

```
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
```

The exists keyword can be used in that way, but really it's intended as a way to avoid counting:

`--this statement needs to check the entire table
select count(*) from [table] where ...

--this statement is true as soon as one match is found
exists ( select * from [table] where ... )
This is most useful where you have if conditional statements, as exists can be a lot quicker than count.`


(7) Functions TBD
------------------

MIN(), MAX(), COUNT(), AVG(), SUM()


VAR_POP(), STDDEV_POP()


UPPER(), LOWER()


TRIM()  remove extra spaces


LENGTH()

OTHER FUNCTIONS:

```
SELECT (name LIKE '%B%') as name1,  SUBSTRING('abcdfs' FROM 1 FOR 3),
OVERLAY(job_tile PLACING 'Assit' FROM 1 FOR 10 ),
job_tile SIMILAR TO '%Assistant%(I||II)',
TRUNC(,3) # Just ignore the decimal point after
CEIL()
FLOOR()
ROUND(column, 2)
GROUP BY GROUPING SETS((a),(b)) #is equivalent to GROUP BY a UNION ALL GROUP BY b
GROUP BY ROLLUP(a,b)
```
ROLLUP. ROLLUP enables a SELECT statement to calculate multiple levels of subtotals across a specified group of dimensions. It also calculates a grand total. ROLLUP is a simple extension to the GROUP BY clause, so its syntax is extremely easy to use.

More to find for CUBE

FETCH FIRST n ROWS ONLY has the following benefits: When you use FETCH statements to retrieve data from a result table, the fetch clause causes DB2 to retrieve only the number of rows that you need. - PostgreSQL

Window Functions
------------------
Window functions allow us to make SQL statements about rows related to the current row during processing. like subquery

1. Aggregate: AVG, SUM, COUNT, MIN, MAX

2. OFFSET: LAG, LEAD, FIRST_VALUE, LAST_VALUE

```
AVG() OVER (PARTITION BY column_name)
FIRST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC)
```
give the values of row value that came before the current processed row:
```
LAG(salary, n, m) OVER (PARTITION BY department ORDER BY salary DESC)
```
后n个和前n个; null 变成 m
give the values of row value that came next the current processed row:
```
LEAD(salary, n, m) OVER (PARTITION BY department ORDER BY salary DESC)
```

3. RANK
These three functions will number each row. Using row_number gives a result that must always be unique. Each row is assigned a different value even if they are equal

The easiest way to explain rank and dense_rank is to imagine ranking the runners of a race. Consider: If 2 runners finish in equal 3rd, is the next runner's place 4th (dense_rank) or 5th (rank).

give info about where it lies in the whole group, 应用在全表的时候可以不用PARTITION BY
```
ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC)
RANK() OVER (PARTITION BY department ORDER BY salary DESC)
DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC)
```
还可以group by后根据group后的值进行window rank

4. nth_value (& first_value) and ntile
Unsurprisngly nth_value will return the nth_value, but if we do not specify a range it will return null if the current value is less than the nth. If we always want something displayed we need to specify a range

ntile(n) divides the group into n equal partitions and denotes which partition each row is in.

Let us go back to our race and print how much faster a runner needs to go to finish on the podium (1st, 2nd or 3rd), the time of the second runner and finally we will use ntile(2) to determine if they are in the top half of runners
```
 select name, time,
 nth_value(time, 3) over (order by time) - time as to_go_faster_to_make_podium,
 nth_value(time, 2) over (order by time RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) as time_of_second_runner,
 ntile(2) over (order by time) as which_half
 FROM runners order by time;
 ```

give info about quartile
```
NTILE(4) OVER (PARTITION BY department ORDER BY salary DESC)
```

5. Calculate Running total

BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW 是默认值
```
SUM(TOTAL) OVER (ORDER BY Order_Date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)

```
6. 取中位数
我的方法是用 ntile(2) 然后分奇偶情况取
或者用row_number正排和倒排，中位数是正排==倒排或者正排==倒排+1｜-1

7. Preceding and Following
Preceding and Following allow us to perform aggregate functions on the rows just before and after the current row.

```
select
name, weight,
min(weight) over (order by name ROWS between 1 preceding and 1 following)
from runners order by name
```
*Use Unbounded Preceding to make sure you don't include extra rows if 2 rows evaluate to the same thing*
```
select
name,
sum(weight) over (order by weight desc rows between unbounded preceding and current row)
 from cats
order by weight desc
```

8. Cume_dist & Percent_rank
These 2 functions calculate the relative rank of a group of rows

percent_rank returns a number from 1 to 0. The highest being 1 and the lowest 0.
cume_dist will return a number from 1 towards 0 but never 0.
Think of it this way: If there are 4 different values do you count down from 1 in steps of 0.25 (percent_rank) or in steps of 0.2 ensuring that we never hit 0 (cume_dist)
```
 select name, time,
 percent_rank() over (order by time),
 cume_dist() over (order by time)
 FROM runners order by time
```
```
select
name,
weight,
cast(cume_dist() over (order by weight)*100 as int)
 from cats
order by weight
```

9. Filter
Filter is to be used with aggregate functions or window functions. It allows us to filter out values

In this example we print average runner time and then filter on the runners weighing less than 90kg to produce the light_runners_time column which will result in bob (UK) being removed.
```
select country,
avg(time) as avg_time,
avg(time) filter (where weight < 90) as light_runners_time
from runners group by country
```

10. Array Agg
Array Agg is not a window function but it is interresting

Array Agg allows us to select several entries into one. Think of it as compressing the values into an Array object
```
select array_agg(time) from runners;
```
```
array_agg
{101,103,104,104,108}
```

(8) COALESCE(A,B,C) -> Return the first Non-NULL value.可以用来将null值转化为其他值。


(9) IN
------------------

The in is best used where you have a static list to pass:

 ```
SELECT * FROM [table]
WHERE [field] in (1, 2, 3);
```

11. window
```
SELECT
  ts,
  group_name,
  metric,
  value,
  AVG(value) OVER w as mov_avg,
  CASE
    WHEN ((value - AVG(value) OVER w)) / (STDEV(value)) OVER w )) > 3 THEN 1
    ELSE 0
  END AS is_outlier
FROM events
WINDOW w AS (PARTITION BY group_name, metric ORDER BY ts ROWS BETWEEN 10 PRECEDING AND 1 PRECEDING)
ORDER BY ts ASC
```


(10) LIKE OR SIMILAR TO
------------------
The LIKE operator is used in a WHERE clause to search for a specified pattern in a column.

There are two wildcards often used in conjunction with the LIKE operator:

% - The percent sign represents zero, one, or multiple characters
_ - The underscore represents a single character

Wildcard
------------------
A wildcard character is used to substitute one or more characters in a string.

Wildcard characters are used with the SQL LIKE operator. The LIKE operator is used in a WHERE clause to search for a specified pattern in a column.
Symbol	Description	Example
%	Represents zero or more characters; bl% finds bl, black, blue, and blob
_	Represents a single character; h_t finds hot, hat, and hit
[] Represents any single character within the brackets; h[oa]t finds hot and hat, but not hit
^	Represents any character not in the brackets; h[^oa]t finds hit, but not hot and hat
-	Represents a range of characters; c[a-b]t finds cat and cbt



(11) JOINs
------------------
Here are the different types of the JOINs in SQL:

(INNER) JOIN: Returns records that have matching values in both tables
LEFT (OUTER) JOIN: Returns all records from the left table, and the matched records from the right table
RIGHT (OUTER) JOIN: Returns all records from the right table, and the matched records from the left table
FULL (OUTER) JOIN: Returns all records when there is a match in either left or right table
A self JOIN is a regular join, but the table is joined with itself.

*The following SQL statement matches customers that are from the same city:*
```
SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
FROM Customers AS A, Customers AS B
WHERE A.CustomerID <> B.CustomerID
AND A.City = B.City
ORDER BY A.City;
```


(12) NULL
------------------

`WHERE column_name IS/IS NOT NULL;`


NULL Functions
------------------

The MySQL IFNULL() function lets you return an alternative value if an expression is NULL:

```
SELECT ProductName, UnitPrice * (UnitsInStock + IFNULL(UnitsOnOrder, 0))
FROM Products;
```

or we can use the COALESCE() function, like this:

```
SELECT ProductName, UnitPrice * (UnitsInStock + COALESCE(UnitsOnOrder, 0))
FROM Products;
```
!不同sql上语法不同，sql server上是isnull(column_name,0),Oracle: NVL(column_name,0)

In SQL server:
```
ISNULL(,)
```

(13) UNION
------------------
The UNION operator is used to combine the result-set of two or more SELECT statements.

Each SELECT statement within UNION must have the same number of columns
The columns must also have similar data types
The columns in each SELECT statement must also be in the same order

```
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

*The UNION operator selects only distinct values by default. To allow duplicate values, use UNION ALL:*

```
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

*Note: The column names in the result-set are usually equal to the column names in the first SELECT statement in the UNION.*

```
SELECT 'Customer' As Type, ContactName, City, Country
FROM Customers
UNION
SELECT 'Supplier', ContactName, City, Country
FROM Suppliers;
```

*Make the first column name as Type*


(14) Date Method

mysql里不可以date之间运算

```
SELECT a.Id
FROM Weather as a, Weather as b
WHERE DATEDIFF(a.recorddate, b.recorddate) = 1 AND (a.Temperature > b.Temperature)
```
in sql server:

```
SELECT DATEDIFF(year, '2017/08/25', '2011/08/25') AS DateDiff;
```

*add/substract date in sql server*
```
DATEADD(MONTH, -5, '9/1/2011')
```
*add/substract date in mysql*
```
DATE_ADD('9/1/2011', INTERVAL 1 DAY)
```

在sql server里取date：left(date, n) 或 Month(date)
in sqllite: strftime('%Y', ratingdate)


(15) With clause
```
WITH query_name1 AS (),
query_name2 AS ()

SELECT * FROM query_name1 JOIN query_name2

```




LEETCODE PROBLEMS
==================

176. Second Highest Salary
------------------

取第二大的，且当空时返回null；
这里有一个用法是
limit 1 offset 1

```
SELECT DISTINCT Salary As SecondHighestSalary
FROM Employee
ORDER BY Salary ASC
LIMIT 1 OFFSET 1
```

这时空返回null的要求达不到，若使用temp table可以

*A temporary table in SQL Server, as the name suggests, is a database table that exists temporarily on the database server. A temporary table stores a subset of data from a normal table for a certain period of time.

Temporary tables are particularly useful when you have a large number of records in a table and you repeatedly need to interact with a small subset of those records. In such cases instead of filtering the data again and again to fetch the subset, you can filter the data once and store it in a temporary table. You can then execute your queries on that temporary table. Temporary tables are stored inside “tempdb” which is a system database. Let’s take a look at how you can use a temporary data in a simple scenario.*

```SELECT name, age, gender
INTO #MaleStudents
FROM student
WHERE gender = 'Male'
```
The name of a temporary table must start with a hash (#)

```
SELECT
(SELECT DISTINCT Salary
FROM Employee
ORDER BY Salary ASC
LIMIT 1 OFFSET 1) As SecondHighestSalary
```


或者用IFNULL function

```
SELECT
IFNULL(
    (SELECT DISTINCT Salary
    FROM Employee
    ORDER BY Salary DESC
    LIMIT 1 OFFSET 1),
    NULL) AS SecondHighestSalary
```

或者换种思路

```
SELECT MAX(Salary) as SecondHighestSalary
FROM Employee
WHERE Salary <
(SELECT MAX(Salary)
FROM Employee);
```

182. Duplicate Emails
------------------

```
SELECT Email
FROM Person
GROUP BY Email
HAVING COUNT(Email) > 1;
```
或者

```
SELECT DISTINCT A.Email
FROM Person as A, Person as B
WHERE A.id <> B.id
AND A.Email == B.Email;
```


196. Delete Duplicate Emails
------------------

```
DELETE A
FROM Person as A, Person AS B
WHERE A.Id > B.Id AND A.Email = B.Email
```

197. Rising Temperature
------------------
*Date Method*
mysql里不可以date之间运算

```
SELECT a.Id
FROM Weather as a, Weather as b
WHERE DATEDIFF(a.recorddate, b.recorddate) = 1 AND (a.Temperature > b.Temperature)
```

*add/substract date in sql server*
```
DATEADD(MONTH, -5, '9/1/2011')
```
*add/substract date in mysql*
```
DATE_ADD('9/1/2011', INTERVAL 1 DAY)
```


627. Swap Salary
------------------
类if else语句的case
```
UPDATE SALARY
SET
SEX = CASE SEX
           WHEN 'm' THEN 'f'
           ELSE 'm'
      END
```


1179. Reformat Department Table
------------------
多个case when

```
select id,
	sum(case when month = 'jan' then revenue else null end) as Jan_Revenue,
	sum(case when month = 'feb' then revenue else null end) as Feb_Revenue,
	sum(case when month = 'mar' then revenue else null end) as Mar_Revenue,
	sum(case when month = 'apr' then revenue else null end) as Apr_Revenue,
	sum(case when month = 'may' then revenue else null end) as May_Revenue,
	sum(case when month = 'jun' then revenue else null end) as Jun_Revenue,
	sum(case when month = 'jul' then revenue else null end) as Jul_Revenue,
	sum(case when month = 'aug' then revenue else null end) as Aug_Revenue,
	sum(case when month = 'sep' then revenue else null end) as Sep_Revenue,
	sum(case when month = 'oct' then revenue else null end) as Oct_Revenue,
	sum(case when month = 'nov' then revenue else null end) as Nov_Revenue,
	sum(case when month = 'dec' then revenue else null end) as Dec_Revenue
from department
group by id
order by id
```

1193. Monthly Transactions I
------------------

Transfer Date Type
corner case: IFNULL使用（加在最内层不管用因为如果没有accept都不会运行！）,不仅时间相同，Country 也要相同
even so, runtime还可以

```
SELECT total.month, total.country, total.trans_count, IFNULL(approve.approved_count,0) AS approved_count, total.trans_total_amount, IFNULL(approve.approved_total_amount,0) AS approved_total_amount
FROM
(SELECT COUNT(id) as approved_count, DATE_FORMAT(trans_date, '%Y-%m') AS month, SUM(amount) as approved_total_amount, country
FROM Transactions
WHERE state = 'approved'
GROUP BY month,country) AS approve
RIGHT JOIN
(SELECT COUNT(id) as trans_count, DATE_FORMAT(trans_date, '%Y-%m') AS month, SUM(amount) as trans_total_amount, country
FROM Transactions
GROUP BY month,country) AS total
ON (approve.month = total.month) and (approve.country = total.country)
GROUP BY total.month, total.country
```

其他的简短方法：

```
SELECT
    DATE_FORMAT(trans_date, '%Y-%m') AS month,
    country,
    COUNT(id) AS trans_count,
    COUNT(IF(state = 'approved', 1, NULL)) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(IF(state='approved', amount, 0)) AS approved_total_amount
FROM Transactions
GROUP BY 1, 2
```
*GROUP BY 1,2*
*IF OR CASE WHEN*

1211. Queries Quality and Percentage
------------------
*Case When 便捷用法*

```
select query_name, round(avg(rating/position), 2) as quality,
       round(100*avg(case when rating<3 then 1 else 0 end), 2) as poor_query_percentage
from Queries
group by query_name
```

1076. Project Employees II
------------------
In MSSQL,

SOLUTION 1:
```
select temp.project_id
from (select project_id, rank() over( order by count(distinct employee_id) desc) as rnk
from project
group by project_id) as temp
where rnk = 1
```

SOLUTION 2:

```
SELECT TOP 1 WITH TIES project_id
FROM Project
GROUP BY project_id
ORDER BY COUNT(employee_id) DESC
```

1083. Sales Analysis II
------------------

```
SELECT s.buyer_id
FROM Sales AS s INNER JOIN Product AS p
ON s.product_id = p.product_id
GROUP BY s.buyer_id
HAVING SUM(CASE WHEN p.product_name = 'S8' THEN 1 ELSE 0 END) > 0
AND SUM(CASE WHEN p.product_name = 'iPhone' THEN 1 ELSE 0 END) = 0;
```

我的思路： accepted should come from request:

```
select ifnull(round(1 - sum(num) / count(num),2),0 )as accept_rate
from
(select sum(case when accept_date is null then 1 else 0 end) as num
from
friend_request left join request_accepted
on sender_id = requester_id and send_to_id = accepter_id
group by sender_id, send_to_id) as mid

```

if not:
```
select ifnull(round(r.num2/ f.num,2),0 ) as accept_rate
from
(select count(distinct sender_id, send_to_id) as num
from friend_request) as f
join
(select count(distinct requester_id, accepter_id) as num2
from
request_accepted) as r
```

other format:

```
SELECT IFNULL(ROUND(
            (SELECT COUNT(DISTINCT requester_id, accepter_id) FROM request_accepted)
            /
            (SELECT COUNT(DISTINCT sender_id, send_to_id) FROM friend_request),
        2), 0)
AS accept_rate

```

1204. Last Person to Fit in the Elevator
------------------

关于roll up，关键在于join的时候把小于该行的都归纳然后group by取和。
我的tedious做法其实本质就是想找小于该列的和<=1000，到该列后>1000的临界点。
其实别人方法很简单：

我一开始思路也是如此，但取的时候是`HAVING SUM(q2.weight) >= 1000` 取最小，思路反了，这样就无法包含不到1000的情况，
正确思路应该是`HAVING SUM(q2.weight) <= 1000` 取最大就好了

```
SELECT q1.person_name
FROM Queue q1, Queue q2
WHERE q1.turn >= q2.turn
GROUP BY q1.turn
HAVING SUM(q2.weight) <= 1000
ORDER BY SUM(q2.weight) DESC
LIMIT 1
```

1045. Customers Who Bought All Products
------------------
本来还在考虑怎么防止有客户买的product没在product table里的情况，
但只要做一个join就可以解决了
有人提到product_id是customer table的foreign key，所以这种情况不会出现 - makes sense!

626. Exchange Seats
------------------
用join的话效率很低，这里用了case when + lead
```
select case when id%2=0 then id-1
when id%2 != 0 and lead(id,1) over (order by id asc) is not null then id + 1
when id%2 != 0 and lead(id,1) over (order by id asc) is null then id

end as id, student
from seat
order by id asc
```
