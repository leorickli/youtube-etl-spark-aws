## Inserting Raw Data Into S3

### Downloading the dataset

Download the [Trending YouTube Video Statistics](https://www.kaggle.com/datasets/datasnaek/youtube-new) dataset on Kaggle. This dataset includes several months (and counting) of data on daily trending YouTube videos. Data is included for the US, GB, DE, CA, and FR regions (USA, Great Britain, Germany, Canada, and France, respectively), with up to 200 listed trending videos per day. Each region’s data is in a separate file. Data includes the video title, channel title, publish time, tags, views, likes and dislikes, description, and comment count.

### Uploading it to S3

Fist, make sure you select N. Virginia (us-east-1) as your region on the AWS console. In the AWS console, search for EC2 and then click on "Create bucket". On "General configuration" -> "Bucket name" it's important to have an excluse and easily identifiable name for your bucket. To make it exclusive, insert your account id as the first set of identification for your bucket name. To make it easily identifiable for this case, insert the "de" (it's a Data Engineering project), "youtube" (inserting youtube data) and "raw" (the raw data that is downloaded from Kaggle). Make sure that you selected "us-east-1" as your region. Leave all the other optins as default. "Create bucket".

<img width="806" alt="Screenshot 2023-07-02 at 13 04 28" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/caaa6c79-b29e-4b3a-ae31-57a9e8a9478c">

Once your bucket has been created, click on it so we can insert a folder inside by clicking on "Create folder". Name your folder "youtube" and click "Create folder".

<img width="811" alt="Screenshot 2023-07-02 at 13 06 01" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/05fb1699-de62-49d4-ac38-012e1ab13963">

Now we have to create a folder for all the json files of the dataset. Click on the "youtube" folder that we just created and then click on "Create folder", give it a name and click "Create folder".

<img width="808" alt="Screenshot 2023-07-02 at 13 11 03" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/32ffb225-a4b0-446f-b3f9-634d209e0be1">

Upload all the json files to this folder by clicking on the folder and selecting "Upload". On the "Upload" section, select "Add files" and select all the json files on the folder. Click "Upload".

<img width="812" alt="Screenshot 2023-07-02 at 13 15 33" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/e1416e08-0c19-4353-b538-9da56dc26866">

Once the upload has been completed we can cary on to uploading the csv files. For future analysis, it's better to create individual folders by country and upload the files to them, just create the folders named by each country and upload that specific csv file for that country in it. The result should be like this:

<img width="670" alt="Screenshot 2023-07-02 at 13 21 31" src="https://github.com/leorickli/youtube-etl-spark-aws/assets/106999054/0b33490f-0082-4cc2-8686-5d5e6c34e69d">
