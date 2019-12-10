# Using scraping functions in Google Sheets

![](https://raw.githubusercontent.com/paulbradshaw/CIJSS_scraping/master/generating_urls_excel.png)

*The image above shows how to generate a series of URLs for a scraper*

*Resources for a short class introducing scraping*. You can see [an example spreadsheet using these techniques here](https://docs.google.com/spreadsheets/d/1jhGt86x-iFbBTBvs3VRUI6sj-iFPUUXCoAY9mp4bHiM/edit#gid=938670368)

**The techniques outlined here are covered in more detail in the first chapters of the book [Scraping for Journalists](https://leanpub.com/scrapingforjournalists). The book also covers scraping using tools like OutWit Hub, and more advanced techniques for scraping databases, spreadsheets and PDFs.**

**Start here**: [this is a webpage](http://www.numbeo.com/pollution/country_result.jsp?country=United+Kingdom) that publishes data on various types of pollution. 

Look at the URL: it has a pattern: `http://www.numbeo.com/pollution/country_result.jsp?country=United+Kingdom`

(for another example, see [DEFRA's page on air monitoring](https://uk-air.defra.gov.uk/latest/currentlevels) and try using the *filter by region* option)

Click on one of the city links: [this is the page for London](http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London). Some things to look for:
* The page has a similar **structure** to the last one - that's a pattern that helps us.
* The **URL** has a similar structure: `http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London`

Can we generate URLs for other cities? Try:
* `http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=Birmingham`
* `http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=Manchester`

What happens when you add a city with a space in the name?

* `http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=milton%20keynes`

The URL **replaces the space** with `%20`

## Writing a scraper function in Google Sheets

We can try to scrape the page by using the `IMPORTHTML` function in Google Sheets. Here's an example:

`=IMPORTHTML("http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London","table",1)`

What is this `IMPORTHTML`?

* `IMPORTHTML` is a **function** in Google Sheets. Functions are words that mean 'use a recipe'. The `SUM` function, for example, is a recipe for adding up a bunch of numbers. Other commonly used functions in spreadsheets include `AVERAGE`, `MEDIAN` and `COUNT`.
* Some functions work in all spreadsheet software, but `IMPORTHTML` is specific to Google Sheets. **It will not work in Excel**. 
* Functions are always followed by parentheses, and normally those parentheses include the *ingredients* for the recipe. Those ingredients are called 'arguments' or 'parameters'.
* Different functions require different numbers of arguments: `IMPORTHTML` needs 3 arguments, which are explained below.
* Functions are a core part of programming. If you want to write more advanced scrapers using languages like Python, Ruby, PHP or R, you will need to use functions too. Using them in spreadsheets is a good way to familiarise yourself with how they work.

Let's break the formula down:

* The `=` sign tells the spreadsheet we want it to do some work, not just store data we are entering
* Then we have the function, followed by ingredients in parentheses
* There are 3 ingredients
* The first ingredient is the URL of the page you want to scrape - **in quotation marks**. The quotation marks are important: if you don't use quotation marks the spreadsheet software will assume you are naming another function.
* The second ingredient is what you want to scrape from that page. There are only two options here: `"table"` or `"list"`. The first looks for the `<table>` tag; the second looks for the `<ul>` or `<ol>` tag (unordered list or ordered list). Again this *must be in quotation marks*.
* The third ingredient specifies *which* table or list you want to scrape: `1` means the first table or list, `2` the second, and so on. Note that this *is not in quotation marks*.

## Making the formula work

From the description above you should be able to see that `IMPORTHTML` will only work if your data is within a  `<table>` tag or `<ul>` or `<ol>`. 

So how do you know if it is?

There are two options: you can either look in the HTML (right-click on the page and select *View source* or similar), or you can just try the formula and see if it works.

The second thing you might be wondering is *how do we know which table or list the data is in?* Again, you can either look in the HTML or try different formulae.

We're going to try different formulae - it's generally much quicker.

If you try this formula, it will grab some information from the page...

`=IMPORTHTML("http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London","table",1)`

...but it's not the information we want.

So change the number to `2`:

`=IMPORTHTML("http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London","table",2)`

Still not what we want. But keep changing the number: eventually `4` does indeed get some data.

By the way, this number is an **index**: it refers to a position (1st, 2nd, 3rd, 4th). Indexes are something else widely used when hard coding scrapers too.

## Using cell references in our formula

It's great that we scraped one page, but what if we want to repeat this across a bunch of pages?

Remember that the URLs have a pattern: we can generate them much more easily than trying to type each one.

And instead of typing each formula from scratch with a different URL, it makes more sense to put those in their own cells. 

Move your formula into a cell in the second row and second column. This is because we'll need some space above or to the left of it to type our URL.

In cell A2 paste the URL: `http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=London`

Now change your formula to reference that: 

`=importhtml(A2,"table",4)`

Note that the cell reference *does not have quotation marks*.

We can do the same for the index: put `4` in cell C1.

Then adapt the formula accordingly:

`=importhtml(A2,"table",C1)`

What's the advantage of this?

It's going to be much quicker to change the number in cell C1 then editing the formula to try different indexes. Likewise, it's going to be quicker to change the URL in A2. And we can do the same with "table" - if you want.

Now in cell A5 we can paste another URL:

`http://www.numbeo.com/pollution/city_result.jsp?country=United+Kingdom&city=Manchester`

And we can copy our formula from B2, and paste it in the cell next to it, B5: 

`=importhtml(A5,"table",C4)`

It generates an `#N/A` error. Why? Check the formula: A5 has a URL, yes, but C4 doesn't have an index. We need to change that back to C1 and **fix it with dollar signs** like so:

`=importhtml(A5,"table",$C$1)`

The dollar signs mean that if you copy this formula and paste it anywhere else, it's not going to change C1 to a different cell reference.

## Using INDEX to specify a cell in the target table

This is great: we can now generate a whole bunch of URLs and copy the formula down to scrape each one. But these tables take up 3 rows each, which makes it problematic to copy it down.

We can solve that by using another spreadsheet function: `INDEX`, and *nesting* our `IMPORTHTML` formula within it. Here's an example:

`=INDEX(importhtml(A2,"table",C$1),2,2)`

The `INDEX` function looks in a specified row and column of a table and grabs the contents.

It has 3 ingredients:

* The table
* The row
* The column

In the example above, the first ingredient is our original formula: `importhtml(A2,"table",C$1)`

In other words, it's the table that we grab with that formula.

The second ingredient is `2` (row 2)

The third ingredient is also `2` (column 2)

So it will grab the contents of the second row, second column, of the table we grab with `IMPORTHTML`!

Now the results occupy only one row, and our job is made easier.

