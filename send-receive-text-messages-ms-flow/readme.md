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
  
  
  ### Set up our Webhook
  
  
  ### Complete the Flow
