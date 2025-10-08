# SQL 101 - Learning the Basics

SQL & Database General Questions
#### 1. What is SQL?

SQL (Structured Query Language) is a standardized programming language used to manage and manipulate data in relational databases. It is the backbone of database management systems and is widely used across industries for tasks such as data retrieval, insertion, updating, and deletion.

OR

SQL stands for Structured Query Language.
It is the standard language used to interact with relational databases.

With SQL, you can:

1. Create databases and tables.

2. Insert data (adding records).

3. Query data (search/filter information).

4. Update data.

5. Delete data.

6. Control permissions and transactions.

Think of SQL as the "English-like" language that databases understand.

#### 2. What databases other than PostgreSQL use SQL?

SQL is universal, but each database adds its own flavor. 

Examples:

- MySQL / MariaDB:  Popular for websites and apps.

- SQLite: Lightweight, used in mobile apps.

- Oracle Database: Enterprise-level, very powerful.

- Microsoft SQL Server (MSSQL): Used in Windows environments.

- IBM Db2: Used in banking/enterprise systems.

All of them use SQL syntax, but sometimes with slight differences.

#### 3. What is a table?

A table is like a spreadsheet in Excel.
It organizes data into rows and columns.
Example: a students table might look like this:

| student_id | name    | age | score |
|------------|---------|-----|-------|
| 1          | Alice   | 20  | 88    |
| 2          | Brian   | 22  | 75    |
| 3          | Cynthia | 21  | 95    |


#### 4. What is a row?

row is a single record in a table.

Example:
```
1 | Alice | 20 | 88 is one row in the students table.
```
5. What is a column?

A column defines the type of information stored.
Example:

```
student_id column → unique number for each student.
name column → stores text.
score column → stores numeric grades.
```

6. How do you comment out an SQL line so that it is ignored by the SQL engine?

Single-line comment:
```
-- This is a comment
```

Multi-line comment:
```
/* This is a 
   multi-line comment */
 ```

SQL Keywords (Specific to PostgreSQL)

`SELECT`

Used to retrieve data.
```
SELECT name, age FROM students;
```
`FROM`

Specifies which table to query.
```
SELECT * FROM students; -- FROM students table
```
`WHERE`

Filters rows based on conditions.
```
SELECT * FROM students WHERE age > 20;
```
`CREATE`

General keyword for making objects (databases, tables, etc.).

`CREATE DATABASE`

Makes a new database.
```
CREATE DATABASE school;
```
`CREATE TABLE`

Makes a new table inside a database.
```
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    name TEXT,
    age INT,
    score DECIMAL
);
```
`DROP`

Deletes objects.

`DROP DATABASE`

Deletes a whole database.
```
DROP DATABASE school;
```
`DROP TABLE`

Deletes a table and all its data.
```
DROP TABLE students;
```
`TRUNCATE TABLE`

Deletes all rows but keeps the table structure.
```
TRUNCATE TABLE students;
```
`JOIN`

Combines rows from two tables based on a related column.

`LEFT JOIN`

Includes all rows from the left table, even if there’s no match in the right.
```
SELECT s.name, c.course_name
FROM students s
LEFT JOIN courses c ON s.student_id = c.student_id;
```
`OUTER JOIN`

PostgreSQL supports `LEFT`, `RIGHT`, and `FULL OUTER JOIN`.

`FULL OUTER JOIN` returns all rows from both tables, even if no match exists.

`VIEW`

A saved query that behaves like a table.
```
CREATE VIEW top_students AS
SELECT name, score FROM students WHERE score > 80;
```
`BEGIN` / `START TRANSACTION` / `COMMIT` / `SAVEPOINT`

These control transactions (a set of operations treated as one unit).

`BEGIN` (or START TRANSACTION) → Start a transaction.

`COMMIT` → Save changes permanently.

`ROLLBACK` → Undo changes in the transaction.

`SAVEPOINT` → Create a “checkpoint” to roll back to without undoing everything.

Example:
```
BEGIN;
UPDATE students SET score = score + 5 WHERE age > 20;
SAVEPOINT after_update;
DELETE FROM students WHERE score < 30;
ROLLBACK TO after_update; -- undo delete, keep update
COMMIT; -- save final changes
```
`LIMIT`

Restricts number of results returned.
```
SELECT * FROM students LIMIT 5;
```
#### What is the difference between `BEGIN` and `START` `TRANSACTION`?

In PostgreSQL, they are the same thing (synonyms).

Both start a transaction.

Other databases (like MySQL) may only support one form.

So you can use either:

`BEGIN`;
-- OR
`START TRANSACTION`;

#### What is `normalization`?

Normalization = the process of organizing data in a database to avoid duplication and ensure consistency.

Main idea: break data into multiple related tables instead of one huge table.

Example (without normalization):
```
student_id	name	course1	course2
1	Alice	Math	Science
2	Brian	Math	History
```
Problem → repeating course data, hard to update.

Normalized version (separate tables):

students
```
student_id	name
1	Alice
2	Brian
```
courses
```
course_id	course_name
1	Math
2	Science
3	History
```
student_courses (relationship table)
```
student_id	course_id
1	1
1	2
2	1
2	3
```
This avoids duplication and keeps data clean.

#### 1) Start as the postgres superuser (Linux)
```
sudo su - postgres
psql
```

`sudo su - postgres` → becomes the Linux postgres user (the DB superuser).

`psql` → starts the interactive PostgreSQL client.

#### 2) Two safe ways to create a database/user (pick one)
Option A — create the DB and make your user the owner

Run in psql as postgres:
```
CREATE DATABASE exam;
CREATE USER mopiyo WITH ENCRYPTED PASSWORD 'replaceThisWithApasswordOfYourChoice';
ALTER DATABASE exam OWNER TO mopiyo;
```

Creates database exam.

Creates user mopiyo (change name/password to match your machine).

Makes mopiyo the owner of exam (owner can create objects inside).

Option B — create the user and let them create DBs

Run in psql as postgres:
```
CREATE USER mopiyo WITH ENCRYPTED PASSWORD 'replaceThisWithApasswordOfYourChoice';
ALTER USER mopiyo CREATEDB;
```

Then you (the OS user mopiyo) can create exam later with createdb exam or in psql -d postgres by CREATE DATABASE exam;.

#### 3) Exit psql and switch back to your normal user
```
\q
exit
```

Now connect to the exam database as the created user:

#if you used Option A (owner) or if your user can connect
`psql -d exam` -U mopiyo

`psql -d exam` from terminal

If you used Option B and havent created exam yet:

psql -d postgres -U mopiyo

CREATE DATABASE exam;

\c exam

6) Quick verification commands in psql

List tables:
```
\d
```

Describe a single table (e.g. student):
```
\d student
```

Show first rows:
```
SELECT * FROM student LIMIT 10;
SELECT * FROM student_subject LIMIT 20;
```

Run the per-student average (rounded to 4 decimals, safe with numeric):
```
SELECT s.id, s.name,
       ROUND(AVG(ss.score::numeric), 4) AS ave_score
FROM student s
LEFT JOIN student_subject ss ON s.id = ss.student_id
GROUP BY s.id, s.name
ORDER BY s.id;
```
# TASKS
```
SELECT * FROM student WHERE first_name = 'Alice';

-- No Colomn is named first_name so it will not work and also it will display number of rows as zero because this query is looking for a row with exactly the name Alice and not anyother thing else so it is good for data that have only one word or numeric data that has numbers only that is when you can use the equal sign. 
```
```
SELECT * FROM student WHERE name LIKE 'Alice%';
```
```
-- this one works because the like will look into the rows and find the row that begins with Alice and this is represented by this wildcard % it will list the rows starting with alice and any other word following it.
```
```
SELECT COUNT(*) FROM student WHERE county_id = 1;
```
```
-- this selects data from the table called student and see the column called county_id and takes the one which is mombasa and give its count, the output will have a title named count and the actual number of rows county id 1 was found.
```
```
--Aliases (effects of capital letters on aliases 
```
```
SELECT COUNT(*) AS "Mombasa" FROM student WHERE county_id = 1; 
```
```
-- -- this selects data from the table called student and see the column called county_id and takes the one which is mombasa and give its count, the output will have a title named count and the actual number of rows county id 1 was found.but it is different from the above one because it has changed the title from Count to Mombasa using AS.
```

```
SELECT COUNT(*) AS Total Students from Mombasa FROM student WHERE county_id = 1;
```
```
-- Did not work because I did not double quote the title
```

```
SELECT COUNT(*) AS "Total Students from Mombasa" FROM student WHERE county_id = 1;
```
```
-- this selects data from the table called student and see the column called county_id and takes the one which is mombasa and give its count, the output will have a title named count and the actual number of rows county id 1 was found. but the title is now Total Students from Mombasa not count.
```
```
-- Aliases
```
```
SELECT COUNT(*) AS mombasa FROM student WHERE county_id = 1;
```
```
-- will still work but the title mombasa will start with a small letter.
```

```
SELECT COUNT(*) AS total_students FROM student WHERE county_name = 'Kisumu';
```
```
-- “How can I count the number of students in a specific county without needing to know the county_id, only the county name?”
```
```
SELECT county_id, COUNT(*) FROM student GROUP BY county_id;
```
```
-- here we are selecting the table student column with county_id then in each county_id we take we group the number of times it has appeared in the rows in this data and then the output will have two columns one with county_id and another with the count each county id appear in a row in this dataset.
```
```
SELECT county_id, COUNT(*) FROM student GROUP BY county_id ORDER BY county_id ASC;
```
```
-- here we are selecting the table student column with county_id then in each county_id we take we group the number of times it has appeared in the rows in this data and then the output will have two columns one with county_id and another with the count each county id appear in a row in this dataset. But it was not in any order from 1 to 47 but this asc will arrange it in a ascending order.
```
```
SELECT county_id, COUNT(*) FROM student GROUP BY county_id ORDER BY county_id;
```
```
-- order by county_id still puts it in an ascending order.
 ```
```

SELECT county_id, COUNT(*) FROM student GROUP BY county_id ORDER BY county_id DESC;
```
```
-- here we are selecting the table student column with county_id then in each county_id we take we group the number of times it has appeared in the rows in this data and then the output will have two columns one with county_id and another with the count each county id appear in a row in this dataset. But it was not in any order from 47 to 1 but this asc will arrange it in a decending order.
```
```
-- Add a New Column named (county name)
```
```
ALTER TABLE student ADD COLUMN county name;
```
```
-- ALTER TABLE means change or add something to the student table
-- ADD COLUMN county_name TEXT means add a new column named county_name, and the data will be text (words)
```
```
/*
Question:
You are working with a PostgreSQL database that contains a table named student. The table already has a column named county_id, but this column only stores numbers from 1 to 47 representing counties in Kenya. To make the data easier to understand, you want to:

Add a new column named county_name to store the actual name of each county as text.

Update the table so that each county_id is matched with its correct county_name (e.g., county_id = 1 becomes 'Mombasa', county_id = 2 becomes 'Kwale', and so on).

Display a summary that shows each county's ID, name, and how many students are from that county.

Write the SQL queries needed to do all of the above.

*/
```
```
ALTER TABLE student ADD COLUMN county_name TEXT;
```
```
/*
ALTER TABLE means change or add something to the student table
ADD COLUMN county_name TEXT means add a new column named county_name, and the data will be text (words)
To Update table student Set county_name to 'The name of the county you want' where county_id is (1-47)
*/
```
```
UPDATE student SET county_name = 'Mombasa' WHERE county_id = 1;
UPDATE student SET county_name = 'Kwale' WHERE county_id = 2;
UPDATE student SET county_name = 'Kilifi' WHERE county_id = 3;
UPDATE student SET county_name = 'Tana River' WHERE county_id = 4;
UPDATE student SET county_name = 'Lamu' WHERE county_id = 5;
UPDATE student SET county_name = 'Taita Taveta' WHERE county_id = 6;
UPDATE student SET county_name = 'Garissa' WHERE county_id = 7;
UPDATE student SET county_name = 'Wajir' WHERE county_id = 8;
UPDATE student SET county_name = 'Mandera' WHERE county_id = 9;
UPDATE student SET county_name = 'Marsabit' WHERE county_id = 10;
UPDATE student SET county_name = 'Isiolo' WHERE county_id = 11;
UPDATE student SET county_name = 'Meru' WHERE county_id = 12;
UPDATE student SET county_name = 'Tharaka-Nithi' WHERE county_id = 13;
UPDATE student SET county_name = 'Embu' WHERE county_id = 14;
UPDATE student SET county_name = 'Kitui' WHERE county_id = 15;
UPDATE student SET county_name = 'Machakos' WHERE county_id = 16;
UPDATE student SET county_name = 'Makueni' WHERE county_id = 17;
UPDATE student SET county_name = 'Nyandarua' WHERE county_id = 18;
UPDATE student SET county_name = 'Nyeri' WHERE county_id = 19;
UPDATE student SET county_name = 'Kirinyaga' WHERE county_id = 20;
UPDATE student SET county_name = 'Murang’a' WHERE county_id = 21;
UPDATE student SET county_name = 'Kiambu' WHERE county_id = 22;
UPDATE student SET county_name = 'Turkana' WHERE county_id = 23;
UPDATE student SET county_name = 'West Pokot' WHERE county_id = 24;
UPDATE student SET county_name = 'Samburu' WHERE county_id = 25;
UPDATE student SET county_name = 'Trans Nzoia' WHERE county_id = 26;
UPDATE student SET county_name = 'Uasin Gishu' WHERE county_id = 27;
UPDATE student SET county_name = 'Elgeyo Marakwet' WHERE county_id = 28;
UPDATE student SET county_name = 'Nandi' WHERE county_id = 29;
UPDATE student SET county_name = 'Baringo' WHERE county_id = 30;
UPDATE student SET county_name = 'Laikipia' WHERE county_id = 31;
UPDATE student SET county_name = 'Nakuru' WHERE county_id = 32;
UPDATE student SET county_name = 'Narok' WHERE county_id = 33;
UPDATE student SET county_name = 'Kajiado' WHERE county_id = 34;
UPDATE student SET county_name = 'Kericho' WHERE county_id = 35;
UPDATE student SET county_name = 'Bomet' WHERE county_id = 36;
UPDATE student SET county_name = 'Kakamega' WHERE county_id = 37;
UPDATE student SET county_name = 'Vihiga' WHERE county_id = 38;
UPDATE student SET county_name = 'Bungoma' WHERE county_id = 39;
UPDATE student SET county_name = 'Busia' WHERE county_id = 40;
UPDATE student SET county_name = 'Siaya' WHERE county_id = 41;
UPDATE student SET county_name = 'Kisumu' WHERE county_id = 42;
UPDATE student SET county_name = 'Homa Bay' WHERE county_id = 43;
UPDATE student SET county_name = 'Migori' WHERE county_id = 44;
UPDATE student SET county_name = 'Kisii' WHERE county_id = 45;
UPDATE student SET county_name = 'Nyamira' WHERE county_id = 46;
UPDATE student SET county_name = 'Nairobi' WHERE county_id = 47;
```
```
-- to test if it worked use
```
```
SELECT county_id, county_name, COUNT(*) FROM student GROUP BY county_id, county_name ORDER BY county_id;
```
```

SELECT REPLACE(name, ' C ', ' C. ') 
FROM student;
```
```
-- This will only replace the middle initial C From not having a period following it to having a period following it.

```
```
SELECT REGEXP_REPLACE(name, ' ([A-Z]) ', ' \1. ') AS fixed_name
FROM student;
```
/*
```
SELECT REGEXP_REPLACE(name, ' ([A-Z]) ', ' \1. ')
```
```
SELECT REGEXP_REPLACE = search, select and replace using a pattern.

name looks inside the student name column.

' ([A-Z]) ' look for a space, then ONE capital letter, then another space.

Example: " C ", " L ", " W "

' \1. ' this says replace it with the same letter \1 but add a dot after it.

So " C " becomes " C. ".

" L " becomes " L. ".

AS fixed_name gives this new column a nice name (fixed_name).


FROM student we are working with the student table.*/
```
```
SELECT name,
       phone,
       LOWER(
         LEFT(split_part(name, ' ', 1), 1) ||   -- first letter of first name
         split_part(name, ' ', 2) ||            -- middle initial
         split_part(name, ' ', 3) ||            -- last name
         RIGHT(REPLACE(phone, '-', ''), 4)     -- last 4 digits of phone number
       ) AS username
FROM student;

```
we can also use regexp

```
SELECT 
  name,
  phone,
  LOWER(
    regexp_replace(name, '^([A-Z]).*? ([A-Z]).*? ([A-Za-z]+).*$', '\1\2\3') 
    || right(regexp_replace(phone, '[^0-9]', '', 'g'), 4)
  ) AS username
FROM student;
```
or removing right we can use
```
SELECT 
  name,
  phone,
  LOWER(
    regexp_replace(name, '^([A-Z]).*? ([A-Z]).*? ([A-Za-z]+).*$', '\1\2\3') 
    || regexp_replace(phone, '.*([0-9]{4})$', '\1')
  ) AS username
FROM student;

the one below is the correct version of the one above
SELECT 
  name,
  phone,
  LOWER(
    regexp_replace(name, '^([A-Z]).*? ([A-Z]).*? ([A-Za-z]+).*$', '\1\2\3') 
    || regexp_replace(regexp_replace(phone, '[^0-9]', '', 'g'), '.*([0-9]{4})$', '\1')
  ) AS username
FROM student;

```
```
/* explanation regexp_replace(phone, '[^0-9]', '', 'g') → remove everything that is not a number (like -, spaces).

right(..., 4) → take the last 4 digits.

Example: "0712-345-678" → "5678".

Putting it together

... || ... → join name part + last 4 phone digits.

LOWER(...) → make it all lowercase.

Final username example:
Name = "John P Doe", Phone = "0712-345-678"
Result = jpdoe5678

This way we only use regexp_replace + right, no split_part, no left.

Do you want me to make it even simpler (like not using regex groups, just stripping spaces and then taking pieces)? That would look even more "beginner style".*/
```
```
/* Explanation

SELECT here we are choosing what to show.

name here we show the student’s full name.

phone here we show their phone number.

The long expression will create the username.

LOWER(...) make everything lowercase.

LEFT(split_part(name, ' ', 1), 1)

split_part(name, ' ', 1) = take the first word from the name (the first name).

LEFT(..., 1) = take just the first letter of that first name.

Example: "Amber" "A"

split_part(name, ' ', 2)

This takes the second word in the name the middle initial.

Example: "C." → "C."

split_part(name, ' ', 3)

This takes the third word in the name → the last name.

Example: "Atieno" "Atieno"

RIGHT(REPLACE(phone, '-', ''), 4)

REPLACE(phone, '-', '') removes the dashes from the phone.

"0783-627-886" "0783627886"

RIGHT(..., 4) takes the last 4 digits → "7886".

|| → this means join things together (concatenate).

First letter + middle initial + last name + last 4 phone digits.

AS username → give this new column the name “username”.*/
```
```

SELECT id, name, school_id
FROM student
WHERE name LIKE '%Ami%'
  AND school_id >= 90;
```
```
/*Explanation in baby steps

SELECT id, name, school_id → show me the id, name, and school_id (we are treating school_id as if it’s the score).

FROM student → get the data from the student table.

WHERE name LIKE '%Ami%' → only rows where the name has "Ami" inside.

AND school_id >= 90; → only show students whose school_id (acting like score) is 90 or higher.

Example result (if using your pasted data):
It will show all students with “Ami” in their name and school_id ≥ 90.*/ 
```
```
SELECT s.county_name,
       ROUND(CAST(AVG(ss.score) AS numeric), 2) AS average_score
FROM student s
JOIN student_subject ss
  ON s.id = ss.student_id
GROUP BY s.county_name
ORDER BY average_score DESC;
```
```
/*
xplanation of the fix

AVG(ss.score) → gives us a double precision (decimal number).

CAST(AVG(ss.score) AS numeric) → changes it into a numeric type that works with ROUND(..., 2).

ROUND(..., 2) → rounds to 2 decimal places.

Now it will work, and you’ll get one average score per county.

Would you like me to also show you a super beginner version without CAST, where you just get the full average per county (no rounding)? */
```
```
SELECT s.id, s.name, county.name AS county_name, school.name AS school_name
FROM student s
JOIN county ON s.county_id = county.id
JOIN school ON s.school_id = school.id
WHERE county.name = 'Turkana';
 ```
 
 -- number 4
```
SELECT 
    s.name AS student_name,
    sub.name AS subject_name,
    c.name AS county_name,        
    sc.name AS school_name,
    ss.score AS subject_score
FROM student_subject ss
JOIN student s ON ss.student_id = s.id
JOIN school sc ON s.school_id = sc.id
JOIN subject sub ON ss.subject_id = sub.id
JOIN county c ON s.county_id = c.id  
WHERE ss.score = (
    SELECT MAX(ss2.score)
    FROM student_subject ss2
    WHERE ss2.subject_id = ss.subject_id
);

```
```
/*explanation
SELECT 
    s.name AS student_name,
    sub.name AS subject_name,
    c.name AS county_name,        
    sc.name AS school_name,
    ss.score AS subject_score


SELECT chooses what information we want to see in the final results.

s.name AS student_name → the student’s name, renamed so it looks nice.

sub.name AS subject_name → the subject’s name (like Math, English).

c.name AS county_name → the county’s name.

sc.name AS school_name → the school’s name.

ss.score AS subject_score → the actual score the student got in that subject.

FROM student_subject ss


This is the main table we are starting from.

student_subject is the link between a student and a subject, because each student can have many subjects and scores.

We give it a short name ss so it’s easier to write.

JOIN student s ON ss.student_id = s.id


This connects each record in student_subject to the actual student table.

The link is ss.student_id = s.id (the student’s ID must match).

JOIN school sc ON s.school_id = sc.id


This connects each student to their school.

s.school_id tells us which school a student belongs to.

sc.id is the school’s unique ID.

JOIN subject sub ON ss.subject_id = sub.id


This connects each record in student_subject to the subject table.

ss.subject_id shows which subject the score belongs to.

JOIN county c ON s.county_id = c.id


This connects each student to their county.

s.county_id says which county the student belongs to.

WHERE ss.score = (
    SELECT MAX(ss2.score)
    FROM student_subject ss2
    WHERE ss2.subject_id = ss.subject_id
);


WHERE filters the results.

We only want the student(s) who got the highest score in each subject.

(SELECT MAX(ss2.score) FROM student_subject ss2 WHERE ss2.subject_id = ss.subject_id) is a subquery.

It looks at the same student_subject table again (aliased as ss2).

MAX(ss2.score) finds the highest score for that subject.

WHERE ss2.subject_id = ss.subject_id makes sure it checks the scores only for the same subject we are currently looking at.

If the student’s score equals that maximum, they are the top student for that subject.

In plain English:

This query lists the top student in each subject across the country.
It shows: the student’s name, the subject, their county, their school, and their score.

The trick is in the WHERE ... (SELECT MAX(...)) part. That’s what ensures we only keep students who had the highest score for each subject.

Do you want me to rewrite this query in a simpler, step-by-step beginner version (like first just list all students with scores, then slowly add filters until we get the “top student per subject”)?
```

```
/* number 5. List the top 10 students in the country. Use the average score of all their subjects. Include a field that shows their ranks ie 1 to 10. Additionally, list the county and school name. */
```
```
SELECT 
    s.name AS student_name,
    c.name AS county_name,
   1 sc.name AS school_name,
    ROUND(CAST(AVG(ss.score) AS numeric), 2) AS average_score,
    RANK() OVER (ORDER BY AVG(ss.score) DESC) AS student_rank
FROM student_subject ss
JOIN student s ON ss.student_id = s.id
JOIN school sc ON s.school_id = sc.id
JOIN county c ON s.county_id = c.id
GROUP BY s.id, s.name, c.name, sc.name
ORDER BY average_score DESC
LIMIT 50;
```
```
  Use of Dense rank tied results

  SELECT 
  s.name AS student_name,
  c.name AS county_name,
  sc.name AS school_name,
  ROUND(AVG(ss.score)::numeric, 2) AS average_score,
  DENSE_RANK() OVER (ORDER BY ROUND(AVG(ss.score)::numeric, 2) DESC) AS student_rank
FROM student_subject ss
JOIN student s ON ss.student_id = s.id
JOIN school sc ON s.school_id = sc.id
JOIN county c ON s.county_id = c.id
GROUP BY s.id, s.name, c.name, sc.name
ORDER BY average_score DESC
LIMIT 50;
```
```
/*1st Explanation
SELECT 
    s.name AS student_name,
    c.name AS county_name,
    sc.name AS school_name,


We’re choosing to display:

s.name → student’s name (renamed as student_name).

c.name → county’s name (renamed as county_name).

sc.name → school’s name (renamed as school_name).

ROUND(CAST(AVG(ss.score) AS numeric), 2) AS average_score,


AVG(ss.score) → calculates the average score of the student across all subjects.

CAST(... AS numeric) → makes sure the result is treated as a number.

ROUND(..., 2) → rounds it to 2 decimal places (for example, 75.6789 becomes 75.68).

Renamed as average_score.

RANK() OVER (ORDER BY AVG(ss.score) DESC) AS student_rank


RANK() → gives a ranking number (1, 2, 3, …).

OVER (ORDER BY AVG(ss.score) DESC) → sorts students by their average score from highest to lowest.

So the best student gets rank 1, the next one rank 2, etc.

FROM student_subject ss


The main table is student_subject (which stores each student’s scores per subject).

Short name ss is used.

JOIN student s ON ss.student_id = s.id


Connects scores (student_subject) to the student who got them.

JOIN school sc ON s.school_id = sc.id


Connects each student to their school.

JOIN county c ON s.county_id = c.id


Connects each student to their county.

GROUP BY s.id, s.name, c.name, sc.name


Because we used AVG() (an aggregate function), we must group all subject scores by each student.

This means: collect all rows belonging to one student → calculate their average.

We group by the student’s ID, name, county, and school so that each student appears once in the results.

ORDER BY average_score DESC


Sort the list of students from the highest average score to the lowest.

LIMIT 10;


Only show the top 10 students in the country.


This query finds the Top 10 students in the country based on the average score of all their subjects.

For each top student, it shows:

Their name

Their county

Their school

Their average score (rounded to 2 decimal places)

Their rank (1 = best, 2 = second best, …)   */ 
```
```
2nd Explanation

explanation

SELECT → choose what data you want to see.

s.name AS student_name → take the student’s name, give it a clear label student_name.

c.name AS county_name → take county’s name, call it county_name.

sc.name AS school_name → take school’s name, call it school_name.

ROUND(AVG(ss.score)::numeric, 2) AS average_score →

AVG(ss.score) = average of all scores per student.

::numeric = cast (change) the type to numeric so we can round.

ROUND(..., 2) = keep only 2 decimals.

Call the result average_score.

DENSE_RANK() OVER (ORDER BY ROUND(AVG(ss.score)::numeric, 2) DESC) AS student_rank →

DENSE_RANK() = give ranking numbers.

OVER (ORDER BY ... DESC) = sort by average score, highest first.

If two people have the same average (after rounding), they get the same rank.

No rank numbers are skipped (e.g. 1, 2, 2, 3).

FROM + JOINs

FROM student_subject ss → main table: scores.

JOIN student s ON ss.student_id = s.id → link score to student.

JOIN school sc ON s.school_id = sc.id → link student to school.

JOIN county c ON s.county_id = c.id → link student to county.

GROUP BY

GROUP BY s.id, s.name, c.name, sc.name → needed because we are using AVG().
It groups all rows of one student together so we can compute their average.

ORDER BY

ORDER BY average_score DESC → show highest average first.

LIMIT

LIMIT 50 → only show the top 50 rows.

This is shorter than the CTE version, easier to defend as "beginner work," and it fixes the problem where ties didn’t rank the same.

Would you like me to also show you a tiny sample table with results so you can actually see how ties now get the same rank (like two students with 95.74 both showing 17)? */
```

```
/*6. List the top schools (school name, total number of students in each school and county name) based on type/category ie top school:
Homeschool
National
Provincial
District
Private*/

```
```
SELECT 
    sc.name AS school_name,
    c.name AS county_name,
    CASE
        WHEN sc.name ILIKE '%National%' THEN 'National'
        WHEN sc.name ILIKE '%Provincial%' THEN 'Provincial'
        WHEN sc.name ILIKE '%District%' THEN 'District'
        WHEN sc.name ILIKE '%Private%' THEN 'Private'
        ELSE 'Homeschool'
    END AS school_type,
    COUNT(st.id) AS total_students
FROM school sc
JOIN student st ON st.school_id = sc.id
JOIN county c ON sc.county_id = c.id
GROUP BY sc.name, c.name, school_type
ORDER BY school_type, total_students DESC;
```

/* 

```sql
SELECT 
    sc.name AS school_name,
    c.name AS county_name,
    CASE
        WHEN sc.name ILIKE '%National%' THEN 'National'
        WHEN sc.name ILIKE '%Provincial%' THEN 'Provincial'
        WHEN sc.name ILIKE '%District%' THEN 'District'
        WHEN sc.name ILIKE '%Private%' THEN 'Private'
        ELSE 'Homeschool'
    END AS school_type,
    COUNT(st.id) AS total_students
FROM school sc
JOIN student st ON st.school_id = sc.id
JOIN county c ON sc.county_id = c.id
GROUP BY sc.name, c.name, school_type
ORDER BY school_type, total_students DESC;
```
---

**Line by line explanation**

```sql
SELECT 
    sc.name AS school_name,
    c.name AS county_name,

* `SELECT` tells the database what columns we want to see in the result.
* `sc.name` is the school’s name from the `school` table.
* `AS school_name` renames that column so the result looks clearer.
* `c.name` is the county’s name from the `county` table.
* `AS county_name` renames it.

---
```
```

    CASE
        WHEN sc.name ILIKE '%National%' THEN 'National'
        WHEN sc.name ILIKE '%Provincial%' THEN 'Provincial'
        WHEN sc.name ILIKE '%District%' THEN 'District'
        WHEN sc.name ILIKE '%Private%' THEN 'Private'
        ELSE 'Homeschool'
    END AS school_type,
```
```
* `CASE ... END` is like an IF-THEN-ELSE.
* It checks the school name text.
* If the school name contains the word "National", then it is labeled as `National`.
* If it contains "Provincial", then `Provincial`, and so on.
* If no condition matches, it defaults to `Homeschool`.
* `AS school_type` renames the result column.

sql
    COUNT(st.id) AS total_students

* `COUNT(st.id)` counts how many students belong to each school.
* `st.id` is the student’s unique identifier.
* `AS total_students` renames the count so it is clear.

sql
FROM school sc
JOIN student st ON st.school_id = sc.id
JOIN county c ON sc.county_id = c.id

* `FROM school sc` means the main table is `school` and we call it `sc` for short.
* `JOIN student st ON st.school_id = sc.id` connects each student to their school.
* `JOIN county c ON sc.county_id = c.id` connects each school to its county.

sql
GROUP BY sc.name, c.name, school_type

* `GROUP BY` groups the results.
* Here we group by school, county, and school type.
* This makes sure the `COUNT` works per group of school.

---

sql
ORDER BY school_type, total_students DESC;

* `ORDER BY` sorts the final results.
* First, it sorts by `school_type` (so all National schools together, all Provincial schools together, etc.).
* Inside each group, it sorts by `total_students` in descending order (largest number of students first).

---


This query lists schools with their county, their type (National, Provincial, District, Private, Homeschool), and how many students each school has. Then it groups them properly and shows the largest schools first in each category.

---

Would you like me to rewrite this same query in a way that looks like a beginner built it step by step (first without CASE, then adding it later)?
```


# Update Data Questions

The following involve updating records, tables, or deleting records. Make use of database transactions to be safe.

#### 1. Delete all records for students with the name Joan and be careful not to delete Joanne

First before deleting make sure you use `SELECT`  command to ensure that you are deleting the correct data 

eg for the above.
```
SELECT * FROM student
WHERE name ~ '^Joan(\s|$)';
```
explanation

`^` → start of string

`Joan` → literal text Joan

`(\s|$)` → followed by either space or end of string
This blocks Joanne because after Joan comes n, not a space or end.

but this
```
SELECT * FROM student
WHERE name ~ '^Joan';
```
Explanation:

`^Joan` → start of string, then Joan.

Nothing after it is restricted, so it will match:

`Joan`

`Joan Atieno`

`Joanne`

`Joanette`

anything starting with `Joan`.

But careful: this means it will also select Joanita, Joanice, etc.
and it will also select `Joanne`
but also Joanne Atieno or Joana is also is selected

so 
```
SELECT * FROM student
WHERE name ~ '^(Joan|Joanne)(\s|$)';
 ```
 strictly selects `Joan` and `Joanne`

 delete query

 ```
 DELETE FROM student
WHERE name ~ '^Joan(\s|$)';
```

#### is there a way to restore the data after deleting??

####  2. Update all students names to UPPERCASE so that e.g. Amber K Ratemo becomes AMBER K RATEMO

preview first, by running:
```
SELECT id, UPPER(name) AS new_name
FROM student;
```
before permanently updating using 
```
UPDATE student
SET name = UPPER(name);

```

Explanation

`UPDATE student` → we’re changing rows in the student table.

`SET name =` ... → we’re modifying the name column.

`UPPER(name)` → takes the existing value of name and converts all letters to uppercase.

Example before/after

`Amber K Ratemo` → `AMBER K RATEMO`

`Amber C Ouma` → `AMBER C OUMA`


####  3. Update all county names to UPPERCASE.

preview first, by running:
```
SELECT id, UPPER(name) AS new_name
FROM county;
```
before permanently updating using 
```
BEGIN;
UPDATE county
SET name = UPPER(name);

```

Explanation

`UPDATE student` → we’re changing rows in the student table.

`SET name =` ... → we’re modifying the name column.

`UPPER(name)` → takes the existing value of name and converts all letters to uppercase.

Example before/after

`Amber K Ratemo` → `AMBER K RATEMO`

`Amber C Ouma` → `AMBER C OUMA`


in the Above question `2` and `3` We have used what is called a transaction, It is initiated by `BEGIN` command and then you will end it with a rollback if you dont want permanent changes or commit if you want permanet changes

example
```
BEGIN;

UPDATE student
SET name = UPPER(name);

COMMIT;
```
to show you want permenent cahnges.
 ```
 BEGIN;

UPDATE student
SET name = UPPER(name);

ROLLBACK;
```
to not ,ake changes permanent or to not save the draft.
```
BEGIN;

UPDATE county
SET name = UPPER(name);

COMMIT;
```
```
BEGIN;

UPDATE county
SET name = UPPER(name);

ROLLBACK;
```

#### Note to take.
- functions are separated with commas.

# TASK
```
Generate a select query that will show potential usernames for each student by combining first letter of first name, middle initial and lastname and last 4 digits of their phone number. eg.
Student with the following details:
Amber C. Atieno 0783-627-886
Should generate a username such as (use lowercase for all characters):
acatieno7886
Display this username as an additional column/field - username.

THE QUERY IS

SELECT 
  name,
  phone,
  LOWER(
    regexp_replace(name, '^([A-Z]).*? ([A-Z]).*? ([A-Za-z]+).*$', '\1\2\3') 
    || regexp_replace(regexp_replace(phone, '[^0-9]', '', 'g'), '.*([0-9]{4})$', '\1')
  ) AS username
FROM student;

WHAT IS REGEXP REPLACE TRYING TO ACCOMPLISH HERE.
THIS QUERY WILL GENERATE A USERNAME FROM PARTS OF NAME COLUMN AND THE FOUR LAST DIGITS OF THE PHONE NUMBER COLUMN.
HERE IS THE PATTERN EXPLAINED.

LOWER - This converts everything inside the parentheses to lowercase.
That means the final username will always appear in small letters, e.g. jdoe3456.
^([A-Z]) - Means that start of string is a capital letter from A-Z and it takes only the first word because it is only the first word that is a capital letter.
.*? - matches any character lazily untill a space
([A-Z]) - takes the middle intial which is a capital letter
.*? - skips the rest untill another space.
([A-Za-z]+) -takes the entire last name which is a mixture of small and capital letters.
.*$ - end of line
'\1\2\3' - capture group it combines the three funtions together
regexp_replace( - the first regexp_replace deals with the 2 functions regexp_replace(..., '.*([0-9]{4})$', '\1') This takes only the last 4 digits of that cleaned phone number.

'[0-9]{4}' → means "exactly four digits"

'$' → means "at the end of the string"

.* → any characters before

'\1' → keeps just that 4-digit group
Example
0726-842-587  → gives "2587"

Inner part:
regexp_replace(phone, '[^0-9]', '', 'g')


[^0-9] → means “any character that is not a digit”

Replace it with '' (nothing)

'g' → global flag = replace all non-digit characters

So this removes spaces, dashes, brackets — leaving only numbers.
Example:
0726-842-587 → becomes 0726842587



```

####  4. Update all phone numbers so that they change from format 0707-155-302 to format 254707155302

```
BEGIN;

UPDATE student
SET phone = regexp_replace(regexp_replace(phone, '-', '', 'g'), '^0', '254');
```
`BEGIN` - Starts a transaction. Any changes you make (like updates or deletes) won’t be permanent until you run `COMMIT;`.

`UPDATE` - Chooses the table named student to make changes in.

`SET PHONE` - Tells PostgreSQL to update the phone column with a new value.

`regexp_replace(phone, '-', '', 'g')` - This removes all dashes (`-`) from the phone number.
`Example`: `0707-155-302` → `0707155302` `'g'` means “replace globally,” so all dashes in the number are removed.

`regexp_replace(..., '^0', '254')` - The outer regexp_replace now looks at the result from the first one.It replaces the starting zero (`^0`) with `‘254’`. `Example`: `0707155302` → `254707155302`.

```
TASK I was to change emails @ globally
I used 

BEGIN;

UPDATE student
SET email = regexp_replace(email, '@', '.', 'g');
```
then check using `select * from student;` querry for both the task and number 4 then decide if you wana `COMMIT;` or `ROLLBACK;`.

then in phone number is remove `'g'` for the global it will only remove the one dash and leave the second dash it will be like this `0783627-886` instead of `0783627886` and in email since there is only one `@` this means that if you remove `'g'` then nothing changes.

####  5. Update records for students from TRANS, THARAKA and WEST counties so that they read TRANS-NZOIA, THARAKA-NITHI and WEST-POKOT respectively.
```
BEGIN;

SELECT * FROM county;

UPDATE county SET name = 'Trans-Nzoia'   WHERE name = 'Trans';
UPDATE county SET name = 'Tharaka-Nithi' WHERE name = 'Tharaka';
UPDATE county SET name = 'West-Pokot'    WHERE name = 'West';

COMMIT;
```

Explanation:

`BEGIN;` → starts your transaction (changes are not yet permanent).

`SELECT * FROM county;` → shows all records before making updates (so you can confirm current names).

`UPDATE` ... → updates county names:

`'Trans'` → `'Trans-Nzoia'`

`'Tharaka'` → `'Tharaka-Nithi'`

`'West'` → `'West-Pokot'`

`COMMIT;` → saves your changes permanently once everything looks good.

If you see something wrong before COMMIT, you can type:
`ROLLBACK;`
Then the updated names by default comes at the bottom of the list.

####  6. Update all students with ALICE .... to ALLISA.
```
BEGIN;                

UPDATE student
SET name = regexp_replace(name, '^Alice', 'Allisa')
WHERE name ~ '^Alice';
```

####  7. Write an SQL query where that shows the letter grade using the following scale - If a student's average score is between:

95 and 100 then the grade => 'A'

90 and 95 then the grade => 'A-'

85 and 90 then the grade => 'B+'

80 and 85 then the grade => 'B'

75 and 80 then the grade => 'B-'

70 and 75 then the grade => 'C+'

65 and 70 then the grade => 'C'

60 and 65 then the grade => 'C-'

55 and 60 then the grade => 'D+'

50 and 55 then the grade => 'D'

49 and below, the grade => 'FAIL'

The results of the query should look as follows:

 |   id   |         name         | score | grade |
|--------|----------------------|--------|--------|
|  1002  | Amber C Chacha       |   45   | FAIL  |
|  1003  | Amber C Idris        |   82   | B     |
|  1004  | Amber C Kamau        |   58   | D+    |
|  1005  | Amber C Kimani       |   94   | A-    |
|  1006  | Amber C Kiptoo       |   49   | FAIL  |
|  1007  | Amber C Maribe       |   59   | D+    |
|  1008  | Amber C Maxx         |   52   | D     |
|  3120  | Zippy W Otieno       |   89   | B+    |
|  3121  | Zippy W Ouma         |   91   | A-    |
|  3122  | Zippy W Ratemo       |   64   | C-    |
|  3123  | Zippy W Wambugu      |   91   | A-    |
|  3124  | Zippy W Wanyonyi     |   59   | D+    |
|  3125  | Zippy W Yegon        |   66   | C     |  

## Securing postgreSQL Databases.

1. What type of authentication did we utilize in the above user creation ie given that we have matched the database user to the Linux system username?

```
The above authentication is called peer authentication, If you are logged into Linux as a user named mopiyo, and there is a PostgreSQL role (database user) also called mopiyo, then you can connect to PostgreSQL without entering a password. PostgreSQL “peers” into the OS username and matches it.

example 
# Logged into Linux as pomollo

psql -d exam

This works without a password if:

The pg_hba.conf file says to use peer for local connections,

And a matching database role pomollo exists.

Ident authentication is similar, but used more often with remote connections. It checks the connecting user’s identity using the Ident protocol. Both rely on the OS username, but:

Peer = local connections only (Linux user = DB user).

Ident = works for both local & remote (uses ident server or ident request).
```

2. What/how did the sed statement in step #9 help accomplish?

```
sed (Stream Editor) is a Linux command-line tool that edits text in files automatically.

In the PostgreSQL setup steps, the sed command was likely used to:

Uncomment configuration lines, or

Replace the default md5 with scram-sha-256 in postgresql.conf or pg_hba.conf.

For example:

sudo sed -i "s/#password_encryption = md5/password_encryption = scram-sha-256/" /etc/postgresql/17/main/postgresql.conf


This command:

-i → edits the file in place.

s/.../.../ → substitute text.

It finds the line #password_encryption = md5 and changes it to password_encryption = scram-sha-256.

Without this, you’d have to open vi and edit manually. So sed helps automate config changes.
```

Peer & Ident

```
Authentication methods explained
Peer & Ident

Peer → OS user must match DB role (local only).

Ident → Uses Ident protocol to verify system username of the connecting user (local or remote).
```

`md5` vs `scram-sha-256`
```
MD5:

Hashing method for passwords.

Stored as md5<hash> in PostgreSQL.

Vulnerable to collision attacks and dictionary attacks.

SCRAM-SHA-256:

Modern, safer hashing algorithm.

Uses salt and iterations to slow brute-force attacks.

Stored as SCRAM-SHA-256$... in PostgreSQL

scram-sha-256:
