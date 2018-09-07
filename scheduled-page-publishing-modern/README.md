Moving into Modern SharePoint from a more traditional Publishing infrastructure means saying goodbye to  a rich, mature set of web content features and replacing them with a framework that has a lot of potential but lacks a few of the features we've been used to.

One of those features is Scheduled Item Publishing, which allows content authors to approve content and set a time when that content would publish and become available to the general population. This is useful for coordinating messaging with other initiatives, such as earnings reports, breaking company news or major announcements. I have a few clients that use this feature quite broadly, and its absence in Modern Site Pages is a stumbling block to Modern adoption.

It turns out, however, that although it's not a native feature in Modern, we can easily enable the same functionality using a Page Approval Flow.

Page Approval Flows were launched back in August (https://techcommunity.microsoft.com/t5/SharePoint/Announcing-SharePoint-page-approvals/td-p/215466) and allow site owners to incorporate a Flow as part of its standard page approval process. You can initialize an approval Flow from the Site Pages library:




Clicking "Configure a Page Approval Flow" will begin the setup process on the Flow.  










Finishing the creation process will generate a boilerplate Flow but won't actually redirect you to it (more on this later). But now that the Flow has been created, if we create a new Page, we'll get an additional step to "Submit for Approval"










## Enabling Scheduled Item Publishing


### Setting up our metadata
Now that our Flow is set up we can start enabling the scheduled publishing. Create a new Content Type derived from Site Page, and add a new field to it called "PublishingDate" (or whatever). Make sure to also include the time as part of the field value. Make your new Content Type the default for Site Pages.

### Hooking up the Flow
Now, let's go to Flow, and find the Flow that we created earlier, and put it in Edit mode.Expand the condition that checks the approval status given by the approver. It should look like this:




Inside the "Yes" block, add a new action of type "Delay Until", and set the PublishingDate field as the Timestamp value for the action. Your Yes block should now look like this:




When the Flow runs, and the publish time has not yet come, you'll see the Flow waiting on the Delay Until step. After it completes you'll see how long the Flow had to wait until the Delay Until allowed it to pass. In this instance, it was about two hours.




It took maybe ten minutes to set up this Flow from the boilerplate, so there was not that much effort at all involved in getting it to work.

## Things to be aware of
There are some strange things to be aware of when using this approach. I hope Microsoft is able to address at least some of these in the future, but as of today (September 2018) there are a few caveats to this pattern

### Flows don't scale well. 
There isn't any way that I'm aware of to easily set up a Flow to receive events form multiple Site Pages libraries. This means that if you have 100 sites where you need scheduled page publishing, you need to implement the identical Flow 100 times. Not ideal. If you ever need to make a simple change (like change an approver), you're going to have a lot of soul-crushing tedious work ahead of you.

### You can't see the approval Flows from the Pages library
There's no way to tell from the Pages library if there's already an Approval Flow set up on the library. In fact, SharePoint will happily allow you to create many Approval Flows on the same library. When this happens you'll be prompted to select which Flow you want to run when you submit for approval. Again, not ideal, content authors probably shouldn't be doing this. 

Oh, and remember that you have 100 approval Flows? Well, good luck finding the one you just created in the Flow portal.

### Flows crap out after 30 days
If 30 days transpire after the invocation of a Flow, the Flow will just stop working. This seems to be a hard limit. So when submitting your Approval Flows, be aware of this limitation.
