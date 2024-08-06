### Database Design and Build - Part 1
DS5111, Summer 2024
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

(./ERD.png)

**9) (2 PTS) Suppose you were asked if your database could also support the UVA SDS Residential MSDS Program. Explain any issues that might arise, changes to the database structure (schema), and new data that might be needed. Note you won’t actually need to support this use case for the project.**

I would need to add a field to the teaching assignments table to indicate whether the course being taught is for Residential or Online. Because some instructors teach courses in both programs, it would not be appropriate to have this field in the instructors table. Also, as far as I know the learning outcomes are supposed to be the same for both programs, so the learning outcomes table should not change. The courses table could potentially change because some Residential elective classes are not offered for the Online program; however, I don’t think that is necessary to address in the courses table and could be better addressed in the teaching assignments table.

*Aside: Would it have been helpful to know this information before designing this database? You bet! Does this sort of thing happen in real life? Certainly. A good engineer/developer would have addressed this need during the planning stage with the right question: Any other use cases we’d want this database to support?*

One case I could imagine would be, as new courses replace old courses, to compare the learning outcomes of the new course and the old course it’s replacing. The new course wouldn’t necessarily need to have identical learning outcomes as the old course; that would likely be on a case by case basis. However, I could see the comparison being valuable as instructors and program managers check to make sure the overall program still covers all the necessary learning objectives.
