# How to Crawl the National Gallery of Art in Washington, D.C.

Download the folder "nearest_neighbors.tar" from the website: https://kilthub.cmu.edu/articles/National_Gallery_of_Art_InceptionV3_Features/10061885

# This scrip crawls more then 96,000 artworks from the gallery


```python
import json
import glob
import codecs
import io
import requests 
import lxml.html
import urllib
import re
import os
```

## Create folder where you want the images to be downloaded 


```python
folder = r"..\National Gallery of Art in Washington"
```

## Create list of JSON Paths


```python
def returnListOfJsonPaths(dirPath):
    """
    All JSON Paths in a folder
    :param Directory to Folder with JSON Paths
    :return: List with all JSON Paths in a Folder
    """
    jsonPattern = os.path.join(dirPath,'*.json')
    fileList = glob.glob(jsonPattern)
    return fileList
```

## Insert below the path where all JSON-Paths are stored, contained in "nearest_neighbors.tar"


```python
List_JSON_Paths = returnListOfJsonPaths(r"..\10061885\nearest_neighbors.tar\work")
#print(List_JSON_Paths)
```

## Extracting the name of the artwork, name of artist, date of creation of the artwork and the URL to the webpage


```python
openJSON = json.load(open(r"..\10061885\nearest_neighbors.tar\work\1937.1.1.json", encoding='utf-8'))
#print(openJSON)

title= openJSON["title"]
title_cleaned = " ".join(re.sub(r'[^a-zA-Z]', ' ', title).split())
print(title_cleaned)


date = openJSON["displaydate"]
date_cleaned = " ".join(re.sub(r'[^a-zA-Z0-9]', ' ', date).split())
print(date_cleaned)

artist = openJSON["attribution"]
artist_cleaned = " ".join(re.sub(r'[^a-zA-Z0-9]', ' ', artist).split())
print(artist_cleaned)

print(title_cleaned + "_" + date_cleaned + "_" + artist_cleaned)

URL_to_Image = openJSON["iiif"]
print(URL_to_Image)
```

    Madonna and Child on a Curved Throne
    c 1260 1280
    Byzantine 13th Century possibly from Constantinople
    Madonna and Child on a Curved Throne_c 1260 1280_Byzantine 13th Century possibly from Constantinople
    https://media.nga.gov/iiif/public/objects/3/5/35-primary-0-nativeres.ptif/full/!800,800/0/default.jpg
    

## Read HTML


```python
def read_html(url):
    source_code = requests.get(url) 
    html_elements = lxml.html.fromstring(source_code.content) 
    return html_elements
```

## XPath to image


```python
read_art_html = read_html("https://media.nga.gov/iiif/public/objects/3/5/35-primary-0-nativeres.ptif/full/!800,800/0/default.jpg") 
path_to_image = "//img[@style]/@src"

print(path_to_image)
```

    //img[@style]/@src
    

## Try to download one image


```python
urllib.request.urlretrieve(URL_to_Image, folder + "\\" + artist_cleaned + "_" + date_cleaned + "_" + artist_cleaned + ".jpg")
```

## Bring it all together and download all images 


```python
count = 0
for file in List_JSON_Paths:
    count += 1
    print(count)
    openJSON = json.load(open(file, encoding='utf-8'))
    
    title= openJSON["title"]
    if title != None:
        title_cleaned = " ".join(re.sub(r'[^a-zA-Z]', ' ', title).split())
        #print(title_cleaned)
    else:
        title_cleaned = "Unknown"
        #print(title_cleaned)

    date = openJSON["displaydate"]
    if date != None:
        date_cleaned = " ".join(re.sub(r'[^a-zA-Z0-9]', ' ', date).split())
        #print(date_cleaned)
    else:
        date_cleaned = ""
        #print(date_cleaned)
        

    artist = openJSON["attribution"]
    if artist != None:
        artist_cleaned = " ".join(re.sub(r'[^a-zA-Z0-9]', ' ', artist).split())
        #print(artist_cleaned)
    else:
        artist_cleaned = "Unknown"
        #print(artist_cleaned)
    

    #print(title_cleaned + "_" + date_cleaned + "_" + artist_cleaned)

    URL_to_Image = openJSON["iiif"]
    
    #print(URL_to_Image)
    
    read_art_html = read_html(URL_to_Image) 
    path_to_image = "//img[@style]/@src"
    
    urllib.request.urlretrieve(URL_to_Image, folder + "\\" + artist_cleaned[:70] + "_" + date_cleaned + "_" + title_cleaned[:100] + ".jpg")
    
print("All Done!")
```

    All Done!
    
