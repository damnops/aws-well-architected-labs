# Level 200: Cost and Usage Analysis

## Authors
- Nathan Besh, Cost Lead, Well-Architected
- Spencer Marley, Commercial Architect


## Feedback
If you wish to provide feedback on this lab, there is an error, or you want to make a suggestion, please email: costoptimization@amazon.com


# Table of Contents
1. [Verify your CUR files are being delivered](#Verify_CUR)
2. [Use AWS Glue to enable access to CUR files via Amazon Athena](#Setup_Athena)
3. [Cost and Usage analysis](#CUR_Analysis)
4. [Tear down](#tear_down)
5. [Rate this Lab](#rate_lab)  
6. [Feedback survey](#survey)


## 1. Verify your CUR files are being delivered <a name="Verify_CUR"></a>
We will verify the CUR files are being delivered, they are in the correct format and the region they are in.

1. Log into the console as an IAM user with the required permissions, go to the **Billing** console, and view the CUR report you created in [AWS Account Setup](../100_1_AWS_Account_Setup/Lab_Guide.md), confirm the **S3 bucket**, and **Report path prefix**:
![Images/AWSBillingAnalysis_0.png](Images/AWSBillingAnalysis_0.png)

2. Go to the **S3** Service console: 
![Images/AWSBillingAnalysis_1.png](Images/AWSBillingAnalysis_1.png)

3. Verify the region where the bucket is located (here it is **US East N.Virginia**), and click on the **bucket name** where the report is delivered(it is blacked out here):
![Images/AWSBillingAnalysis_2.png](Images/AWSBillingAnalysis_2.png)

4. You should see a **aws-programmatic-access-test-object** which was put there to verify AWS can deliver reports, and also the folder which is the report prefix - **cur**. Click on the folder name for the **prefix** (here it is cur):
![Images/AWSBillingAnalysis_3.png](Images/AWSBillingAnalysis_3.png)

5. Click on the **folder name** which is also part of the prefix (here it is WorkshopCUR):
![Images/AWSBillingAnalysis_4.png](Images/AWSBillingAnalysis_4.png)

6. Click on the **prefix** folder, here it is WorkshopCUR, then drill down in the current year and month:
![Images/AWSBillingAnalysis_5.png](Images/AWSBillingAnalysis_5.png)

7. You can see the delivered CUR file, it is in the **parquet** format:
![Images/AWSBillingAnalysis_7.png](Images/AWSBillingAnalysis_7.png)

You have successfully verified that the CUR files are being delivered and in the correct format.


**Sample Files**
You may not have substantial or interesting usage, in this case there are sample files that you can use in the code section. You will need to create the required structure in S3, download these files and then upload them into s3.

**NOTE**: Do not save the links below, open them in a new window and download the files. They should be approximately 1Mb in size each, if you have files that are 65kb - then you have downloaded the web page and not the parquet files.

Create a folder structure, such as -bucket name-/cur/WorkshopCUR/WorkshopCUR/year=2018/month=12 and copy the parquet files below into each months folder. You will need to edit the SQL file with the correct name.

- [Workshop.sql](Code/WorkshopCUR-create-table.sql)
- [October 2018 Usage](Code/Oct2018-WorkshopCUR-00001.snappy.parquet)
- [November 2018 Usage](Code/Nov2018-WorkshopCUR-00001.snappy.parquet)
- [December 2018 Usage](Code/Dec2018-WorkshopCUR-00001.snappy.parquet)


## 2. Use AWS Glue to enable access to CUR files via Amazon Athena <a name="Setup_Athena"></a>
We will use AWS Glue and setup a scheduled Crawler, which will run each day. This crawler will scan the CUR files and create a database and tables for the delivered files. If there are new versions of a CUR, or new months delivered - they will be automatically included.

We will use Athena to access and view our CUR files via SQL. Athena is a serverless solution to be able to execute SQL queries across very large amounts of data. Athena is only charged for data that is scanned, and there are no ongoing costs if data is not being queried, unlike a traditional database solution.



1 -  Go to the **Glue** console: 
![Images/Glue0.png](Images/Glue0.png)

2 - Click on **Get started** if you have not used Glue before

3 - Ensure you are in the region where your CUR files are delivered, click on **Crawlers** and click **Add crawler**:
![Images/Glue1.png](Images/Glue1.png)

4 - Enter a **Crawler name** and click **Next**: 
![Images/Glue2.png](Images/Glue2.png)

5 - Select **Select data stores**, and click **Next**:
![Images/Glue3.png](Images/Glue3.png)

6 - Ensure you select **Specified path in my account**, and click the **Folder icon**:
![Images/Glue4.png](Images/Glue4.png)

7 - Select the bucket with the CUR files, and click **Select**: 
![Images/Glue5.png](Images/Glue5.png)

8 - Enter the following exclude patterns (1 per line), and click **Next**:
```
**.json, **.yml, **.sql, **.csv, **.gz, **.zip
```
![Images/Glue6.png](Images/Glue6.png)

9 - Click **Next**:
![Images/Glue7.png](Images/Glue7.png)

10 - Select **Create an IAM role**, enter a **role name**, and click **Next**:
![Images/Glue8.png](Images/Glue8.png)

11 - Click the **Down arrow**, and select a **Daily** Frequency:
![Images/Glue9.png](Images/Glue9.png)

12 - Enter in a **Start Hour** and **Start Minute**, then click **Next**:
![Images/Glue10.png](Images/Glue10.png)

13 - Click **Add database**:
![Images/Glue11.png](Images/Glue11.png)

14 - Enter a **Database name**, and click **Create**:
![Images/Glue12.png](Images/Glue12.png)

15 - Click **Next**:
![Images/Glue13.png](Images/Glue13.png)

16 - Review the crawler and click **Finish**:
![Images/Glue14.png](Images/Glue14.png)

17 - Select the checkbox next to the crawler, click **Run crawler**:
![Images/Glue15.png](Images/Glue15.png)

18 - You will see the Crawler was successful and created a table:
![Images/Glue16.png](Images/Glue16.png)

19 - Go to the **Athena** Console:
![Images/Glue17.png](Images/Glue17.png)

20 - Select the drop down arrow, and click on the new database:
![Images/Glue18.png](Images/Glue18.png)

21 - A new table called **workshop_c_u_r** will have been created, we will now load the partitions. Click on the **3 dot menu** and select **Load partitions**:
![Images/AWSBillingAnalysis_14.png](Images/AWSBillingAnalysis_14.png)

22 - You will see it execute the command **MSCK REPAIR TABLE**, and in the results it will add partitions to the metastore for each month that has a billing file:
![Images/AWSBillingAnalysis_15.png](Images/AWSBillingAnalysis_15.png)
NOTE: If it did not add partitions, then there is an error and there will be no data. 
Check
- The database name is correct & the same as the SQL file
- The folder names **year** and **month** are in S3 and the case matches
- There are parquet files in each of the month folders

23 -  We will now preview the data.  Click on the **3 dot menu** and select **Preview table**:
![Images/AWSBillingAnalysis_16.png](Images/AWSBillingAnalysis_16.png)

24 - It will execute a **Select * from** query, and in the results you will see the first 10 lines of your CUR file:
![Images/AWSBillingAnalysis_17.png](Images/AWSBillingAnalysis_17.png)

25 - (Optional if you have a linked account) We will create a member account table, this is for large organizations or partners - that want only a single accounts usage to be visible to them.
Copy and paste the following code:
```
CREATE TABLE linked_AccountID
WITH (
      format = 'Parquet',
      parquet_compression = 'SNAPPY')
AS SELECT * FROM "workshopcur"."workshop_c_u_r"
where "line_item_usage_account_id" = 'AccountID'
```
NOTE: replace **AccountID** with the 12 digit account ID of your member account.

You will see a new table created on the left:
![Images/AWSBillingAnalysis_18.png](Images/AWSBillingAnalysis_18.png)
NOTE: You can restrict and grant access to this specific member account table through IAM policies. This will be covered in the 300 level billing analysis lab 

You have successfully setup your CUR file to be analyzed. You can now query your usage and costs via SQL.



## 3. Cost and Usage analysis <a name="CUR_Analysis"></a>
We will now perform some common analysis of your usage through SQL queries. You will be charged for Athena usage by the amount of data that is scanned - the source files are monthly, and in parquet format - which is compressed and partitioned to minimise cost. Be careful to include **limit 10** or similar at the end of your queries to limit the amount of data that comes back.

For each of the queries below, copy and paste each query into the query window, click **Run query** and view the results.
![Images/AWSBillingAnalysis_19.png](Images/AWSBillingAnalysis_19.png)

We will restrict the queries to a single month (December, 2018) by including the following line:
```
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
```


### 3.1 What data is available in the CUR file?
We will learn how to find out what data is available for querying in the CUR files, this will show what columns there are and some sample data in those columns.

1. What columns and data are in the CUR table?
```
select * from "workshopcur"."workshop_c_u_r" 
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
limit 10;
```

2. What are all the different values in a column? (the column we use is **line_item_line_item_description**)
```
select distinct "line_item_line_item_description" from "workshopcur"."workshop_c_u_r"
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
limit 10;
```

3 Give me all columns from the CUR, where a specific value is in a column (here the column **line_item_line_item_type** contains the word **Usage** somewhere, note the capital 'U'):
```
select * from "workshopcur"."workshop_c_u_r"
where "line_item_line_item_type" like '%Usage%' and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
limit 10;
```

4. What billing periods are available?
```
SELECT distinct bill_billing_period_start_date FROM "workshopcur"."workshop_c_u_r"
limit 10;
```

### 3.2 Top Costs
To efficiently optimize its useful to view the top costs in different categories, such as service, description or tags.

1. Top10 Costs by AccountID:
```
select "line_item_usage_account_id", round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_usage_account_id"
order by cost desc
limit 10;
```

2. Top10 Costs by Product:
```
select "line_item_product_code", round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_product_code"
order by cost desc
limit 10;
```

3. Top Costs by Line Item Description
```
select "line_item_product_code", "line_item_line_item_description", round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_product_code", "line_item_line_item_description"
order by cost desc
limit 10;
```

4. Top EC2 Costs
```
select "line_item_product_code", "line_item_line_item_description", round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where "line_item_product_code" like '%AmazonEC2%' and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_product_code", "line_item_line_item_description"
order by cost desc
limit 10;
```

5. Top EC2 OnDemand Costs
```
select "line_item_product_code", "line_item_line_item_description", round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where "line_item_product_code" like '%AmazonEC2%' and "line_item_usage_type" like '%BoxUsage%' and  month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_product_code", "line_item_line_item_description"
order by cost desc
limit 10;
```

### 3.3 Tagging and Chargeback
Common in large organizations is the requirement to allocate costs back to specific business units. It is also critical for optimization to be able to allocate costs to workloads, to measure workload efficiency.
**NOTE**: This will only work if you have tags enabled in your billng files, and they are the same as the examples here - **resource_tags_user_cost_center**

1. Top 20 Costs by line item description and CostCenter Tag
```
SELECT "bill_payer_account_id", "product_product_name", "line_item_usage_type", "line_item_line_item_description", resource_tags_user_cost_center, round(sum(line_item_unblended_cost),2) as cost FROM "workshopcur"."workshop_c_u_r"
where length("resource_tags_user_cost_center") >0 and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "resource_tags_user_cost_center", "bill_payer_account_id", "product_product_name", "line_item_usage_type", "line_item_line_item_description"
order by cost desc
limit 20
```

2. Top 20 costs by line item description, without a CostCenter Tag
```
SELECT "bill_payer_account_id", "product_product_name", "line_item_usage_type", "line_item_line_item_description", round(sum(line_item_unblended_cost),2) as cost FROM "workshopcur"."workshop_c_u_r"
where length("resource_tags_user_cost_center") = 0 and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "bill_payer_account_id", "product_product_name", "line_item_usage_type", "line_item_line_item_description"
order by cost desc
limit 20
```

### 3.4 Reserved Instance, On Demand and Spot Usage
To improve the use of pricing models across a business, these queries can assist to highlight the top opportunities for Reserved Instance (top On Demand cost), and also identify who is successful with pricing models (Top users of spot).
**NOTE**: You will need specific usage in your account that matches the instance types below, for this to work correctly.

1. Who used Reserved Instances
Identify which accounts used the available RIs, and what they would have paid with public pricing. Ideal for chargeback within an organization.
```
select "bill_payer_account_id", "bill_billing_period_start_date", "line_item_usage_account_id", "reservation_reservation_a_r_n", "line_item_product_code", "line_item_usage_type", sum("line_item_usage_amount") as Usage, "line_item_unblended_rate", sum("line_item_unblended_cost") as Cost, "line_item_line_item_description", "pricing_public_on_demand_rate", sum("pricing_public_on_demand_cost") as PublicCost from "workshopcur"."workshop_c_u_r"
where "line_item_line_item_Type" like '%DiscountedUsage%' 
group by "bill_payer_account_id", "bill_billing_period_start_date", "line_item_usage_account_id", "reservation_reservation_a_r_n", "line_item_product_code", "line_item_usage_type", "line_item_unblended_rate", "line_item_line_item_description", "pricing_public_on_demand_rate"
```

2. T2 family instance usage
Observe how much is being spent on each different family (usage type) and how much is covered by Reserved instances.
```
select "line_item_usage_type", sum("line_item_usage_amount") as usage, round(sum("line_item_unblended_cost"),2) as cost from "workshopcur"."workshop_c_u_r"
where "line_item_usage_type" like '%t2.%' and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_usage_type"
order by "line_item_usage_type"
```

3. Costs By running type
Divide the cost by usage (hrs), and see how much is being spent per hour on each of the usage types. Compare **BoxUsage** (On Demand), to **HeavyUsage** (Reserved instance), to **SpotUsage** (Spot).
``` 
select "line_item_usage_type", round(sum("line_item_usage_amount"),2) as usage, round(sum("line_item_unblended_cost"),2) as cost, round(avg("line_item_unblended_cost"/"line_item_usage_amount"),4) as hourly_rate from "workshopcur"."workshop_c_u_r"
where "line_item_product_code" like '%AmazonEC2%' and "line_item_usage_type" like '%Usage%' and month(bill_billing_period_start_date) = 12 and year(bill_billing_period_start_date) = 2018
group by "line_item_usage_type"
order by "line_item_usage_type"
```

4. Show unused Reserved Instances
This will show how much of your reserved instances are not being used, and sorts it via cost of unused portion (recurring fee).
You can use this in two ways:
- See where you have spare RI's and modify instances to match, so they will use the RIs
- Convert your existing RI's if possible

```
select bill_billing_period_start_date, product_region, line_item_usage_type, reservation_reservation_a_r_n, reservation_unused_quantity, reservation_unused_recurring_fee from "workshopcur"."workshop_c_u_r"
where length(reservation_reservation_a_r_n) > 0 and reservation_unused_quantity > 0
order by bill_billing_period_start_date, reservation_unused_recurring_fee desc
```



## 4. Tear down <a name="tear_down"></a>
Amazon Athena only charges when it is being used, i.e. data is being scanned - so if it is not being actively queried, there are no charges. It is also best practice to regularly analyze your usage and cost, so there is no teardown for this lab.

## 5. Rate this lab<a name="rate_lab"></a> 
[![1 Star](Images/star.png)](http://dx1572sre29wk.cloudfront.net/Cost_200_4_1star) [![2 star](Images/star.png)](http://dx1572sre29wk.cloudfront.net/Cost_200_4_2star) [![3 star](Images/star.png)](http://dx1572sre29wk.cloudfront.net/Cost_200_4_3star) [![4 star](Images/star.png)](http://dx1572sre29wk.cloudfront.net/Cost_200_4_4star) [![5 star](Images/star.png)](http://dx1572sre29wk.cloudfront.net/Cost_200_4_5star)


## 6. Survey <a name="survey"></a>
Thanks for taking the lab, We hope that you can take this short survey (<2 minutes), to share your insights and help us improve our content.

[![Survey](Images/survey.png)](https://amazonmr.au1.qualtrics.com/jfe/form/SV_bdUZiCBDHx4M4At)


This survey is hosted by an external company (Qualtrics) , so the link above does not lead to our website.  Please note that AWS will own the data gathered via this survey and will not share the information/results collected with survey respondents.  Your responses to this survey will be subject to Amazons Privacy Policy.

 

