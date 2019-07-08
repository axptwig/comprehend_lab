# Amazon Comprehend Email Analysis Workshop
Welcome to the Comprehend Email Analysis workshop. In this workshop, you will create a data pipeline to extract sentiment information from an email dump and create a network graph to show assocations across an organization.

## Backstory
You are playing the role of an infrastructure architect and your primary customers are the analytics and BI teams at your organization. They want to get insight into sentiment information of incoming and outgoing emails and have instructed you to help the cloud engineering team build a POC using a public data set. In the context of this lab, we will be using a [public dataset](http://www.ahschulz.de/enron-email-data/) of the emails from the Enron scandal.

### The Cloud Engineering Team

The cloud engineering team has put together a set of resources that you can use to build out the workflow. They practice infrastructure as code, so all the components are created using a set of cloudformation scripts that are available in this git repository.

These resources form the basics of the ETL pipeline and make use of services such as Lambda Functions, AWS Glue and Comprehend as well as IAM roles that will be needed to access the services.

When a CloudFormation script completes execution, it returns a set of Outputs that provide details on the resources that were created. 

In order to complete the lab, you will need to be familiar with where to find these outputs and how to use them in the lab. Once you create the CloudFormation stack, spend a little time navigating the console to find these outputs.

## Current Challenges
The email's are currently stored in a csv file that was created from a dump of database. We need to perform some ETL to clean up and join the appropriate columns to display in the application.


## Setup

> ***For this lab, ensure the current AWS region is US East (N. Virginia).***

## Workflow Components

## Final Setup
