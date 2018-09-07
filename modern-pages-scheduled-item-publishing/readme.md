# Scheduled Item Publishing in Modern SharePoint Site Pages

For those of us used to the rich content publishing features in the old "Classic" SharePoint Publishing model, the new Modern experience takes a little getting used to. For example, until very recently there wasn't much at all in the way of content approval to say nothing of the more advanced features like scheduled item publishing.  This month (August 2018) Microsoft [announced](https://techcommunity.microsoft.com/t5/SharePoint/Announcing-SharePoint-page-approvals/td-p/215466) a new solution for page approvals utilizing Flow under the covers, which promises to enable the sort of funcitonality we were used to in Classic publishing sites.

One of the Classic features my client use all the time is scheduled item publishing. In Classic Publishing libraries, this was a setting on the library that allowed content approvers to set a date and time when the page would be published, and it worked pretty well. This feature is missing on Modern, but with the new Flow capability, we can bring it back.

## Enabling the Approval Flow

On the Modern Site Pages library, in the Flow dropdown, we not have the option "Configure Page Approval Flow".

![Configure Page Approval](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic1.png?raw=true "Configure Page Approval")

If we choose the option to do this we get a slide out panel that allows us to set up our list of approvers.

![List of Approvers](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic2.png?raw=true "List of Approvers")

![List Approvers](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic3.png?raw=true "List Approvers")

Now, once we've done this, we get the option to "Submit For Approval". Clicking this option opens an initiation form where we can kick off the approval process. The users specified in the flow configuration will get the Approval email, and on approval the page will get published.


## Updating the Flow to support scheduled item publishing

To enable scheduled item publishing we need to do two things. First, we need a way to specify the date on which we want to publish. An easy way to do this is to add a Date field onto the Site Pages library. Use a custom content type that inerits from Site Page add this field there.

The second thing we need to do is modify the Flow to add a "Delay Until" action, using the Date field we added to our content type. We'll put this inside the "Yes" branch of the condition that follows the approval result. It looks something like this:

![Yes Branch](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic4.png?raw=true "Yes Branch")


![Delay Until](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic5.png?raw=true "Delay Until")

Now, when we submit a page for approval, we can see the Flow waiting until the publish date and time before proceeding on to publish the page.

![Delaying](https://github.com/dgusoff/blog/blob/master/modern-pages-scheduled-item-publishing/pic6.png?raw=true "Delaying")

## Some things to be aware of

All in all, this process works pretty well, but the whole Approval Flow business has some rough edges, and some things that don't quite work as well as they should.

### There's no way to see configured Approval Flows for a library.
SharePoint will happily allow you to configure many approval Flows on a single library, because the UI has no way to show you the Flow(s) that have already been configured. Unless there's a way I'm not aware of, the only way to see your approval Flow is to go directly to [Flow](https://flow.microsoft.com) and pick your Flow from the list.  Which can be a problem, especially considering...

### Flows don't scale well.
If you have 200 sites, you'll need to configure 200 separate instances of the Approval Flow. Obviously this is a governance and maintenance nightmare, and woe be upon the person who has to do all this grunt work, because Flow creation is not easily automated. One possibility might be to create a master Flow that all the other Flows call via HTTP request, and simply update each Approval Flow to launch that.

### You're stuck naming individual people as approvers, no SharePoint or AD groups
The UI doesn't allow for this, but the approval email actions don't allow this, so I think that's the cause of the limitation. We can do a little more work inside the Flow to fetch a SharePoint group but that's maybe a topic for another time.

### Flows crap out after 30 days
If 30 days transpire after the invocation of a Flow, the Flow will just stop working. This seems to be a hard limit. So when submitting your Approval Flows, be aware of this limitation.

## Wrapping it up
SharePoint's Modern initiative replaced a mature, battle-hardened system in Classic Publishing, and naturally there would be some funcitonality gaps to close. Microsoft is working quickly to address this, and it seems reasonable to expect further evelopments along this path. For now, at least we now have a way to deliver scheduled item publishing to our clients in Modern SharePoint.
