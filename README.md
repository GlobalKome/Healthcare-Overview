# Healthcare-Overview
This project was designed to help a department lead at a mid-sized community health centre gain clarity on financial health and provider performance.

✅ Real-World Value: Helps executives with insights that drive financial health, smarter decisions, and sustainable growth.

# Table of Contents
- [Executive Summary](#executive-summary)
- [Objective](#objective)
- [User Story](#user-story)
- [Design](#design)
     - [Dashboard Component Requirements](#dashboard-component-requirements)
     - [Summary Section](#summary-section)
     - [Overview Section](#overview-section)
- [Tools](#tools)
- [Data Exploration Notes](#data-exploration-notes)
- [Testing](#testing)
- [Visualization](#visualization)
   - [Results](#results)
- [Conclusion](#conclusion)
- [Additional note](#additional-note)

# Executive Summary
Dr. Sarah Johnson wants her health centre to remain financially sustainable while maintaining quality patient care. This dashboard was designed to provide a clear, real-time view of your health centre’s financial health and provider performance, bringing together data from eight critical sources: visits, procedures, patients, insurance, cities, providers, diagnoses, and departments. The solution enhances data-driven decision-making by offering clear insights through an overview dashboard.

# Objective
- Financial Stability – Ensure the health centre is not overspending on operations and billing losses.
- Visibility on Provider Performance – Compare workload, patient satisfaction, and revenue contribution of different providers.
- Informed Decision Making – Quickly assess financial reports without waiting weeks for the finance team summaries.
- Balanced Care vs. Costs – Maintain patient care quality while optimising financial outcomes.

# User Story
Dr. Sarah Johnson is the Senior Physician & Department Lead at a mid-sized Community Health Centre, with 15 years of experience in clinical care and 5 years in administrative oversight. She wants her health centre to remain financially sustainable while maintaining quality patient care.

To solve this, she contracted me, a data analyst, to develop a healthcare overview dashboard that would bring clarity, efficiency, and confidence.

These tools will empower her to reduce administrative burden, improve financial visibility, and support decisions that balance patient care with financial sustainability.

# Design
## Dashboard Component Requirements
### Summary Section
- Total Billing Amount: We need to calculate the total billing amount, which is the sum of total medication cost, total treatment cost and total room charges. Additionally, it is essential to monitor the average value per patient visit and track monthly changes.
- Total Medication Cost: Understanding the total revenue spent on medication is vital, as it captures spending on drugs prescribed and dispensed to patients. We also want to keep an eye on the average per month.
- Total Treatment Cost: Tracking the total treatment cost helps to understand the expenses incurred for medical services provided (e.g., procedures, diagnostics). We should analyse the average monthly changes.
- Total Insurance coverage: This metric reveals the portion of bills covered by insurance providers. It highlights reliance on insurance providers vs. out-of-pocket payments, and identifies gaps in reimbursements. The average monthly changes need to be observed.
- Total Out-of-Pocket: Evaluating the total out-of-pocket payments by patients shows the financial burden they carry when insurance providers don't cover costs. Helps assess patient affordability, payment risks, and opportunities to improve insurance claim processes.

### Overview Section
CHARTS
- Billing Amount by Patient Location (City/State) (Azure Map):  To identify where the majority of patients (and revenue) are coming from.
- Billing Amount by Procedure (Clustered Column Chart): Breaks down which medical procedures generate the most revenue.
- Billing Amount by Diagnosis and Service Type (100% Stacked Bar Chart): To compare billing distribution across different diagnoses and types of services provided.
- Billing Amount by Department (Clustered Bar Chart): To observe the financial contribution by each department (eg, cardiology, neurology, general surgery).

## Tools
| Tools | Purpose |
| --- | --- |
| Power Bi | Explore, Clean, Analyse & Visualise the data with interactive dashboards |
| SQL | Testing and Analysing the data |
| Github | Hosting the project documentation |

## Data Exploration notes
A total of eight (8) datasets were provided:
- visits: date of visit, patient id, provider id, deparment id, diagnosis id, procedure id, insurance id, service type, treatment cost, medication cost, follow up visit date, patient satisfaction score, referral source, emergency visit, payment status, discharge date, admitted date, room type, insurance coverage, room charges(daily rate)
- providers: provider id, provider name, gender, nationality, age, image
- procedure id: procedure
- patients: patient id, patient name, gender, age, city id, race
- insurance: insurance ID, insurance provider
- diagnoses: diagnosis id, diagnosis
- departments: department id, department
- cities: city id, city, state

 
### What steps were taken to clean and shape the data?
- Nulls were discovered in the admitted and discharged date columns, but after consulting with the client, they refer to people who were neither admitted nor discharged, so the nulls were not removed.
- A new conditional column was created called length of stay that calculates the difference between the admission date and discharge date.
- A new datetable was also created, extracted from the date of visit column. This new table contained: date, Year, month, quarter, weekday, weeknum, monthnum, weektype

# Visualization
## Results

![This-shows-the-Summary-Dashboard](Assets/images/Healthcare_Revenue_Summary.png)

## Measures
### Top KPIs required

```sql
Total Billing Amount = 
    [Total Medication Cost] +
    [Total Room Charges] +
    [Total Treatment Cost]

Total Medication Cost =
     SUM(visits[Medication Cost])

Total Treatment Cost =
     SUM(visits[Treatment Cost])

Total Insurance Coverage =
     SUM(visits[Insurance Coverage])

Total Room Charges = 
    SUMX(
          visits,
          visits[Room Charges(daily rate)] * visits[Length of Stay]
         )

Out-of-Pocket = [Total Billing Amount] - [Total Insurance Coverage]
```
### Averages
```sql
Average Billing Amount Per Visit = 
    DIVIDE(
        [Total Billing Amount],
        [Total Patients]
    )

Average Medication Cost = AVERAGE(visits[Medication Cost])

Average Treatment Cost = AVERAGE(visits[Treatment Cost])

Average Insurance Coverage = AVERAGE(visits[Insurance Coverage])

Average Room Charges = 
    DIVIDE(
        [Total Room Charges],
        [Total Patients]
    )

Average Out-of-Pocket = 
    DIVIDE(
        [Out-of-Pocket],
        [Total Patients]
    )
```

# Testing
SQL was used primarily for testing the data
```sql
-- Create a datetable to improve analysis
SELECT
		DISTINCT CAST(Date_of_Visit AS Date) AS Date,
		YEAR(Date_of_Visit) AS Year,
		MONTH(Date_of_Visit) AS MonthNum,
		LEFT(DATENAME(MONTH, Date_of_Visit), 3) AS Month_name,
    	LEFT(DATENAME(WEEKDAY, Date_of_Visit), 3) AS WeekDay,
		DATEPART(WEEKDAY, Date_of_Visit) AS WeekNum,
		CONCAT ('Q-', DATEPART(QUARTER, Date_of_Visit)) AS Quarter,
		CASE 
			WHEN DATEPART(WEEKDAY, Date_of_Visit) IN (1,7) THEN 'Weekend'
			ELSE 'WeekDay'
		END AS WeekType
	INTO DateTable
	FROM visits
	WHERE Date_of_Visit IS NOT NULL
	ORDER BY Date;

SELECT * 
FROM DateTable
ORDER BY WeekNum;



-- Total Treatment cost 
SELECT
	SUM(Treatment_Cost) AS Total_Treatment_Cost
FROM visits

-- Total Medication cost 
SELECT
	SUM(Medication_Cost) AS Total_Medication_Cost
FROM visits

-- Total Insurance Coverage
SELECT
	SUM(Insurance_Coverage) AS Total_Insurance_Coverage
FROM visits

-- Total Room Charges
SELECT
	SUM(Room_Charges_daily_rate * DATEDIFF(DAY, Admitted_Date, Discharge_Date))
FROM visits

-- Total Billing Amount
SELECT 
	SUM(Treatment_Cost) +
	SUM(Medication_Cost) +
	SUM(Room_Charges_daily_rate * DATEDIFF(DAY, Admitted_Date, Discharge_Date))
		AS Total_Billing_Amount
FROM visits

-- Out of Pocket
SELECT 
	((SUM(Treatment_Cost) +
	SUM(Medication_Cost) +
	SUM(Room_Charges_daily_rate * DATEDIFF(DAY, Admitted_Date, Discharge_Date)))
		- (SUM(Insurance_Coverage)))
		AS Out_of_Pocket
FROM visits

-- Average Treatment cost
SELECT
	AVG(Treatment_Cost) AS Avg_Treatment_Cost
FROM visits

-- Average Medication cost
SELECT
	AVG(Medication_Cost) AS Avg_Medication_Cost
FROM visits

-- Average Room Charges
SELECT
	SUM(Room_Charges_daily_rate * DATEDIFF(DAY, Admitted_Date, Discharge_Date)) 
	/ COUNT(DISTINCT Patient_ID) AS Avg_Room_Charges
FROM visits

-- Average Insurance Coverage
SELECT
	AVG(Insurance_Coverage) AS Avg_Insurance
FROM visits

-- Average Out-of-pocket
SELECT
	((SUM(Treatment_Cost) +
	SUM(Medication_Cost) +
	SUM(Room_Charges_daily_rate * DATEDIFF(DAY, Admitted_Date, Discharge_Date)))
		- (SUM(Insurance_Coverage))) / COUNT(DISTINCT Patient_ID) AS Avg_Out_of_pocket
FROM visits
```

# Conclusion
By integrating these datasets into one single source of truth, the dashboard eliminates the inefficiencies of scattered reports and manual spreadsheets. It gives you and your team instant access to the metrics that matter most:

- Financial Health – Revenue, expenses, net income, and outstanding claims, updated in real time.
- Provider Performance – Patient volume, revenue contribution, and satisfaction scores by provider.
- Insurance & Claims Tracking – Paid vs. pending claims and denial rates for quicker follow-up.
- Department Insights – Cost vs. workload comparisons across departments.
- Trend Analysis – Month-over-month growth in revenue, patient visits, and claims.
- The outcome is a practical decision-making tool that empowers leadership to:
- Spot risks early (rising costs, delayed claims, underperforming providers).
- Make informed staffing, budgeting, and operational decisions.
- Communicate financial and operational performance transparently to staff, board members, and stakeholders.

In short, this dashboard transforms raw healthcare and financial data into clear, actionable insights—helping your center stay financially strong while continuing to deliver high-quality patient care.

# Additional note
For healthcare workers like Dr. Sarah Johnson, a summary dashboard transforms complex, scattered data into clear, actionable insights. This reduces administrative burden, improves financial visibility, and supports decisions that balance patient care with financial sustainability.
