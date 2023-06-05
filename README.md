###### Dataset source: Higher Education Student Data
###### [Chart 7 - HE students by subject area and sex 2014/15 to 2018/19](https://data.europa.eu/data/datasets/higher-education-student-data?locale=en "Dataset Link")
###### Columns: AcademicYear, SubjectArea, Sex, Number.
# Analyze Gender Education by Subject
![Input data](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture1.png "(Input Data)")
### (Input Data)
##### Start data import on line 14 as the previous lines contain the data source and some metadata.
![Empty records](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture2.png "(Empty records)")
##### We found some records doesn’t contain any data except for the sex and this was preventing us from correctly splitting the data, so we filtered the data on academic year when it is not empty true then output it in Education Dataset file to work on it. _(Filter)_
![Tile method](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture3.png "(Tile method)")
##### Using Tile method we choose equal records and number of tiles 2 to split the data into 2 sets.
![filter](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture4.png "(filter)")
##### Using filter on Tile_Num we will export the data into 2 excel files the first half is in Destination_1 if filter is True and the second half is in Destination_2 if it is false.
![export the data into 2 excel files](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture5.png "(export the data into 2 excel files)")
### (Alteryx Transformations)
#### 1: filter records on specific condition (custom condition: Filter out subjects that contain either studies or combined)
![filter on specific condition](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture6.png "(filter on specific condition)")
#### 2: choose any string column and uppercase the first character only (Using Formula)
![Using Formula](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture7.png "(Using Formula)")
#### 3: split any column into many columns (Using Text To Columns)
![Using Text To Columns](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture8.png "(Using Text To Columns)")
#### We wanted to add ‘20’ before the End Academic Year2, So we used Formula:
![Formula](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture9.png "(Formula)")
#### Using SELECT, we removed the unused columns, renamed the new columns and adjusted the type of students number.
![SELECT](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture10.png "(SELECT)")
#### 4: replace any white spaces with underscore for any string column (Using Formula REGEX_Replace)
![Formula REGEX_Replace](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture11.png "(Formula REGEX_Replace)")
#### Divide the current Destination1 aka. First Half again into two parts. (using Tile, Filter, Select, Output Data)
![Tile, Filter, Select, Output Data](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture12.png "(Tile, Filter, Select, Output Data)")
## NOW Let’s move to SSIS
#### Import Desination_2 using Excel Source
![Import](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture13.png "(Import)")
### (SSIS Transformations)
#### 1: filter records on specific condition (custom condition: Filter out subjects that contain either studies or combined using Conditional Split)
![Conditional Split](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture14.png "(Conditional Split)")
#### 2: choose any string column and uppercase the first character only (Using Derived Column)
![Derived Column](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture15.png "(Derived Column)")
#### 3: split any column into many columns (Using Derived Column)
![Derived Column](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture16.png "(Derived Column)")
#### we deleted the unused columns:
![deleted the unused columns](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture17.png "(deleted the unused columns)")
#### 4: replace any white spaces with underscore for any string column (Using Derived Column)
![Derived Column](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture18.png "(Derived Column)")
#### Divide the current Destination2 aka. Second Half again into two parts. (using Row Sampling Transformation, Excel Destination)
![Row Sampling Transformation, Excel Destination](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture19.png "(Row Sampling Transformation, Excel Destination)")
### Final Result
![Final Result](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture20.png "(Final Result)")
#### Load part 1 from first and second halves in destination 1 in SQL Server:
##### Using MULTIPLEFLATFILE to make the connection to folder that contain them.
![MULTIPLEFLATFILE](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture21.png "(MULTIPLEFLATFILE)")
![AlwaysUseDefaultCodePage](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture22.png "(AlwaysUseDefaultCodePage)")
#### We change the OLE DB Destination advanced editor setting DefaultCodePage to 65001 and make AlwaysUseDefaultCodePage True to solve the column cannot be processed because more than one code page. (From: Stakeoverflow)
![First Half Part1 and Second Half Part1](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture23.png "(First Half Part1 and Second Half Part1)")
#### and here it is 76 rows (40 rows from First Half Part1 & 36 rows from Second Half Part1)
#### Same steps to load the Part 2:
![First Half Part2 and Second Half Part2](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture24.png "(First Half Part2 and Second Half Part2)")
#### and here it is 74 rows (39 rows from First Half Part1 & 35 rows from Second Half Part1)
![Both of them are now in SQL Server](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture25.png "(Both of them are now in SQL Server)")
#### Both of them are now in SQL Server.
## NOW Let’s move to Power BI
#### Load our data from SQL Server
![Load](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture26.png "(Load)")
#### Append the second table to the first table and adjust the type of #students
![Append](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture27.png "(Append)")
## Dashboard
![Dashboard](https://github.com/varino-alfried/DataWarehouse_SSIS_Altreyx/blob/varino-alfried-screenshots/screenshots/Picture28.png "(Power BI Dashboard)")
## Insights
#### Compare students gender per subject.
#### Compare number of students over years.
#### Compare number of students and gender per subject and year.


