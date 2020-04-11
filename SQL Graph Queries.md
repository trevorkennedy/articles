# Directed Graph Queries in T-SQL

![Org chart](http://www.cadalyst.com/files/cadalyst/nodes/2007/6039/0307learncurve-3.jpg)

## Overview

Graphs represent networks of connections. Social networks can either be directed (a tree structure) or undirected (each node can have connections). The organizational chart above is an example of a directed graph while the network below demonstrates a undirected one.

![](http://i.stack.imgur.com/YA7NX.png)

## Purpose
In this example we will use a parent-child relationship to represent our graph. The purpose of this article is show to count the number of children each parent has.

## Table structure
Only a single table is need to represent these associations in the form of a hierarchy. The ID column contains the child ID as the primary key and a ParentID which contains references the ID of that person's parent. If the ParentID is null then that person does not have any parents. This also identifies the root node in our organization chart.

| ID (PK) | ParentID |
|:------- |:---------|
| 1       | NULL     |
| 2       | 1        |
| 3       | 2        |
| ...     | ...      |

## Create the association table
Run the sql script below to create a temporary association table called #children.

~~~sql
-- Create a temporary table
CREATE TABLE children (ID INT PRIMARY KEY, ParentID INT);
 
-- Populate the table with values
INSERT INTO children VALUES
(1, NULL),
(2, 1),
(3, 1),
(4, 2),
(5, 1),
(6, 3),
(7, 3),
(8, NULL);
~~~
 
## First degree children
Counting the number of 1st degree children for each person is easy enough. Simply join the table to its self where the ParentID of the first table equals the ID of the second. Use left join if you want to include those persons without any children.

~~~sql
SELECT b.ID, count(a.ParentID) AS Kids
FROM children AS b
LEFT JOIN children AS a ON a.ParentID=b.ID
GROUP BY b.ID;
~~~

Here is a partial result set:

| ID      | Kids |
|:------- |:-----|
| 1       | 3    |
| 2       | 1    |
| 3       | 2    |
| ...     | ...  |

## Second degree children
 
Counting the number of second-degree children (grandkids) is done in a similar fashion. The subquery 's' counts the number of grandchildren by joining the parents to the children and by making sure that each child has a child (i.e. grandchild) using `a.ParentID is not null`

The outer query just joins the count of grandkids to a list of all the persons in the table. The `COALESCE` function is used to return a zero for those persons without any grandkids.

~~~sql
SELECT c.ID, COALESCE(Tally,0) AS GrandKids
FROM children AS c
LEFT JOIN
(
	SELECT a.ParentID, count(b.ID) AS Tally
	FROM children AS b
	JOIN children AS a ON b.ParentID=a.ID and a.ParentID is not null
) s on c.ID = s.ParentID;
~~~

The result set is show below:

| ID      | GrandKids |
|:------- |:----------|
| 1       | 3         |
| 2       | 0         |
| 3       | 0         |
| ...     | ...       |
 
## N degree children
Counting all of the descendants (the nth degree) is a bit more complicated and requires using [Common Table Expressions](https://technet.microsoft.com/en-US/library/ms186243(v=SQL.105).aspx). A common table expression (CTE) can be thought of as a temporary result set that is defined within the execution scope of a single statement. The benefit of CTE is that is self-referencing and can be referenced multiple times in the same query thus supporting recursion. 

The CTE used to recursively count all of the children for each person (ancestor) is created using the `WITH` keyword. The first step in the CTE subquery is to select all of the records in the children table and then union this set to that of their children. This is the same methodology used in our query to count all of the first degree children above. The second step is to select the count of the decedents from our CTE.

~~~sql
WITH cte AS
(
    SELECT  ID, ParentID
    FROM    children
    UNION ALL
    SELECT  p.ID, p.ParentID
    FROM    cte
    JOIN    children p
    ON      p.ID = cte.ParentID
)
SELECT ID, COUNT(*) - 1 as Descendants
FROM cte
GROUP BY ID;
~~~

Note that we must now subtract one from the total count returned due to the initial union statement in the CTE.

The result set is show below:

| ID      | Descendants |
|:------- |:------------|
| 1       | 6           |
| 2       | 1           |
| 3       | 2           |
| ...     | ...         |

## Conclusion

You can now store a directed graph in SQL Server using an association table and query that table to count the descendants and 1, 2, and N degrees of separation. Trivial modifications to the examples here can be used to return a list of said descendants and have therefore been omitted.
