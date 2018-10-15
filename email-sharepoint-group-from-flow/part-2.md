# Emailing a SharePoint Group from Flow, part 2: The custom connector

In part one of this blog I showed how email all users from a SharePoint group using Microsoft Flow. In part two I'll show how to reuse this Flow from other Flows using a Custom Connector.

The "Email a SharePoint Group" Flow from part one is a standalone Flow that does one thing - emailing the SharePoint group. It's invoked via an HTTP trigger, which means that to consume it from another Flow, you need to wire up a Request action with the appropriate URL and POST data. This isn't ideal for two reasons: It requires knowing the arcane details of the endpoint and how to set it up in a Flow, and it necessitates exposing the endpoint URL to anyone needing to use it from a Flow.

In this blog we'll abstract away the details of that action by wrapping it in a Custom Connector.

In other words, we're going to go from this:

<image of raw http action>


to this:

<image of custom connector in action>

### What you'll need
In order to follow along you'll need the following:
1. The Flow you created in part one (link)
2. Postman (you can downloa dit here: https://www.getpostman.com/

### Creating the Custom Connector

There are a couple different ways to generate a Custom Connector for Flow. We'll use a Postman collection to help us generate the API definition that Flow needs to set up the connector.

We'll use the following data as the inputs for our Postman collection. Remember, our Flow is onvoked via a URL and :
URL: https://prod-23.westus.logic.azure.com:443/workflows/0184591fa2bd4547b5ed01046cb51d18/triggers/manual/paths/invoke?api-version=2016-06-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=qdPtgtY0Rn8vBAYNBLeISLh_I2s1etjWw7QrOZ_TO8Q

POST Body:

{
    "siteUrl": "https://m365x981313.sharepoint.com/sites/Home",
    "groupName": "Home Members"
}

In Postman, create a new collection by clicking "New", then "Collection" in the top menu.

<image>
  
  Give it a name and click "Create".
  
  <image>
  
  Next, "Add Requests"
  
  <image>
  
  Next, "Open in Builder"
  
  <image>
  
  Change the method to "POST" and enter in the URL. Notice the parameters get parsed out of the URL and added to the params section of the request builder.
  
  <image>
  
  In the body section, click the "Raw" radio button and paste in our POST data:
  
<image>
  
Set up your Content-Type and Accept headers as shown:

<image>
  
Click "Send to send the rqquest. If you've done everything properly, your Flow should have been called and executed successfully.

<image>
  
  
  
  
  
  
  

