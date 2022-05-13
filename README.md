# Web Scraping with 1 Line of Bash

![bash2notify](https://user-images.githubusercontent.com/25712242/168389905-6f639c5a-61f2-49c3-bad6-605a31dded19.png)

Python with the Beautiful Soup library is a great approach for web scraping, however for small projects as few as one Bash statement can be used to get web data.

Below is an example where the Lynx utility is used to dump the “Sunshine Village Snow Forecast” web page to find how much snow they had.
![toplift](https://user-images.githubusercontent.com/25712242/168390387-a6079907-9fe4-4841-9fc1-809e1cf8da57.png)

## The Lynx Text Browser

The first step in web scraping is to get a web page into a searchable format.

One of the documented aproaches for Web scraping with Bash is to use **_cURL_** with the **_html2text_** tool, this method works fine, however using the Lynx browser offered a one step solution with a cleaner text output.

To install Lynx on Raspian/Debian/Ubuntu use:
```bash
sudo apt install lynx
```
The lynx **_-dump_** option will output a web page to text with HTML tags and Javascript removed. It’s important to note that what you see on the page may not match the outputted text.

Below is an example to get the new snow at Sunshine Village. On the web page Javascript is used to show the snow depth as either centimetres or inches, but on the text output both units and their values are shown.

![mismatch-1](https://user-images.githubusercontent.com/25712242/168391330-03903ee2-388e-4f90-bc6c-88795aafddb4.png)

Bash has a good selection of string manipulation tools. Below is an example to extract the first part of string to only show the snow in centimeters (cm):
```bash
$ theurl="https://www.snow-forecast.com/resorts/Sunshine/6day/mid"
$ thestr="New snow in Sunshine Village:"
$ # Create a variable with the result string from Lynx
$ newsnow=$(lynx -dump "$theurl" | grep "$thestr")
$ 
$ echo "$newsnow"
   New snow in Sunshine Village:  4.8cm1.9in on Fri 8th (after 3 PM)
$    
$ # Get the first part of the string before "cm"
$ # The %% gets the first part
$ echo "${newsnow%%cm*} cm"
   New snow in Sunshine Village:  4.8 cm
```

## Example Bash Webscraping Project 

An example project to get scrape the new snow and base snow depth at Sunshine Ski Resort would be:
```bash
#!/bin/bash
#
# skitrip.sh - show the Sunshine ski conditions in a notification
#
theurl="https://www.snow-forecast.com/resorts/Sunshine/6day/mid"
 
# Get the new snow depth
thestr="New snow in Sunshine Village:"
result=$(lynx -dump "$theurl" | grep "$thestr")
newsnow="${result%%cm*} cm"
 
# Get the base
thestr="Top Lift:"
base=$(lynx -dump "$theurl" | grep "$thestr")
 
# Show the results in a desktop notification
msg="$newsnow\n$base (base)"
icon="$HOME/Downloads/mountain.png"
notify-send -t 10000000 -i "$icon"  "Sunshine Ski Resort" "$msg"
```

This example uses the notify-send utility to put a message on a Linux desktop.

![notification](https://user-images.githubusercontent.com/25712242/168392112-9201a186-c8e4-4e7a-afd2-e7ad04258c4b.png)

## Summary

Scraping web pages can be tricky and the pages can change at anytime.

The Lynx worked on many pages but not all.

The grep utility is extremely useful and it offers a lot of interesting options, such as getting lines before or after the found string.


