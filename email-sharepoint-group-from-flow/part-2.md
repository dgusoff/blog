# Emailing a SharePoint Group from Flow, part 2: The custom connector

In [Part One](https://derekgusoff.wordpress.com/2018/10/04/email-a-sharepoint-group-from-a-flow) of this blog I showed how email all users from a SharePoint group using Microsoft Flow. In part two I'll show how to reuse this Flow from other Flows using a Custom Connector.

The "Email a SharePoint Group" Flow from part one is a standalone Flow that does one thing - emailing the SharePoint group. It's invoked via an HTTP trigger, which means that to consume it from another Flow, you need to wire up a Request action with the appropriate URL and POST data. This isn't ideal for two reasons: It requires knowing the arcane details of the endpoint and how to set it up in a Flow, and it necessitates exposing the endpoint URL to anyone needing to use it from a Flow.

In this blog we'll abstract away the details of that action by wrapping it in a Custom Connector.

In other words, we're going to go from this:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/call-flow.png "Call Flow from another flow")


to this:

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/use-custom-action.png? "the flow in use")

### What you'll need
In order to follow along you'll need the following:
1. The Flow you created in [Part One](https://derekgusoff.wordpress.com/2018/10/04/email-a-sharepoint-group-from-a-flow)
2. Postman (you can download it here: https://www.getpostman.com/

### Creating the Custom Connector

There are a couple different ways to generate a Custom Connector for Flow. We'll use a Postman collection to help us generate the API definition that Flow needs to set up the connector.

We'll use the following data as the inputs for our Postman collection. Remember, our Flow is onvoked via HTTP request to: https://prod-23.westus.logic.azure.com:443/workflows/0184591fa2bd4547b5ed01046cb51d18/triggers/manual/paths/invoke?api-version=2016-06-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=qdPtgtY0Rn8vBAYNBLeISLh_I2s1etjWw7QrOZ_TO8Q

POST Body:
````json
{
    "siteUrl": "https://mytenant.sharepoint.com/sites/Home",
    "groupName": "Home Members"
}
````

I won't go into detail about how to create a Postman collection, (you can read about it [here](https://www.getpostman.com/docs/v6/postman/collections/creating_collections)), but in a nutshell you'll want to set up your POST request to your Flow URL and add the JSON to the body. Don't forget to set the Content-Type to application/json, and choose to export a V1 collection version.

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/Postman.png? "Postman collection")

Once you've exported the collection, you'll be ready to move into Flow and set up the custom connector.

## Setting up the Custom Connector

In Flow, click the gear icon in the top bar and select "Custom Connectors", then "Create Custom Connector" and select "Import a Postman Collection".

Once the collection imports, you'll see a four-step wizard where you'll configure your connector.

In step 1, General, you have options to change the default color and logo. You can accept the defaults or set a custom look for the connector. We'll stick with the defaults here.

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/step-1.png? "step 1")

In step 2, Authentication Type, leave the default at "No Authentication"


![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/step-2.png? "step 2")

Step 3, Definition, is the most involved. Here you can tweak your actions and add new ones. We'll have one action already defined, and you'll see the four query string parameters passed into the POST request. We'll want to hide these from the Flow author, so we'll click on each one, select "Edit", and set its visibility to "Internal"

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/edit-param-1.png? "step 3")


![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/edit-param-2.png? "step 3")

Step 4, Test, will require we click the "Create Connector" button and actually consume the connector. We should see all our parameters, and if we add an appropriate POST body and click "Test Operation", we should get a 202 response back. Also we should see that our souce Flow was triggered and ran successfully.

## Create the Flow

Now we're ready to create our Flow and consume the custom connector. Here I'll just create a Button-triggered Flow and search my actions using the word "Email". You'll see I got two custom connectors back because I created two of them in my dev sandbox.

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/search-connector.png? "search connector")

When you configure the action it'll prompt you for a site url and a group name where the email will be sent. If I had set the visibility of the Content-Type parameter to "Hidden" that would have been gone from the interface as well.

![alt text](https://github.com/dgusoff/blog/blob/master/email-sharepoint-group-from-flow/use-custom-action.png? "the flow in use")

Now you have a custom connector that you can re-use in all your flows to email any SharePoint group in any site your Flow author has access to. You also have a pattern for creating sub-Flows and a way to execute them in a scalable manner.

## Homework

Try to create your own custom connector to email a SharePoint group. Pass the subject line and email body as parameters. Let me know how it sent in the comments.
















  
  
  
  
  
  
  

