## aa

### ;lsdakfj

Now we can use Athena to make our first queries on the data. To do that, search for "Athena" in the search box. You will see the table that Crawler has created in the "Tables" menu, click on the three dots on the side and select "Preview table". Before using Athena, it asks you to provide the outpout location for the queried data. To do that, select the "Settings" tab and click on "Manage". In the "Manage settings" section, we can insert the bucket we want Athena to store its queries. It's better to not create a new bucket just for that, instead, we just create a folder inside the bucket that we created for this project. To do that, open a new AWS, go to S3, and create an "athena-queries" folder for the queries. After creating the folder, head back to Athena and select the folder that you just created. Click "Save".

<img width="814" alt="Screenshot 2023-07-02 at 15 19 27" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/c84f2ee2-ae83-485b-836d-49468f06632c">

Now we can make our queries, but there is a problem: Athena cannot understand this particular json data.

<img width="990" alt="Screenshot 2023-07-02 at 15 23 37" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d72de706-5d82-448c-a942-f7f559fbab6c">

The problem is that Athena is not smart enough to understand the section of data that we want from json. We want the keys and values to use the serialized/deserialized principle. The raw json file is formatted in a more humanly readable way, but it's better to use a differente format so Crawler can read it:

<img width="536" alt="Screenshot 2023-07-02 at 16 12 59" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/071123ae-d145-434b-bdc4-67fef0a9bc87">

For us to be able to query the data in Athena, it's better to convert the data into tabular form which is basically Apache parquet format. We will use Lambda to make an ETL job to extract the json file, transform it to parquet, and load it to another S3 bucket for querying. Before we do that, we have to create another IAM role for Lambda to have access to the S3 bucket that has the raw json files. To do that, open IAM in another folder and go to "Access management" -> "Roles" to "Create role". Select "Lambda". "Next".

<img width="1099" alt="Screenshot 2023-07-02 at 15 52 49" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/4b97204e-0927-418d-be31-c64da13646ed">

Search for "AmazonS3FullAccess", click on the check box on this item, and click "Next".

<img width="1084" alt="Screenshot 2023-07-02 at 15 54 00" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/ec15bbc3-a607-45a0-b24b-17871937e849">

Give it a name in the same pattern as the first IAM role and click "Next".

<img width="811" alt="Screenshot 2023-07-02 at 15 56 24" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/00ea07f7-9569-4cb6-8433-53b729a51f9c">

Now go to "Add permissions" -> "Attach policies" to add another permission, this one will allow Lambda to access Glue services. Search for "AWSGlueServiceRole", click on the check box on this item, and click "Add permissions".

<img width="1373" alt="Screenshot 2023-07-02 at 17 07 41" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/ea02431c-e032-438d-b018-009cf670cbbf">

We can ceate the Lambda function. Search for "Lambda" on the search box and then click on "Create function". Give it a name and select the latest version of Python. On "Execution role", select "Create and existing role", click on the role that you just created. Click on "Create function".

<img width="1342" alt="Screenshot 2023-07-02 at 16 06 31" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/a9259108-4c2d-4e0a-afcb-f7be492613c3">
<img width="1345" alt="Screenshot 2023-07-02 at 16 06 43" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/c154845d-b308-4842-900a-cdfe30846475">

Once the Lambda function is created and we are inside the Lambda editor, we will use the following code for the ETL job:

```
import awswrangler as wr
import pandas as pd
import urllib.parse
import os

# Temporary hard-coded AWS Settings; i.e. to be set as OS variable in Lambda
os_input_s3_cleansed_layer = os.environ['s3_cleansed_layer']
os_input_glue_catalog_db_name = os.environ['glue_catalog_db_name']
os_input_glue_catalog_table_name = os.environ['glue_catalog_table_name']
os_input_write_data_operation = os.environ['write_data_operation']


def lambda_handler(event, context):
    # Get the object from the event and show its content type
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    try:

        # Creating DF from content
        df_raw = wr.s3.read_json('s3://{}/{}'.format(bucket, key))

        # Extract required columns:
        df_step_1 = pd.json_normalize(df_raw['items'])

        # Write to S3
        wr_response = wr.s3.to_parquet(
            df=df_step_1,
            path=os_input_s3_cleansed_layer,
            dataset=True,
            database=os_input_glue_catalog_db_name,
            table=os_input_glue_catalog_table_name,
            mode=os_input_write_data_operation
        )

        return wr_response
    except Exception as e:
        print(e)
        print('Error getting object {} from bucket {}. Make sure they exist and your bucket is in the same region as this function.'.format(key, bucket))
        raise e
```

Once the code has been inserted, click on "Deploy" to set the code in the Lambda function. The provided code is a Python script that defines a Lambda function for processing JSON data stored in an S3 bucket. Here's a breakdown of what the code does:

1. It imports the necessary libraries: awswrangler for reading and writing data to S3 and Glue, pandas for data manipulation, urllib.parse for URL decoding, and os for accessing environment variables.
2. The function lambda_handler is the entry point for the Lambda function. It takes two parameters: event and context. event contains information about the triggering event, and context provides information about the runtime environment.
3. The function retrieves the bucket name and key of the S3 object from the event.
4. The script reads the JSON data from the S3 object into a Pandas DataFrame using the awswrangler library.
5. The script performs some data transformation by normalizing the JSON data and extracting specific columns into a new DataFrame (df_step_1).
6. The transformed DataFrame is then written back to S3 as Parquet format using the wr.s3.to_parquet function from awswrangler. The function also specifies additional parameters such as the S3 path, Glue catalog database and table names, and the write operation mode.
7. Finally, the function returns the response from the wr.s3.to_parquet function.

Before we advance on defining the environment variables, we have to create an S3 bucket that will be the repository for the clean data transformed by Lambda. To do that, open a new S3 tab and create a new bucket for the clean data, just change the "raw" at the bucket name to "clean". Once the bucket is created, create a "youtube" folder inside it.

<img width="811" alt="Screenshot 2023-07-02 at 16 39 21" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/166923cd-16b1-4b5d-8f3b-6cd87e591f2d">

Now head back to Lambda to create the environment variables. Go to "Configuration" tab and on the menu, select on "Environment variables" and click "Edit". Add the environment variables according to the image below:

<img width="703" alt="Screenshot 2023-07-02 at 22 32 35" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/dcb2f170-9139-478d-b562-64ba76811a98">

Before we start testing the function, we have to add Lambda layers to make the deployment of your code faster, this way we will not get any errors. On "Layers", click "Add a layer", select the "AWSSDKPandas-Python310" and "Version" as 3 or the latest version.

<img width="811" alt="Screenshot 2023-07-02 at 22 22 51" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/2cb01f8c-a818-49e5-85fc-41b612d398ed">

Go to the "Configuration" tab again and select "General configuration", click on "Edit". In this section, we have to increase the timeout of the lambda function so it has more time to process the code. On "Timeout", increase it to 15 minutes.

<img width="814" alt="Screenshot 2023-07-02 at 18 10 59" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/c3a714ad-a4be-414d-8959-ddfea479c9bd">

Finally we are able to test this function, go to "Test" -> "Configure test event". There are a lot of testing templates that we can use, on "Template", give a name like "S3-put" and select "S3-put" to test putting data inside an S3 bucket. We will use a random S3 bucket for the testing, we just have to provide the bucket name and the file that we want to use in this particular test. To do that, open a new tab and go to S3, click on one of the json files and click on "Copy S3 URI".

<img width="1361" alt="Screenshot 2023-07-02 at 17 31 36" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/996ab7c7-4870-48f2-8a11-efc796617cb2">

You also have to copy the bucket name of the file that you are using. Head back to Lambda and insert the bucket name on "example_bucket". On "test%2Fkey", insert the URI that you copied on S3 but remove the "s3://134742874617-de-youtube-raw/" part. It should be like the image below:

<img width="628" alt="Screenshot 2023-07-02 at 17 41 05" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d6070010-215e-48a6-b924-a2cf150d7295">










