## Crawling the Data with Crawler

### Creating the IAM role

In the process of creating a Crawler job, you will be asked to create an IAM role. We need to set up permissions by creating an IAM role because the service Glue wants to access data from the service S3. To do that, search for IAM on the search bar. On IAM menu, go for "Access management" -> "Roles" and click on "Create role". On "Use cases for other AWS services", select "Glue". "Next".

<img width="827" alt="Screenshot 2023-07-01 at 20 37 11" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/396cf20d-9b4c-43b1-bf47-87c4d0b63c2f">

Now that we create the role for Glue we are going to add the permissions for it to have access. On "Permission policies", search for "AmazonS3FullAccess" and click on the checkmark of the policy that has been found. "Next".

<img width="1090" alt="Screenshot 2023-07-01 at 20 41 31" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d2c73dca-ef9a-4935-aa61-163188ce6986">

Give the role name the same name as you bucket but removing "raw" and ending with "glue-role". Click on "Create role". Now we need to add another permission to S3. Click on the role we just created, click on "Add permission" -> "Attach policies". Search for "AWSGlueServiceRole" to give more access to Glue jobs and your role should have these two permissions:

<img width="1109" alt="Screenshot 2023-07-01 at 21 00 06" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/19e2f99a-b4dc-4a4f-ad04-ffff50289b15">

### Creating the Crawler job

Now that we set up our IAM role we can begin creating our Crawler jog in Glue. To do that, search for "Glue" in the search bar. In the Glue menu, go to "Data Catalog" -> "Crawlers" and click "Create Crawler". You can name it based on your S3 folder name and insert "glue-catalog-1" at the end.

<img width="917" alt="Screenshot 2023-07-02 at 14 51 26" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/cfd7334a-b320-4720-9f3f-6e0451eb9cd5">

On Step 2, click on "Add data source". We need to provide the S3 bucket path that we stored our data so select our "youtube" folder. For now we will be crawling over the json data, select the "raw_statistics_reference_data" that has the json files. Click on "Add an S3 data source".

<img width="610" alt="Screenshot 2023-07-02 at 14 53 48" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/ca20c438-bbee-4c70-b10c-d1d06e64f3ab">

On Step 3 we can select the IAM role that we created before, just select it on the "Existing IAM role". Click "Next".

<img width="918" alt="Screenshot 2023-07-02 at 14 54 50" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/dbad2535-112d-43ad-a84e-b932a70c765a">

On Step 4 we have to create a new database that Crawler will store the queries that we make. Click on "Add database", it will create a new page. Give a name to it and click "Next".

<img width="1192" alt="Screenshot 2023-07-02 at 14 57 34" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d6681ca3-dd00-450b-b98e-e94c5275fbc8">

Refresh the "Target database" and select the database we just created. "Next".

<img width="912" alt="Screenshot 2023-07-02 at 14 58 38" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/e3bd6aa4-bd3b-4a1c-9556-ec5c8da86255">

Review all the steps and click on "Create crawler". Once the crawler has been created, we can click on "Run crawler" to start the cataloging process, wait a few moments to be completed. Once completed, go to the Glue menu and click on "Data catalog" -> "Tables". Here we can see the tables that Crawler created. We can check that Crawler created a schema for the table.

<img width="1181" alt="Screenshot 2023-07-02 at 15 05 06" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/4d0035cd-500c-4536-a897-b6cd9141a38c">
