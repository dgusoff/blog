# Copy Link in Modern SharePoint - non-obvious security implications you should know about

Recently I encountered a strange issue in a client's Intranet during the content buildout phase. They'd given read-only access to a group of pilot users, and loaded up their site with pages and links to documents. Then they began to notice that these pilot users appeared to have the ability to delete the documents, and logged a bug with us.

We discovered that the document library had hundreds of files with broken permission inheritance, and that the Everyone principal had been granted Contribute permission on each one of these documents, meaning they could edit and even delete the documents. 

Thinking that some rogue user had inadvertently (or "advertently") shared those documents in error, we ran a script that looped through all the documents in each library and restored the permission inheritance on each one. Then we discovered that the several hundred or so hyperlinks to the documents throughout the system began returning 404s.

Eventually we tracked the issue down to a "feature" of the Copy Link action bar item in Modern SharePoint document libraries. We discovered that Copy Link does a bit more than merely return a link to the document to the user's clipboard.

![The Document Action Bar](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink1.png.png?raw=true "The Document Action Bar")

My client had been using SharePoint's Copy Link functionality to create those links, just as we had taught them to. But what we didn't realize was that clicking Copy Link was actually breaking the security inheritance on the document, and *sharing it* to the entire company.  This was because the tenant settings that drove this funcitonality were left in their default settings, which inexplicably default to the most permissive - the most insecure - setting.

Check out what happens when you click the button:

![Copy Link Dialog](https://github.com/dgusoff/blog/blob/master/Copy-Link-Security-Implications/images/copylink2.png?raw=true "The Copy Link Dialog")

Once you see this dialog, permission inheritance has already been broken and the permission "Anyone with the link can edit" has already been applied. If you select another option, the permission will update - even to the point of reinstating permission inheritance if "People With Existing Access" is selected. Also, the link regenerates, and previously generated links become stale and return 404s.



At this point, actually clicking the Copy button is pointless - the file 
