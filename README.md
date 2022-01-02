# THM-OhSINT-Writeup
Writeup of 2PAC's Attempt at the TryHackMe OhSINT Room

# OhSINT
This writeup cover's 2PAC's completion of the [OhSINT Room on TryHackMe](https://tryhackme.com/room/ohsint). Compared to other rooms, this one was incredibly sparse containing a link to a single task file and the simple question: **What information can you possible get with just one photo?** With that in mind, the room had seven flags for us to find so lets get to work.

Before moving into the actual flags, we need to take a look at what we were provided. It is just a lone jpeg of a blank WindowsXP desktop as shown below:

![WindowsXP](https://user-images.githubusercontent.com/85370905/147888352-e123c3f2-f0af-4b45-9ffc-4b8cfcb902ee.jpg)

When exploiting an image for a CTF or real-world use, it is worthwhile to first look at the available metadata before moving on. In this specific situation it is really all we have to go on before exploring potential steganographic avenues because the desktop image doesn't have any file or program icons. [ExifTool](https://github.com/exiftool/exiftool) is one of the best options for consitently extracting metadata from a wide variety of file types and sources. Below is the extracted metadata for the image in question:

![image](https://user-images.githubusercontent.com/85370905/147888528-532a2a43-5506-4e50-bcef-c1ea5edcfe20.png)

Normally when an image's metadata has a location, it will prove useful in geolocating a target. In this specific case, the coordinates: 54 deg 17' 41.27" N, 2 deg 15' 1.33" W
plotted to a random location on the B6255 road in Yorkshire Dales National Park, UK and were more of a red herring than useful intelligence for the flags in question. With the geocoordinates a dead end, one other piece of data stands out within the metadata, the copyright tag.

![image](https://user-images.githubusercontent.com/85370905/147888609-3cb34ecd-85cd-46bf-b722-8c27d03ae021.png)

A simple google search of this persona revealed some possible sources for investigation:

![image](https://user-images.githubusercontent.com/85370905/147888632-b5a61427-95c7-4172-af0b-f718451a1d56.png)

With these additional links let's determine which flags we can answer and search for more possible sites if required.

## Flag 1: What is this users avatar of?
**Answer:** *Cat*
### Methodology:
The first google result for OWoodFlint is a Twitter page. Opening it shows a cat as the personas avatar which THM acknowledges as correct indicating we have identified the proper target.

![image](https://user-images.githubusercontent.com/85370905/147888705-6d08bc42-205f-4152-aebd-e933dbc88f02.png)

## Flag 2: What city is this person in?
**Answer:** *London*
### Methodology:
Looking at OWoodFlint's twitter history we can find a tweet that highlights an unsecured wireless access point's BSSID near their house.

![image](https://user-images.githubusercontent.com/85370905/147888734-5634d2cf-8274-49b6-abe7-1858e49f5fba.png)

[WiGLE](https://wigle.net) is the go to place for geolocating WiFi access points (Note you will need a free account to do anything beyond heat map searches of WiFi access). Doing an advanced search for the BSSID in question returns a single result and quickly reveals a location in London if the map button is selected.

![image](https://user-images.githubusercontent.com/85370905/147888831-aba2123d-fd35-4bb1-b1e1-1c4c1a08a0cb.png)

## Flag 3: Whats the SSID of the WAP he connected to?
**Answer:** *UnileverWiFi*
### Methodology:
WiGLE provides BSSID -> SSID resolution and it is available in the same search we used for Flag 2.

![image](https://user-images.githubusercontent.com/85370905/147888881-a4b71268-b2d5-4070-8a9b-46617c349607.png)

## Flag 4: What is his personal email address?
**Answer:** *OWoodflint@gmail.com*
### Methodology:
Looking through the rest of OWoodFlint's Twitter profile nothing points to a possible email address. Looking at the other google results, the readme.md for OWoodFlint's single Github repository has their email address listed.

![image](https://user-images.githubusercontent.com/85370905/147888927-d506037f-2f3b-4654-986b-044af4ae58bb.png)

While on the top of Github repositories, it is always worthwhile to look at older commits to see if anything else might be available that was deleted in the latest commit. In this case, there is a history of four commits, but none of the older ones add anything to the investigation.

## Flag 5: What site did you find his email address on?
**Answer:** *Github*
### Methodology:
This flag is implied from our research for Flag 4.

## Flag 6: Where has he gone on holiday?
**Answer:** *New York*
### Methodology:
With nothing to indicate a possible trip or vacation on Twitter or Github, looking at the final result on Google delivers this flag. A Wordpress Blog with a single post indicating they are on vacation to New York. Additionally, from an OSINT perspective it is important to notice the url for this page is https://oliverwoodflint.wordpress.com/author/owoodflint/. We now have the full name of the persona *Oliver Wood Flint* which could be userful for further google dorking or reconnaissance.

![image](https://user-images.githubusercontent.com/85370905/147889027-4a936767-81b8-4d22-9850-8637f4e3f025.png)

## Flag 7: What is this persons password?
**Answer:** *pennYDr0pper.!*
### Methodology:
This was probably the most difficult question because it was unitiuative. When I found it, it was pretty obvious. Initially, I checked [HaveIBeenPwned](https://haveibeenpwned.com/) which indicated OWoodflint@gmail.com was found in a single databreach of Gravatar in 2020:

![image](https://user-images.githubusercontent.com/85370905/147889119-35bc5b73-eb5f-422a-af20-7d67bef84716.png)

This breach however did not have any passwords leaked. Going back to the available sites we found on Google, only the WordPress page gave the possibility of customization. Looking at the page source one thing stood out:
![image](https://user-images.githubusercontent.com/85370905/147889176-93d6f2c6-65dc-42f3-8a22-3fd84de99e2b.png)

The color:#ffffff tag indicates the text should be white on the webpage. Going back to the webpage and highlighting the contents we see that the final flag was just hidden on the blog post:

![image](https://user-images.githubusercontent.com/85370905/147889200-ef1087c2-f213-4a61-8e58-c3749f790843.png)

Overall I hope you enjoyed this write-up to a fairly simple challenge on TryHackMe. One thing worth pointing out that has not been mentioned yet is the need for Operational Security (OPSEC) when conducting OSINT investigations. You do not want to let the persona know they are under surveillance or reconnaisance and while this is just a simple room on TryHackMe, individuals should treat any investigation like a real world investigation. If you choose to do this room you were see there are numerous instances of THM users violating OPSEC principles on both the Twitter (retweets/replies) and Github (Pull Requests) in an attempt to be cute or funny. Not only does this actively harm a potential investigation, but it also hurts attempts by future users to use the THM platform.

-2PAC


# Who we are: ![nilbin-sec-orb3x3](https://user-images.githubusercontent.com/85370905/121083132-0ad60580-c7ad-11eb-90d8-ed91622db41f.png)
Nilbin_Sec is a recently formed hacker group formed out of some of the hardcore fans of Nathan Barnatt's Pseudo-Alternate Reality Game character Dad. Currently we focus our pursuits on OSINT, cryptography, web exploitation, and custom hardware hacking. We can be reached at: nilbinsec (at) gmail (dot) com. If you are interested in learning more about the Dad universe, check out his youtube channel at: https://www.youtube.com/channel/UCFzpLhfgdPPVJ_7YrVO-GSw.
