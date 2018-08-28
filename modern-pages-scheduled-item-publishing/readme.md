# Scheduled Item Publishing in Modern SharePoint Site Pages

For those of us used to the rich content publishing features in the old "Classic" SharePoint Publishing model, the new Modern experience takes a little getting used to. For example, until very recently there wasn't much at all in the way of content approval to say nothing of the more advanced features like scheduled item publishing.  This month (August 2018) Microsoft [announced](https://techcommunity.microsoft.com/t5/SharePoint/Announcing-SharePoint-page-approvals/td-p/215466) a new solution for page approvals utilizing Flow under the covers, which promises to enable the sort of funcitonality we were used to in Classic publishing sites.

One of the Classic features my client use all the time is scheduled item publishing. In Classic Publishing libraries, this was a setting on the library that allowed content approvers to set a date and time when the page would be published, and it worked pretty well. This feature is missing on Modern, but with the new Flow capability, we can bring it back.

## Enabling the Approval Flow

On the Modern Site Pages library, in the Flow dropdown, we not have the option "Configure Page Approval Flow".

(image)

If we choose the option to do this we get a slide out panel that allows us to set up our list of approvers.

(image)

Now, once we've done this, and we've enabled Content Approval on the Pages library (list settings-> versioning settings), we get the option to "Submit For Approval".

(image)

## Updating the Flow to support scheduled item publishing

To enable scheduled item publishing we need to do two things. First, we need a way to specify the date on which we want to publish. An easy way to do this is to add a Date field onto the Site Pages library. Use a custom content type that inerits from Site Page add this field there.

The second thing we need to do is modify the Flow to add a "Delay Until" action, using the Date field we added to our content type.
