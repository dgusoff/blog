# Copy Link in Modern SharePoint - non-obvious security implications you should know about

Recently I encountered a strange issue in a client's Intranet during the content buildout phase. They'd given read-only access to a group of pilot users, and loaded up their site with pages and links to documents. Then they began to notice that these pilot users appeared to have the ability to delete the documents, and logged a bug with us.

We discovered that the document library had hundreds of files with broken permission inheritance, and that the Everyone principal had been granted Contribute permission on each one of these documents, meaning they could edit and even delete the documents. 
