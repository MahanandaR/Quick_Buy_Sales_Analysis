# **Quick_Buy : Live Sales Dashboard**

<img width="1320" height="741" alt="image" src="https://github.com/user-attachments/assets/6bd8f853-7916-4861-95a1-7b05dd6c50aa" />

## Table of Contents

- [Problem Statement](#problem-statement)
- [Project Objective](#project-objective)
- [Setup](#setup)
- [ER Diagram](#er-diagram)
- [Database Schema](#database-schema)
- [SQL Views](#sql-views)
- [ETL Pipeline](#etl-pipeline)
- [GitHub Actions](#github-actions)
- [Power BI Dashboard](#power-bi-dashboard)
- [Results & Insights](#results--insights)
- [How To](#how-to)
- [Folder Structure](#folder-structure)
- [License](#license)

------------------------------------------------------------------------------
## Problem Statement

Quick Buy is a leading superstore operating across the United States, sells thousands of products across regions.

Currently, the store relies heavily on multiple spreadsheets and manual SQL queries to track business performance.

This manual process makes it :

Hard to keep data structured and consistent.
Time-consuming to prepare reports.
Difficult to update daily transaction data manually.
Challenging for non-technical users to extract and understand key business insights.
To solve these challenges, Quick Buy aims to build an automated system that :

Collects and stores all data in a structured database.
Cleans and updates data automatically using an ETL Pipeline.
Uses SQL to analyze data and extract meaningful business insights.
Displays insights through an interactive Power BI Dashboard.
Helps managers and executives quickly understand business performance and make better decisions.

-----------------------------------------------------------------------------

## Project Objective

Build an automated system for Quick Buy that provides accurate and up-to-date business insights.

To achieve this, the project focuses on the following objectives :

Centralized Data Storage
Store all business data (orders, customers and products) in a single SQL database.
Ensure data is well-structured, accurate and easy to access.
Automated ETL Pipeline
Build an ETL (Extract, Transform, Load) pipeline using Python and SQLAlchemy.
Automatically load new data into the database on a daily schedule.
Reduce manual reporting work and save time for the analytics.
Data Cleaning and Transformation
Automatically clean raw data by fixing missing, duplicate or inconsistent values.
Ensure that data is clean and ready for the analysis.
Secure and Stable Database Connection
Use a secure connection between Python and the database.
Store credentials safely using configuration files or environment variables.
Keep the system stable with low chances of connection failure.
SQL Views for Business Insights
Create SQL views to simplify complex queries and calculations.
Use these views as a consistent data source for the dashboard.
Interactive Power BI Dashboard
Build dashboard to visualize different aspects of the business like orders, customers, etc.
Make dashboard simple, clear and useful for both technical and non-technical users.
Daily Report Refresh & Monitoring
Schedule automatic daily dashboard refreshes to display the most up-to-date data.
Add basic logging to monitor ETL runs and quickly identify failures.
