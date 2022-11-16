# Web-scraping-and-extracting-data-from-Web

# Scraping data from web with BeatifulSoap package

This code is about data collection from web with beatifulsoap package in Python

from bs4 import BeautifulSoup as bs
#import pandas as pd
#import time
#import requests #forsending http requests
#import random
# 
#assigning the page url 
#page = requests.get("addpageurlhere")
#page
# 
#Getting the webpage's  html content
#soup = bs(page.content)
#soup
# 
#there we are filtering the html data of the page to the content we want. goes to all divs and extracts divs with mentioned class
#my = soup.find_all("div", class_="quoteText")
# 
#burda da simdi aldik bahsettigimiz seyi, ama div icinde baska seyler de var malum,
#burada loop ile sadece text leri aldiyoruz istedigimiz quote lar text cunku digerleri text degil
#now that we have necessary divs, we are now omitting the unncessary info in divs, and we are only extracting the "text"s inside divs
quotes2 = [i.text for i in my]
#quotes2
#as can be understood, I'm here extracting quotes from a web page which list quotes from famous people

#this way, we already have all the quotes from the main page, however, we may want python to go to some other pages and get all the quotes from those pages as well
#I will write a for loop to go through different pages, also this time I will get both quote and author info and merge them in a dataframe

authors = []

quotes = []

#List of URLs for page 2 and 3
urls = [f"write url here in the format of the site...page={i}" for i in range(2,4)]

#List for Randomizing our request rate
rate = [i/10 for i in range(10)]

#Iterating through the URLS to get quotes and authors
for url in urls:
        
    # Accessing Webpage
    page = requests.get(url)
    
    # Getting the webpage's html content
    soup = bs(page.content)

    # Adding the authors and quotes to related lists
    quotes.extend([i.text for i in soup.find_all("div", class_="quoteText")])
    
    #burada author lar span icinde oldugu icin div yerine span yaptik class i da
    #here as author info in in span sections, we used span instead of div
    authors.extend([i.text for i in soup.find_all("span", class_="authorOrTitle")])
    
    # Randomizing our request rate  
    time.sleep(random.choice(rate))
   

#creating a df
df = pd.DataFrame()
#assigning author and quotes infos to the dataframe
df['Authors'] = authors
df['Quotes'] = quotes

note: url loop, and html sections we will search may differ from page to page and needs to be customized accordingly

# Extracting table from web
#we will extract central bank interest rates table form wikipedia

url = "https://en.wikipedia.org/wiki/List_of_countries_by_central_bank_interest_rates"
html = requests.get(url).content
df_list = pd.read_html(html)
df2 = df_list[0]

# Extracting image from Web
URL = "paste url here"
getURL = requests.get(URL, headers={"User-Agent":"Mozilla/5.0"})
print(getURL.status_code)

#getting web page's html content
soup = bs(getURL.text, 'html.parser')
 
#filtering all parts of html code to parts containing word 'img'
images = soup.find_all('img')
print(images)

#getting the sources of the images with a for loop
imageSources = []
 
for image in images:
    imageSources.append(image.get('src'))
 
print(imageSources)

however not every url we get is a complete url, some are missing characters like https: etc. to complete this we will use urlJoin function in 
requests package

resolvedURLs = []

for image in images:
    src = image.get('src')
    resolvedURLs.append(requests.compat.urljoin(URL, src))
    
this loop completes the missing parts. 

now that we have the urls for each images on the relevant page, we can save all of them with a for loop inside a images folder we created in the working directory

for image in lurl:
    webs = requests.get(image)
    open('images/' + image.split('/')[-1], 'wb').write(webs.content)
    #split the url and uses the part after the last / as the name of the file
