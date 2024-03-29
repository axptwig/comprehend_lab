# Amazon Comprehend Email Analysis Workshop
Welcome to the Comprehend Email Analysis workshop!

In this workshop, you will create a data pipeline to extract sentiment information from an email dump and create a network graph to show assocations across an organization.

## Backstory
You are playing the role of an infrastructure architect and your primary customers are the analytics and BI teams at your organization. They want to get insight into sentiment information of incoming and outgoing emails and Fhave instructed you to help the cloud engineering team build a POC using a public data set. In the context of this lab, we will be using a [public dataset](http://www.ahschulz.de/enron-email-data/) of the emails from the Enron scandal.

## Current Challenges
The email data is presently stored in a partitioned csv file inside of S3 that was created from a dump of the master database. Our goal is to perform ETL processes and mold the data into a form where we can submit it to the Comprehend service as well as display the returned information in our application. Since we are working with such a large amount of emails, we will be submitting jobs to Amazon Comprehend using the [Asynchronous Batch Processing](https://docs.aws.amazon.com/comprehend/latest/dg/how-async.html) api call to submit our jobs. Since our data is stored in partitioned CSV and Amazon comprehend only accepts raw text files, we need to extract the email body, recipient info, and sentiment from the email dump in order to feed this data into our hosted application.

## High Level Architecture Diagram
   ![arch](./images/Architecture.png)  
## Workflow Components
  * Amazon S3
  * IAM 
  * AWS Glue
  * Amazon Comprehend
  * Amazon Athena
  * Amazon Quicksight

## Instructions
> ***For this lab, ensure the current AWS region is US East (N. Virginia).***

### S3 Setup
  1. Clone this repo to your local computer and navigate to the root directory of the repo
    * If you downloaded the repo zip file, you will need to unzip the repo folder before continuing onward.
  1. The next step is to unzip email_dump.zip on your local machine. The extracted archive folder should contain 3 folders named /employeelist/, /message/, and /recipientinfo/.
  1. Open a browser and navigate to the AWS console
  1. Type S3 in the search bar and select the Amazon S3 to be redirected to the service dashboard
  1. Choose *Create Bucket* and enter a unique DNS compliant name for your s3 bucket
    * leave all other options as default and continue through the wizard to configure your bucket
    
  1. Once your S3 bucket has been successfully created, select the bucket in the table in order to step into the bucket's root directory
  1. Select 'Create Folder' button and create two folders named 'input' and 'output' in the root of the bucket. Leave all encryption options as default.
  ![s3_root_struct](./images/S3_Root_Structure.png)
  1. Step into your /input/ folder and create two folders inside it, /raw/ and /sentiment/
  1. Now step back out and into the output folder you created previously to create two additional folders named, /text/, and /sentiment/.
  ![input_struct](./images/S3_Output_Structure.png)
  1. Once created, return back to the root directory of your s3 bucket.
  1. Now select the 'input' folder and step into this directory. 
  1. Upload the email_dump folder containing the email data which we unzipped earlier by dragging and dropping the folder from finder or windows explorer into the browser window.
  1. Select Upload and wait for the files to appear in the bucket.
  ![s3_comprehennd_struct](./images/S3_Comprehend_Input.png)
  1. Congrats, you have now completed the first section of this lab! Move on to configure the ETL process and sentiment analysis jobs.

### AWS Glue Crawler Configuration
In this section we will be making use of AWS Glue to transform our email data into optimized file formats. AWS Glue is a fully managed extract, transform, and load (ETL) service that makes it easy for customers to prepare and load their data for analytics. You can create and run an ETL job with a few clicks in the AWS Management Console. You simply point AWS Glue to your data stored on AWS, and AWS Glue discovers your data and stores the associated metadata (e.g. table definition and schema) in the AWS Glue Data Catalog. Once cataloged, your data is immediately searchable, queryable, and available for ETL. Let's begin!
  1. First, select the services drop down menu from the top left corner of the AWS console. Search for 'AWS Glue' and select this option to navigate the the AWS Glue console.
  1. Choose the *Crawlers* option from the left hand side menu. You can use a crawler to populate the AWS Glue Data Catalog with tables. A crawler can crawl multiple data stores in a single run. Upon completion, the crawler creates or updates one or more tables in your Data Catalog. Extract, transform, and load (ETL) jobs that you define in AWS Glue use these Data Catalog tables as sources and targets. The ETL job reads from and writes to the data stores that are specified in the source and target Data Catalog tables.
  1. Select the blue 'Add Crawlers' button to begin configuration
  ![s3_root_struct](./images/Glue_Add_Crawler.png)
  1. Give your crawler a name such as emailCrawler and hit next.
  1. We are then given two option, we can either crawl a data store (such as s3) or we can update existing tables in the catalog. In this case, our data catalog is currently empty since we are just creating our first crawler. We will need to use this crawler to populate the catalog using the data we previously uploaded to s3. Select the 'data stores' radio button and hit next.
    ![s3_root_struct](./images/Glue_Source_Type.png)
  1. In the next screen, select S3 as your data store and input the s3 path for where our raw data is located. Currently we have 3 discrete folders in s3 that store data from the messages, recipientinfo, and employeelist tables. We will need to point our crawler to the URL for each of these folders. 
    * The URL should look similar to s3://mybucketname/input/messages/  
    ![Glue_outputs](./images/Glue_Add_Data.png)
  1. On the following screen, select 'Yes' for adding another data store and hit next. 
  1. On the this screen add the s3 paths for the remaining 2 folders in s3. Once you have completed adding all three folders as our data sources, hit next. Note: We should in total have 3 'Chosen Data Sources' which is displayed in the right hand side menu.**s3://mybucketname/input/employeelist/**s3://mybucketname/input/recipientinfo/*
  ![Glue_output](./images/Glue_three_stores.png)
  1. This time, when the add another data store screen loads, select no and hit next.
  1. On the 'Choose an IAM role' menu, select 'Create an IAM role' and give it a custom name before selecting next.
  ![Glue_output](./images/Glue_IAM_Role.png)
  1. Leave the "frequency' drop down as the default value of 'on-demand' and select next. We will be manually running the crawler once it is created + configured. 
  1. We have now reached the step where we Configure the crawler's output. To do this, we first must create a database in the Glue data catalog. Select the blue 'add database button' and assign a name to our newly created database.
    * Make sure you have the newly created database selected in the output database dropdown.
    ![Glue_output](./images/Glue_Crawler_output.png)
  1. Hit next and finish on the following screen to return back to the main glue crawler page. You should see the crawler you just created in the table on screen. 
  1. Select the checkbox next to the name of your crawler to select it. Once the check box has been selected, the option to run the crawler at the top of the screen becomes available. Select run crawler to crawl the data stored in s3. The Glue Crawler will populate your output database with metadata information about the crawled tables including file format and schema.
  1. Wait for the crawler job to complete and your crawler's status returns to Ready.
  
### ETL with AWS Glue 

### Amazon Comprehend Configuration
  1. Now that we have successfully performed ETL functions on our data and extracted our message body into text files. we are ready to begin sentiment analysis on the data.
  1. Once again, select the services drop down from the top left of the screen and search for 'Amazon Comprehend' and navigate to the comprehend service dashboard.
  1. From the main screen select the orange 'Launch Comprehend' button.
  1. For this use case, we will be configuring a Comprehend 'Analysis Job'. Start by selecting Analysis Jobs in the left hand side menu.
  1. Select the orange 'Create Job' to begin configuring our Sentiment Analysis job parameters.
    ![comprehend_job](./images/Comprehend_Create_Job.png)  
  1. Give the job a name so you can refer to it's status later in the lab.
  1. Under 'Analysis Type' select the 'sentiment' option as well as english for the language since our emails are written in american english.
    ![comprehend_job](./images/Comprehend_Job_Settings.png)  
  1. For the 'input data' section, leave the 'My Documents' radio button selected and enter the path to email dumps in text format found in s3. The path should look something similar to s3://mybucket/input/sentiment/
    ![comprehend_job](./images/Comprehend_Input_Data.png)  
  1. The Output data section defines where the Comprehend service will output the details of our sentiment analysis job once it completes. Enter the s3 path to the empty sentiment folder in s3. The path should look similar to s3://mybucket/output/sentiment/
    ![comprehend_job](./images/Comprehend_Output_Data.png)  
  1. Our final step is to create an IAM service role which allows comprehend to access data in your s3 bucket. Select 'Create an IAM role' with permissions to access input and output s3 buckets. Give the role a name and select the orange 'Create Job' button.
  1. Congrats! You have now kicked off you first sentiment analysis job. The service will take a few minutes to process all the text files and once completed you should recieve a tar.gz file in your specified output location containing the sentiment information for each record.
### Preparing Data for Application

### Viewing the data in the AWS Amplify
  



## Final Setup
