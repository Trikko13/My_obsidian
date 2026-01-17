
Вот DDL скрипты для создания таблиц в PostgreSQL на основе предоставленного кода. Я также исправлю некоторые моменты, такие как тип данных для `HIREDATE`, чтобы использовать `DATE` вместо `DATETIME`, так как в PostgreSQL нет типа `DATETIME`.
f
### DDL Скрипты


```sql
-- Создание таблицы EMP
DROP TABLE IF EXISTS EMP;
CREATE TABLE EMP (
    EMPNO INT NOT NULL,
    ENAME VARCHAR(10),
    JOB VARCHAR(9),
    MGR INT,
    HIREDATE DATE,  -- Используем тип DATE
    SAL INT,
    COMM INT,
    DEPTNO INT,
    PRIMARY KEY (EMPNO)  -- Добавляем первичный ключ
);

-- Вставка данных в таблицу EMP
INSERT INTO EMP VALUES
    (7369, 'SMITH',  'CLERK',     7902, '1980-12-17', 800, NULL, 20),
    (7499, 'ALLEN',  'SALESMAN',  7698, '1981-02-20', 1600,  300, 30),
    (7521, 'WARD',   'SALESMAN',  7698, '1981-02-22', 1250,  500, 30),
    (7566, 'JONES',  'MANAGER',   7839, '1981-04-02', 2975, NULL, 20),
    (7654, 'MARTIN', 'SALESMAN',  7698, '1981-09-28', 1250, 1400, 30),
    (7698, 'BLAKE',  'MANAGER',   7839, '1981-05-01', 2850, NULL, 30),
    (7782, 'CLARK',  'MANAGER',   7839, '1981-06-09', 2450, NULL, 10),
    (7788, 'SCOTT',  'ANALYST',   7566, '1982-12-09', 3000, NULL, 20),
    (7839, 'KING',   'PRESIDENT', NULL, '1981-11-17', 5000, NULL, 10),
    (7844, 'TURNER', 'SALESMAN',  7698, '1981-09-08', 1500,    0, 30),
    (7876, 'ADAMS',  'CLERK',     7788, '1983-01-12', 1100, NULL, 20),
    (7900, 'JAMES',  'CLERK',     7698, '1981-12-03', 950, NULL, 30),
    (7902, 'FORD',   'ANALYST',   7566, '1981-12-03', 3000, NULL, 20),
    (7934, 'MILLER', 'CLERK',     7782, '1982-01-23', 1300, NULL, 10);

-- Создание таблицы DEPT
DROP TABLE IF EXISTS DEPT;
CREATE TABLE DEPT (
    DEPTNO INTEGER,
    DNAME VARCHAR(14),
    LOC VARCHAR(13),
    PRIMARY KEY (DEPTNO)  -- Добавляем первичный ключ
);

-- Вставка данных в таблицу DEPT
INSERT INTO DEPT VALUES
    (10, 'ACCOUNTING', 'NEW YORK'),
    (20, 'RESEARCH',   'DALLAS'),
    (30, 'SALES',      'CHICAGO'),
    (40, 'OPERATIONS', 'BOSTON');

-- Создание таблицы T1
DROP TABLE IF EXISTS T1;
CREATE TABLE T1 (
    ID INTEGER,
    PRIMARY KEY (ID)  -- Добавляем первичный ключ
);

-- Вставка данных в таблицу T1
INSERT INTO T1 VALUES (1);

-- Создание таблицы T10
DROP TABLE IF EXISTS T10;
CREATE TABLE T10 (
    ID INTEGER,
    PRIMARY KEY (ID)  -- Добавляем первичный ключ
);

-- Вставка данных в таблицу T10
INSERT INTO T10 (ID) VALUES 
    (1), (2), (3), (4), (5), (6), (7), (8), (9), (10);

-- Создание таблицы T100
DROP TABLE IF EXISTS T100;
CREATE TABLE T100 (
    ID INTEGER,
    PRIMARY KEY (ID)  -- Добавляем первичный ключ
);

-- Вставка данных в таблицу T100
INSERT INTO T100 (ID) VALUES 
    (1), (
    
v


```





select empno, sal,
	case when sal <= 2000 then 'Underpaid'
		 when sal >= 2000 then 'Overpaid'
	else 'ok' 
	end as status
from recipes.emp;


select coalesce(comm, 0)
	from recipes.emp;


select ename, job
	from recipes.emp
	where deptno in (10,20)
	and (ename like '%I%' or job like '%ER');

-- 1.Сортировка по зависящему от данных ключу
select ename, sal, job, comm
from recipes.emp 
order by case when job = 'SALESMAN' then comm else sal end

-- 2.
select ename, sal, job, comm,
case when job = 'SALESMAN' then comm else sal end as ordered
from recipes.emp
order by 5

-- 3. Объединение нескольких таблиц через union 
select ename as ename_and_dname , deptno
from recipes.emp
where deptno = 10
union all
select '---------', null
from recipes.t1
union all 
select dname, deptno
from recipes.dept;


select e.ename, d.loc
from recipes.emp e, recipes.dept d
where e.deptno = d.deptno 
and e.deptno = 10





