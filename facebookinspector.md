# Scraping Facebook with the Inspector

Scraping Facebook is very tricky - but there is one simple way to collect information from Facebook: the Inspector.

Log in to Facebook using a browser like Chrome or Firefox - then open the Inspector that those browsers have by right-clicking somewhere on the page and selecting **Inspect**.

The inspector should open up on the bottom half of the screen (or perhaps on the right side).

Switch to the *Network* tab. This monitors traffic as a page is loaded: for example, images that the page loads, as well as external JavaScript, cookies and data that it pulls in.

But it only starts monitoring once it's open. So, now you can navigate to the page you want to 'store' (or refresh the page if you're already on it).

![](https://raw.githubusercontent.com/paulbradshaw/scraping/master/fbinspector.png)

You should see the Network view fill up with details on all the files loaded.

Click on the 'cog' icon to the far right of the Network view, and select **Save all as HAR**

This will save the files loaded by the page as a .har file - you can treat this as you do a JSON file. There will be lots of information but it should include posts or other items you want to drill down into.
