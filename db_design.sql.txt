USE ROLE DS5111_DBT;
USE DATABASE DS5111_SU24;
-- CREATE SCHEMA pah4rw;

USE SCHEMA PAH4RW;

USE WAREHOUSE COMPUTE_WH;

//CREATE TABLE synthetic AS
//SELECT 1 as data
//UNION ALL
//SELECT 2 as data;

// Q1
SELECT DISTINCT mnemonic, name FROM "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" WHERE active = TRUE;

//Q2
SELECT DISTINCT mnemonic, name FROM "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" WHERE active = FALSE;

//Q3
SELECT instructor FROM "DS5111_SU24"."PAH4RW"."PAH4RW_INSTRUCTORS_CSV" WHERE employee_status = 'Not a current employee';

//Q4
SELECT mnemonic, COUNT(learning_outcome) AS learning_outcome_count 
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_LEARNING_OUTCOMES_CSV" 
GROUP BY mnemonic;

//Q5


//Q6
SELECT lo.mnemonic, lo.learning_outcome, co.name, co.mnemonic 
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_LEARNING_OUTCOMES_CSV" lo
LEFT JOIN "DS5111_SU24"."PAH4RW"."PAH4RW_COURSES_CSV" co ON lo.mnemonic = co.mnemonic
WHERE lo.learning_outcome RLIKE '.*(SQL|NoSQL|SQLite|MySQL).*';

//Q7
SELECT instructor
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
WHERE term_name = 'summer2021' AND mnemonic = 'ds5100';

//Q8
SELECT DISTINCT instructor
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
WHERE term_name = 'fall2021'
ORDER BY instructor;

//Q9
SELECT term_name, instructor, COUNT(instructor) AS course_count
FROM "DS5111_SU24"."PAH4RW"."PAH4RW_TEACHING_ASSIGNMENTS_CSV"
GROUP BY term_name, instructor
ORDER BY term_name, instructor;

//Q10a


//Q10b