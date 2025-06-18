# Employee_salary-Data-base-SQL-Analysis
This project is a simple employee database management system created using SQL. It includes the creation of table: Departments and Employees, along with sample data and several SQL queries to retrieve information. The project is aimed at demonstrating how to set up a basic employee database and perform common operations.

# SQL Queries
Create database Employee;

use employee;
DROP TABLE IF EXISTS Employee_salary;
CREATE TABLE Employee_salary (
  EEID INT NOT NULL PRIMARY KEY,
  Full_Name VARCHAR(30),
  Department VARCHAR(30),
  Business_Unit VARCHAR(40),
  Gender VARCHAR(10), 
  Ethnicity VARCHAR(20),
  employee_salary FLOAT,
  Age INT,
  Hire_Date DATE,
  Annual_Salary FLOAT,
  Bonus_perc DECIMAL(5,2),
  Country VARCHAR(30),
  City VARCHAR(20),
  Exit_Date DATE
);
ALTER table Employee_salary
Add column Job_Title Varchar(30);

ALTER TABLE Employee_salary
DROP PRIMARY KEY;
alter table Employee_salary
modify column EEID varchar (10) Not Null;
Alter Table Employee_salary
ADD primary key (EEID);

ALTER TABLE Employee_salary
DROP PRIMARY KEY;

ALTER TABLE Employee_salary
MODIFY COLUMN Annual_Salary VARCHAR(20);

ALTER TABLE Employee_salary
MODIFY COLUMN Bonus_perc VARCHAR(10);


Select * From Employee_salary;

-- Finding Any duplicate Records 
Select EEID, Count(*) 
From Employee_salary
Group By EEID
Having Count(*) > 1;
-- No Duplicates Records 
-- Departament wise count
Select Department, Count(*)
From Employee_salary
Group By Department ;

ALTER  Table Employee_salary
Drop Column Employee_salary ;
Select * From Employee_salary;
 -- Basic Satistical Analysis 
 
Select  AVG(Annual_salary)
From Employee_salary;

Select MAX(Annual_salary)
From Employee_salary;

Select Min(Annual_salary)
From Employee_salary;

Select 
      department,
      Sum(Annual_salary) as total_salary
From Employee_salary
group by department;

-- Gender Distribution across dep 
select Department,gender ,count(*)
from Employee_salary 
Group By department,gender
order by department,gender;

-- Business Unit
Select Business_unit,gender,count(*)
from Employee_salary
group by Business_unit,gender 
order by Business_unit, gender;

Select Gender,count(*)
From Employee_salary
Group by Gender;

-- Bonus Salary insights 

Select department, round(AVG(Bonus_perc),2) as Avg_Bonus
From Employee_salary
Group by department
order by Avg_Bonus Desc
limit 2;

--* How many Employee have a Bonus Percentage greater than 0.1
select count(*) as Highest_Bonus_Employee 
From Employee_salary 
where Bonus_perc > 0.1 ;

-- * which city And Country  pay highest avg Annual_Salary
Select City, Round(AVG(Annual_salary),2) As Highest_Avg_salary
From Employee_salary 
Group  by City 
order By Highest_Avg_salary Desc
limit 1; 

Select Country,Round(AVg(Annual_salary),2) As Highest_Avg_salary
From Employee_salary 
Group By Country 
order by Highest_Avg_salary Desc
limit 1;

-- * Which Department where all Employee Have Greater than  0% bonus 
Select Department ,Count(*)
From Employee_salary 
Group by Department
Having Max(Bonus_perc) >= 0;

-- list of employee with salarys above the company average 
select EEID,Full_Name,Gender
From employee_salary
where (Annual_Salary) > (select AVG(Annual_Salary) From Employee_salary);


-- Date _Based analyze
Select*
From Employee_salary
Where Hire_date < '2010-01-01' And (Exit_date is not Null);

select Exit_date,Count(*) As Exit_employee
from Employee_salary
group by Exit_date ;

-- who are most recently hired  employee 
Select* 
From Employee_salary
order By Hire_date DESC
limit 5;

-- Window Function
-- Rank()

Select EEID, Full_Name,Department,Annual_salary,
 Rank() over (partition by Department order by Annual_salary desc) As salary_rank
 From Employee_salary;

Select EEID ,Full_Name,department, Annual_salary,
Dense_rank() Over (partition by department order by Annual_salary desc) as Dense_salary_rnk
from Employee_salary;


-- Using Row Number Find Mostly Recently Hire Per Dep

WITH RankedHire AS (
    SELECT EEID,
           Department,
           Gender,
        ROW_NUMBER() OVER (PARTITION BY Department ORDER BY Hire_Date DESC) AS rn
    FROM 
        Employee_salary
)
SELECT * 
FROM RankedHire 
WHERE rn = 1;


-- store procedure 

DELIMITER $$
Create procedure GetEmployeeByDepartment(IN dept_name varchar(50))
Begin 
     Select* From Employee_salary
     where Department = dept_name;
END$$

 call GetEmployeeByDepartment('Finance');
