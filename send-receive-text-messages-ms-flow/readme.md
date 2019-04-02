## Send and receive text messages in Microsoft Flow

As the software development landscape matures, levels of abstraction keep getting higher. Every generation of technology raises the starting point a notch, enabling developers to create more and more interesting things while "boilerplate" or setup tasks become increasingly automated.

Nowadays, with tools like [Flow](https://flow.micrsoft.com) and [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview), we can create some pretty cool integrated solutions without even having to write code.

Microsoft Flow is becoming a go-to no-code tool for authoring business process automations in the Microsoft ecosystem, replacing the old SharePoint Designer tool we all hated so much. In this blog post we'll explore sending and receiving text messages via Microsoft Flow and integrating them into other systems to create fast, mobile-ready business applications.

### What you'll need

You'll need a few things set up in order to follow along:

* You'll need an Office 365 license that includes Flow and Email
* You'll need a [Twilio](https://www.twilio.com/) account. You can create a [free trial account](https://www.twilio.com/try-twilio) that includes a set amount of free credit to play around with. Twilio is a paid service, so you'll need to pay for using it in real applications, but the free trial comes with (I think) $15 dollars worth of credit, and you can send and receive a couple thousand text messages with that amount, so you don't have to commit to anything until you're sure about the solution.


### Building the solution

We'll follow four steps to build out our sample Flow:
  1. Create a shell HTTP-triggered Flow
  2. Set up a webhook for incoming texts against our Twilio number
  3. Send ourselves an email with the text content
  4. Send ourselves a text message repeating the incoming message back to us
  
  
  ### Create an HTTP-Triggered Flow
  Go to [Flow](https://flow.micrsoft.com) and create a new Flow with an HTTP trigger.  Use the Create From Blank template and search using "HTTP" until you find the "When an HTTP request is received"  trigger, and select it.
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/http_trigger.png "HTTP Trigger")
  
  
  Our trigger will populate on the design surface. We won't be able to see the trigger's URL until after we save the Flow, and we don't know what the post body schema is going to be, so we'll leave that blank for now.  Also, we need at least one action before we can save the Flow, so let's just create a variable to hold the POST body of the trigger request. We'll use it to generate our schema after we invoke our webhook for the first time.  I used the Initialize Variable action, named the variable "Body", set its data type as Object, and initialized it to the "Body" element from the trigger request.
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/flow_stub.png "Flow Stub")

  
  Now give your Flow a name and save it. I named mine "Twilio Trigger" and note that once the Flow saves, the URL is populated in the action. Put this on your clipboard as we'll use it in the next step.
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/flow_stub_saved.png "Flow Stub2")
  
  
  
  ### Set up our Webhook
  Once you've set up your Twilio trial account, you'll need to create a SMS Project and a phone number. I do not find Twilio's user interface easy to use, and I usually have to stumble around a bit before I can get anything done. But you'll need to create a new project of type "Programmable SMS" and create a new phone number inside that project. To create a new number, go to https://www.twilio.com/console/phone-numbers/incoming and click the plus sign to obtain a new number. Once you have the number, go ahead and click it.
  
  Note: for trial accounts, you will only be able to send and receive texts between your Twilio number and the phone you use to set up the trial.
  
  Once you've clicked the number, look for the "Messaging" section and look for the "A message comes in" line. Paste your Flow URL into the text box and leave the defaults on the two dropdowns ("Webhook" and "Post"), and click Save.
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/webhook.png "webhook")
  
  Your webhook is now pointing to your new Flow. Send a text message to the Twilio number from the phone you used to set up your trial, and navigate to the Flow you built earlier. If you've done everything correctly, you should see one successful run of your Flow.
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/flow_succeeded.png "Flow First Run")
  
  
  If you do not see any runs, then your webhook is misconfigured. If you see a run and it's failed, then something has gone wrong with the Flow.
  
   ### Complete the Flow
  
  But assuming all went well, if you drill into the Flow run you can see the POST data sent to the Flow in the Body variable we set up.
    
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/body_value.png "Body Value")
   
  
  Next we'll copy that text and use it to generate our expected body schema for the HTTP Trigger. Put the Flow in edit mode, open up the HTTP trigger, click "Use sample payload to generate schema", and paste in the text from the Body variable:
  
  
  ![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/body_schema_trigger.png "Body Schema Trigger")
    
  
  That body JSON looks something like this:
  
  ``` json
  {
  "$content-type": "application/x-www-form-urlencoded",
  "$content": "VG9Db3VudHJ5PVVTJlRvU3RhdGU9REMmUU2MDlhN2EyM2IwOWQ4MjQxNmU4NTg3OCZBY2NvdW50U2lkPUFDOGNlOTQwOTM1ZDU5MDBjY2YxNzdmMDQzZjc4NDgyYWEmRnJvbT0lMkIxNTE3Mjk1OTgwNiZBcGlWZXJzaW9uPTIwMTAtMDQtMDE=",
  "$formdata": [
    {
      "key": "ToCountry",
      "value": "US"
    },
    {
      "key": "ToState",
      "value": "DC"
    },
    {
      "key": "SmsMessageSid",
      "value": "SM11d18181e323232323d82416e85234"
    },
    {
      "key": "NumMedia",
      "value": "0"
    },
    {
      "key": "ToCity",
      "value": ""
    },
    {
      "key": "FromZip",
      "value": ""
    },
    {
      "key": "SmsSid",
      "value": "SM11d18181e609a74244242416e85987"
    },
    {
      "key": "FromState",
      "value": "MI"
    },
    {
      "key": "SmsStatus",
      "value": "received"
    },
    {
      "key": "FromCity",
      "value": ""
    },
    {
      "key": "Body",
      "value": "Hello there"
    },
    {
      "key": "FromCountry",
      "value": "US"
    },
    {
      "key": "To",
      "value": "+12028738201"
    },
    {
      "key": "ToZip",
      "value": ""
    },
    {
      "key": "NumSegments",
      "value": "1"
    },
    {
      "key": "MessageSid",
      "value": "SM11d18181e21212121212416e85456"
    },
    {
      "key": "AccountSid",
      "value": "ACxxxxxxxxxxxxxxxxxxa"
    },
    {
      "key": "From",
      "value": "+15172953322"
    },
    {
      "key": "ApiVersion",
      "value": "2010-04-01"
    }
  ]
}
  
  ```
  
  Notice that all the data we care about - namely the sender and the message itself - are buried in an array of key-value pairs. To get at this data from our Flow, we need to loop through all the pairs and check until we find what we're looking for. It's not ideal but it'll work. What we'll do is loop through the keys, look for the one named "Body", store the value of Body in a variable.
  
  We'll add a condition step, and inside the "choose a value" box we'll select "key" from the Dynamic Content selector. When we do this, Flow will automatially wrap that condition inside an Apply To Each iterator. When "Key" is equal to "Body" we'll store the value in a string variable, and then outside the loop we'll email the text message to ourselves, completing the Flow.
  
Here are the actions to complete the Flow:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/text_msg_variable.png "Flow 1")


![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/apply_to_each.png "Flow 2")


![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/send_email.png "Flow 3")


### Testing
Now let's send another text to the Twilio number. If all goes well, the Flow trigger again and light up green, and you'll have the text content in your Email inbox.

### Sending Text Messages
Sending a text message is much more straightforward.  There is a standard Twilio connector in Flow, and you need to set up your initial connection parameters before fleshing out the action.  To do this we need to grab our account SID and your auth token.  You can get these from the Twilio dashboard at www.twilio.com/console 

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/twilio_project_settings.png "Twilio settings")

## Setting up the Twilio connector

The first time you set up any Twilio integrations in Flow you'll be prompted to flesh out your connector with the Account ID and auth token. Add a new action, search for "Twilio" and select the "Send Text Message" action.

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/twilio_action_1.png "Twilio action 1")

If this is the first Twilio integration you've done on your tenant, Flow will prompt you to set up your connector. Give it a name and enter your Account ID and Auth Token from the Twilio portal.

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/twilio_action_2.png "Twilio action 2")

After configuring the connection you'll be able to fill out the action details. The 'From' phone number will be a dropdown list of all the phone numbers associated with the account. The 'To' number is a plain text number, but if you have a trial account you'll only be able to successfully message the number linked to your account, and the text is also a plain text field. Of couse you can use Flow dynamic data and functions inside these text fields.

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/send-receive-text-messages-ms-flow/twilio_action_3.png "Twilio action 3")


### Possibilities
Having the ability to interact with text messaging in Flow opens up a wide range of possibilities. Flow is a connection machine, and enables us to integrate with a wide array of services. Throw in some Azure functions and the power of Azure service offerings and we can implement anything we can imagine: AI-driven chat bots, reservation systems, bulk reminders and notifications, social media monitoring, critical IT systems monitoring, just to name a few off the top of my head.



### Conclusion
We can easily create mobile-ready integrations with Microsoft Flow that can send and receive text messages. Receiving text messages involves a webhook and a bit of setup, and sending text messages is a simple action out of the box.





  
  
