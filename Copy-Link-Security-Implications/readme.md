# Copy Link in Modern SharePoint - non-obvious security implications you should know about

Recently I encountered a strange issue in a client's Intranet during the content buildout phase. They'd given read-only access to a group of pilot users, and loaded up their site with pages and links to documents. Then they began to notice that these pilot users appeared to have the ability to delete the documents, and logged a bug with us.

We discovered that the document library had hundreds of files with broken permission inheritance, and that the Everyone principal had been granted Contribute permission on each one of these documents, meaning they could edit and even delete the documents. 

Thinking that some rogue user had inadvertently (or "advertently") shared those documents in error, we ran a script that looped through all the documents in each library and restored the permission inheritance on each one. Then we discovered that the several hundred or so hyperlinks to the documents throughout the system began returning 404s.

Eventually we tracked the issue down to a "feature" of the Copy Link action bar item in Modern SharePoint document libraries. We discovered that Copy Link does a bit more than merely return a link to the document to the user's clipboard.

![The Document Action Bar](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink1.png.png?raw=true "The Document Action Bar")

My client had been using SharePoint's Copy Link functionality to create those links, just as we had taught them to. But what we didn't realize was that clicking Copy Link was actually breaking the security inheritance on the document, and *sharing it* to the entire company.  This was because the tenant settings that drove this functionality were left in their default settings, which inexplicably default to the most permissive - the most insecure - setting.

Check out what happens when you click the button:

![Copy Link Dialog](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink2.png?raw=true "The Copy Link Dialog")

Once you see this dialog, permission inheritance has already been broken and the permission "Anyone with the link can edit" has already been applied. If you select another option, the permission will update - even to the point of reinstating permission inheritance if "People With Existing Access" is selected. Also, the link regenerates, and previously generated links become stale and return 404s.

![Copy Link Options](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink3.png?raw=true "Copy Link Options")

## The link structure will tell the sharing story

If a Copy Link operation results in broken inheritance, it will look different from a link that does not.

A Sharing Link looks like this:
https://m365x692092.sharepoint.com/:w:/g/Ea90HDWefS1BnLLgtVkMNJgBdpUI6LiBC7Kw4pj0g-CIAQ?e=troe2t

..while a non-shared link will look like this:
https://m365x692092.sharepoint.com/:w:/r/Shared%20Documents/CAS/Marketing%20Strategy%20Future.docx?d=w351c74af7d9e412d9cb2e0b5590c3498&csf=1&e=TWfoVC

Note that a sharing link shows the tenant followed by a long string of crap, and the non-sharing link, while also containing its share of trailing junk, also seems to incorporate a physical path as part of its structure. So using this pattern you should be able to tell if a Copy Link resulted in broken inheritance.

## My thoughts on this
You have some options for setting the default behavior of this function, but like I said the default default is the most permissive. The decision to have it behave this way vexes me somewhat. In previous versions of SharePoint it's been difficult and tedious to break permission inheritance through the UI, and I think it ought to be that way. Breaking inheritance should only be done with serious consideration as it's difficult to support and also has performance implications - a Microsoft employee once told me that breaking inheritance "makes SQL cry". Maybe in the cloud we care less about performance implications because all that stuff is abstracted away. But it's still there and I'd have to believe Microsoft cares about its servers. Anyway...

## Know your tenant settings
We can manage the tenant-wide default behavior for Copy Link by navigating directly to https://<tenant>-admin.sharepoint.com/_layouts/15/online/ExternalSharing.aspx

There are a number of settings related to Sharing on this page but the ones we care about are under the headings "Default Link Type" and "Default Link Permission". The defaults look like this.

![Tenant Settings](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink4.png?raw=true "Tenant Settings")

Note that in the Copy Link dialog we had four options for how to share the link, and the tenant setting only allows for three, excluding, maddeningly, the "People with Existing Access" option, the one I think should be the default.  If we select the "Direct - specific people" option, though, and just not actually specify any people, the result will be the same.

The "Use shorter links" option only substitutes the "guestaccess.aspx" url with the crypic sharing url we saw earlier, nothing really to see there.  The Default Link Permission setting, if set to Read, will at least limit the damage done if files are inadvertently shared to the general population.

## Manipulating the settings using PowerShell
Of course these settings can be set using PowerShell at both the Tenant and Site Collection level. The Site Collection level settings will override the Tenant level settings for the site in question.  Check out the documentation for [Set-SPOTenant](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps) and [Set-SPOSite](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps). The options you want to look into are, on both commands, **DefaultSharingLinkType** and **DefaultSharingLinkType**. Make sure to check out the other settings related to sharing just to get a feel for how they work.




