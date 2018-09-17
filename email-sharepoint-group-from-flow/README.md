# Email a SharePoint Group from a Flow

tech community conversation: https://techcommunity.microsoft.com/t5/PowerApps-Flow/Email-a-SharePoint-Group-from-Flow/td-p/99737

One of the greatest features of Flow is the ability to send emails. But there isn't a native way to send emails to SharePoint groups. Anyone who's done substantial work with workflows knows that emailing individual users is fraught with issues. People leave companies or change roles, and if a workflo21w explicitly names an individual as an email recipient, any personnel change will break existing processes, necessitating rework. The best practice in this situation is to email a group, and manage group membership as needed.

Flow doesn't allow an action to do this, and based on this Tech Community conversation (link) lots of people are asking for it. Recently Microsoft provided the ability to issue raw REST requests against SharePoint from a Flow, and indeed we can use this pattern to fetch users from a group. Once we have that list of users we can them email them using Flow.

## Creating a reusable Flow

I'm going to do something a little different with this Flow. I have a number of situations where I need to email SharePoint groups, and I don't want to have to do this work every time the requirement comes up. What I'm going to do instead is create a standalone Flow that only emails a group, that I can call from other Flows.

One way to do this is to author the Flow using an HTTP trigger. That is, the Flow will listen on an HTTP endpoint, and be invoked whenever a request is made to it. The advantage of this trigger is that it can be invoked from pretty much anywhere: another Flow, an Azure Function, a console app, a mobile app, Postman, anything that can issue web requests can take advantage of this service.

Because we want this Flow to be flexible and configurable, able to email any group on any site in our tenant, we're going to pass in the name of the group and the site URL to the Flow via JSON. Here's how the initial setup of the Flow looks so far:

(screen shot of flow shell with HTTP trigger and response

## Setting up the REST Request

Like I mentioned earlier, Flow gives us the ability to issue REST requests against SharePoint. If you've never worked with REST or with web services in general, it might seem a little daunting. But in Flow, the most difficult part of the process, authentication, is already handled for you, so all you have to do is craft the requests and parse the responses. Flows run under the security context of the user who authored the Flow, and the authentication headers will be automatically provided by Flow. (Note - there are some security implications to consider when authoring Flows - I'll discuss those at the end of this article.)

If you're not already familiar with the SharePoint REST interface, take a few minutes to read up on the user, group, and role REST documentation from Microsoft: https://msdn.microsoft.com/en-us/library/office/dn531432.aspx.  

We have some options around how to specify the group we're using - either by its name or by its numeric ID. We'll be using the group name in this example, because it seems like it would be a little more user friendly. Our REST request is going to query for all the users inside the group specified in the request, inside the site specified by the request.  It's going to look like this:

GET <site url>/_api/web/sitegroups/getbyname(<group name>)/users
  
 Set up the HTTP Request to SharePoint Action
 
 Parse the returned JSON
 [code]
 {
  "value": [
    {     
      "Email": "AdeleV@M365x692098.OnMicrosoft.com"
    },
    {     
      "Email": "GradyA@M365x692098.OnMicrosoft.com"
    }
  ]
}
[/code]

## Parse the JSON
Now we've retrieved the data from SharePoint representing the group users. But the Flow only sees this as a string, even though it's JSON structured data. We need to tell the Flow to treat this as structured JSON, and to do this, we need the Parse JSON Action.

So, after the SharePoint HTTP call, drop a Parse JSON action onto your design surface. Set it up to use the Body from the SharePOint HTTP call as its content.  For the schema, click the "use sample payload" link and paste this into it:

{
    "d": {
      "results": [
        {               
          "Email": "AdeleV@mytenant.OnMicrosoft.com"
        }
      ]
    }
}

So now your Parse JSON action looks like this:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/pic3.png "Parse JSON")

## Build the recipients string

## Set up the email action

## Test the Flow


## Call from another Flow


  
 

