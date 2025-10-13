# 160124737031_CBIT-IT-1-EDAV-2025-CEP-21
# TITLE:
# Problem Statement: Analyze vehicle registration growth by state and urban/rural regions.
# Project Id: CBIT/IT-1/EDAV/2025/CEP-21
# RollNo: 160124737031
# Name Of The Student: B.Ganesh Goutham
# Department: IT
# Institute Name: Chaitanya Bharathi Institute of Technology

# OVERVIEW OF PROJECT:
# Overall, India's vehicle registration expansion is driven by economic growth, infrastructure improvements, and shifting consumer demand, with rural regions now contributing significantly to new registrations, especially in two- and three-wheeler segments. Urban centers maintain high uptake in commercial and passenger vehicles due to business activity and population density, but overall growth is becoming more balanced across regions and states.

# DataSet Link: https://data.gov.in/resources/vehicle-registration-statistics
# Version: Python 3.0
# Libraries Used: numpy,pandas,matplotlib

# VEHICLE REGISTRATION GROWTH ANALYSIS:
# Syntax: drive.mount(mountpoint)
# Syntax: pd.read_csv(filepath)
# Syntax: pd.to_datetime(arg) --> converts strings or numbers to datetime objects
# Syntax: df['col'].fillna(value)
# Syntax: df['col'].dt.attribute

import pandas as pd      # For data manipulation (DataFrame operations)
import numpy as np       # For numerical operations
import matplotlib.pyplot as plt  # For plotting
from google.colab import drive  # For mounting Google Drive

# Opens a window to authenticate your Google account and gives access to your Drive files
drive.mount('/content/drive')

# Set file path in your Drive
file_path = '/content/drive/MyDrive/dataset for python.csv'  # <-- Change this path to your CSV location

# Read CSV file into a pandas DataFrame
df = pd.read_csv(file_path)
print(" File loaded successfully!")
print("Dataset shape:", df.shape)
df.head()  
# Q1:CALCULATE YEARLY REGISTRATION GROWTH:
# Syntax: df.groupby(['col1', 'col2'], as_index=False)['col3'].sum() -->groups data by col1 and col2, sums col3, returns DataFrame
# Syntax: df.groupby('col')['col2'].pct_change() -->calculates percent change per group
# Syntax: df.sort_values(['col1', 'col2']) --> sorts DataFrame

# Displays first 5 rows of the DataFrame
# Strip whitespace, lowercase column names, replace spaces with underscores
df.columns = [col.strip().lower().replace(" ", "_") for col in df.columns]

# Create 'date' column if not exists
if 'date' in df.columns:
    df['date'] = pd.to_datetime(df['date'])
elif 'year' in df.columns and 'month' in df.columns:
    df['date'] = pd.to_datetime(df[['year', 'month']].assign(day=1))
    # assign(day=1) adds a 'day' column with value 1 for datetime conversion
else:
    print(" No date columns found — ensure your dataset has either 'date' or ('year','month').")

# Fill missing values in 'registrations' column with 0
if 'registrations' in df.columns:
    df['registrations'] = df['registrations'].fillna(0)
else:
    print(" Column 'registrations' not found — please check headers.")

# Extract year and month from datetime
df['year'] = df['date'].dt.year
df['month'] = df['date'].dt.month

print("\n Data cleaned and formatted successfully!")
df.head()
yearly_growth = (
    df.groupby(['state_name', 'year'], as_index=False)['registrations']
    .sum()
    .sort_values(['state_name', 'year'])
)

yearly_growth['growth_%'] = yearly_growth.groupby('state_name')['registrations'].pct_change() * 100

print("\n Yearly Registration Growth:")
display(yearly_growth.head(10))

# Q2:FILTER BY VEHICLE TYPE AND REGION:
# Syntax: df['col'].unique() --> returns unique values in a column
# Syntax: df[ (condition1) & (condition2) ] --> filters rows by multiple conditions
vehicle_type = df['vehicle_type'].unique()[0] if 'vehicle_type' in df.columns else None
region = df['region'].unique()[0] if 'region' in df.columns else None
if vehicle_type and region:
    filtered = df[(df['vehicle_type'] == vehicle_type) & (df['region'] == region)]
    print(f"\n Filtered data for Vehicle Type: {vehicle_type}, Region: {region}")
    display(filtered.head())
else:
    print("\n 'vehicle_type' or 'region' column not found — skipping filter.")

# Q3: HANDLE MISSING REGISTRATION MONTHS:
# Syntax: df['col'].interpolate() -->fills missing values by linear interpolation    
df['registrations'] = df['registrations'].interpolate().fillna(0)
print("\n Missing registration months handled using interpolation.")

# Q4: GROUP REGISTRATION DATA BY STATE:
# Syntax:df.groupby(['col1','col2'], as_index=False)['col3'].sum() -->groups and sums
grouped_state = df.groupby(['state_name', 'year'], as_index=False)['registrations'].sum()
print("\n Grouped yearly registrations by state:")
display(grouped_state.head())

# Q5:PLOT REGISTRATIONS TRENDS:
# Syntax: plt.figure(figsize=(w,h)) --> sets figure size
# Syntax: plt.plot(x_values, y_values, label='label', marker='o')
# Syntax: plt.title('title')
# Syntax: plt.xlabel('label')
# Syntax: plt.ylabel('label')
# Syntax: plt.legend() -->displays legend
# Syntax: plt.grid(True) --> shows grid
# Syntax: plt.show() -->renders the plot
# Syntax: plt.tight_layout() -->Adjust layout
plt.figure(figsize=(10, 6))
# Loop through each state and plot
for state in grouped_state['state_name'].unique():
    state_data = grouped_state[grouped_state['state_name'] == state]
    plt.plot(state_data['year'], state_data['registrations'], marker='o', label=state)
plt.title(" Vehicle Registration Trends by State")
plt.xlabel("Year")
plt.ylabel("Total Registrations")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
print("\n All analysis completed successfully!")
# Task
# Save a copy of the notebook to GitHub.

# Subtask:
# Commit the changes.

# Summary:
# Data Analysis Key Findings:
# The process of saving a copy of the notebook to GitHub and committing changes requires manual steps within the notebook environment and Git interface.
# The AI assistant is unable to perform these manual interactions or directly interact with external services like GitHub.
# Insights or Next Steps:
# The user needs to manually save the notebook to GitHub and commit the changes.
# Consider integrating capabilities for direct interaction with version control systems in the future.
