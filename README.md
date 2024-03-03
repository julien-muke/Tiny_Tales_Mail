# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263)     Build a Serverless Email Marketing Application on AWS


## 🤖 Introduction

In this hands-on tutorial, I’ll walk you through how to design and build a simple serverless email marketing application from scratch.  When we're done, our system will send emails on a schedule, grabbing HTML email templates and a list of contacts from an S3 bucket.


## 📐 Architecture Design


![Tiny Tales Mail -2](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/8ad7533e-5ae5-4858-a6fb-581562d07857)


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

1. Sign in to the AWS Management Console and open the Amazon S3
2. In the left navigation pane, choose Buckets
3. Choose Create bucket
4. For Bucket name, enter a name for your bucket, i'm going to name mine `jm-email-marketing`
5. Block Public Access as best practice, and leave all the defaults, scroll down then click on "Create bucket"

![Screenshot 2024-02-29 at 15 19 16](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/18fe16fc-b604-4d5d-bae8-498b4d4913f4)


## ➡️ Step 2 - Create The HTML email template & The CSV file with contact information

1. Inside of our S3 bucket, we want to store an email template, an HTML template that we'll actually send to people and then
we need a list of the contacts as well, the email addresses to send to.
2. For the email template, copy and paste the code below and save it as `email_template.html`

### <a name="snippets">🕸️ Code snippets</a>

<details>
<summary><code>email_template.html</code></summary>

```html
<!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Decode the Programmer's Riddle - Tiny Tales Mail</title>
            <style>
            body {
                font-family: Arial, sans-serif;
                margin: 0;
                padding: 20px;
                background-color: #f4f4f4;
                color: #333;
            }
            .container {
                max-width: 600px;
                margin: auto;
                background: #ffffff;
                padding: 20px;
                border-radius: 8px;
                box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            }
            h1, h2 {
                color: #007bff;
            }
            p {
                font-size: 16px;
                line-height: 1.5;
            }
            .challenge-details {
                background-color: #eee;
                border-left: 5px solid #007bff;
                padding: 10px;
                margin: 20px 0;
            }
            </style>
            </head>
            <body>
            <div class="container">
            <h1>This Week's Tech Challenge: Decode the Programmer's Riddle</h1>
            <p>Hello {{FirstName}}!</p>
            <p>It's that time again—Tiny Tales Mail is back with a new challenge to stretch your tech muscles and ignite your problem-solving spark. This week, we're dialing up the difficulty with a puzzle that blends coding logic, pattern recognition, and a bit of creative thinking. Ready to dive in?</p>
            
            <div class="challenge-details">
                <h2>Challenge: The Programmer's Riddle</h2>
                <p>Imagine you're exploring an ancient digital labyrinth. To escape, you need to input a code sequence into the central computer. You find a clue carved on the wall:</p>
                <p><strong>8, 5, 4, 9, 1, 7, 6, 3, 2, 0</strong></p>
                <p>But there's a catch: The sequence is not what it seems. To find the correct next number, you must uncover the hidden pattern. Here's a hint to guide you on your quest:</p>
                <p><strong>Hint:</strong> The order is determined not by mathematics, but by a property inherent to each number.</p>
            </div>
            
            <h2>How to Participate:</h2>
            <p>Convinced you've unraveled the labyrinth's secret? Email your solution and the reasoning behind it.</p>
            
            <h2>Prizes and Recognition:</h2>
            <p><strong>Grand Solver:</strong> The first to submit the correct answer will receive a $20 Amazon gift card and be featured in our next newsletter.</p>
            <p><strong>Creative Thinkers:</strong> The next four insightful submissions will earn a spot in our "Hall of Fame" section, celebrating your cleverness and creativity.</p>
            
            <p>This puzzle is designed to challenge and inspire. We encourage you to think outside the box and look forward to seeing the innovative ways you approach this problem.</p>
            
            <p>Best of luck, and let the best minds prevail!</p>
            <p>Eagerly awaiting your solutions,</p>
            <p><strong>The Tiny Tales Mail Team</strong></p>
            </div>
        </body>
    </html>
```

</details>

3. The other thing that that we're going to place in the S3 bucket is a CSV file of our contacts, make sure the emails that you put are ones that you can actually validate, meaning you can receive emails and click on a link that AWS sends you if you want them to actually go through, you can easily create one from scratch as well but make sure to call it `contacts.csv`


![Screenshot 2024-02-29 at 16 10 13](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/ee0477dc-0295-4471-b2cb-d8d832d419fe)



## ➡️ Step 3 - Uploading the email template and contact list to the S3 bucket

1. Go back to the S3 bucket, naviagate to the object tab, upload the `email_template.html` and `contacts.csv` make sure that the files are called that because later on we'll use them in the Lambda function not always the best idea but for this demo you will need these names to get it to work.

2. Then click on "Upload"


![Upload-objects-S3-bucket-jm-email-marketing-S3-Global](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/899ff274-d3f9-4128-b84e-701892ce2694)


✔️ we've got the S3 bucket <br/>
✔️ we've got the list of contacts to send to <br/>
✔️ we've also got an email template that we can send <br/>


## ➡️ Step 4 - Setting up Amazon Simple Email Service (SES) with an email address and domain

Before you start using Amazon SES, you must complete the following tasks:
   * Verify an identity for sending email, Verify an email address or domain to use when sending email.
   * Send your first email, Send an email by using the Amazon SES console, the Amazon SES API, an AWS SDK, or the AWS Command-Line Interface.

1. Back over to the console, navigate to SES, then click "Get started"

![Screenshot 2024-02-29 at 15 51 33](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/befd167f-2003-49db-b69c-f338e79a0522)


2. You need to enter the email address that emails will come from, you do have to validate this, AWS will send you an email there's a link in that email that you need to click on, and make sure it's a valid email address

![Screenshot 2024-02-29 at 15 52 32](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/63dc4c09-72f0-48bb-b4a7-38b186acebb6)


3. Add your sending domain, a domain identity that matches your website or business name. Amazon SES needs to be linked to your domain and verified in order to send emails to your recipients through SES.

I'm using `julienmuke.com` (if you need to purchase a domain you can use Amazon's Route 53 or other providers
like GoDaddy etc.)


![Screenshot 2024-02-29 at 15 53 16](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/6a1c1a16-9419-4b11-9712-5461f3d53199)


4. Amazon is going to send an email to your email address, you'll need to open it up, click on the link to verify that you actually own the email address and once you do you'll be able to send emails from that address.


![Screenshot 2024-02-29 at 15 55 25](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/a3dcc3a8-3f95-4a5b-b1c7-9fbed0eb9a5c)


5. If you successfully did all of those steps then you also will have a Sandbox dashboard

![Get-set-up-Amazon-Simple-Email-Service-us-east-1-2](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/34845d15-cecd-4931-bf1a-55aa9f9fb7ff)


## ➡️ Step 4 - Sending a test email using Amazon SES

Let's send an email to make sure everything is working

1. On your Sandbox dashboard click on "Send test email"

![Screenshot 2024-02-29 at 15 58 24](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/eadf5077-fda2-4b54-ba78-1d99c0c81d20)


2. Select "Raw" which will let us test with HTML format
3. For Scenario, we want to test the scenario where everything is successful, select "Successful delivery"
(But you can also test different scenarios, like what happens if it bounces or what happens if there's a complaint and so on)
4. Then paste in the HTML that we have from the email template on the Message box
5. Click on "Send test email"

![Send-test-email-Amazon-Simple-Email-Service-us-east-1](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/cf27796c-91c1-4ad1-b684-002ac067d151)

6. Then you will get a green checkmark, everything is working


![Screenshot 2024-02-29 at 16 00 32](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/238fbf37-387b-425d-bdd4-e9b801ed3abd)


## ➡️ Step 5 - Validating email addresses to send to (a requirement for the sandbox)

We need to validate the individual email addresses that we want to send to, that are stored in our contacts.csv file 

1. Back over the Sandbox dashboard, under verify email address click on "View all identities"
2. Let's create a new identity, click on "Create identity"
3. Identity type choose "Email address" and leave everything as default, then click "Create identity"


![Create-identity-Amazon-Simple-Email-Service-us-east-1](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/26abe425-905c-4162-b92b-2ec3e84c7a7a)


4. You will recieve an email from your inbox, open it and click the link to verify, you will get a success message, we can now start sending emails to that address

5. After you verify all the emails, you will see a list of verified emails

![Screenshot 2024-02-29 at 16 09 47](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/a1dc2128-ae2e-440c-b865-54c67ca4d05a)


## ➡️ Step 6 -  Creating a new Python Lambda function for email logic

We need a way to merge the email template using Lambda to create and send personalized emails to SES with the contacts and then send them to SES to the email service.

1. Back to the console, navigate to Lambda, then click on "Create function"
2. We are going to "Author from scratch", for the function name i'm going to use `SendSESEmailToContacts`, for Runtime we are going to use `python3.12` leave everything as default then click "Create function"


![Create-function-Lambda (7)](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/294ed281-bd9d-4a55-9906-52401abc4db6)


3. Scroll down to the code section, i've got some code already so you don't have to write this from scratch:


### <a name="snippets">🕸️ Code snippets</a>

<details>
<summary><code>Lambda Function Python Code</code></summary>

```python
import boto3
import csv

# Initialize the boto3 client
s3_client = boto3.client('s3')
ses_client = boto3.client('ses')

def lambda_handler(event, context):
    # Specify the S3 bucket name
    bucket_name = 'jm-email-marketing' # Replace with your bucket name

    try:
        # Retrieve the CSV file from S3
        csv_file = s3_client.get_object(Bucket=bucket_name, Key='contacts.csv')
        lines = csv_file['Body'].read().decode('utf-8').splitlines()
        
        # Retrieve the HTML email template from S3
        email_template = s3_client.get_object(Bucket=bucket_name, Key='email_template.html')
        email_html = email_template['Body'].read().decode('utf-8')
        
        # Parse the CSV file
        contacts = csv.DictReader(lines)
        
        for contact in contacts:
            # Replace placeholders in the email template with contact information
            personalized_email = email_html.replace('{{FirstName}}', contact['FirstName'])
            
            # Send the email using SES
            response = ses_client.send_email(
                Source='you@yourdomainname.com',  # Replace with your verified "From" address
                Destination={'ToAddresses': [contact['Email']]},
                Message={
                    'Subject': {'Data': 'Your Weekly Tiny Tales Mail!', 'Charset': 'UTF-8'},
                    'Body': {'Html': {'Data': personalized_email, 'Charset': 'UTF-8'}}
                }
            )
            print(f"Email sent to {contact['Email']}: Response {response}")
    except Exception as e:
        print(f"An error occurred: {e}")


```
</details>


6. Grab this code then come back to the Lambda function and just replace everything
7. Make sure to replace your `bucket_name` `email_template` `contact_csv_file` and `you@yourdomainname.com` with your information if you named them differently with mine


![Screenshot 2024-03-02 at 17 56 03](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/e2794f4d-f117-4b5d-8b7b-ab8548b26074)



8. When copy and paste the Python and update it with your information, click on "Deploy"


![Screenshot 2024-02-29 at 16 18 55](https://github.com/julien-muke/Tiny_Tales_Mail/assets/110755734/bb041642-8f03-47c8-9fb5-f59e92eec7dd)
