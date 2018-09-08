# Email a SharePoint Group from a Flow

tech community conversation: https://techcommunity.microsoft.com/t5/PowerApps-Flow/Email-a-SharePoint-Group-from-Flow/td-p/99737

One of the greatest features of Flow is the ability to send emails. But there isn't a native way to send emails to SharePoint groups. Anyone who's done substantial work with workflows knows that emailing individual users is fraught with issues. People leave companies or change roles, and if a workflo21w explicitly names an individual as an email recipient, any personnel change will break existing processes, necessitating rework. The best practice in this situation is to email a group, and manage group membership as needed.

Flow doesn't allow an action to do this, and based on this Tech Community conversation (link) lots of people are asking for it. Recently Microsoft provided the ability to issue raw REST requests against SharePoint from a Flow, and indeed we can use this pattern to fetch users from a group. Once we have that list of users we can them email them using Flow.

Creating a reusable Flow

I'm going to do something a little different with this Flow. I have a number of situations where I need to email SharePoint groups, and I don't want to have to do this work every time the requirement comes up. What I'm going to do instead is create a standalone Flow that only emails a group, that I can call from other Flows.

One way to do this is to author the Flow using an HTTP trigger. 

Setting up the REST Request

