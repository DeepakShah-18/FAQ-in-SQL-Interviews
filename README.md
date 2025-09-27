# FAQ-in-SQL-Interviews
**These are the frequently asked questions in SQL interviews where we can get the same result by using different type of queries.**

**Please be advised that these syntax/code is supportable in MySQL and PostgreSQL environment**

---

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


---

--**Check the highest salary** 

Select *
From Employees
WHERE Salary = (SELECT MAX(Salary) FROM Employees)
;

***RESULT -***

<img width="1536" height="992" alt="image" src="https://github.com/user-attachments/assets/faaefd39-a59f-4e16-9b68-de6e10d06d9b" />


-- **Check highest salary with selective columns**

Select EmpID, Emp_Name, Salary
From Employees
WHERE Salary = (SELECT MAX(Salary) FROM Employees)
;

***RESULT -***

<img width="1268" height="820" alt="image" src="https://github.com/user-attachments/assets/4c89913b-32f4-491a-af2a-efef68dad521" />


---

-- **Check the 2nd Highest Salary**

Select *
From Employees
Where Salary <(Select Max(Salary) From Employees)
ORDER BY Salary DESC
LIMIT 1;


-- **Check the 2nd Highest Salary with selective columns**

Select EmpID, Emp_Name, Salary
From Employees
Where Salary <(Select Max(Salary) From Employees)
ORDER BY Salary DESC
LIMIT 1;

***RESULT -***

<img width="1184" height="936" alt="image" src="https://github.com/user-attachments/assets/3cfc0b2e-6646-4f27-b3df-edf56e408f4b" />


---

-- **Check the 3rd highest salary using TOP function (Interview based question and can be run in SQL server only)**

SELECT TOP 1 *
FROM Employees
WHERE Salary < (
    SELECT MAX(Salary)
    FROM Employees
    WHERE Salary < (SELECT MAX(Salary) FROM Employees)
)
ORDER BY Salary DESC;



-- **Check the 3rd highest salary using Rank function**

Select *
From (Select RANK() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;




-- **Check the 3rd highest salary using Dense_Rank function**

Select *
From (Select DENSE_RANK() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;



-- **Check the 3rd highest salary using Row_Number function**

Select *
From (Select Row_Number() Over(Order By Salary DESC) AS Highest_Salary_Position , 
			EmpID , Emp_Name , Salary From Employees) AS Ranked_Employee
Where Highest_Salary_Position  = 3;



-- **Check the 3rd highest salary using CTE and double layer of subquery**

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

***RESULT -***

<img width="1574" height="960" alt="image" src="https://github.com/user-attachments/assets/af47bf26-397a-46cb-a866-e20def5e7070" />


---

---- **Align the Hierarchy using LEFT JOIN**

Select E.EmpID AS Employee_ID , E.Emp_Name AS Employee_Name , E.Salary , M.Emp_Name AS Manager_Name, M.Salary 
From Employees E LEFT JOIN Employees M
ON E.ManagerID = M.EmpID
;

***RESULT -***

<img width="1784" height="1550" alt="image" src="https://github.com/user-attachments/assets/a3220265-7c5c-4da0-8778-9f2e064550c8" />


---

---- **Check the employee who is getting higher salary than their manager**

Select *
From Employees

---**(Currently we do not have any employee for this query to be run successfully so I'm adding two employees with higher salary for the better results)**


Insert INTO Employees VALUES	
(14, 'Thomas', 138000, 3),
(15, 'Tom', 125000, 2)
;

-- **Let's begin with the requested query now**


Select E.EmpID AS Employee_ID , E.Emp_Name AS Employee_Name , E.Salary AS SALARY , 
		M.EmpID AS Manager_ID , M.Emp_Name AS Manager_Name , M.Salary AS Manager_Salary ,
		(e.Salary - m.Salary) AS Variance
From Employees E LEFT JOIN Employees M
	ON E.ManagerID = M.EmpID  
WHERE E.Salary > M.Salary;

***RESULT -***

<img width="2246" height="1316" alt="image" src="https://github.com/user-attachments/assets/05779696-0291-45c3-b62b-320a8c893441" />


---**Just realized that we had Kevin earning more than his Manager's salary. However, adding 2 more employees really helped us to extract result with more than 1 row.** 

**Story of the line - Human eyes can miss the details but not the SQL code while manipulating the data.**

---

