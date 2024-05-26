# health-care-analysis-sql

-- creating tables.
CREATE TABLE Patients (
patient_id INT PRIMARY KEY,
patient_name VARCHAR(50),
age INT,
gender VARCHAR(10),
city VARCHAR(50)
);
CREATE TABLE Symptoms (
symptom_id INT PRIMARY KEY,
symptom_name VARCHAR(50)
);
CREATE TABLE Diagnoses (
diagnosis_id INT PRIMARY KEY,
diagnosis_name VARCHAR(50)
);
CREATE TABLE Visits (
visit_id INT PRIMARY KEY,
patient_id INT,
symptom_id INT,
diagnosis_id INT,
visit_date DATE,
FOREIGN KEY (patient_id) REFERENCES Patients(patient_id),
FOREIGN KEY (symptom_id) REFERENCES Symptoms(symptom_id),
FOREIGN KEY (diagnosis_id) REFERENCES Diagnoses(diagnosis_id)
);
-- Insert data into Patients table
INSERT INTO Patients (patient_id, patient_name, age, gender, city)
VALUES
(1, 'John Smith', 45, 'Male', 'Seattle'),
(2, 'Jane Doe', 32, 'Female', 'Miami'),
(3, 'Mike Johnson', 50, 'Male', 'Seattle'),
(4, 'Lisa Jones', 28, 'Female', 'Miami'),
(5, 'David Kim', 60, 'Male', 'Chicago');
-- Insert data into Symptoms table
INSERT INTO Symptoms (symptom_id, symptom_name)
VALUES
(1, 'Fever'),
(2, 'Cough'),
(3, 'Difficulty Breathing'),
(4, 'Fatigue'),
(5, 'Headache');
-- Insert data into Diagnoses table
INSERT INTO Diagnoses (diagnosis_id, diagnosis_name)
VALUES
(1, 'Common Cold'),
(2, 'Influenza'),
(3, 'Pneumonia'),
(4, 'Bronchitis'),
(5, 'COVID-19');
-- Insert data into Visits table
INSERT INTO Visits (visit_id, patient_id, symptom_id, diagnosis_id, visit_date)
VALUES
(1, 1, 1, 2, '2022-01-01'),
(2, 2, 2, 1, '2022-01-02'),
(3, 3, 3, 3, '2022-01-02'),
(4, 4, 1, 4, '2022-01-03'),
(5, 5, 2, 5, '2022-01-03'),
(6, 1, 4, 1, '2022-05-13'),
(7, 3, 4, 1, '2022-05-20'),
(8, 3, 2, 1, '2022-05-20'),
(9, 2, 1, 4, '2022-08-19'),
(10, 1, 2, 5, '2022-12-01');

-- 1. Write a SQL query to retrieve all patients who have been diagnosed with COVID-19

SELECT PATIENT_NAME ,diagnosis_name
FROM Patients P
JOIN Visits V 
ON P.patient_id = V.patient_id
Join Diagnoses D 
ON V.diagnosis_id = D.diagnosis_id
where diagnosis_name ='COVID-19'

-- 2. Write a SQL query to retrieve the number of visits made by each patient
--    ordered by the number of visits in descending order
SELECT PATIENT_NAME ,COUNT(patient_name) AS NUMBER_OF_VISITS
FROM Patients P
JOIN Visits V 
ON P.patient_id = V.patient_id
GROUP BY patient_name
ORDER BY 2 DESC

-- 3. Write a SQL query to calculate the average age of patients who have been diagnosed with Pneumonia.
SELECT avg(age) as avg_age_of_patients_with_pnemonia,diagnosis_name
FROM Patients P 
JOIN Visits V
ON P.patient_id=V.patient_id
JOIN Diagnoses D
ON V.diagnosis_id=D.diagnosis_id
WHERE diagnosis_name= 'Pneumonia'
group by diagnosis_name

--4. Write a SQL query to retrieve the top 3 most common symptoms among all visits.
SELECT TOP 3 symptom_name ,count(S.symptom_id) AS no_of_visit
from Symptoms S 
join Visits V
ON S.symptom_id = V.symptom_id
group by symptom_name
ORDER BY 2 DESC

-- 5. Write a SQL query to retrieve the patient who has the highest number of different symptoms reported.
SELECT top 1 PATIENT_NAME ,count(S.symptom_id) as no_of_symptoms
FROM Patients P
JOIN Visits V
ON P.patient_id = V.patient_id 
JOIN Symptoms S 
ON V.symptom_id = S.symptom_id
group by patient_name
order by 2 desc


-- 7. Write a SQL query to retrieve the top 5 cities with the highest number of visits,
 --   along with the count of visits in each city.

 select city,count(visit_id) as no_of_visits from Patients P
 JOIN Visits V
 ON P.patient_id =V.patient_id 
 group by city
 order by 2 desc

 -- 8. Write a SQL query to find the patient who has the highest number of visits in a single day, 
   --  along with the corresponding visit date.

   SELECT TOP 1 PATIENT_NAME,VISIT_DATE,COUNT(visit_id) AS NO_OF_VISIT FROM Patients P 
   JOIN VISITS V 
   ON P.patient_id = V.patient_id
   GROUP BY patient_name,visit_date
   ORDER BY 3 DESC

   -- 9. Write a SQL query to retrieve the average age of patients for each diagnosis,
   --    ordered by the average age in descending order.

   SELECT DIAGNOSIS_NAME , AVG(AGE) as avg_age
   FROM Diagnoses D
   JOIN Visits V 
   ON D.diagnosis_id = V.diagnosis_id
   JOIN Patients P
   ON V.patient_id = P.patient_id
   group by diagnosis_name
   order by 2 desc

   -- 10. Write a SQL query to calculate the cumulative count of visits over time, ordered by the visit date.

   SELECT VISIT_DATE ,COUNT(*) OVER (ORDER BY VISIT_DATE) AS NUMBER_OF_VISITS 
   FROM Visits 
   ORDER BY visit_date
