-- Создание таблиц


CREATE TABLE "regions" (
id SERIAL PRIMARY KEY,
name varchar
);

CREATE TABLE "locations" (
id SERIAL PRIMARY KEY,
address varchar,
region_id integer REFERENCES regions (id)
);

CREATE TABLE "departments" (
id SERIAL PRIMARY KEY,
name varchar,
location_id integer REFERENCES locations (id)
);

CREATE TABLE "employees" (
id SERIAL PRIMARY KEY,
name varchar,
last_name varchar,
hire_date date,
salary integer,
email varchar,
manager_id integer REFERENCES employees (id),
department_id integer REFERENCES departments (id)
);

ALTER TABLE departments ADD COLUMN manager_id integer REFERENCES employees (id);


-- Заполнение таблиц


INSERT INTO regions (name) VALUES
('Russia'),
('Australis');

INSERT INTO locations (address, region_id) VALUES
('Moscow, Lenina str., 5', 1),
('Kanberra, Peace str., 3', 2);

INSERT INTO departments (name, location_id) VALUES
('development', 1),
('sales', 2);

ALTER TABLE employees ALTER COLUMN manager_id SET DEFAULT Null;

INSERT INTO employees (name, last_name, hire_date, salary, email, department_id) VALUES 
('Andrey', 'Pankin', '2017-05-01', '4000', 'andrey.pankin@gmail.com',1),
('Marina', 'Nikitina', '2015-07-02', '4000', 'marina.nikitina@dualbootpartners.com',1),
('Oleg', 'Podlesniy', '2016-10-10', '3500', 'oleg.podlesniy@yandex.ru',1),
('Artur', 'Ivanov', '2013-05-18', '3500', 'artur.ivanov@gmail.com',1),
('Venera', 'Simonova', '2013-11-08', '3500', 'venera.simonova@dualbootpartners.com',2),
('Rail', 'Valiev', '2019-02-13', '2000', 'rail.valiev@gmail.com',2),
('Tatiana', 'Veselova', '2020-05-19', '2500', 'tatiana.veselova@gmail.com',2),
('Igor', 'Petrov', '2016-09-02', '3000', 'igor.petrov@gmail.com',2);

UPDATE employees SET hire_date = '2023-03-11' WHERE name = 'Igor';
UPDATE employees SET last_name = 'Kozhevnikov' WHERE name = 'Igor';


-- Запросы на получение выборок


-- Показать работников у которых нет почты или почта не в корпоративном домене (домен dualbootpartners.com)
SELECT * FROM employees WHERE email IS NULL OR email NOT LIKE '%dualbootpartners.com';

-- Получить список работников нанятых в последние 30 дней
SELECT * FROM employees WHERE current_date - hire_date < 30;

-- Найти максимальную и минимальную зарплату по каждому департаменту
SELECT d.name, MIN (e.salary) min_salary, MAX (e.salary) max_salary
FROM employees e
JOIN departments d ON (e.department_id = d.id)
GROUP BY d.name

-- Посчитать количество работников в каждом регионе
SELECT r.name, COUNT(*) 
FROM employees e 
JOIN departments d ON (e.department_id = d.id) 
JOIN locations l ON (d.location_id = l.id)
JOIN regions r ON (l.region_id = r.id)
GROUP BY r.name;

-- Показать сотрудников у которых фамилия длиннее 10 символов
SELECT * FROM employees WHERE LENGTH (last_name) > 10;

-- Показать сотрудников с зарплатой выше средней по всей компании
SELECT * FROM employees WHERE salary > (SELECT AVG(salary) FROM employees);