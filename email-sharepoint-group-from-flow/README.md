# Email a SharePoint Group from a Flow

tech community conversation: https://techcommunity.microsoft.com/t5/PowerApps-Flow/Email-a-SharePoint-Group-from-Flow/td-p/99737

One of the greatest features of Flow is the ability to send emails. But there isn't a native way to send emails to SharePoint groups. Anyone who's done substantial work with workflows knows that emailing individual users is fraught with issues. People leave companies or change roles, and if a workflow explicitly names an individual as an email recipient, any personnel change will break existing processes, necessitating rework. The best practice in this situation is to email a group, and manage group membership as needed.

Flow doesn't allow an action to do this, and based on [this Tech Community conversation](https://techcommunity.microsoft.com/t5/PowerApps-Flow/Email-a-SharePoint-Group-from-Flow/td-p/99737) lots of people are asking for it. Recently Microsoft provided the ability to issue raw REST requests against SharePoint from a Flow, and indeed we can use this pattern to fetch users from a group. Once we have that list of users we can them email them using Flow.

## Creating a reusable Flow

I'm going to do something a little different with this Flow. I have a number of situations where I need to email SharePoint groups, and I don't want to have to do this work every time the requirement comes up. What I'm going to do instead is create a standalone Flow that only emails a group, that I can call from other Flows.

One way to do this is to author the Flow using an HTTP trigger. That is, the Flow will listen on an HTTP endpoint, and be invoked whenever a request is made to it. The advantage of this trigger is that it can be invoked from pretty much anywhere: another Flow, an Azure Function, a console app, a mobile app, Postman, anything that can issue web requests can take advantage of this service.

Because we want this Flow to be flexible and configurable, able to email any group on any site in our tenant, we're going to pass in the name of the group and the site URL to the Flow via JSON. Here's how the Flow trigger looks so far:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/pic1.png "HTTP Trigger")

Use this sample payload to generate the JSON schema:

````JSON
{
    "siteUrl": "foo",
    "groupName": "foo"
}
````

## Setting up the REST Request

Like I mentioned earlier, Flow gives us the ability to issue REST requests against SharePoint. If you've never worked with REST or with web services in general, it might seem a little daunting. But in Flow, the most difficult part of the process, authentication, is already handled for you, so all you have to do is craft the requests and parse the responses. Flows run under the security context of the user who authored the Flow, and the authentication headers will be automatically provided by Flow. (Note - there are some security implications to consider when authoring Flows - I'll discuss those at the end of this article.)

If you're not already familiar with the SharePoint REST interface, take a few minutes to read up on the user, group, and role REST documentation from Microsoft: https://msdn.microsoft.com/en-us/library/office/dn531432.aspx.  

We have some options around how to specify the group we're using - either by its name or by its numeric ID. We'll be using the group name in this example, because it seems like it would be a little more user friendly. Our REST request is going to query for all the users inside the group specified in the request, inside the site specified by the request.  It's going to look like this:

````http
GET <site url>/_api/web/sitegroups/getbyname(<group name>)/users
````

Now, drop a "Send an HTTP request to SharePoint" action onto the diesign view after the trigger.  Set it up like this:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/pic2.png "SharePoint request")

OK, so now we've set up out trigger and used the data sent to it to invoke a call to SharePoint's REST interface, which will return the serialized user data as a string. Next we need to transform that into structured data the Flow can use.

## Parse the JSON
Now we've retrieved the data from SharePoint representing the group users. But the Flow only sees this as a string, even though it's JSON structured data. We need to tell the Flow to treat this as structured JSON, and to do this, we need the Parse JSON Action.

So, after the SharePoint HTTP call, drop a Parse JSON action onto your design surface. Set it up to use the Body from the SharePOint HTTP call as its content.  For the schema, click the "use sample payload" link and paste this into it:

````json
{
    "d": {
      "results": [
        {               
          "Email": "AdeleV@mytenant.OnMicrosoft.com"
        }
      ]
    }
}
````

So now your Parse JSON action looks like this:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/pic3.png "Parse JSON")

## Build the recipients string
A collection of recipients in a Flow Email action is represented by a semicolon list of email addresses. Since we now have an JSON array of objects containing these addresses, we now need to loop through the results and add the delimited email addresses into a string variable.

First, let's create the variable and initialize it to an empty string:

![alt text](https://raw.githubusercontent.com/dgusoff/blog/master/email-sharepoint-group-from-flow/init_variable.png "Init Variable")
    

  
 Next we need to loop through the results array. Do do this we add an "Apply to Each" action.

## Set up the email action

## Test the Flow


## Call from another Flow

## About those security implications
You should be logged in using a "service account" when authoring Flows. If you create the Flow using your normal user account, three things will happen. First, the emails will appear to be coming from yout user account; second, the Flow will assume the security context of your account, which means it'll break if your account's permissions con't perform the actions against the specified site. If you leave the company all your Flows will break.  And third, it will be difficult for your colleagues to maintain or even find the Flows you've written. 

So create a "Flow Author" licensed account for this purpose. You can name it whatever makes sense to your organization.


  
 

