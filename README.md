
<h1> Get all birthdays from Facebook </h1>
A Python script to retrieve all birthdays from your friends on Facebook.

A couple of years ago I deleted my FB account, realized I only used it to keep track of my friend's birthdays. 
I used the following code to download the birthdays from my account.

Before you start:
* Get the right geckodriver: every Chrome / Firefox instance needs a different geckodriver
* I'm using Firefox here, change that to your favorite browser

```python
from selenium import webdriver
import pandas as pd
import time

# path to the geckodriver
webdriver_path = 'geckodriver_v0.18.exe'

# define xpaths
xpaths = {'friends': "//li[@class='_43q7']",
         'hoverovertext': 'data-tooltip-content'}
```

Fire up Firefox and go to the birthday's page, go to Firefox and log on in Facebook


```python
driver = webdriver.Firefox(executable_path= webdriver_path)
driver.get('https://www.facebook.com/events/birthdays/')
```
Make sure you're logged in. The following part scrolls all the way done so all birthdays are loaded.

```python
# scroll all the way down
for scroll in xrange(20):
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
```
Scrape the names
```python
# define lists
names = []
bds =[]

# loop through friends
for person in driver.find_elements_by_xpath(xpaths['friends']):
    
    # get hoverover text
    person_text = person.find_element_by_class_name('link').get_attribute(xpaths['hoverovertext'])
    
    # retrieve name
    name = person_text.split('(')[0]
    
    # retrieve date of birth
    bd = person_text.split('(')[1].replace(')','')
    
    # append to lists
    names.append(name)
    bds.append(bd)
```

Get the results

```python
print('Got %d friends' % len(names))

# save to csv
bd_db = pd.DataFrame({'Name': names, 'BD': bds})
bd_db['Name'] = bd_db['Name'].str.encode("ascii","ignore")
bd_db.to_csv('%s FB Birthdays.csv' % str(time.strftime('%Y%m%d')))
```
