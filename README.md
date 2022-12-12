# 🏠 Data Cleaning using SQL : Nashville Housing Data

## Data Cleaning

Data cleaning is the process of fixing or removing incorrect, corrupted, incorrectly formatted, duplicate, or incomplete data within a dataset. It is important to clean the data before analyzing it so that the results obtained and insights uncovered are reliable.


There is no one absolute way to define the steps involved in the process of data cleaning as the process varies from dataset to dataset. However, the following components should always be taken care of.

- Completeness
- Accuracy
- Validity
- Consistency
- Uniqueness

## About the Dataset

This is a public dataset about the Houses present in the Nashville City of USA. And it is freely available on the website of Kaggle.
It contains more than **56000** rows and **31** columns. The attributes included are UniqueID of the house, its ParcelID and Address, Owner's Name and Address, Sale Date, Sale Price and Legal Reference and many more.

***Dataset: [Link](https://www.kaggle.com/datasets/tmthyjames/nashville-housing-data)***


## Cleaning up the data

The dataset is present in the form of an Excel File. I imported it in the SQL Server to continue with the process of Data Cleaning.

#### 1. Standardising the Date Format

Currently, the `SaleDate` has the data type **DATETIME** and contains the timestamp which is irrelevant here.
So, I'll convert it into **DATE** to get rid of the timestamp.

**Before:**

![NH1 Uncleaned](https://user-images.githubusercontent.com/96012488/207054755-bd9c8c91-0142-49e9-ac3d-8aee6c91058d.png)

````sql
UPDATE NashvilleHousing
SET SaleDate = CONVERT(Date,SaleDate);
````

**After:**

![NH 1 Cleaned](https://user-images.githubusercontent.com/96012488/207055312-013b8e3f-0486-446e-8b34-126a0418559f.png)

#### 2. Populating null Property Address values

````sql
SELECT *
FROM NashvilleHousing
WHERE PropertyAddress IS NULL;
````

**Before:**

![NH 2 Uncleaned](https://user-images.githubusercontent.com/96012488/207055826-1474a1c1-47b2-472a-ae8e-a249ddb81710.png)

- Thus, we have a few properties whose addresses are not provided in the dataset.

After a little bit of exploring the dataset, I realized that properties having the same `ParcelID` have the same `PropertyAddress`. So, what we can do is for
the properties with null 'PropertyAddress' but a matching 'ParcelId' with some other property, we can fill the same 'PropertyAddress' as that of the matching Property.

- This can be done using **Self Join.**
 

➡️ Fetching relevant PropertyAddresses to fill the null values


- Here, we join the table `NashvilleHousing` with itself where the ParcelID are same but the UniqueID are different.

````sql
SELECT a.ParcelID,a.PropertyAddress,b.ParcelID,b.PropertyAddress,ISNULL(a.PropertyAddress,b.PropertyAddress) AS required_address
FROM NashvilleHousing a
JOIN NashvilleHousing b
ON a.ParcelID = b.ParcelID AND a.[UniqueID ] <> b.[UniqueID ]
WHERE a.PropertyAddress IS NULL
````

![NH 2 Cleaned (1)](https://user-images.githubusercontent.com/96012488/207056536-8dcd4320-94f0-4ef6-af6a-aa7cd49925a6.png)


➡️ Filling the null values with the relevant data


````sql
UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
FROM NashvilleHousing a
JOIN NashvilleHousing b
ON a.ParcelID = b.ParcelID AND a.[UniqueID ] <> b.[UniqueID ]
WHERE a.PropertyAddress IS NULL
````

- Now, we do not have any null values in the `PropertyAddress` column.

![NH 2 Cleaned (2)](https://user-images.githubusercontent.com/96012488/207057526-aa7e232d-6e9a-4725-bd55-fad84d4814db.png)











