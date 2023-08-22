---
title: "Download National Water Model (NWM) Data from Google Cloud Using Gsutil"
date: 2022-11-17T11:56:57-10:00
tags: [Google Cloud,NWM]
---

[The National Water Model (NWM)](https://water.noaa.gov/about/nwm) is a hydrologic modelling framework that simulates observed streamflow and complements official National Weather Service (NWS) river forecasts across the U.S., particularly produces guidance at locations that has no traditional river forecast. In the past, it is very difficult to access any NOAA archives. However, thanks to the [NOAA Open Data Dissemination (NODD) program](https://www.noaa.gov/information-technology/open-data-dissemination), a great number of NOAA data are now online cooperating with Amazon Web Services, Google, and Microsoft Azure. I found archived NWM outputs on [Google Cloud](https://console.cloud.google.com/storage/browser/national-water-model?pageState=(%22StorageObjectListTable%22:(%22f%22:%22%255B%255D%22))&prefix=&forceOnObjectsSortingFiltering=false). It's super to have the archive, but the next question came in: **How am I going to bulk download? I don't want to click folders by folders and download files by files.**  

&nbsp; 

Fortunately, Google provides the [Google Cloud Command Line Interface (gcloud CLI)](https://cloud.google.com/sdk/docs/install-sdk), and the way to use it is very similar to how we use the shell. You just need to install the latest gcloud CLI with the selected system following their [guide](https://cloud.google.com/sdk/docs/install-sdk#windows). Then, you can start using it!  

&nbsp; 

First, you'll want to know the gsutil URI of the data you're going to download (here, NWM) by checking the configuration:  

![gsutil.jpg](/attachments/gsutil.JPG)  

  
Then, you can go to your terminal or jupyter notebook/lab to view the contains (with the URI) of the folder by typing in the terminal:
```
gsutil -m ls "<gsutil URI>"
```

or in jupyter notebook/lab:

```
!gsutil -m ls "<gsutil URI>"
```

Of course, you can view it one the Google Cloud web console, but it will be much faster and clear by using gsutil when there are tons of data in the folder.  

&nbsp; 

After you figure out what data you want to download, you can download it by the concept of `cp`: simply copy the data from Google Cloud to your computer. The way to use it through gsutil is: `gsutil -m cp "<URI of the file>" .`. Besides download a single file, you can also download a folder by using `gsutil -m cp -r "<URI of the folder>" .`. Even better, you can also apply the wildcard using gsutil, ex: `gsutil -m ls "gs://national-water-model/nwm.202104*/" .` - in my case, it will list all the subfolders and files under each NWM folder in April, 2021.    
  
&nbsp; 

Is't it cool? Give it a try! 