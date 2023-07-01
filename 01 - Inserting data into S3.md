## Inserting data into S3

### Downloading the dataset

Download the [Crime Data in Brazil](https://www.kaggle.com/datasets/inquisitivecrow/crime-data-in-brazil) dataset on Kaggle. This dataset contains structured data about all crime occurrences that have been acted upon by the PM, the main police force in Sao Paulo. The dataset is not consistent in its completeness, as some of the towns comprising the Greater Sao Paulo were slow in collecting full data. It also does not contain the actual historic of each crime report, as that would violate privacy.

### Uploading it to S3

Fist, make sure you select N. Virginia as your region on the AWS console. In the AWS console, search for EC2 and then click on "Create bucket". On "General configuration" -> "Bucket name" it's important to have an excluse and easily identifiable name for your bucket. To make it exclusive, insert your account id as the first set of identification for your bucket name. To make it easily identifiable for this case, insert the "de" (it's a Data Engineering project), "crime" (inserting crime data) and "raw" (the raw data that is downloaded from Kaggle). Make sure that you selected "us-east-1" as your region. "Create bucket".

<img width="808" alt="Screenshot 2023-07-01 at 17 13 11" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/182eb263-7196-4fa7-b749-bc2581cb48d1">

Once your bucket has been created, click on it so we can insert a folder inside by clicking on "Create folder". Name your folder "cime" and click "Create folder".

<img width="816" alt="Screenshot 2023-07-01 at 17 19 21" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/60b5e651-7212-42b5-8aa6-24b487cc4f8a">

For this dataset we will use only the "RDO" (Digital Record of Occurence), this is the online "BO" (Police Report) that you fill when making a crime report. To do that, click on the "crime" folder you just created and then click on "Upload". Upload only the RDO 1, 2, 3 and the column description one.

<img width="816" alt="Screenshot 2023-07-01 at 17 43 40" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/1187c74b-de1b-4c75-b10f-dd0aecccb335">

You can track the upload progress now, it will take some time.

<img width="1093" alt="Screenshot 2023-07-01 at 20 17 08" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/d5a69757-0616-479e-bf26-602cb8a755c7">
