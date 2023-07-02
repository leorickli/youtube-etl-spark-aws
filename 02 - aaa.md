## aaa

In the AWS console, search for Glue. On the Glue menu, select "Data catalog" -> "Crawlers" and click "Create crawler". You can name it based on your S3 folder name and insert "glue-catalog-1" at the end.

<img width="920" alt="Screenshot 2023-07-01 at 20 25 10" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/29eb0339-2738-4045-ad9d-1011a385f800">

On Step 2, click on "Add data source". We need to provide the S3 bucket path that we stored our data so select our "crime" folder. Click on "Add an S3 data source".

<img width="608" alt="Screenshot 2023-07-01 at 20 29 08" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/886aefaf-f2fc-41ee-9f6c-a40c33d4559d">

Click "Next" for Step 3. For this step we need to set up permissions by creating an IAM role because the service Glue wants to access data from the service S3. To do that, search for IAM on the search bar. On IAM menu, go for "Access management" -> "Roles" and click on "Create role". On "Use cases for other AWS services", select "Glue". "Next".

<img width="827" alt="Screenshot 2023-07-01 at 20 37 11" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/396cf20d-9b4c-43b1-bf47-87c4d0b63c2f">

Now that we create the role for Glue we are going to add the permissions for it to have access. On "Permission policies", search for "AmazonS3FullAccess" and click on the checkmark of the policy that has been found. "Next".

<img width="1090" alt="Screenshot 2023-07-01 at 20 41 31" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d2c73dca-ef9a-4935-aa61-163188ce6986">

Give the role name the same name as you bucket but removing "raw" and ending with "glue-role". Click on "Create role". Now we need to add another permission to S3. Click on the role we just created, click on "Add permission" -> "Attach policies". Search for "AWSGlueServiceRole" to give more access to Glue jobs and your role should have these two permissions:

<img width="1109" alt="Screenshot 2023-07-01 at 21 00 06" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/19e2f99a-b4dc-4a4f-ad04-ffff50289b15">

Now we can get back at creating our Crawler job, refresh the "Existing IAM role" by clicking on the refresh button. Select the IAM role that we just created. "Next".

<img width="917" alt="Screenshot 2023-07-01 at 21 03 20" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/b3539ce4-c23a-4ea2-99b9-1c70aa0588e5">

Now we have to create our new database. Click on "Add database" and it will create a new page. Give a name to it and click "Next",

<img width="1198" alt="Screenshot 2023-07-01 at 21 06 08" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/86aa2a66-8427-43b4-83d3-4362115b9cdf">

Refresh the "Target database" and select the database we just created. "Next"

<img width="918" alt="Screenshot 2023-07-01 at 21 07 49" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d71aa6d5-4fc8-46b4-a543-91c0615c5d12">

Review all the steps and click on "Create crawler". Once the crawler has been created, we can click on "Run crawler" to start the cataloging process, wait a few moments to be completed. Once completed, go to the Glue menu and click on "Data catalog" -> "Tables". Here we can see the tables that Crawler created. In my case I had a problem with RDO 2 so I had to edit the schema manually by clicking on the "Edit schema as JSON". I just copied the schema from the other tables and pasted in the bad table.

<img width="1180" alt="Screenshot 2023-07-01 at 21 49 52" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/851e4ee7-9b1b-4b88-b16a-811df40d6f46">

Once the schema has been created with Crawler then we can see the data. Go to "Actions" -> "View dta", this will take us to Athena. Athena will say that we have to set up a query result location in Amazon S3, to do that, click on "Edit settings" of this message, this will take us to manage Athena settings.

<img width="1345" alt="Screenshot 2023-07-01 at 21 57 47" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/8048ca66-2024-4465-8551-3327b32e1b3b">

On "Manage settings", it's asking to insert a bucket name for the locations of the query results. Go to S3 and create a new bucket for Athena.

<img width="815" alt="Screenshot 2023-07-01 at 22 07 31" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/689e2796-1e72-43b2-8f63-b7a8a8f116bb">

Now we can get back to "Manage settings" on Athena to insert the new bucket that we created. Click on "Save"

<img width="810" alt="Screenshot 2023-07-01 at 22 20 21" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/2a44d0fb-dd6f-4124-8219-9135b7d254b0">













