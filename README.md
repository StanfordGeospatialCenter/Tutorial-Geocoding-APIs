Tutorial-Geocoding-APIs
=======================

### How to use various Web APIs and OpenRefine for geocoding and augmenting data with geographic attributes. 

### Getting Ready for the Tutorial 
This tutorial will demonstrate how to use OpenRefine to submit URLs to web-based Geocoding APIs in order to augment an existing dataset with things like latitude & longitude coordinates, elevation values, drive times, etc...

OpenRefine is a piece of open-source software that allows you to manipulate data in many different ways. It's not just great for geocoding and augmenting datasets, but is really great for cleaning up 'dirty' data, too. 

For the needs of this tutorial, OpenRefine allows us to build and submit URLs to a web service (like a geocoding API) over and over tens, hundreds, even thousands or more times, saving us the trouble of typing a URL into a browser and copy&pasting values, one at a time. OpenRefine runs in your browser, but does so locally by installing itself as a sort of server running on your own machine.  OpenRefine used to be called GoogleRefine, but has recently been re-branded, so much of the support materials you will find on-line will still refer to GoogleRefine.  

![Open Refine Logo] (http://opensas.files.wordpress.com/2013/06/logo-openrefine-40.png)

You will, of course, need to download OpenRefine in order to complete this tutorial. You can get the appropriate version for your operating system from [OpenRefine.org] (http://openrefine.org/). In the meantime, there are 3 videos on the OpenRefine frontpage. You should take a look at all three, because they give a great, concise overview of many of the capabilities of OpenRefine. For the purposes of this tutorial, it wouldn't hurt to watch the 3rd video, linked here:

http://www.youtube.com/watch?feature=player_embedded&v=5tsyz3ibYzk

You might also want to take a look at this page on "___Understanding Expressions___" in OpenRefine:

https://github.com/OpenRefine/OpenRefine/wiki/Understanding-Expressions

And, here's the full Documentation Wiki:

https://github.com/OpenRefine/OpenRefine/wiki/Documentation-For-Users


## What is an [Geocoding] API?
An API (Application Programming Interface) is, essentially, a set of instructions that allows computer programs to pass data back and forth.  In the following exercises, we will be using several Web APIs.  The Web APIs we will use allow you to submit a URL just like when you type a URL into your browser address bar.  The important difference is that the URL we are submitting has a bit of data (an address or coordinate pair) that we want to know something about. Rather than giving us back an HTML file that instructs our browser to retrieve images, text and other object and arrange them into a webpage, the Web APIs we will be using give us back information about the address/coordinate pair we submitted.  

### Submitting a URL
Here’s the basic structure of one of the URLs you will submit to the one of the Web APIs we will be working with:

```
http://www.museum.tulane.edu/webservices/geolocatesvcv2/glcwrap.aspx?Country=USA&Locality=bogalusa&state=la&fmt=JSON
```

From the sample above:
```
http://www.museum.tulane.edu/webservices/geolocatesvcv2/glcwrap.aspx?
``` 
...is the base URL of the (Geolocate Search API) [http://www.museum.tulane.edu/geolocate/]...

```
Country=USA&Locality=bogalusa&state=la&fmt=JSON
```
...are the parameters of the search. In this case, there are four basic parameters to the search...

```
Country=USA
```
...this first **parameter** indicates to the Geolocate API what country the placename you are searching for is in.  This is a **required** parameter in the Geolocate API.

```
&Locality=bogalusa
```
...this is the **placename** you are searching for.
```
&state=la
```
...this is the name of the state that the locality you are looking for is in. In the Geolocate API, this is a required parameter if you are searching for placenames within the USA.

```
&fmt=JSON
```
...this parameter indicates the type of output you would like to get back from the Geolocate API. Currently, the Geolocate API is able to return JSON or GeoJSON.  We will discuss these formats, and their differences, later. 

Here are the rest of the supported parameters from the Geolocate API:

![Geolocate API Parameters](/images/Geolocate_API_Parameters.png)

### Getting back JSON
For the time being, try submitting the above search to the Geolocate API by clicking on this link:

http://www.museum.tulane.edu/webservices/geolocatesvcv2/glcwrap.aspx?Country=USA&Locality=bogalusa&state=la&fmt=JSON

What you get back should look like this (_sans_ the fancy syntax highlighting Github does):
```JSON
{
"engineVersion" : "GLC:4.93|U:1.01374|eng:1.0",
"numResults" : 1,
"executionTimems" : 109.2002,
"resultSet" : { "type": "FeatureCollection",
"features": [
{ "type": "Feature",
"geometry": {"type": "Point", "coordinates": [-89.84861, 30.79083]},
"properties": {
"parsePattern" : "BOGALUSA",
"precision" : "High",
"score" : 83,
"uncertaintyRadiusMeters" : 4490,
"uncertaintyPolygon" : "Unavailable",
"displacedDistanceMiles" : 0,
"displacedHeadingDegrees" : 0,
"debug" : ":GazPartMatch=False|:inAdm=True|:Adm=WASHINGTON|:NPExtent=7455|:NP=BOGALUSA|:KFID=LA:ppl:9064|BOGALUSA"
}
}
 ],
"crs": { "type" : "EPSG", "properties" : { "code" : 4326 }}
}
}
{
"engineVersion" : "GLC:4.93|U:1.01374|eng:1.0",
"numResults" : 1,
"executionTimems" : 109.2002,
"resultSet" : { "type": "FeatureCollection",
"features": [
{ "type": "Feature",
"geometry": {"type": "Point", "coordinates": [-89.84861, 30.79083]},
"properties": {
"parsePattern" : "BOGALUSA",
"precision" : "High",
"score" : 83,
"uncertaintyRadiusMeters" : 4490,
"uncertaintyPolygon" : "Unavailable",
"displacedDistanceMiles" : 0,
"displacedHeadingDegrees" : 0,
"debug" : ":GazPartMatch=False|:inAdm=True|:Adm=WASHINGTON|:NPExtent=7455|:NP=BOGALUSA|:KFID=LA:ppl:9064|BOGALUSA"
}
}
 ],
"crs": { "type" : "EPSG", "properties" : { "code" : 4326 }}
}
}
```
##What is JSON?
For our purposes, we don't really need to go deeply into what JSON (JavaScript Object Notation) is. It is enough for you to know that it is a format for storing and exchanging data in human readable text format and that it is an output format for all of the APIs we will use, as well as most web-based data APIs.
###Making sense of JSON
OK, so that JSON we got back might be a little intimidating if you aren't used to looking at code, but if you just bend your knees and take a deep breath, then look closely, you will see that there is actually some useful information in there! In fact, if what we are doing is geocoding placenames, it's got exactly what we need on the line that begins with **"geometry":**
```
"geometry": {"type": "Point", "coordinates": [-89.84861, 30.79083]},
```
**See them!? There are longitude/latitude coordinates in there! That's what we are after!**  You can make the JSON data even more readable with http://jsonviewer.stack.hu/. 

Copy&Paste the JSON from the example we clicked earlier into the http://jsonviewer.stack.hu/ text tab and click on the Format Button in the Main Menu. Then switch to the Viewer Tab...

![JSONViewer Collapsed] (/images/JSONViewerCollapsed.png)

Then, Expand all of the elements in the hierarchy so that you have something that looks like this:

![JSONViewer Collapsed] (/images/JSONViewerExpanded.png)

You can see that the latitude and longitude coordinates are under the coordinates object. If you track back through the hierarchy from the coordinates object, you will find that the address of the longitude coordinate [-89.84861] is:

```
resultSet.features[0].geometry.coordinates[0]
```


#Geolocate Tulane
###Limits
There are no licensing restrictions on the Tulane Geolocate service. The API can be used for any geocoding purpose and for any amount of data. That said, the service does have some technical limitations. I have observed that a more stable result is achieved when using no more than 5 requests per second. Another thing to consider is the purpose for which Geolocate was created. Geolocate is very good at geocoding to administrative boundaries, but was not designed to geocode street addresses, so if that is what you have, you will have to use an alternative service.

Here are the Geolocate Parameters, once again:

![JSONViewer Collapsed] (/images/Geolocate_API_Parameters.png)

Most of these are fairly straightforward. The ones that most concern us  for the current tutorial are the following:

  __locality__ - This is the "address" or placename you are actually looking for. Geolocate is actually built to handle fairly esoteric localities (like "3 miles north of the confluence of the X and Y rivers").
  
  __country__ - This is the country that your locality is in. This is a required parameter for all Geolocate Searches.
  
  __state__ - This is listed as an optional parameter in the Geolocate JSON Wrapper DOc, but it is __REQUIRED__ if you are geocoding data within the United States and set your 'country=USA'.
  
  
###API Overview

Again, here’s the basic structure of the URLs you will submit to the Geolocate JSON Wrapper Web API:

```
http://www.museum.tulane.edu/webservices/geolocatesvcv2/glcwrap.aspx?Country=USA&Locality=bogalusa&state=la&fmt=JSON
```

From the sample above:
```
http://www.museum.tulane.edu/webservices/geolocatesvcv2/glcwrap.aspx?
``` 
...is the base URL of the (Geolocate Search API) [http://www.museum.tulane.edu/geolocate/]...

```
Country=USA&Locality=bogalusa&state=la&fmt=JSON
```

are the parameters of the search. In this case, there are four basic parameters to the search.

###Geocoding with Geolocate
___this section in progress___

#Geonames.org
##Create an Account

http://www.geonames.org/

##Examine the API Docs

http://www.geonames.org/export/ws-overview.html

We're interested in the ___Search___ API

http://www.geonames.org/export/geonames-search.html

##Use the Advanced Search Box to Help Build a URL
http://www.geonames.org/advanced-search.html?

1. Search for 'Bexar County, Texas'; Country='United States'; Feature Class='Country,State,Region...'

The resulting URL should look like this:
```
http://www.geonames.org/advanced-search.html?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=
```
Open up a new Browser Tab and Cut&Paste the part of the URL from the 'q=' parameter, all the way to the end of the URL, like this:

```
q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=
```

We're going to build our API Url from this. First, paste the API Base URL in front of the part you just pasted:

Here is the Base URL:
```
api.geonames.org/search?
```
Here is what it should look like after pasting the two together:
```
api.geonames.org/search?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=
```

You can go ahead and hit enter to submit the URL. WHat you will get is an XML file, with an error message. This is because to use the API, you are required to identify yourself each time you submit a query. Now, you need to add a parameter that identifies you. You will add the username parameter, using your own username that you created when you registered with Geonames.org:

The parameter is:
```
&username=?????????
```
Where you will replace the ???????? with your Geonames Username. Just put this right on the end of the URL you are building:
```
api.geonames.org/search?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=&username=?????????
```

Now try hitting enter to submit the URL, again. You should see something like the below (it's a large amount of text, since we haven't limited the returns, yet):
```XML
This XML file does not appear to have any style information associated with it. The document tree is shown below.
<geonames style="MEDIUM">
<totalResultsCount>14</totalResultsCount>
<geoname>
<toponymName>Bexar County</toponymName>
<name>Bexar County</name>
<lat>29.44896</lat>
<lng>-98.52002</lng>
<geonameId>4674023</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADM2</fcode>
</geoname>
<geoname>
<toponymName>City of Elmendorf</toponymName>
<name>City of Elmendorf</name>
<lat>29.25547</lat>
<lng>-98.31695</lng>
<geonameId>7173379</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Helotes</toponymName>
<name>City of Helotes</name>
<lat>29.56325</lat>
<lng>-98.70832</lng>
<geonameId>7173601</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Selma</toponymName>
<name>City of Selma</name>
<lat>29.58652</lat>
<lng>-98.31338</lng>
<geonameId>7174414</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Somerset</toponymName>
<name>City of Somerset</name>
<lat>29.22848</lat>
<lng>-98.65671</lng>
<geonameId>7174465</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Von Ormy</toponymName>
<name>City of Von Ormy</name>
<lat>29.28019</lat>
<lng>-98.65567</lng>
<geonameId>7226947</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Castle Hills</toponymName>
<name>City of Castle Hills</name>
<lat>29.52284</lat>
<lng>-98.51971</lng>
<geonameId>7172817</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Grey Forest</toponymName>
<name>City of Grey Forest</name>
<lat>29.61693</lat>
<lng>-98.68332</lng>
<geonameId>7173551</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Hill Country Village</toponymName>
<name>City of Hill Country Village</name>
<lat>29.58311</lat>
<lng>-98.48913</lng>
<geonameId>7173621</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Leon Valley</toponymName>
<name>City of Leon Valley</name>
<lat>29.49534</lat>
<lng>-98.614</lng>
<geonameId>7173662</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>City of Shavano Park</toponymName>
<name>City of Shavano Park</name>
<lat>29.58617</lat>
<lng>-98.55632</lng>
<geonameId>7174427</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>Town of Saint Hedwig</toponymName>
<name>Town of Saint Hedwig</name>
<lat>29.41971</lat>
<lng>-98.20471</lng>
<geonameId>7175009</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>Town of Hollywood Park</toponymName>
<name>Town of Hollywood Park</name>
<lat>29.59954</lat>
<lng>-98.48391</lng>
<geonameId>7175181</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
<geoname>
<toponymName>Town of China Grove</toponymName>
<name>Town of China Grove</name>
<lat>29.39303</lat>
<lng>-98.34435</lng>
<geonameId>7175602</geonameId>
<countryCode>US</countryCode>
<countryName>United States</countryName>
<fcl>A</fcl>
<fcode>ADMD</fcode>
</geoname>
</geonames>
```

Now we will add the last two parameters to the URL and see what the result looks like. First, we want to return JSON, instead of XML, so let's add the paramet 'JSON' just after the 'search' and '?' in the URL, so our URL looks like this:
```
http://api.geonames.org/searchJSON?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=&username=mapninja
```

Hit enter and notice that returned format has changed (also note that I have formatted the following for easier viewing and that the JSON you get back will not have line breaks and indents):
```JSON
{
  "totalResultsCount": 14,
  "geonames": [
    {
      "adminCode1": "TX",
      "lng": "-98.52002",
      "geonameId": 4674023,
      "toponymName": "Bexar County",
      "countryId": "6252001",
      "fcl": "A",
      "population": 1714773,
      "countryCode": "US",
      "name": "Bexar County",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "second-order administrative division",
      "adminName1": "Texas",
      "lat": "29.44896",
      "fcode": "ADM2"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.31695",
      "geonameId": 7173379,
      "toponymName": "City of Elmendorf",
      "countryId": "6252001",
      "fcl": "A",
      "population": 1488,
      "countryCode": "US",
      "name": "City of Elmendorf",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.25547",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.70832",
      "geonameId": 7173601,
      "toponymName": "City of Helotes",
      "countryId": "6252001",
      "fcl": "A",
      "population": 7341,
      "countryCode": "US",
      "name": "City of Helotes",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.56325",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.31338",
      "geonameId": 7174414,
      "toponymName": "City of Selma",
      "countryId": "6252001",
      "fcl": "A",
      "population": 5540,
      "countryCode": "US",
      "name": "City of Selma",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.58652",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.65671",
      "geonameId": 7174465,
      "toponymName": "City of Somerset",
      "countryId": "6252001",
      "fcl": "A",
      "population": 1631,
      "countryCode": "US",
      "name": "City of Somerset",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.22848",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.65567",
      "geonameId": 7226947,
      "toponymName": "City of Von Ormy",
      "countryId": "6252001",
      "fcl": "A",
      "population": 1085,
      "countryCode": "US",
      "name": "City of Von Ormy",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.28019",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.51971",
      "geonameId": 7172817,
      "toponymName": "City of Castle Hills",
      "countryId": "6252001",
      "fcl": "A",
      "population": 4116,
      "countryCode": "US",
      "name": "City of Castle Hills",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.52284",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.68332",
      "geonameId": 7173551,
      "toponymName": "City of Grey Forest",
      "countryId": "6252001",
      "fcl": "A",
      "population": 483,
      "countryCode": "US",
      "name": "City of Grey Forest",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.61693",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.48913",
      "geonameId": 7173621,
      "toponymName": "City of Hill Country Village",
      "countryId": "6252001",
      "fcl": "A",
      "population": 985,
      "countryCode": "US",
      "name": "City of Hill Country Village",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.58311",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.614",
      "geonameId": 7173662,
      "toponymName": "City of Leon Valley",
      "countryId": "6252001",
      "fcl": "A",
      "population": 10151,
      "countryCode": "US",
      "name": "City of Leon Valley",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.49534",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.55632",
      "geonameId": 7174427,
      "toponymName": "City of Shavano Park",
      "countryId": "6252001",
      "fcl": "A",
      "population": 3035,
      "countryCode": "US",
      "name": "City of Shavano Park",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.58617",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.20471",
      "geonameId": 7175009,
      "toponymName": "Town of Saint Hedwig",
      "countryId": "6252001",
      "fcl": "A",
      "population": 2094,
      "countryCode": "US",
      "name": "Town of Saint Hedwig",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.41971",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.48391",
      "geonameId": 7175181,
      "toponymName": "Town of Hollywood Park",
      "countryId": "6252001",
      "fcl": "A",
      "population": 3062,
      "countryCode": "US",
      "name": "Town of Hollywood Park",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.59954",
      "fcode": "ADMD"
    },
    {
      "adminCode1": "TX",
      "lng": "-98.34435",
      "geonameId": 7175602,
      "toponymName": "Town of China Grove",
      "countryId": "6252001",
      "fcl": "A",
      "population": 1179,
      "countryCode": "US",
      "name": "Town of China Grove",
      "fclName": "country, state, region,...",
      "countryName": "United States",
      "fcodeName": "administrative division",
      "adminName1": "Texas",
      "lat": "29.39303",
      "fcode": "ADMD"
    }
  ]
}
```

Finally, let's use the **&maxRows=1** parameter to tell Geonames to only return a single row of information, and the **&featureCode=ADM2** parameter (we only want County Names, returned), so that our URL looks like this:

```
http://api.geonames.org/searchJSON?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=&username=mapninja&maxRows=1&featureCode=ADM2
```
Hit enter to test the URL and you should return the following (again, unformatted):
```JSON
{
  "totalResultsCount": 1,
  "geonames": [
    {
      "countryId": "6252001",
      "adminCode1": "TX",
      "countryName": "United States",
      "fclName": "country, state, region,...",
      "countryCode": "US",
      "lng": "-98.52002",
      "fcodeName": "second-order administrative division",
      "toponymName": "Bexar County",
      "fcl": "A",
      "name": "Bexar County",
      "fcode": "ADM2",
      "geonameId": 4674023,
      "lat": "29.44896",
      "adminName1": "Texas",
      "population": 1714773
    }
  ]
}
```
##Bulk Geocoding Against the Geonames API with OpenRefine
Now it's time to start up OpenRefine and use the URL we've just created to submit a Geocoding Request for each of our Texas Counties.

1. Start OpenRefine by double-clicking the Google-Refine.exe (yours will likely have a version number or it might even be called OpenRefine, if you downloaded the Beta release)

2. Click on the **Create Project** link.

3. Select the option to **Get data from this computer** and Click on the **Choose Files** button.

4. Browse to the TexasHealthByCounty.csv and Select it. Click Next

5. Make sure the data is properly formatted in the Preview, change the **Parse data as** parameters until the data is properly formatted. Click **Create Project** in the upper right corner of the page.

##Concatenating Fields in OpenRefine
If you haven't already, now is a good time to take a look at the basics of the GREL (Google Refine Expresion Language)  https://github.com/OpenRefine/OpenRefine/wiki/Understanding-Expressions.

We will first concatenate the COunty and State fields, inserting "County, " as well, for a well formatted placename.

1. Click on the Drop-down Arrow next to the County fieldname and go to **Edit Column>Add Column Based on This One>**

2. Name your new column 'placename'

3. Paste the following into the Expression Window:
```
value+" County, Texas"
```

![OpenRefine Expression Window] (/images/Geocoding_7.png)


4. Notice that your Preview updates and you should see 'Anderson County, Texas' as the result for the first record. Click OK to calculate the concatenation for all of the records in the table.

##Using Our URL as a Template

Now is the time to return to your URL you created, and Copy it from the Browser Address Bar. Again, it should be something like this (with ?????? replaced by your Geonames Username):
```
http://api.geonames.org/searchJSON?q=Bexar+County%2C+Texas&country=US&featureClass=A&continentCode=&username=???????&maxRows=1&featureCode=ADM2
```
1. Now Click on the Drop-down Arrownext to the '**placename**' field and go to **Edit Column>Add Column by Fetching URL**

2. Paste your __template__ URL into the Expression Window (this will result in a null values in the preview, that is fine):

3. Now, Copy the following text:
```
'+ escape(value,'url')+'
```

4. **Carefully** select the following text from the URL you just pasted into the Expression window and replace it by pasting the text you just copied from the clipboard"
```
Bexar+County%2C+Texas
```

5. Add single quotes (') to the beginning and end of the Expression and you should see the syntax error dismissed.

6. Name your New Column "**GEONAMESJSON1**" and set the Throttle Delay value to '200' milliseconds (this will tell OpenRefine to submit 5 Geocode Requests per second).

![OpenRefine Expression Window] (/images/Geocoding_8.png)

Wait for the geocoding to finish (a few minutes, HOORAY FOR PROGRESS MESSAGES!) and you should have something like this:

![OpenRefine Expression Window] (/images/Geocoding_9.png)

##Parsing the JSON for what you need

Cut and paste one of your JSON records into the text tab of the Online JSON Viewer at http://jsonviewer.stack.hu/, then click **Format** and then click on the **Viewer** Tab and expand all of the elements in the panel on the left:

![OpenRefine Expression Window] (/images/Geocoding_11.png)

```
value.parseJson().geonames[0].lat
```
![OpenRefine Expression Window] (/images/Geocoding_10.png)

```
value.parseJson().geonames[0].lng
```
##Clean the 'NR' Values Out of Your Table

##Transform the OutComes and Factors Fields to Numeric

##Export Your Data to a CSV

##Display XY Data in ArcMap

##Spatial Join Your Table to the Counties Data

##Symbolize



#Google APIs
###Limits
###Geocoding
###Elevation
###Directions

#Important Links:
OpenRefine.org: http://openrefine.org/

Understanding OpenRefine Expressions: https://github.com/OpenRefine/OpenRefine/wiki/Understanding-Expressions

The Geonames.org Geocoding API: http://www.geonames.org/export/geonames-search.html

The Tulane Geolocate JSON Wrapper API Documentation: http://www.museum.tulane.edu/geolocate/files/glcJSON.pdf

The Google Geocoding API: https://developers.google.com/maps/documentation/geocoding/

The Google Directions API: https://developers.google.com/maps/documentation/directions/

The Google Elevation API: https://developers.google.com/maps/documentation/elevation/

Online JSON Viewer: http://jsonviewer.stack.hu/


