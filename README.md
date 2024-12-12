# Data Cleaning and Dashboard Creation for Atliq Employee Data

## Problem Statements
1. **Working Preference**: Analyze the preference between Work from Home (WFH) and Working from Office.
2. **Employee Wellness**: Calculate the percentage of sick leave (SL) to monitor employee wellness.

---

## Steps to Clean Data in Power BI

### Data Preparation
1. **Load Data**:
    - Import the dataset into Power BI.
2. **Duplicate Table**:
    - Duplicate the table as a template for transformations.
3. **Remove Attendance Key**:
    - Remove the `Attendance Key` column from the dataset.
4. **Focus on April 2022**:
    - Keep only the sheet for April 2022 and remove all others.
5. **Expand Sheet**:
    - Expand the data from the April 2022 sheet.
6. **Set Headers**:
    - Use the first row as headers and remove the top row.
7. **Rename Columns**:
    - Rename columns to `Employee Code` and `Name`.
8. **Unpivot Columns**:
    - Unpivot all columns except `Employee Code` and `Name`.
9. **Data Type Adjustments**:
    - Change data types as required and remove any errors.

### Parameterization
10. **Manage Parameter**:
    - Create a parameter named `Worksheet` with the current value set to `April 2022`.
11. **Filter Rows Using Parameter**:
    - Apply the parameter filter to rows.
12. **Create Custom Function**:
    - Create a new function named `GetData`.
13. **Remove Attendance Table**:
    - For all sheets, remove the `Attendance Table`.
14. **Invoke Custom Function**:
    - Go to `Add Column` and invoke the custom function `GetData`.
15. **Adjust Template Table**:
    - Modify the template table to ensure consistency in types.
16. **Keep Required Columns**:
    - Retain only the following columns:
      - `Sheet Name`
      - `Date`
      - `Value`
      - `Employee ID`
      - `Name`

---

## Creating New Measures and Columns

### Measures
1. **Total Working Days**:
   ```DAX
   Total Working Days = 
   VAR totaldays = COUNT('Final Data'[Value])
   VAR nonworkdays = CALCULATE(COUNT('Final Data'[Value]), 'Final Data'[Value] IN {"Wo", "HO"})
   RETURN totaldays - nonworkdays
   ```

2. **Present Days**:
   ```DAX
   Present Days = 
   VAR Presentdays = CALCULATE(COUNT('Final Data'[Value]), 'Final Data'[Value] = "P")
   RETURN Presentdays + [WFH Count]
   ```

3. **WFH Count Measure**:
   ```DAX
   WFH Count = SUM('Final Data'[WFH Count])
   ```

4. **Presence Percentage**:
   ```DAX
   Presence % = DIVIDE([Present Days], [Total Working Days], 0)
   ```

5. **WFH Percentage**:
   ```DAX
   WFH % = DIVIDE([WFH Count], [Present Days], 0)
   ```

6. **SL Count Measure**:
   ```DAX
   SL Count = SUM('Final Data'[SL Count])
   ```

### Columns
1. **WFH Count Column**:
   ```DAX
   WFH Count = SWITCH(TRUE(),
       'Final Data'[Value] = "WFH", 1,
       'Final Data'[Value] = "HWFH", 0.5,
       0)
   ```

2. **Month**:
   ```DAX
   Month = STARTOFMONTH('Final Data'[Date])
   ```

3. **SL Count Column**:
   ```DAX
   SL Count = SWITCH(TRUE(),
       'Final Data'[Value] = "SL", 1,
       'Final Data'[Value] = "HSL", 0.5,
       0)
   ```

4. **Day of Week**:
   ```DAX
   Day of Week = FORMAT('Final Data'[Date], "ddd")
   ```

5. **Week Number**:
   ```DAX
   WeekNo = "W " & WEEKNUM('Final Data'[Date])
   ```

---

## Dashboard Visualizations

1. **Slicers**:
    - Add slicers for `Date`.

2. **Cards**:
    - Insert cards for:
        - `Presence %`
        - `WFH %`
        - `SL %`

3. **Table**:
    - Create a table with the following columns:
        - `Name`
        - `Presence %`
        - `WFH %`
        - `SL %`

4. **Matrix Table**:
    - Add metrics table:
        - **Rows**: `Name`
        - **Columns**: `Date`
        - **Values**: `Value`

5. **Area Charts**:
    - **Presence %**:
        - X-Axis: `Date`
        - Y-Axis: `Presence %`
    - **WFH %**:
        - X-Axis: `Date`
        - Y-Axis: `WFH %`
    - **SL %**:
        - X-Axis: `Date`
        - Y-Axis: `SL %`
## Conclusion

By following the outlined steps, we successfully cleaned and transformed the data for meaningful analysis. The dashboard provides key insights into employee work preferences and wellness, enabling Atliq to make data-driven decisions. 

### Key Insights:
1. The **Presence %**, **WFH %**, and **SL %** metrics offer a comprehensive view of employee attendance and preferences.
2. The integration of slicers and dynamic visualizations allows for deeper analysis of trends across dates and employees.
3. This dashboard can be further enhanced with predictive analytics and additional KPIs to support HR strategies and improve employee engagement.

The approach ensures scalability for future data additions and supports Atliq in fostering a healthy and productive work environment.
