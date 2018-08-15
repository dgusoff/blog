# Copy Link in Modern SharePoint - non-obvious security implications you should know about

Recently I encountered a strange issue in a client's Intranet during the content buildout phase. They'd given read-only access to a group of pilot users, and loaded up their site with pages and links to documents. Then they began to notice that these pilot users appeared to have the ability to delete the documents, and logged a bug with us.

We discovered that the document library had hundreds of files with broken permission inheritance, and that the Everyone principal had been granted Contribute permission on each one of these documents, meaning they could edit and even delete the documents. 

Thinking that some rogue user had inadvertently (or "advertently") shared those documents in error, we ran a script that looped through all the documents in each library and restored the permission inheritance on each one. Then we discovered that the several hundred or so hyperlinks to the documents throughout the system began returning 404s.

Eventually we tracked the issue down to a "feature" of the Copy Link action bar item in Modern SharePoint document libraries. We discovered that Copy Link does a bit more than merely return a link to the document to the user's clipboard.

[[image  -- the copy link action bar item]]

My client had been using SharePoint's Copy Link functionality to create those links, just as we had taught them to. But what we didn't realize was that clicking Copy Link was actually breaking the security inheritance on the document, and *sharing it* to the entire company.  This was because the tenant settings that drove this funcitonality were left in their default settings, which inexplicably default to the most permissive - the most insecure - setting.

[[image of copy link with options selected]]

When you select a document in SharePoint and click that Copy Link button, you're rpesented with a dialog
