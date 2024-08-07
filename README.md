### Database Design and Build - Part 1
DS5111, Summer 2024\
Brett Genz

Design Questions

**1) (3 PTS) What tables should you build?**

There are 4 types of information: courses, instructors, teaching assignments, and learning outcomes. Based on this, the database will need those 4 tables.

**2) (2 PTS) For each table, what field(s) will you use for primary key?**

| Table |	Primary key |
| ------|---------------|
|Courses|	mnemonic    |
| Instructors|	instructor|
|Teaching assignments|	mnemonic and instructor|
|Learning outcomes|	learning_outcome|


**3) (2 PTS) For each table, what foreign keys will you use?**

| Table |	Primary key |
| ------|---------------|
|Courses|	none    |
| Instructors|	none|
|Teaching assignments|	none|
|Learning outcomes|	mnemonic|


**4) (2 PTS) Learning outcomes, courses, and instructors need a flag to indicate if they are currently active or not. How will your database support this feature? In particular:**

*If a course will be taught again, it will be flagged as active. If the course won’t be taught again, it will be flagged as inactive.*

*It is important to track if an instructor is a current employee or not.*
*Learning outcomes for a course can change. You’ll want to track if a learning outcome is currently active or not.*

I added a column to the instructor table to incorporate the information in the raw data (specifying the two inactive instructors, and tagging the rest as current employees).

The courses table includes a column indicating which are or are not active.

For the learning outcomes table, this would need to be addressed with a query of the courses and learning outcomes table. However, the only indicator currently in the data for the status of learning objectives is whether or not a course is active.


**5) (1 PT) Is there anything to normalize in the database, and if so, how will you normalize it? Recall the desire to eliminate redundancy.**

Yes, the multiple entries for instructors in the capstone courses are not atomic, and need to be atomized. I will handle this by splitting the cells with multiple instructors. 

**6) (1 PT) Are there indexes that you should build? Explain your reasoning.**

I don’t believe that any additional indexes need to be built … at least not with the way I built the tables (see ERD below). Between the two primary keys of ‘mnemonic’ and ‘instructor’, which serve as indexes by default, I think that provides ample ability to access everything in all of the tables.

**7) (2 PTS) Are there constraints to enforce? Explain your answer and strategy.**
*For example, these actions should not be allowed:*
*- Entering learning objectives for a course not offered by the School of Data Science*
*- Assigning an invalid instructor to a course*

Primary key constraint: I have not yet checked this thoroughly, but just based on my recollection of what my different courses in the program have covered, there has been some overlap of topics. In theory, I would think that learning outcomes should be unique throughout the program; I believe it would be worth double-checking to make sure no two courses have the same learning outcome (and the work would be in checking to see if two basically identical outcomes are just phrased differently).

Basic constraints: Basic checks to ensure any new entries are not null and are of the correct data type for each field (in this case nearly all text but a few other types).

Foreign key constraint: Strategy would be to query unique values for each foreign key in the table in which it is a primary key and ensure there is a one-to-one match (this could be done with a pandas join or with a SQL query).

**8) (5 PTS) Draw and submit a Relational Model for your project. For an example, see Beginning Database Design Solutions page 115 Figure 5-28.**

![](./ERD.png)

**9) (2 PTS) Suppose you were asked if your database could also support the UVA SDS Residential MSDS Program. Explain any issues that might arise, changes to the database structure (schema), and new data that might be needed. Note you won’t actually need to support this use case for the project.**

I would need to add a field to the teaching assignments table to indicate whether the course being taught is for Residential or Online. Because some instructors teach courses in both programs, it would not be appropriate to have this field in the instructors table. Also, as far as I know the learning outcomes are supposed to be the same for both programs, so the learning outcomes table should not change. The courses table could potentially change because some Residential elective classes are not offered for the Online program; however, I don’t think that is necessary to address in the courses table and could be better addressed in the teaching assignments table.

*Aside: Would it have been helpful to know this information before designing this database? You bet! Does this sort of thing happen in real life? Certainly. A good engineer/developer would have addressed this need during the planning stage with the right question: Any other use cases we’d want this database to support?*

One case I could imagine would be, as new courses replace old courses, to compare the learning outcomes of the new course and the old course it’s replacing. The new course wouldn’t necessarily need to have identical learning outcomes as the old course; that would likely be on a case by case basis. However, I could see the comparison being valuable as instructors and program managers check to make sure the overall program still covers all the necessary learning objectives.


### Database Design and Build - Part 2

**1) (1 PT) Which courses are currently included (active) in the program? Include the course mnemonic and course name for each.**

Query:\
SELECT mnemonic, name FROM "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" WHERE active = TRUE;

Results:\
|MNEMONIC|NAME|
|--------|----|
|ds_biz_anaytics|Business Analytics for Data Scientists|
|ds_tech_bootcamp|Technical Bootcamp|
|ds5110|Big Data Systems|
|ds6002|Big Data Ethics|
|ds6011|Data Science Capstone Project Work I|
|ds6013|Data Science Capstone Project Work II|
|ds6030|Statistical Learning|
|ds6040|Bayesian Machine Learning|
|ds6050|Deep Learning|
|sarc5400|Data Visualization|
|stat6021|Linear Models for Data Science|


**2) (1 PT) Which courses were included in the program, but are no longer active? Include the course mnemonic and course name for each.**

Query:\
SELECT mnemonic, name FROM "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" WHERE active = FALSE;

Results:\
|MNEMONIC	|NAME|
|-----------|----|
|ds6003	|Practice and Application of Data Science Part 2|
|ds6012	|Big Data Ethics Part 2|

**3) (1 PT) Which instructors are not current employees?**

Query:\
SELECT instructor FROM "DS5111_SU24"."PAH4RW"."PAH4RW_INSTRUCTORS_CSV" WHERE employee_status = 'Not a current employee';

Results:\
|INSTRUCTOR|
|----------|
|Jeremy Bolton|
|Luis Felipe Rosado Murillo|

**4) (1 PT) For each course (active and inactive), how many learning outcomes are there?**

Query:\
SELECT mnemonic, COUNT(learning_outcome) AS learning_outcome_count 
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_LEARNING_OUTCOMES_CSV" 
GROUP BY mnemonic;

Results:\
|MNEMONIC|LEARNING_OUTCOME_COUNT|
|--------|----------------------|
|cs5012|9|
|ds_biz_analytics|9|
|ds_tech_bootcamp|15|
|ds5001|8|
|ds5100|34|
|ds5110|37|
|ds6001|10|
|ds6002|2|
|ds6003|3|
|ds6011|4|
|ds6012|2|
|ds6013|4|
|ds6030|19|
|ds6040|37|
|ds6050|24|
|stat6021|13|



**5) (2 PTS) Are there any courses with no learning outcomes? If so, provide their mnemonics and names.**

No, the same query I used for question 4 shows that every course has at least 2 learning outcomes.

Query:\
SELECT mnemonic, COUNT(learning_outcome) AS learning_outcome_count 
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_LEARNING_OUTCOMES_CSV" 
GROUP BY mnemonic;

Results:\
See table of results for question 4.

**6) (2 PTS) Which courses include SQL as a learning outcome? Include the learning outcome descriptions, course mnemonics, and course names in your solution.**

In this case I know from the raw data that there was another learning outcome in ds6001 about NoSQL, but it is not showing up in my results. I had a number of import errors when loading the data into tables, and I suspect that some of the rows did not make it into my tables in Snowflake.

Query:\
SELECT lo.mnemonic, lo.learning_outcome, co.name, co.mnemonic 
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_LEARNING_OUTCOMES_CSV" lo
LEFT JOIN "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" co ON lo.mnemonic = co.mnemonic
WHERE lo.learning_outcome LIKE '%SQL%';

Results:\
|MNEMONIC	|LEARNING_OUTCOME	|NAME|
|-----------|-------------------|----|
|ds5110	|Apply Spark SQL to data analysis tasks	|Big Data Systems|
|ds6001	|"Understand how to implement databases Python: SQLite	|Practice and Application of Data Science|
|ds6001	|Understand how to query databases with SQL 	|Practice and Application of Data Science|
    

**7) (1 PT) Who taught course ds5100 in Summer 2021?**

Query:\
SELECT instructor
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
WHERE term_name = 'summer2021' AND mnemonic = 'ds5100';

Results:\
|INSTRUCTOR|
|----------|
|Nada Basit|

**8) (1 PT) Which instructors taught in Fall 2021? Order their names alphabetically, making sure the names are unique.**

Query:
SELECT DISTINCT instructor
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
WHERE term_name = 'fall2021'
ORDER BY instructor;

Results:\
|INSTRUCTOR|
|----------|
|Adam Tashman|
|Bill Basener|
|Eric Field|
|Jason Williamson|
|Jeffrey Woo|
|Jon Kropko|
|Judy Fox|
|Luis Felipe Rosado Murillo|
|Mike Porter|
|Panagiotis Apostolellis|
|Pete Alonzi|
|Raf Alvarado|
|Rich Nguyen|
|Sree Mallikarjun|


**9) (1 PT) How many courses did each instructor teach in each term? Order your results by term and then instructor.**

Query:\
SELECT term_name, instructor, COUNT(instructor) AS course_count
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
GROUP BY term_name, instructor
ORDER BY term_name, instructor;

Results:\
|TERM_NAME|INSTRUCTOR|COURSE_COUNT|
|---------|----------|------------|
|fall2021|Adam Tashman|1|
|fall2021|Bill Basener|1|
|fall2021|Eric Field|1|
|fall2021|Jason Williamson|1|
|fall2021|Jeffrey Woo|1|
|fall2021|Jon Kropko|2|
|fall2021|Judy Fox|1|
|fall2021|Luis Felipe Rosado Murillo|2|
|fall2021|Mike Porter|1|
|fall2021|Panagiotis Apostolellis|1|
|fall2021|Pete Alonzi|1|
|fall2021|Raf Alvarado|1|
|fall2021|Rich Nguyen|1|
|fall2021|Sree Mallikarjun|1|
|spring2021|Adam Tashman|1|
|spring2021|Bill Basener|2|
|spring2021|Eric Tassone|1|
|spring2021|Jeffrey Woo|1|
|spring2021|Jeremy Bolton|1|
|spring2021|Judy Fox|1|
|spring2021|Marc Ruggiano|1|
|spring2021|Pete Alonzi|1|
|spring2021|Raf Alvarado & Teague Henry|1|
|spring2021|Renee Cummings|1|
|spring2021|Sree Mallikarjun|1|
|spring2021|Stephen Baek|1|
|summer2021|Adam Tashman|1|
|summer2021|Jason Williamson|1|
|summer2021|Jeffrey Woo|1|
|summer2021|Jeremy Bolton|1|
|summer2021|Jon Kropko|1|
|summer2021|Nada Basit|1|
|summer2021|Pete Alonzi|1|
|summer2021|Peter Gedeck|1|
|summer2021|Raf Alvarado|1|
|summer2021|Raf Alvarado & Teague Henry|1|
|summer2021|Renee Cummings|2|
|summer2021|Rich Nguyen|1|
|summer2021|Sree Mallikarjun|1|
|summer2021|Teague Henry|1|


**10a) (2 PTS) Which courses had more than one instructor for the same term? Provide the mnemonic and term for each. Note this occurs in courses with multiple sections.**

Unfortunately I'm having trouble with this and 10.b. ... I will get to them if possible before the Friday deadline.

**10b) (1 PT) For courses with multiple sections, provide the term, course mnemonic, and instructor name for each. Hint: You can use your result from 10a in a subquery or WITH clause.**


