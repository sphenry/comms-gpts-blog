# Send Email from Your GPTs in 15 Mins with Azure Communication Services, and No Code!

[ChatGPT 'GPTs'](https://openai.com/blog/introducing-gpts)  are amazing! You can create custom versions of ChatGPT that combine instructions, extra knowledge, and any combination of skills. Wouldn't it be great is you're new assistant could talk to the rest of the world? Sending emails through Azure Communication Services is surprisingly straightforward and requires no coding expertise. Here's how you can set it up in just 15 minutes.

## Step 1: Create an Azure Communication Service Resource
First, you need to create an Azure Communication Service Resource. Follow the instructions on the [Create Azure Communication Services resource](https://learn.microsoft.com/en-us/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp) to get started.

Copy the `connection string` from the `Keys` blade of your Communication resource. You'll need it later.
![Alt text](image-1.png)
## Step 2: Create an Email Service
Next, create an Email Service by following this [Create the Email Communications Service resource using portal](https://learn.microsoft.com/en-us/azure/communication-services/quickstarts/email/create-email-communication-resource).
![Alt text](image-2.png)

## Step 3: Set Up a Free Azure Subdomain
Back in the Communication resource, select 'Try Email'. Under 'Select a domain', choose 'Set up a free Azure subdomain' and select the email service you just created.
![Alt text](image-3.png)
Use the 'Try email' feature to ensure that your setup is working correctly. Copy and save somewhere the 'Send email from' address (it starts with `donotreply`). You'll need it later.
![Alt text](image-11.png)

## Step 4: Create a Logic App Resource
Now, search and create a new 'Logic App' resource in your Azure portal. In the Logic App designer, select 'When an HTTP request is received'.
![Alt text](image-4.png)

## Step 5: Define JSON Payload
In the designer, click 'use sample payload', and add a JSON payload with "to", "subject", and "content". Then click "done".
```json
{
  "to" : "test@example.com",
  "subject" : "subject" ,
  "content" : "email body"
}
```
![Alt text](image-5.png)

## Step 6: Add Send Email Operation
Add a 'New step', search for "communication email" and add the 'send email' operation. Give it the connection name 'Email' and paste the Connection string from the Communication resource Keys blade. Click 'Create'.
![Alt text](image-6.png)

## Step 7: Set Email Parameters
Set the 'from' field to the email address found on the 'Try email' page. Set 'To Email' to the 'to' field, 'Subject' to 'subject'
![Alt text](image-7.png)

For the Body, click in the text area, then on the right, select 'see more' and add 'content'.
![Alt text](image-12.png)


## Step 8: Test Your Setup
Click save, then 'Run Trigger with Payload'. Test it with a JSON payload containing "to", "subject", and "content" fields.
```json
{
  "to" : "test@example.com",
  "subject" : "subject" ,
  "content" : "<p><b>email body</b></p>"
}
```
![Alt text](image-8.png)

## Step 9: Integrate with OpenAI GPTs
At [OpenAI GPTs editor](https://chat.openai.com/gpts/editor), click 'new GPT' and 'configure'. Name it "Email Sender" and set the description and instructions as mentioned.
```
Compose wonderful emails and send them
```
```
You are a GPT the help people write emails and send them. Before you send any emails show a preview and confirm that you will send it
```
![Alt text](image-9.png)

## Step 10: Add Actions and JSON Schema
Click 'Add actions' in your GPT configuration. Enter the following JSON:
```json
{
  "openapi": "3.1.0",
  "info": {
    "title": "Send Message API",
    "description": "API for sending a message to a specified email address.",
    "version": "v1.0.0"
  },
  "servers": [
    {
      "url": "https://<<logic app url>>.azure.com"
    }
  ],
  "paths": {
    "/workflows/9a83a...&sig=ePEB-srbi-XXXXXXXXXXXXXXXXXXXXXXXX" : {
      "post": {
        "description": "Send a message to a given email address",
        "operationId": "SendMessage",
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "properties": {
                  "to": {
                    "type": "string",
                    "format": "email",
                    "description": "Email address of the recipient"
                  },
                  "subject": {
                    "type": "string",
                    "description": "The message subject"
                  },
                  "content": {
                    "type": "string",
                    "description": "The body content of the email encoded as escaped HTML"
                  }
                },
                "required": ["to", "message"]
              }
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "components": {
    "schemas": {}
  }
}
```
Go to the 'When a HTTP request is received' block in the Logic App Designer and copy the URL from the HTTP POST URL field. Copy the URL  and split it in two. The first part is the URL from `https` to `:443` goes into the `url` parameter below, everything after that (from `/workflows` to the end) goes in the first field under `path`.
![Alt text](image-14.png)
![Alt text](image-15.png)

Leave Authentication to none, and Privacy Policy blank
## Step 11: Test Your GPT
Finally, try out your GPT in the preview pane to see it in action!

By following these steps, you can easily integrate Azure Communication Services with OpenAI GPTs to send emails effortlessly.
![Alt text](image-10.png)