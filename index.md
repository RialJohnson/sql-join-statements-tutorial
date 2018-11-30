# SQL Join Statements 
By Rial Johnson, Jadin Casey, Stuart Dilts

Before beginning this tutorial, you should be familiar with relational database concepts. Basic knowledge of setting up a database and querying with SQL is assumed. If this describes you, get ready to learn about SQL Join Statements!

A Join statement in SQL is used when we want to group together rows
from multiple tables. This is usually used to get information on
relationships split across tables. In this tutorial, we'll explore methods of utilizing Join statements to query our data. Before proceeding, you should be familiar with
constructing relational databases. The dataset we'll be using in this tutorial can be found in the
[friends.sql](https://bitbucket.org/databasesrule/writing-project/src/master/friends.sql)
file.

We can do this by joining the
rows in the different tables based on
a certain condition. Usually, this condition is that the rows from the
two different tables share a common value, but we can use any condition that
can be specified in SQL.

Here is an example to make things a little
more clear. Suppose we have two tables: one contains students and the
other records a relationship joining students together called
**Friends**. *StudentName* in the **Friends** table is the name of the
student, and *Friend* in the **Friends** table is the name of their
friend. If we want to get information about students' friends, we
must use a join statement to combine the information contained in the
different tables.
Here is an example that matches a student with their friends:

    SELECT *
    FROM Student
    JOIN Friends
    ON Student.StudentName = Friends.StudentName;

In this example, we want to join the rows from the **Student** and
**Friends** tables. The `FROM Student` line shows that we are going to
use rows from the **Student** table, and the line below states that we
are joining with the **Friends** table. We could have also chosen to select `FROM` Friends `INNER
JOIN` on Students. These are functionally equivalent.

The line `ON Student.StudentName = Friends.StudentName` is the
condition that we're joining the two tables on. It is simply stating
that we want to match all of the rows in the Student table
(`Student.StudentName`) with all of the rows in the Friends table with
a matching name column (`Friends.StudentName`).

The result of our query is that the rows in the **Student** and **Friends**
tables are merged for all students that have an entry in *StudentName*
column in the **Friends** table. Suppose this is our **Student** table:


Name     | Grade    | HasPet |FavoriteColor
-------- | -------- | ------ |-------------
John     | 7        | 1	     |Red
Susan    | 9        | 0	     |Purple
Timmy    | 7        | 1	     |Orange
Patty    | 11       | 1	     |Red
Ricky    | 10       | 0	     |Blue
Oscar    | 8        | 0	     |Cyan

And this is our **Friends** table:

Name  | Friend
----- | ------
John  | Timmy
Susan | Ricky
Timmy | John
Patty | Oscar

Then our query will return:

StudentName  | Grade      | HasPet     | FavoriteColor | StudentName | Friend
-----------  |---------- | ---------- | ------------- | ----------- | ----------
John         | 7         | 1          | Red           | John        | Timmy
Susan        | 9         | 0          | Purple        | Susan       | Ricky
Timmy        | 7         | 1          | Orange        | Timmy       | John
Patty        | 11        | 1          | Red           | Patty       |  Oscar


Our query returns all the names and grades of all
students that have an entry in the **Friends** table. The *Grade*, *HasPet* and
*FavoriteColor* columns are taken directly from the values in the **Student**
table.

To recap, an SQL join statement looks like this:

    SELECT [columns]
    FROM [a table]
    JOIN [other table]
    ON [Join condition]

As with other SQL queries, we can use `WHERE` in join statements to
filter the table produced by the join, and `SELECT` to grab
certain columns from the table.

## Types of Joins

### Inner Joins

The most popular and widely used Join is called an Inner Join. If a Join type isn’t specified, the default will be Inner Join. Here’s an example, note that the `INNER` keyword can be used but is not necessary:

    SELECT Student.name, grade
    FROM Student
    JOIN Friends
    ON (Student.name = Friends.name
    AND Student.grade = Friends.Friend_grade)
    OR Student.grade = 10;

To clarify terminology in the next section, remember that Inner Joins only include
rows that have values matched in the `ON` clause, and that all other rows are discarded from the table.

### Outer Joins

Outer Joins combine tables without trimming the table as much as an Inner Join. Outer Joins include rows from other tables that do not necessarily match with at least one row in the other tables. To gain a better understanding, consider the output of the below queries. The first uses an Inner Join, and the second uses a specific type of Outer Join called Left Join.

    SELECT Student.name, grade
    FROM Student
    INNER JOIN Friends
    ON Student.Name = Friends.Name

    SELECT Student.name, grade
    FROM Student
    LEFT JOIN Friends
    ON Student.Name = Friends.Name

Look for rows whose columns contain null values: you will see that students that do not
have friends are included in the output of the Outer Join, while the columns added from
the Friends table are left null. More examples of Outer Joins can be found at the end of this section.

There are three types of outer joins in SQL: Left Outer Join, Right Outer Join,
and Full Outer Join. These commands allow for unmatched rows to be pulled in
from different tables.  This Venn Diagram should provide a simplified by intuitive understanding of the difference in Join types using our Students and Friends example:

![](https://i.imgur.com/BoTSx1n.png)

### Left Join

Left (Outer) Join combines the matching values like an Inner Join, but also includes unmatched
values from the left table. The left table is the first table specified in SQL. In the below example,
this is the **Student** table.

    SELECT Student.name, friend
    FROM Student
    LEFT JOIN Friends
    ON Student.name = Friends.name

Output:

Name  | Friend
----- | -----
John  | Timmy
Susan | Ricky
Timmy | John
Patty | Oscar
Ricky | null

Notice that if we used an Inner Join the query would have omitted the last row because the **Friends**
table doesn't have an entry for Ricky. Using a Left Join, all the elements in **Student**
are returned.

### Right Join

Right (Outer) Join behaves just like a Left Join, but includes values from the right table instead of the left. The right table is the first table specified in SQL.  In the below example,
this is the **Friends** table.

To illustrate this idea, we’ll revise the **Friends** table to the following:

Name  | Friend | Friend_grade
----- | ----- | -----
John  | Timmy | 7
Susan | Ricky | 10
Timmy | John | 7
Patty | Oscar | 12
Emile | John  | 7

**Student** doesn’t contain an entry for the name Emile so the following query:

    SELECT Student.name, HasPet
    FROM Student
    RIGHT JOIN Friends
    ON Student.Name = Friends.Name

Would output:

Name  | HasPet
----- | -----
John  | 0
Susan | 1
Timmy | 1
Patty | 0
Emile | NULL

In an Inner Join, Emile wouldn’t have been returned at all but with a Right Join all elements
of the **Friends** table are returned resulting in Emile having a null value for the HasPet
column because Emile cannot be found in the  **Student** table.

### Full Outer Join

Full Outer Join behave like a combined Left Join and Right Join. Unmatched values
from the left and right tables are included in the resulting output. Left Joins and Right Joins are subtypes of Full Outer Joins.

Once again using the modified **Friends** table from the Right Join example:

Name  | Friend | Friend_grade
----- | ----- | -----
John  | Timmy | 7
Susan | Ricky | 10
Timmy | John | 7
Patty | Oscar | 12
Emile | John  | 7

We’ll use a Full Outer Join:

    SELECT Student.name, friend, HasPet
    FROM Student
    FULL OUTER JOIN Friends
    ON Student.Name = Friends.Name

Output:

Name   | Friend | HasPet
----- | ----- | -----
John  | Timmy | 0
Susan | Ricky | 1
Timmy | John  | 1
Patty | Oscar | 0
Ricky | null  | 0
Emile | John  | NULL

Ricky contains a null value because he doesn’t exist in the **Friends** table and thus doesn't have Friend information. Emile has a null value because he doesn’t exist in the **Student**
table and therefore doesn't have HasPet information.

### Self Join

As the name might indicate, a Self Join joins a table with itself.
As indicated by the title, a self join is a join in which a table is joined with itself.
Different tables aren't being joined, no `JOIN` keyword is actually required.

The following example, queries the **Student** table so that it returns possible friendships
based on students who share a favorite color:

    SELECT A.StudentName as Student,
           B.StudentName as Possible_friend,
           A.FavoriteColor as Shared_FavoriteColor
    FROM Student A, Student B
    WHERE A.StudentName <> B.StudentName
    AND A.FavoriteColor = B.FavoriteColor
    ORDER BY A.FavoriteColor;

Will output:

Student  | Possible_friend      | Shared_FavoriteColor
-------- | -------- | --------
John     | Patty     | Red
Patty    | John       | Red

In the query, there are two instances of the table **Student** and they are compared
against each other to find rows that have different names and the same favorite color.
It might be a difficult idea to wrap your mind around at first, but Self Joins can be very powerful.


##Complex Queries##
Our examples thus far have been fairly simple, introducing one idea or SQL querying technique at a time. SQL is nice in that it works how you might expect; most statements can be combined together to build more complex queries.

A helpful tip for building more complex join statements (and SQL queries in general)
is to start small and work up the query that you'd like. Begin by selecting from
the correct tables, making the correct joins, and ensuring you have the desired output.
Then make small changes to refine your query and build up to a powerful query. This is especially helpful when nesting SQL queries.

Here’s a few more things you can do with SQL Join statements:

## Renaming Columns

The `AS` keyword is used to change the column name in the output of a query. The format
is simply: `<column-name> AS <desired-name>`. Changing column names is a useful way to keep output organized and clear without modifying the resulting data. It’s especially valuable when developing complex queries.

Here’s a basic example using the `AS` keyword:

    SELECT Student.StudentName as friend_one,
           Friend.friend as friend_two
    FROM Student
    INNER JOIN Friend
    ON Student.StudentName = Friend.StudentName

Results in:

friend_one  | friend_two
-------- | -------- | --------
John     | Timmy
Susan    | Ricky
Timmy   | John
Patty   | Oscar

While changing column names is not typically necessary, it’s a powerful tool used to organize and format output.

## Multiple Condition Join Statements

While all of our examples so far have joined tables on a single condition, we are not limited to a single condition to filter our join statements. The following query demonstrates a case where time and code can be saved by joining on multiple conditions in a single SQL statement. In our example, we want to retrieve everyone that is a friend of a student, or is in the same grade as them. 

    SELECT A.StudentName, B.StudentName, A.FavoriteColor, B.FavoriteColor
    FROM Student A, Student B
    INNER JOIN Friends
    ON Friends.StudentName = A.StudentName AND (Friends.Friend = B.StudentName OR
          (B.StudentName <> A.StudentName AND B.FavoriteColor = A.FavoriteColor));


This will output:

StudentName  |StudentName  |FavoriteColor  |FavoriteColor
----------- | ----------- | -------------  |-------------
John       |  Timmy  |      Red       |     Orange       
John       |  Patty     |   Red          |  Red          
Susan     |   Ricky   |     Purple    |     Blue         
Timmy    |    John    |     Orange  |       Red          
Patty       | John       |  Red          |  Red          
Patty       | Oscar     |   Red         |  Cyan   

We can use any type of valid conditional statement to filter our query and get the exact data we are looking for.  This makes Join statements incredibly powerful for querying databases.

## Joining Multiple Tables

Sometimes it can be useful to join more than two tables. Imagine we have a database that represents multiple classrooms of students at a school. Each classroom has its own table in the database and we want to return all the students with a pet in the school. We can accomplish this by joining all the tables and selecting where *HasPet = 1*.

Here are three tables that each represent a classroom:

**Class #1**

ID    | Name   | HasPet
-------- | -------- | ------ 
1        | John     | 1	     
2        | Susan   | 0
3        | Ben      | 1

**Class #2**

ID    | Name   | HasPet
-------- | -------- | ------ 
4        | Zack    | 0	     
5        | Drake  | 0
6        | Mac    | 1

**Class #3**

ID    | Name   | HasPet
-------- | -------- | ------ 
7        | Ahna    | 1	     
8        | Katie  | 1
9        | Ben    | 1

We want to join the tables on their ID’s as there are two students that share the name *Ben*.

Here is our query:

    SELECT Class1.*, Class2.*, Class3.*
    FROM Class1
        JOIN Class2
            ON Class2.ID = Class1.ID
        JOIN Class3
            ON Class3.ID = Class1.ID
    WHERE HasPet = 1

It will output:

ID    | Name   | HasPet
-------- | -------- | ------ 
1        | John     | 1
3        | Ben      | 1
6        | Mac    | 1
7        | Ahna    | 1	     
8        | Katie  | 1
9        | Ben    | 1

Each additional table to be added to the query needs its own individual Join statement. By following this syntax we can join any number of tables, but keep in mind that this can get complex quickly.

## Underlying Mechanics

We’ve seen a lot of examples and use cases for Join statements, but how do they work? Conceptually, join statements are two different operations on the data: a cross
product of the tables to be joined followed by a `SELECT ... WHERE` operation. The cross product can be seen by running a join without specifying the `ON [conditions]` portion
of the query:

    SELECT *
    FROM Student
    INNER JOIN Friends

A cross product matches each item in the first table to all entries in each other joined table. This can be confirmed by checking the size of each table:

    SELECT COUNT(*) FROM Friends;
    -- > 4
    SELECT COUNT(*) FROM Student;
    -- > 6
    SELECT COUNT(*) FROM Student
    INNER JOIN Friends;
    -- > 24

Since each entry in the **Student** table is matched with 10 other entries, there are
`4 * 6` entries in the resulting table.

We can now filter the table with a `WHERE` clause to perform a query equivalent to one
using a join statement.

    SELECT *
    FROM Student
    INNER JOIN Friends)
    WHERE ID = ID1;

This query will now generate the same table as the one returned by a query using a join
statement:

    SELECT *
    FROM STUDENT
    INNER JOIN Friends
    ON ID = ID1;

Although the output appears to be the same for both queries, `WHERE` does not perform the
same operation as `ON`, and the two are not interchangeable; this only works with
Inner Joins. In reality, the actual mechanics of joins are more complex and not easily
captured with simple queries, but the behavior is the same: generate a table
based of the type of join and then filter the table with the conditions
specified in the `ON` clause.

## Final Thoughts

You should hopefully now have a solid understanding of what SQL Join statements are and why you would want to use them. We’ve explored many types and techniques that will serve as building blocks to help you build advanced SQL queries. Remember that Joins are versatile and robust: if there’s something you want do with them, you probably can. Use this tutorial as a reference and query away.

## References

[W3Schools](https://www.w3schools.com/sql/sql_join.asp)
[SQL-Join.com ](http://www.sql-join.com/sql-join-types/)
[TutorialsPoint](https://www.tutorialspoint.com/sql/sql-using-joins.htm)

