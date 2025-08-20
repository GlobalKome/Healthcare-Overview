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
- [Findings](#findings)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)

# Executive Summary
Dr. Sarah Johnson wants her health centre to remain financially sustainable while maintaining quality patient care. This dashboard was designed to provide a clear, real-time view of your health centre’s financial health and provider performance, bringing together data from eight critical sources: visits, procedures, patients, insurance, cities, providers, diagnoses, and departments. The solution enhances data-driven decision-making by offering clear insights through an overview dashboard.

# Objective
- Financial Stability – Ensure the health centre is not overspending on operations and billing losses.
- Visibility on Provider Performance – Compare workload, patient satisfaction, and revenue contribution of different providers.
- Informed Decision Making – Quickly assess financial reports without waiting weeks for the finance team summaries.
- Balanced Care vs. Costs – Maintain patient care quality while optimising financial outcomes.

# User Story
Dr. Sarah Johnson is the Senior Physician & Department Lead at a mid-Sized Community Health Centre, with 15 years of experience in clinical care and 5 years in administrative oversight. She wants her health centre to remain financially sustainable while maintaining quality patient care.

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
- Billing Amount by Patient Location (City/State) (Azure Map):  To identify seasonality and long-term trends in lending activities
- Billing Amount by Procedure (Clustered Column Chart): To identify regions with significant lending activity and assess regional disparities
- Billing Amount by Diagnosis and Service Type (100% Stacked Bar Chart): To allow the client to understand the distribution of loans across various term lengths.
- Bililng Amount by Department (Clustered Bar Chart): How lending metrics are distributed among borrowers with different employment lengths, helping us assess the impact of employment history on loan applications.

## Tools
| Tools | Purpose |
| --- | --- |
| Power Bi | Explore, Clean, Analyse & Visualise the data with interactive dashboards |
| SQL | Testing and Analysing the data |
| Github | Hosting the project documentation |

## Data Exploration notes
I observed that the dataset can be categorised into three sections 
   Applicant Details: id, emp_length, emp_title, annual_income, application_type, home_ownership
   Loan Details: loan_amount, term, int_rate, installment, loan_status, grade, sub_grade, purpose
   Payment and Credit History: total_payment, dti (debt-to-income), total_acc, issue_date, last_payment_date, next_payment_date, last_credit_pull_date

Excel was used to check through for null values, and emp_title field had null values.
While loading the dataset into SQL, four fields were in the wrong data type.

What steps were taken to clean and shape the data?
- emp_title field was found to be a negligible field hence nothing was done to it
- The fields with wrong data type were fixed in SQL

# Visualization
## Results

![This-shows-the-Summary-Dashboard](Assets/images/LPPMS-Summary.png)

![This-shows-the-Overview-Dashboard](Assets/images/LPPMS-Overview.png)

![This-shows-the-Details-Dashboard](Assets/images/LPPMS-Details.png)

## Measures
### Top KPIs required
### Total Loan Applications
```sql
Total Loan Applications = COUNT(bank_loan_data[id])
```
### Total Funded Amount
```sql
Total Funded Amount = SUM(bank_loan_data[loan_amount])
```
### Total Received Amount 
 ```sql
 Total Amount Received = SUM(bank_loan_data[total_payment])
 ``` 
### Average Interest Rate
```sql
Avg Interest Rate = AVERAGE(bank_loan_data[int_rate])
```
### Average DTI
```sql
Avg Dti = AVERAGE(bank_loan_data[dti])
```
### Good Loan %
```sql
Good Loan % = (CALCULATE([Total Loan Applications], bank_loan_data[Good vs Bad Loan] = "Good Loan")) / [Total Loan Applications]
```
### Bad Loan %
```sql
Bad Loan % = (CALCULATE([Total Loan Applications], bank_loan_data[Good vs Bad Loan] = "Bad Loan")) / [Total Loan Applications]
```
# Testing
SQL was used primarily for testing the data

# Findings
- The number of loan applicants categorized as current and fully paid is much higher than those categorized as charged off.
- There is a clear upward trend in loan applications, funded amounts, and amounts received.
- Borrowers with 10+ years of work experience take larger loans.
- The top five loan purposes are debt consolidation, credit card, other, home improvement, and major purchase.
  
# Recommendations
The dashboards have been successfully developed to provide a centralized and interactive view of the bank’s retail loan performance. While the dataset holds more insights than covered here, the following recommendations are based on notable patterns that align with the client’s need for better institutional visibility and decision-making.

- Maintain existing credit approval criteria in these high-performing segments and use the Overview Dashboard to track them quarterly. Consider targeted retention offers for reliable borrowers to encourage repeat business.
- Align staffing, loan processing capacity, and marketing resources with this growth trend. Consider scaling automated credit checks and loan processing workflows to meet rising demand without increasing operational bottlenecks.
- Treat this group as a prime borrower segment. Consider targeted premium loan products or preferential interest rates for long-tenure professionals to increase market share in this low-risk category.
- Use these categories as anchors for specialized lending packages with tailored repayment options. Marketing campaigns can focus on these high-demand purposes to maximize uptake.

# Conclusion
The Loan Portfolio Performance Monitoring System now provides Phoenix Bank with the tools to translate raw lending data into actionable insights. These findings confirm that the bank is performing strongly in repayment rates, experiencing portfolio growth, and has clear opportunities to refine loan offerings for targeted borrower groups.

By continuously using these dashboards, management can:

- Detect risk patterns early
- Focus on profitable borrower segments
- Adjust product strategies based on demand trends
- Strengthen the institution’s data-driven decision-making culture

While these insights are valuable, they represent only part of the broader analytical capabilities now available through the dashboard suite. With ongoing monitoring and deeper exploration, Phoenix Bank can further enhance its lending strategy and portfolio performance.
