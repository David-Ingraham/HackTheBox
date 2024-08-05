# Web Enumeration

Enumeration itself means to list something or count and mention the items in a set. In the context of web penetration testing this means looking for files or directories not intened for public use or visability on a webserver (running on port 80 http or 443 https). 


# GoBuster
A tool like goBuster can be used in conjuction with a collection of common words to bruteforce the possible resources on a webserver. curl <ip address> will send a GET request tot he root directory of the server that will likley be confirgured to respond with the a file titled index.* ,(php, html jsx, etc). But how would you know if there are other files on the server side not being displayed by the client? goBuster will see if there are files on the server that match the strings in your commonWords file. The same process applies so subdomains. Gobuster accomplishes this with a dns server. GoBuster will list the http responds codes for the resources so you can see which ones are available.


# Banner Grabing
You can also use curl with the -IL flags to grab the headers of the http request to find more details about the server such as the server software version. whatweb and eyewitness also help get more info on a server.

# Cerificates
A certificate list information about the company using the server. This info can be used to create a phising attack to target people working at the company.

# Robots.txt
This file may exist on a web server to impede or alter the effect of web scraping or web crawling. Web scraping frameworks automatically look for this file so that the site maintainer can limit certain resources only to regular web traffic. In doing so, they also reveal the existence of certain files that might be explotaible. 

 

 
