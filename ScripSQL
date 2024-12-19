-- Crear tabla Sales en la capa Silver --
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Sales` AS
SELECT *
FROM `tpintegradorev.my_project_bronze.SalesA2`
WHERE
  ORDERNUMBER IS NOT NULL
  AND ORDERDATE IS NOT NULL
  AND STATUS IS NOT NULL
  AND PRODUCTLINE IS NOT NULL
  AND PRODUCTCODE IS NOT NULL
  AND CUSTOMERNAME IS NOT NULL
  AND DEALSIZE IS NOT NULL;

-- Creación de la tabla Dim_Customer
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Dim_Customer` AS 
SELECT 
    ROW_NUMBER() OVER (ORDER BY CUSTOMERNAME, CITY) AS CUSTOMERNUMBER,
    CUSTOMERNAME,
    PHONE,
    ADDRESSLINE1,
    ADDRESSLINE2,
    CITY,
    STATE,
    POSTALCODE,
    COUNTRY,
    TERRITORY,
    CONTACTLASTNAME,
    CONTACTFIRSTNAME
FROM (
    SELECT DISTINCT 
        CUSTOMERNAME, 
        PHONE, 
        ADDRESSLINE1, 
        ADDRESSLINE2, 
        CITY, 
        STATE, 
        POSTALCODE, 
        COUNTRY, 
        TERRITORY, 
        CONTACTLASTNAME, 
        CONTACTFIRSTNAME
    FROM `tpintegradorev.my_project_silver.Sales`
)
WHERE CUSTOMERNAME IS NOT NULL
ORDER BY CUSTOMERNUMBER;

-- Creación de la tabla Dim_Status
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Dim_Status` AS 
SELECT  
    ROW_NUMBER() OVER (ORDER BY STATUS) AS STATUSNUMBER,
    STATUS
FROM (
    SELECT DISTINCT STATUS
    FROM `tpintegradorev.my_project_silver.Sales`
)
WHERE STATUS IS NOT NULL
ORDER BY STATUSNUMBER;

-- Creación de la tabla Dim_Product
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Dim_Product` AS 
SELECT 
    PRODUCTCODE,
    PRODUCTLINE,
    MSRP
FROM (
    SELECT DISTINCT 
        PRODUCTCODE, 
        PRODUCTLINE, 
        MSRP
    FROM `tpintegradorev.my_project_silver.Sales`
)
WHERE PRODUCTCODE IS NOT NULL
ORDER BY PRODUCTCODE;

-- Creación de la tabla Dim_DealSize
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Dim_DealSize` AS 
SELECT 
    ROW_NUMBER() OVER (ORDER BY DEALSIZE DESC) AS DEALSIZENUMBER,
    DEALSIZE
FROM (
    SELECT DISTINCT DEALSIZE
    FROM `tpintegradorev.my_project_silver.Sales`
)
WHERE DEALSIZE IS NOT NULL
ORDER BY DEALSIZE DESC;

-- Creación de la tabla Fact_Sales
CREATE OR REPLACE TABLE `tpintegradorev.my_project_silver.Fact_Sales` AS 
SELECT 
    s.ORDERNUMBER,
    s.ORDERDATE,
    s.QUANTITYORDERED,
    s.ORDERLINENUMBER,
    ds.STATUSNUMBER,
    s.PRODUCTCODE,
    dc.CUSTOMERNUMBER,
    dd.DEALSIZENUMBER,
    s.PRICEEACH
FROM `tpintegradorev.my_project_silver.Sales` s
LEFT JOIN `tpintegradorev.my_project_silver.Dim_Status` ds 
    ON s.STATUS = ds.STATUS
LEFT JOIN `tpintegradorev.my_project_silver.Dim_Customer` dc 
    ON s.CUSTOMERNAME = dc.CUSTOMERNAME AND s.CITY = dc.CITY
LEFT JOIN `tpintegradorev.my_project_silver.Dim_DealSize` dd 
    ON s.DEALSIZE = dd.DEALSIZE;
