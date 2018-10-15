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

There are a couple different ways to generate a Custom Connector for Flow. We'll use a Postman collection 
