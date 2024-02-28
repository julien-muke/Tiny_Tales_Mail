# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263)     Build a Serverless Email Marketing Application on AWS


## 🤖 Introduction

In this hands-on tutorial, I’ll walk you through how to design and build a simple serverless email marketing application from scratch.  When we're done, our system will send emails on a schedule, grabbing HTML email templates and a list of contacts from an S3 bucket.


## 📐 Architecture Design


![Tiny Tales Mail ](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/95530b37-1f1d-4ff8-8d27-6af3dda24c8f)



## ⚙️ AWS Services Used

* Amazon Simple Email Service (SES)
* AWS Lambda
* Amazon Simple Storage Service (S3)
* Amazon EventBridge
* AWS Identity and Access Management (IAM)


## 🔋 Features


👉 A place to store email templates and list of contacts

👉 A way to send emails

👉 A way to “merge” email templates with contacts and send them to the email service

👉 A way to trigger sending of emails on a schedule



## ➡️ Step 1 - Creating an S3 bucket to store email templates and contacts