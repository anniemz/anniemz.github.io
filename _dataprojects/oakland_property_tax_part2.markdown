---
layout: project_page
title: 'Oakland Property Tax: Part 2 Scraping Data'
image_path: /assets/img/projects/oaklandpart2.jpg

---

The first part of my analysis required gathering property tax data for each parcel of land in the City. Luckily for me, this is publicly available data via the [Alameda County's Assesor's office](https://www.acgov.org/ms/prop/index.aspx).* If you type in a parcel number such as "1-111-1", you will be brought to a page where all the data is presented in tabular form. 

I attempted to scrape this data by parsing the html with Beautiful Soup, but each page is unfortunately served up by indecipherable javascript and I found that each page had to be loaded to be scraped. Thus I landed on Selenium. 

*See [part 5](/dataprojects/oakland_property_tax_part5) for an important lesson learned and better source for this data. 

<img src = "/assets/img/projects/oaklandpart2.jpg" />
<h1> Using selenium </h1>

To scrape each page, I used a python packaged called Selenium. It's mostly for automated testing in various browsers but seems to be used a lot for scraping. To get started, I installed the package and brought it into my project:

`from selenium import webdriver`

To open a page, create a driver: 

`driver = webdriver.Chrome(executable_path=CHROMEDRIVER_PATH, chrome_options=chrome_options)`

then, you can pull up a page 

`driver.get(http://acgov.org/MS/prop/index.aspx?PRINT_PARCEL=1-111-1)`

and from there dissect various parts of the page by parsing the html. For example: 

`property_tax = driver.find_element_by_id("FormView1_roll_net_tax_label").text`

<h1>Adding proxies </h1>
While I tried my best to scrape gently and politely* by only making 1 request every 10 seconds or so, I would pretty much uniformly get blocked after about an hour of scraping. 

To get around this, I had to add in 25 rotating proxies, which I bought from [proxybonanza.com](proxybonanza.com) for the low price of $10. 

Using proxies with Selenium is a little tricky and annoying. One way to do it, is to add a plugin to Chrome with the proxy information embedded in the plugin file. Unfortunately when you use chromedriver with a plugin, you cannot run it in "headless" mode and therefore Chrome is constantly open in the background. For more info on how to do this, take a look at the `proxy_util` script I used.

*including abiding by the site's [robots.txt](https://www.acgov.org/robots.txt) file.


<h1>Creating queuing system </h1>
I created a very basic queuing system to keep track of where I was in the process and be able to easily restart the scraper should I get booted off or run into an error. The "queue" was essentially a list of all possible parcel numbers that I generated based on the typical format of X-XXX-XX. The queuing system would pop off a parcel number from a list, load the page, scrape the information, save it to the CSV and the delete the parcel number from the list. And so on. 



<h1> Voila </h1>

Many hours of scraping later, I had a massive CSV file of parcel numbers and assessed property values. I then modified the scraper to pull address information from a different part of the Alameda County's assessors office website and repeated the process. 

Take a look at the 2 scrapers I built [here](https://github.com/anniemz/propertytax_scraper) and [here](https://github.com/anniemz/addressdata_scraper)


[**Next Part: PostGIS**](/dataprojects/oakland_property_tax_part3)
