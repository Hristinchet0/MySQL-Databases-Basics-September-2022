CREATE DATABASE Company;
USE Company;

CREATE TABLE employee (
	emp_id INT PRIMARY KEY,
    first_name VARCHAR(40),
    last_name VARCHAR(40),
    birth_day DATE,
    sex VARCHAR(1),
    salary INT,
    super_id INT,
    brunch_id INT
);

CREATE TABLE branch (
	branch_id INT PRIMARY KEY,
    branch_name VARCHAR(40),
    mgr_id INT,
    mgr_start_date DATE,
    FOREIGN KEY (mgr_id) REFERENCES employee(emp_id) ON DELETE SET NULL
);

ALTER TABLE employee
ADD FOREIGN KEY(branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL;

ALTER TABLE employee
ADD FOREIGN KEY(super_id) REFERENCES employee(emp_id) ON DELETE SET NULL;

CREATE TABLE client (
	client_id INT PRIMARY KEY,
    client_name VARCHAR(40),
    branch_id INT,
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id) ON DELETE SET NULL
);

CREATE TABLE branch_supplier (
	branch_id INT,
    supplier_name VARCHAR(40),
    supply_type VARCHAR(40),
    PRIMARY KEY (branch_id, supplier_name),
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id) ON DELETE CASCADE
);

INSERT INTO employee 
VALUES(100, 'David', 'Wallace', '1967-11-17', 'M', 250000, NULL, NULL);

INSERT INTO branch VALUES(1, 'Corporate', 100, '2006-02-09');

UPDATE employee
SET branch_id = 1
WHERE emp_id = 100;

INSERT INTO employee VALUES(101, 'Jan', 'Levinson', '1961-05-11', 'F', 110000, 100, 1);

INSERT INTO employee VALUES(102, 'Michael', 'Scott', '1964-03-15', 'M', 75000, 100, NULL);

INSERT INTO branch VALUES(2, 'Scranton', 102, '1992-04-06');

UPDATE employee
SET branch_id = 2
WHERE emp_id = 102;

INSERT INTO employee VALUES(103, 'Angela', 'Martin', '1971-06-25', 'F', 63000, 102, 2);
INSERT INTO employee VALUES(104, 'Kelly', 'Kapoor', '1980-02-05', 'F', 55000, 102, 2);
INSERT INTO employee VALUES(105, 'Stanley', 'Hudson', '1958-02-19', 'M', 69000, 102, 2);

INSERT INTO employee VALUES(106, 'Josh', 'Porter', '1969-09-05', 'M', 78000, 100, NULL);

INSERT INTO branch VALUES(3, 'Stamford', 106, '1998-02-13');

UPDATE employee
SET branch_id = 3
WHERE emp_id = 106;

INSERT INTO employee VALUES(107, 'Andy', 'Bernard', '1973-07-22', 'M', 65000, 106, 3);
INSERT INTO employee VALUES(108, 'Jim', 'Halpert', '1978-10-01', 'M', 71000, 106, 3);

INSERT INTO branch_supplier VALUES(2, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Patriot Paper', 'Paper');
INSERT INTO branch_supplier VALUES(2, 'J.T. Forms & Labels', 'Custom Forms');
INSERT INTO branch_supplier VALUES(3, 'Uni-ball', 'Writing Utensils');
INSERT INTO branch_supplier VALUES(3, 'Hammer Mill', 'Paper');
INSERT INTO branch_supplier VALUES(3, 'Stamford Lables', 'Custom Forms');

INSERT INTO client VALUES(400, 'Dunmore Highschool', 2);
INSERT INTO client VALUES(401, 'Lackawana Country', 2);
INSERT INTO client VALUES(402, 'FedEx', 3);
INSERT INTO client VALUES(403, 'John Daly Law, LLC', 3);
INSERT INTO client VALUES(404, 'Scranton Whitepages', 2);
INSERT INTO client VALUES(405, 'Times Newspaper', 3);
INSERT INTO client VALUES(406, 'FedEx', 2);

CREATE TABLE works_with (
	emp_id INT,
    client_id INT,
    total_sales INT,
    PRIMARY KEY (emp_id, client_id),
    FOREIGN KEY (client_id) REFERENCES client(client_id) ON DELETE CASCADE,
    FOREIGN KEY (emp_id) REFERENCES employee(emp_id) ON DELETE CASCADE
);

INSERT INTO works_with VALUES(105, 400, 55000);
INSERT INTO works_with VALUES(102, 401, 267000);
INSERT INTO works_with VALUES(108, 402, 22500);
INSERT INTO works_with VALUES(107, 403, 5000);
INSERT INTO works_with VALUES(108, 403, 12000);
INSERT INTO works_with VALUES(105, 404, 33000);
INSERT INTO works_with VALUES(107, 405, 26000);
INSERT INTO works_with VALUES(102, 406, 15000);
INSERT INTO works_with VALUES(105, 406, 130000);

-----------------------------------------------------------------------------------------------

/*намери всички служители*/
SELECT * FROM employee;

/*намери всички клиенти*/
SELECT * FROM client;

/*намери всички случители сортирани по заплата*/
SELECT * FROM employee
ORDER BY salary DESC; /*ASC/DESC*/

/*намери всички служители соритани първо по пол, после по име*/
SELECT * FROM employee 
ORDER BY sex, first_name;

/*намери първите 5 служителя*/
SELECT * FROM employee 
LIMIT 5;

/*намери първото и последното име на служителите*/
SELECT first_name, last_name FROM employee;

/*намери името и фамилията first_name -> forename, last_name -> surname*/
SELECT first_name AS forename, last_name AS surname
FROM employee;

/*намери всички различни полове*/
SELECT DISTINCT sex
FROM employee;

/*намери всички служители с ИД 2*/
SELECT * FROM employee
WHERE branch_id = 2;

/*намери всички служители по ИД и име които са родени след 1969*/
SELECT emp_id, first_name, last_name
FROM employee
WHERE birth_day >= 1970-07-07;

/*намери всички служители с клон_ид 2 пол ж*/
SELECT * FROM employee
WHERE branch_id = 2 AND sex = 'F';

/*намери всички служители които са жени и са родени след 1979г или взимат 
заплата над 80000*/
SELECT * FROM employee
WHERE (birth_day >= 1970-01-01 AND sex = 'f') OR salary > 80000;

/*намери всички служители родени между 1970 и 1975*/
SELECT * FROM employee
WHERE birth_day BETWEEN '1970-01-01' AND '1975-01-01';

/*намери всички служители с имена Jim, Michael, Johnny или David*/
SELECT * FROM employee
WHERE first_name IN ('Jim', 'Michael', 'Johnny', 'David');

---------------------------------------------------------------------------------

/*намери броя на служителите*/
SELECT COUNT(super_id) FROM employee;

/*намери средната заплата на всички служители*/
SELECT AVG(salary) FROM employee;

/*намери сумата на заплатата на всички служители*/
SELECT SUM(salary) FROM employee;

/*намери колко мъже и жени служители има*/
SELECT COUNT(sex), sex FROM employee
GROUP BY sex;

/*намери общата сума на продажбите на продавачите*/
SELECT SUM(total_sales), emp_id FROM works_with
GROUP BY client_id; 

/* след тази команда получих Error Code: 1055 incompatible with sql_mode=only_full_group_by*/
/*намерих инфо за решаване на проблема в google, с кода долу*/
SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));

/*намери общата похарчена сума от всеки клиент*/
SELECT SUM(total_sales), client_id
FROM works_with
GROUP BY client_id;

------------------------------------------------------------------------------------------------------

/*намери всички клиенти които съдържат в името си LLC*/
SELECT * FROM client 
WHERE client_name LIKE '%LLC';

/*намери всички доставчици които съдържат в името 'label'*/
SELECT * FROM branch_supplier
WHERE supplier_name LIKE '% Label%';

/*намери всеки служител роден 10тия ден от месеца YYYY-DD-MM */
SELECT * FROM employee
WHERE birth_day LIKE '_____10%';

/*намери всички клиенти които са училища*/
SELECT * FROM client
WHERE client_name LIKE '%Highschool%';

---------------------------------------------------------------------------------------------------------

/*обедини таблиците на първите имена на служителите и имената на доставчиците*/
SELECT employee.first_name AS Employee_branch_Names
FROM employee
UNION
SELECT branch.branch_name 
FROM branch;

/*намери списъка с имената на всички клиенти и клонови доставчици */
SELECT client.client_name AS 'Non-Employee_Entities', client.branch_id
FROM client
UNION
SELECT branch_supplier.supplier_name, branch_supplier.branch_id
FROM branch_supplier;

----------------------------------------------------------------------------------------------------------

/*добави клон*/
INSERT INTO branch VALUES(4, "Buffalo", NULL, NULL);

/*намери всички клонове и имената на техните мениджъри*/
SELECT employee.emp_id, employee.first_name, branch.branch_name
FROM employee
JOIN branch
ON employee.emp_id = branch.mgr_id;

----------------------------------------------------------------------------------------------------------------

/*намери всички имена на служители които имат продажби над 50000*/
SELECT employee.first_name, employee.last_name
FROM employee
WHERE employee.emp_id IN (SELECT works_with.emp_id
						FROM works_with
                        WHERE works_with.total_sales > 50000);
                        
/*намери всички клиенти, които се межежират от клона който Michael Scott менежира (да предположим че знаем ID на Michael)*/
SELECT client.client_id, client.client_name
FROM client
WHERE client.branch_id = (SELECT branch.branch_id
						  FROM branch
                          WHERE branch.mgr_id = 102);
                          
/*намери всички клиенти, които се межежират от клона който Michael Scott менежира (не знаем ID на Michael)*/

SELECT client.client_id, client.client_name
FROM client
WHERE client.branch_id = (SELECT branch.branch_id
						  FROM branch
                          WHERE branch.mgr_id = (SELECT employee.emp_id
												 FROM employee
                                                 WHERE employee.frist_name = 'Michael' AND employee.last_name = 'Scott'
                                                 LIMIT 1));
                                                 
/*Намерете имената на служителите, които работят с клиенти, обслужвани от клона на Scranton*/
SELECT employee.first_name, employee.last_name
FROM employee
WHERE employee.emp_id IN (SELECT works_with.emp_id
						  FROM works_with)
AND employee.branch_id = 2;

/*намерете всички клиенти които са похарчили повече от 100000*/
SELECT client.client_name
FROM client
WHERE client.client_id IN (SELECT client_id
						   FROM (
								SELECT SUM(works_with.total_sales) AS totals, client_id
								FROM works_with
								GROUP BY client_id) AS total_client_sales
						   WHERE totals > 100000
);

---------------------------------------------------------------------------------------------------------------------------


-- CREATE
--     TRIGGER `event_name` BEFORE/AFTER INSERT/UPDATE/DELETE
--     ON `database`.`table`
--     FOR EACH ROW BEGIN
-- 		-- trigger body
-- 		-- this code is applied to every
-- 		-- inserted/updated/deleted row
--     END;

CREATE TABLE trigger_test (
     message VARCHAR(100)
);



/*пише се в command line client*/
DELIMITER $$
CREATE
    TRIGGER my_trigger BEFORE INSERT
    ON employee
    FOR EACH ROW BEGIN
        INSERT INTO trigger_test VALUES('added new employee');
    END$$
DELIMITER ;


INSERT INTO employee
VALUES(109, 'Oscar', 'Martinez', '1968-02-19', 'M', 69000, 106, 3);

/*пише се в command line client*/
DELIMITER $$
CREATE
    TRIGGER my_trigger2 BEFORE INSERT
    ON employee
    FOR EACH ROW BEGIN
        INSERT INTO trigger_test VALUES(NEW.first_name);
    END$$
DELIMITER ;


INSERT INTO employee
VALUES(110, 'Kevin', 'Malone', '1978-02-19', 'M', 69000, 106, 3);

/*пише се в command line client*/
DELIMITER $$
CREATE
    TRIGGER my_trigger3 BEFORE INSERT
    ON employee
    FOR EACH ROW BEGIN
         IF NEW.sex = 'M' THEN
               INSERT INTO trigger_test VALUES('added male employee');
         ELSEIF NEW.sex = 'F' THEN
               INSERT INTO trigger_test VALUES('added female');
         ELSE
               INSERT INTO trigger_test VALUES('added other employee');
         END IF;
    END$$
DELIMITER ;


INSERT INTO employee
VALUES(111, 'Pam', 'Beesly', '1988-02-19', 'F', 69000, 106, 3);

/*намираме информацията 
SELECT * FROM trigger_test;

/*изтриваме тригерите
DROP TRIGGER my_trigger;
DROP TRIGGER my_trigger2;
DROP TRIGGER my_trigger3;
----------------------------------------------------------------------------------------------------------------------



