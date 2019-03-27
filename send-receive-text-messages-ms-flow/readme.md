## Send and receive text messages in Microsoft Flow

As the software development landscape matures, levels of abstraction keep getting higher. Every generation of technology raises the starting point a notch, enabling developers to create more and more inetresting things while "boilerplate" or setup tasks become increasingly automated.

Nowadays, with tools like Flow (link) and Azure Logic Apps (link), we can create some pretty cool integrated solutions without even having to write code.

Microsoft Flow is becoming a go-to no-code tool for authoring business process automations in the Microsoft ecosystem, replacing the old SharePoint Designer tool we all hated so much. In this blog post we'll explore sending and receiving text messages via Microsoft Flow and integrating them into other systems to create fast, mobile-ready business applications.

### What you'll need

You'll need a few things set up in order to follow along:

* You'll need an Office 365 license that includes Flow and Email
* You'll need a Twilio (link) account. You can create a free trial account (with no card!) (https://www.twilio.com/try-twilio) that includes a set amount of free credit to play around with. Twilio is a paid service, so you'll need to pay for using it in real applicaitons, but the free trial comes with (I think) $15 dollars worth of credit, and you can send and receive a couple thousand text kessages with that amount, so you don't have to commit to anything until you're sure about the solution.


### Building the solution

We'll follow three steps to build out our sample Flow:
  1. Create a shell HTTP-triggered Flow
  2. Set up a webhook for incoming texts against our Twilio number
  3. Finish off the Flow and send ourselves an email with the text content
  
  
  ### Create an HTTP-Triggered Flow
  Go to Flow (link) and create a new Flow with an HTTP trigger.  Use the Create From Blank template and search using "HTTP" until you find the "When an HTTP request is received"  trigger, and select it.
  
  (image)
  
  Our tirgger will populate on the design surface. We won't be able to see the trigger's URL until after we save it, and we don't know what the post body schema is going to be, so we'll leave that blank for now.  Also, we need at least one action before we can save the Flow, so let's just create a variable to hold the POST body of the trigger request. We'll use it to generate our schema after we invoke our webhook for the first time.  I used the Initialize Variable ction, named the variable "Body", set its data type as Object, and initialized it to the "Body" element from the trigger request.
  
  (flow stub)
  
  Now give your Flow a name and save it. I named mine "Twilio Trigger" and note that once the Flow saves, the URL is populated in the action. Put this on your clipboard as we'll use it in the next step.
  
  (flow stub saved) 
  
  
  
  
  ### Set up our Webhook
  Once you've set up your Twilio trial account, you'll need to create a SMS Project and a phone number. I do not find Twilio's user interface easy to use, and I usually have to stumble around a bit before I can get anything done. But you'll need to create a new project of type "Programmaable SMS" and create a new phone number inside that project. To create a new number, go to https://www.twilio.com/console/phone-numbers/incoming and click the plus sign to obtain a new number. Once you have the number, go ahead and click it.
  
  Note: for trial accounts, you will only be able to send and receive texts between your Twilio number and the phone you use to set up the trial.
  
  Once you've clicked the number, look for the "Messaging" section and look for the "A message comes in" line. Paste your Flow URL into the text box and leave the defaults on the two dropdowns ("Webhook" and "Post"), and click Save.
  
  Your webhook is now pointing to your new Flow. Send a text message to the Twilio number from the phone you used to set up your trial, and navigate to the Flow you built earlier. If you've done everything correctly, you should see one successful run of your Flow.
  
  (flow_run)
  
  If you do not see any runs, then your webhook is misconfigured. If you see a run and it's failed, then something has gone wrong with the Flow.
  
  But assuming all went well, if you drill into the Flow run you can see th epOST data sent to the Flow in the Body variable we set up.
  
  (body value)
  
  Next we'll copy that text and use it to generate our expected body schema for the HTTP Trigger. Put the Flow in edit mode, open up the HTTP trigger, click "Use smaple payload to generate schema", and paste in the text from the Body variable:
  
  (body_schema_trigger)
  
  That body JSON looks something like this:
  
  link to json
  
  
  
  ### Complete the Flow
