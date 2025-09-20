# FAQ-in-SQL-Interviews
These are the frequently asked questions in SQL interviews where we can get the same result by using different type of queries.

Create Table Employees 
(EmpID INT Primary Key, 
Emp_Name Varchar(50), 
Salary Decimal(10,2), 
ManagerID INT Null, 
Foreign Key (ManagerID) REFERENCES Employees(EmpID)
);

INSERT INTO Employees (EmpID, Emp_Name, Salary, ManagerID) VALUES
-- Top level (no manager)
(1, 'Alice',   150000, NULL),       -- CEO
-- Second level
(2, 'Bob',     120000, 1),
(3, 'Charlie', 110000, 1),
(4, 'Diana',   130000, 1),
-- Third level
(5, 'Eve',      95000,  2),
(6, 'Frank',    98000,  2),
(7, 'Grace',   102000,  3),
(8, 'Heidi',    87000,  3),
(9, 'Ivan',     99000,  4),
(10,'Judy',     91000,  4),
-- Extra variety for ranking
(11,'Kevin',   125000, 2),
(12,'Laura',   107000, 3),
(13,'Mallory',  92000, 4);




-- Check the highest salary 

Select *
From Employees
WHERE Salary = (SELECT MAX(Salary) FROM Employees)
;


-- Check highest salary with selective columns

Select EmpID, Emp_Name, Salary
From Employees
WHERE Salary = (SELECT MAX(Salary) FROM Employees)
;

-- Check the 2nd Highest Salary

Select TOP 1 *
From Employees
Where Salary <(Select Max(Salary) From Employees)
ORDER BY Salary DESC
;


-- Check the 2nd Highest Salary with selective columns

Select TOP 1 EmpID, Emp_Name, Salary
From Employees
Where Salary <(Select Max(Salary) From Employees)
ORDER BY Salary DESC
;


-- Check the 3rd highest salary using TOP function

SELECT TOP 1 *
FROM Employees
WHERE Salary < (
    SELECT MAX(Salary)
    FROM Employees
    WHERE Salary < (SELECT MAX(Salary) FROM Employees)
)
ORDER BY Salary DESC;

-- Check the 3rd highest salary using Rank function

Select *
From (Select RANK() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;


-- Check the 3rd highest salary using Dense_Rank function

Select *
From (Select DENSE_RANK() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;


-- Check the 3rd highest salary using Row_Number function

Select *
From (Select Row_Number() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;


-- Check the 3rd highest salary using CTE and double layer of subquery

WITH CTE_SET AS (
				SELECT EmpID, Emp_Name, Salary
				FROM Employees
				WHERE Salary <(Select Max(Salary)
								From Employees
								WHERE Salary < (Select MAX(Salary) From Employees))
				)
SELECT *
FROM CTE_SET 
WHERE Salary = (SELECT MAX(Salary) FROM CTE_SET)
;
