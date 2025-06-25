# Hospital_Management_System_sql_project

![](https://github.com/ab21bisht/Hospital_Management_System_sql_project/blob/main/img%202.jpg)

## Business Problems and Solutions

### 1. Write a query to count the number of appointments for each status (e.g., Scheduled, Completed, No-show, Cancelled).
```sql
select 
	status,count(status)
from 
	appointments
group by 1;
```

### 2. Write a query to find patients who have more than one appointment scheduled.
```sql
select 
	patient_id, count(*) as appointment_count
from 
	appointments
group by 1
having count(*) >1;
```

### 3. Write a query to calculate the total billing amount grouped by payment status (e.g., Paid, Pending, Failed).
```sql
select 
	payment_status,sum(amount) 
from 
	billing
group by 1;
```

### 4. Write a query to find doctors who have not had any appointments scheduled.
```sql
select 
	* 
FROM 
	doctors d
LEFT JOIN 
	appointments a 
ON 
	d.doctor_id = a.doctor_id
WHERE 
	a.appointment_id IS NULL;
```

### 5. Write a query to calculate the average treatment cost for each type of treatment.
```sql
select 
	treatment_type, round(avg(cost),0) as average_cost
from 
	treatments
group by 1;
```

### 6. Write a query to list all patients who have had emergency appointments, including their names and appointment dates.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name, appointment_date
from 
	patients as p
join 
	appointments as a
on 
	a.patient_id = p.patient_id
where 
	reason_for_visit = 'Emergency';
```

### 7. Write a query to find the most common reason for visits in the `appointments` table.
```sql
select 
	reason_for_visit, count(reason_for_visit) 
from 
	appointments
group by 1
order by 2 desc;
```

### 8. Write a query to retrieve billing details for treatments that have been completed, including patient names and treatment costs.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name,
	t.treatment_type,
	b.amount
from 
	billing as b
join 
	patients as p
on 
	p.patient_id = b.patient_id
join 
	treatments as t
on 
	b.treatment_id = t.treatment_id
where 
	payment_status ='Paid';
```

### 9. Write a query to find all appointments that were scheduled in the last 30 days.
```sql
select 
	* 
from 
	appointments
WHERE 
	appointment_date >= (SELECT MAX(appointment_date) FROM appointments) - INTERVAL '30 days';
```

### 10. Write a query to list all patients along with their insurance providers.
```sql
select 
	insurance_provider,count(insurance_provider)
from 
	patients
group by 1;
```

### 11. Write a query to count the total number of treatments each patient has received.
```sql
select 
	p.patient_id,
	concat(p.first_name,' ',p.last_name) as full_name,
	count(treatment_id) as number_of_treatment
from 
	appointments as a
join 
	treatments as t
on 
	a.appointment_id = t.appointment_id
join 
	patients as p
on 
	p.patient_id = a.patient_id
group by 1;
```

### 12. Write a query to find patients who do not have any billing records.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name
from 
	patients as p
left join 
	billing as b
on 
	b.patient_id = p.patient_id
where 
	b.patient_id is null;
```

### 13. Write a query to show the number of appointments scheduled each month for the current year.
```sql
select
	extract(month from appointment_date) as month,
	count(appointment_id) as number_of_appointments
from 
	appointments
group by 1
order by 1 asc;
```

### 14. Write a query to find the top 5 doctors with the most years of experience.
```sql
select 
	concat(first_name,' ',last_name) as full_name,
	years_experience
from 
	doctors
order by 2 desc
limit 5;
```

### 15. Write a query to find the top 3 patients with the highest total billing amounts.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name,
	sum(b.amount)
from 
	patients as p
join 
	billing as b
on 
	b.patient_id = p.patient_id
group by 1
order by 2 desc
limit 3;
```

### 16. Write a query to find all follow-up appointments that occurred after an initial consultation for each patient.
```sql
SELECT
	fa.patient_id,
	p.first_name AS patient_first_name,
	p.last_name AS patient_last_name,
	ca.appointment_date AS consultation_date,
	fa.appointment_date AS follow_up_date,
	fa.reason_for_visit AS follow_up_reason
FROM 
	appointments fa 
JOIN 
	appointments ca 
ON 
	fa.patient_id = ca.patient_id 
JOIN 
	patients p 
ON 
	fa.patient_id = p.patient_id
WHERE 
	ca.reason_for_visit = 'Consultation'
AND 
	fa.reason_for_visit = 'Follow-up'
AND 
	fa.appointment_date > ca.appointment_date
ORDER BY 
	fa.patient_id, fa.appointment_date;
```

### 17. Write a query to count the number of cancelled appointments for each month.
```sql
select
	extract(month from appointment_date) as month,
	count(appointment_id) as cancellation_count
from 
	appointments
where 
	status = 'Cancelled'
group by 1
order by 1;
```

### 18. Write a query to list all patients who have had at least one "No-show" appointment.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name
from 
	appointments as a
join 
	patients as p
on 
	p.patient_id = a.patient_id
where 
	status = 'No-show';
```

### 19. Write a query to calculate the average age of patients based on their date of birth.
```sql
select
	round(avg(EXTRACT(YEAR FROM AGE(CURRENT_DATE, date_of_birth))),0) AS average_age
from 
	patients;
```

### 20. Write a query to list all emergency treatments along with their costs and the corresponding patient names.
```sql
select 
	concat(p.first_name,' ',p.last_name) as full_name,
	t.treatment_type,
	b.amount,
	a.reason_for_visit
from 
	appointments as a
join 
	treatments as t
on 
	a.appointment_id = t.appointment_id
join 
	patients as p
on 
	a.patient_id = p.patient_id
join 
	billing as b 
on 
	b.patient_id = p.patient_id
where 
	reason_for_visit = 'Emergency';
```

### 21.Find the top 5 patients who have spent the most on "Therapy" treatments and have had at least 3 appointments in total. For these patients, list their full name, total amount spent on "Therapy", and the count of their "Therapy" appointments.
```sql
select 
	a.reason_for_visit,
	concat(p.first_name,' ',p.last_name) as full_name,
	count(a.appointment_id) as total_appointment,
	sum(b.amount) as total_amount_spent
from 
	appointments as a
join 
	billing as b
on 
	a.patient_id = b.patient_id
join 
	patients as p
on 
	p.patient_id = b.patient_id
where 
	reason_for_visit = 'Therapy'
group by 1,2
having 
	count(a.appointment_id) >= 3
order by 3 desc
limit 5;
```

### 22.Calculate the total revenue (sum of amount) for each hospital_branch from "Paid" bills, and also show the percentage of "Paid" bills out of the total bills for that branch. Order the results by total paid revenue in descending order.
```sql
with cte as (
select 
	d.hospital_branch hospi_branch,
	sum(b.amount) as total_bill
from 
	doctors as d
join 
	appointments as a
on 
	d.doctor_id = a.doctor_id
join 
	billing as b 
on 
	b.patient_id = a.patient_id
group by 1
),
cte1 as(
select 
	d.hospital_branch as hospi_branch,
	sum(amount) as total_bill_paid
from 
	doctors as d
join 
	appointments as a
on 
	d.doctor_id = a.doctor_id
join 
	billing as b 
on 
	b.patient_id = a.patient_id
where 
	payment_status = 'Paid'
group by 1

)
select 
	cte1.hospi_branch,
	cte1.total_bill_paid,
	cte.total_bill,
	concat(round((cte1.total_bill_paid/cte.total_bill)*100,2),'%') as paid_percentage
from 
	cte
join 
	cte1
on 
	cte1.hospi_branch = cte.hospi_branch;
```

### 23.Create a comprehensive profile for each patient, including:
--Patient's full name and email.
--Total number of appointments.
--Number of "Completed" appointments.
--Total amount billed (sum of amount).
--Average cost per treatment.
--The specialization of the doctor they most frequently visited.
--The treatment_type they received most often.
```sql
WITH PatientBaseStats AS (
    SELECT
        p.patient_id,
        CONCAT(p.first_name, ' ', p.last_name) AS full_name,
        p.email,
        COUNT(DISTINCT a.appointment_id) AS total_appointments, 
        SUM(CASE WHEN a.status = 'Completed' THEN 1 ELSE 0 END) AS completed_appointments,
        SUM(b.amount) AS total_amount_billed,
        CASE
            WHEN COUNT(DISTINCT t.treatment_id) > 0 THEN round(SUM(t.cost) / COUNT(DISTINCT t.treatment_id),2)
            ELSE 0.0
        END AS average_cost_per_treatment
    FROM
        patients AS p
    JOIN
        appointments AS a ON p.patient_id = a.patient_id
    LEFT JOIN 
        billing AS b ON p.patient_id = b.patient_id
    LEFT JOIN
        treatments AS t ON b.treatment_id = t.treatment_id
    GROUP BY
        p.patient_id, p.first_name, p.last_name, p.email
),
PatientSpecialtyVisits AS (
    SELECT
        p.patient_id,
        d.specialization,
        COUNT(a.appointment_id) AS specialization_visit_count
    FROM
        patients AS p
    JOIN
        appointments AS a ON p.patient_id = a.patient_id
    JOIN
        doctors AS d ON a.doctor_id = d.doctor_id
    GROUP BY
        p.patient_id, d.specialization
),
RankedSpecialtyVisits AS (
    SELECT
        patient_id,
        specialization,
        specialization_visit_count,
        ROW_NUMBER() OVER(PARTITION BY patient_id ORDER BY specialization_visit_count DESC, specialization ASC) AS rn
    FROM
        PatientSpecialtyVisits
),
MostFrequentSpecialization AS (
    SELECT
        patient_id,
        specialization AS most_frequent_specialization
    FROM
        RankedSpecialtyVisits
    WHERE
        rn = 1
),
PatientTreatmentTypeCounts AS (
    SELECT
        p.patient_id,
        t.treatment_type,
        COUNT(t.treatment_id) AS treatment_type_count
    FROM
        patients AS p
    JOIN
        appointments AS a ON p.patient_id = a.patient_id
    JOIN
        treatments AS t ON a.appointment_id = t.appointment_id
    GROUP BY
        p.patient_id, t.treatment_type
),
RankedTreatmentTypes AS (
    SELECT
        patient_id,
        treatment_type,
        treatment_type_count,
        ROW_NUMBER() OVER(PARTITION BY patient_id ORDER BY treatment_type_count DESC, treatment_type ASC) AS rn
    FROM
        PatientTreatmentTypeCounts
),
MostFrequentTreatmentType AS (
    SELECT
        patient_id,
        treatment_type AS most_frequent_treatment_type
    FROM
        RankedTreatmentTypes
    WHERE
        rn = 1
)
SELECT
    pbs.full_name,
    pbs.email,
    pbs.total_appointments,
    pbs.completed_appointments,
    pbs.total_amount_billed,
    pbs.average_cost_per_treatment,
    COALESCE(mfs.most_frequent_specialization, 'N/A') AS most_frequent_doctor_specialization,
    COALESCE(mft.most_frequent_treatment_type, 'N/A') AS most_frequent_treatment_type
FROM
    PatientBaseStats AS pbs
LEFT JOIN
    MostFrequentSpecialization AS mfs ON pbs.patient_id = mfs.patient_id
LEFT JOIN
    MostFrequentTreatmentType AS mft ON pbs.patient_id = mft.patient_id
ORDER BY
    pbs.full_name;
```

### 24.Identify doctors whose "No-show" appointment rate for "Consultation" visits is higher than the average "No-show" rate for "Consultation" visits across all doctors. List the doctors full name, their "No-show" consultation rate, and the total number of "Consultation" appointments they had.
```sql
WITH DoctorConsultationStats AS (
    SELECT
        d.doctor_id,
        CONCAT(d.first_name, ' ', d.last_name) AS doctor_full_name,
        COUNT(a.appointment_id) AS total_consultations,
        SUM(CASE WHEN a.status = 'No-show' THEN 1 ELSE 0 END) AS no_show_consultations
    FROM
        doctors AS d
    JOIN
        appointments AS a ON d.doctor_id = a.doctor_id
    WHERE
        a.reason_for_visit = 'Consultation'
    GROUP BY
        d.doctor_id, doctor_full_name
),
DoctorNoShowRates AS (
    SELECT
        doctor_id,
        doctor_full_name,
        total_consultations,
        no_show_consultations,
        CASE
            WHEN total_consultations > 0 THEN CAST(no_show_consultations AS REAL) / total_consultations
            ELSE 0.0
        END AS doctor_no_show_rate
    FROM
        DoctorConsultationStats
),
OverallAverageNoShowRate AS (
    SELECT
        CAST(SUM(no_show_consultations) AS REAL) / SUM(total_consultations) AS overall_avg_rate
    FROM
        DoctorConsultationStats
    WHERE
        total_consultations > 0 
)
SELECT
    dnr.doctor_full_name,
    dnr.doctor_no_show_rate,
    dnr.total_consultations
FROM
    DoctorNoShowRates AS dnr,
    OverallAverageNoShowRate AS oanr
WHERE
    dnr.doctor_no_show_rate > oanr.overall_avg_rate
ORDER BY
    dnr.doctor_no_show_rate DESC;
```

### 25.For each patient, find the longest period (in days) between any two consecutive "Completed" appointments. If a patient has only one or zero completed appointments, they should not be included. List the patients full name and the maximum gap in days.
```sql
WITH PatientCompletedAppointments AS (
    SELECT
        p.patient_id,
        CONCAT(p.first_name, ' ', p.last_name) AS patient_full_name,
        a.appointment_date,
        LAG(a.appointment_date, 1, a.appointment_date) OVER (
            PARTITION BY p.patient_id
            ORDER BY a.appointment_date
        ) AS previous_appointment_date
    FROM
        patients AS p
    JOIN
        appointments AS a ON p.patient_id = a.patient_id
    WHERE
        a.status = 'Completed'
),
AppointmentGaps AS (
    SELECT
        patient_id,
        patient_full_name,
        appointment_date,
        previous_appointment_date,
        (appointment_date - previous_appointment_date) AS gap_in_days
    FROM
        PatientCompletedAppointments
    WHERE
        appointment_date != previous_appointment_date
)
SELECT
    patient_full_name,
    MAX(gap_in_days) AS longest_gap_in_days
FROM
    AppointmentGaps
GROUP BY
    patient_full_name
HAVING
    COUNT(appointment_date) > 0
ORDER BY
    longest_gap_in_days DESC;
```
