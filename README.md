# Data Cleaning With SQL
### Project-Overview
This projects aims to clean,transform and preprocess a housing dataset using SQL to prepare it for analysis and visualization
### Data Sources
Dataset: The primary dataset used for this analysis is the "Nashville Housing Data.xlsx" file.
### Tools
- SQL Server Management Studio(SSMS)

### Tasks:
- Data Inspection:
  Identify and document data quality issues such as Null values, duplicates and data entry errors.
- Data Cleaning:
  Use SQL to remove duplicates, fill in missing values and corrext data entry errors.
- Data Transformation:
  Create new columns, update relevant columns and delete unwanted columns to facilitate analysis

  ### SQL Skilss Used:
  - SELECT statements with filters and sorting
  - UPDATE and DELETE statements for data cleaning
  - SELF JOINS and CTEs for data transformation












### Nashville Housing Data Cleaning in SQL Queries
```sql

-------------------------------------------------------------------------------------------------
--1. STANDARDIZE DATE FORMAT:

Select * from nashvillehousing


alter table nashvilleHousing
add Salesdate Date

Update nashvilleHousing
set Salesdate=convert(Date, Saledate)

----------------------------------------------------------------------------------------------------
--2. POPULATE PROPERTY ADDRESS DATA: 

select a.ParcelID,a.PropertyAddress,b.ParcelID,
b.PropertyAddress ,
isnull(a.PropertyAddress,b.PropertyAddress)
from nashvilleHousing a
join nashvilleHousing b
on a.parcelid=b.parcelid and a.[UniqueID ]<>b.[UniqueID ]
where a.PropertyAddress is null


update a
set a.PropertyAddress=isnull(a.PropertyAddress,b.PropertyAddress)
from nashvilleHousing a
join nashvilleHousing b
on a.parcelid=b.parcelid and a.[UniqueID ]<>b.[UniqueID ]
where a.PropertyAddress is null

----------------------------------------------------------------------------------------------------

--3. BREAKING OUT ADDRESS INTO INDIVIDUAL COLUMNS(Address, City, State):

Alter table nashvilleHousing
add Asset_Address varchar(255)

update nashvilleHousing
set Asset_Address=parsename(REPLACE(propertyaddress,',','.'),2)

-- For City:

Alter table nashvilleHousing
add City varchar(255)

update nashvilleHousing
set City=parsename(REPLACE(propertyaddress,',','.'),1)

-- For Address:

Alter Table nashvilleHousing
Add PropertyOwnerAddress  nvarchar(255)


Update nashvilleHousing
set PropertyOwnerAddress=parsename(replace(owneraddress,',','.'),3)

-- For Owner's City:

Alter Table nashvilleHousing
Add Owners_City  nvarchar(255)

Update nashvilleHousing
set Owners_City=parsename(replace(owneraddress,',','.'),2)

-- For State:

Alter Table nashvilleHousing
Add State  nvarchar(255)

Update nashvilleHousing
set State=parsename(replace(owneraddress,',','.'),1)

--------------------------------------------------------------------------------------------------------------
--4. CHANGE Y AND N TO YES AND NO USING CASE STATEMENT:

Alter Table nashvilleHousing
Add SoldVacant  nvarchar(255)

Update nashvilleHousing
set SoldVacant= case when Soldasvacant =Y Then 'Yes'
		  when Soldasvacant =N Then 'NO'
		  Else SoldAsVacant

---------------------------------------------------------------------------------------------------------------
--5. REMOVED DUPLICATES:

with cte as
(
Select *, ROW_NUMBER() over (partition by parcelid,
propertyaddress,saleprice,saledate,legalreference
order by parcelid,propertyaddress,saleprice,
saledate,legalreference )Row_Num
from nashvilleHousing
)
select * from cte
where Row_Num >1

----------------------------------------------------------------------------------------------------------------
--6. DELETE UNUSED COLUMNS:


Alter Table nashvilleHousing
Drop column propertyaddress,TaxDistrict,Saledate,owneraddress,Soldasvacant

-----------------------------------------------------------------------------------------------------------------
```




