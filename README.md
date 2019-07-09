# Amazon Comprehend Email Analysis Workshop
Welcome to the Comprehend Email Analysis workshop!

In this workshop, you will create a data pipeline to extract sentiment information from an email dump and create a network graph to show assocations across an organization.

## Backstory
You are playing the role of an infrastructure architect and your primary customers are the analytics and BI teams at your organization. They want to get insight into sentiment information of incoming and outgoing emails and have instructed you to help the cloud engineering team build a POC using a public data set. In the context of this lab, we will be using a [public dataset](http://www.ahschulz.de/enron-email-data/) of the emails from the Enron scandal.

### The Cloud Engineering Team

The cloud engineering team has compiled resources that you can use to build out the workflow. They practice infrastructure as code, so all the infrastructure deployed was previously created using a set of cloudformation scripts.

These resources include an S3 bucket with the email data stored in the /input directory as well as a front end application to explore and view the data.

## Current Challenges
The email data is presently stored in a partitioned csv file inside of S3 that was created from a dump of the master database. Our goal is to perform ETL processes and mold the data into a form where we can submit it to the Comprehend service as well as display the returned information in our application. Since we are working with such a large amount of emails, we will be submitting jobs to Amazon Comprehend using the [Asynchronous Batch Processing](https://docs.aws.amazon.com/comprehend/latest/dg/how-async.html) api call to submit our jobs. Since our data is stored in partitioned CSV and Amazon comprehend only accepts raw text files, we need to extract the email body, recipient info, and sentiment from the email dump in order to feed this data into our hosted application.

## Instructions
> ***For this lab, ensure the current AWS region is US East (N. Virginia).***

### S3 Setup
  1. Clone this repo to your local computer and navigate to the root directory of the repo
    * If you downloaded the repo zip file, you will need to unzip the repo folder before continuing onward.
  1. Unzip email_dump.zip
  1. Open a browser and navigate to the AWS console
  1. Type S3 in the search bar and select the Amazon S3 to be redirected to the service dashboard
  1. Choose *Create Bucket* and enter a unique DNS compliant name for your s3 bucket
    * leave all other options as default and continue through the wizard to configure your bucket
  1. Once your S3 bucket has been successfully created, select the bucket in the table in order to step into the bucket's root directory
  1. Select 'Create Folder' button and create two folders named 'input' and 'output' in the root of the bucket
  1. Once created, select the 'input' folder and step into this directory. 
  1. Upload the email_dump folder containing the email data which we unzipped earlier

### AWS Glue Configuration

### Amazon Comprehend Configuration
  

## High Level Architecture Diagram

## Workflow Components
  * Amazon S3
  * IAM 
  * AWS Glue
  * Amazon Comprehend
  * Amazon Athena
  * Amazon Quicksight

## Final Setup
