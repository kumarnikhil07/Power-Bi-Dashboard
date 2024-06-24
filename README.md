# E-Store Performance Monitoring Dashboard(SQL and Power Bi)

### Dashboard Link : https://app.powerbi.com/groups/me/reports/23f394ae-f951-42b7-b50a-d6f8f4202e95/ReportSectionfe965657616459089be0?experience=power-bi

## Problem Statement

This project aims to closely monitor the performance of a recently inaugurated e-store, leveraging data-driven insights to make informed decisions that enhance customer acquisition, engagement, and ultimately drive sales for business growth.

##Insights and Analysis
This project focuses on the following key areas:

Evaluate Products and Brands Performance: Analyze the performance of various products and brands available on the e-store.
Analyze Marketing Campaign Parameters: Evaluate the effectiveness of different marketing campaigns and assess e-store performance across different regions.

##Business Benefits
The insights derived from this analysis offer several business benefits:

Enhance Operational Efficiency:
Analyzing performance metrics helps in managing store inventories, products, and orders more effectively.
Decision Making:
In-depth analysis provides valuable insights for making informed decisions, such as identifying ideal locations for expansion.
Insights can guide marketing strategy adjustments, such as prioritizing promotions for top-selling products to attract more customers.

### Steps followed 

- Step 1 : Load data into SQL, and created some features or columns for better analysis.
- Code for each feature:
  
-- 1. Update UserType based on various conditions
UPDATE demo_table3
SET UserType = 
    CASE
        WHEN Financial_Status != 'paid' OR
             CHARINDEX('test', Email) > 0 OR
             Paid_at IS NULL OR
             Total = 0 THEN 'Test User'
        ELSE 'Regular User'
    END;

-- 2. Update OrderType based on Cart Value and Cart Quantity percentiles
UPDATE demo_table3
SET OrderType = 
    CASE
        WHEN Total > (SELECT PERCENTILE_CONT(0.98) WITHIN GROUP (ORDER BY Total) FROM demo_table3) THEN 'Bulk Order'
        WHEN quantity_per_order > (SELECT PERCENTILE_CONT(0.98) WITHIN GROUP (ORDER BY quantity_per_order) FROM demo_table3) THEN 'Bulk Order'
        ELSE 'Regular Order'
    END;

-- 3. Update Retailer based on OrderNumber and OrderType
WITH OrderCounts AS (
    SELECT email, paid_at, COUNT(DISTINCT id1) AS OrderNumber
    FROM demo_table3
    GROUP BY email, paid_at
)
UPDATE demo_table3
SET Retailer = 
    CASE 
        WHEN oc.OrderNumber > 2 THEN 'Retail Order'
        WHEN OrderType = 'Bulk Order' THEN 'Retail Order'
        ELSE 'Non Retail Order'
    END
FROM demo_table3
JOIN OrderCounts oc ON demo_table3.email = oc.email AND demo_table3.paid_at = oc.paid_at;

-- 4. Update Discount_group based on Discount_Code
UPDATE demo_table3
SET Discount_group = 
    CASE 
        WHEN Discount_Code LIKE '%Paytm%' THEN 'Paytm discount'
        WHEN Discount_Code LIKE '%mpin%' THEN 'Magic Pin discount'
        WHEN Discount_Code IN ('ITCstoreCash100', 'ITCStoreEK', 'SummerstoreCK10', 'itcstorecash100', 'itcstoreek', 'summerstoreck10', 'itcstoreck', 'itcstorebuyblast', 'itcstoreflash', 'essential', 'jan20') THEN 'Cashkaro discount'
        WHEN Discount_Code IN ('Phpe', 'itcstorephpe', 'itcpp4', 'itcphpe25', 'itcphpe400') THEN 'Phonepay discount'
        WHEN Discount_Code LIKE '%gpay%' THEN 'Gpay discount'
        WHEN Discount_Code IN ('itcstorecs', 'itcstorecsapr') THEN 'Convosight'
        ELSE NULL
    END;

-- 5. Update cart_buster_item based on Total value
UPDATE demo_table3
SET cart_buster_item =  
    CASE  
        WHEN Total > 1500 THEN 'Cart_Buster_2'
        WHEN Total >= 1000 AND Total <= 1499 THEN 'Cart_Buster_1'
        ELSE 'No'
    END;

-- 6. Update InvalidOrder based on UserType and Discount_Amount
UPDATE demo_table3
SET InvalidOrder =  
    CASE 
        WHEN UserType = 'Test User' THEN 'Yes'
        WHEN Discount_Amount > 0.9 * Total THEN 'Yes'
        ELSE 'No'
    END;

-- 7. Update Repeater based on the count of distinct orders per email
UPDATE demo_table3 
SET Repeater = 
    CASE 
        WHEN (SELECT COUNT(DISTINCT(order_id)) FROM demo_table3 GROUP BY email) > 1 THEN 'Yes'
        ELSE 'No'
    END;





  
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in none of the columns errors & empty values were present.
- Step 5 : Calculated some measures like avg order value, avg quantity per order etc for better analysis. Exa-
  Measure=sum(Master_Table_tagging[Quantity_per_order])/DISTINCTCOUNT(Master_Table_tagging[id]) 
- Similarly created  other measures.
- Step 6 : In the report view, under the view tab, theme was selected.
- Step 7 : Since the data contains various features such as different Products and Brands, selected various visuals present in power bi, like waterfall, column, bar chart etc.
- Step 8 : Visual filters (Slicers) were added for four fields named "Year", "Month", "State" & "City".
- Step 9 : Ten card visuals were added to the canvas, representing average avg order value, total discount, total number of Orders, avg quantity per order etc.
- Step 10 : Tool tips was used to show number of Customer per state and city together. 
- Step 11 : The Dashboard contains four tabs: 

  ## Product Overview tab:
  - This tab shows product insights, such as top product by quantity sold, selling of product with time etc.
  - Two Slicers have been used- year & month.
  - Screenshot: 
![image](https://github.com/nikhil9325/Power-Bi-Dashboard/assets/131294221/17b3f956-74ea-4e2d-8c3a-c3c526ad746f)

  ## Order overview tab:
  - This tab shows Orders insights, such as number of Orders placed by different Discount Group, Number of Bulk Orders, Orders having less than or equal to ten items etc.
  - Four Slicers have been used- Year, Month, State and City.
  -  Screenshot:
![image](https://github.com/nikhil9325/Power-Bi-Dashboard/assets/131294221/10ea8096-3ec3-4668-8496-4556ee45230c)

 
  ## Store engagement:
  - This tab shows Store insights, such as number of first time vs. Repeater Customers, Revenue Pattern etc. 
  - Two Slicers have been used- Year, Month.
  - Screenshot:
![image](https://github.com/nikhil9325/Power-Bi-Dashboard/assets/131294221/39ea3c8a-1d33-426a-bb57-f30129d98c86)

  
 
  ## Marketing Overview:

  - This tab shows marketing strategy used by the Company.
  - Two Slicers have been used- Year, Month.
  - Screenshot :
![image](https://github.com/nikhil9325/Power-Bi-Dashboard/assets/131294221/c156d840-d9fe-4740-ab6a-1c858c928b51)

 ## Suggestions based on Important Insights: 
 1) Marketing :
- Google, Deals102, and Facebook are successful UTM sources, while CPC and Affiliate are effective mediums in Marketing. In Order to achieve more ROI, marketing activities should be intensified in festive season.

2) Business Growth:
- Brands like Sunfeast, Ashirvaad and Savlon have shown high sales and revenue, so partnering with them would be beneficial for business growth. Dynamic pricing will also be helpful in increasing revenue and sales.

3) Expansion of Business:
- Analyzing consumer behavior and marketing parameters in Delhi and Maharashtra offers expansion opportunities, as in these regions the business is 
successful. 

4) Inventory Management:
- The store received the highest number of orders during the festive season. To ensure future orders are fulfilled, order trends and marketing activity can be utilized.
        
     
