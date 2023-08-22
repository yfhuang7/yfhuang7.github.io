---
title: Circular Statistics in R
date: 2018-11-12 00:53:27
tags: [research tool, statistics, R]
---

Circular statistics is a very interesting tool to visualize continuous data, such as direction, angles, or time.  I'm now applying it to visualize the peakflow occurrence time in Hawaii.  I found this book is very helpful: [Circular statistics in R by Arthur Pewsey, Markus Neuhauser, and Graeme D. Ruxton](http://circstatinr.st-andrews.ac.uk/).  I practiced the functions in the R package, circular, by following this book and applied it to my own research.  I'm showing some simple examples below, see if you're interested in. :)

For sure, we need to install and load this package, [circular](https://cran.r-project.org/web/packages/circular/circular.pdf)
{% codeblock %}
  install.packages("circular")
  library(circular)
{% endcodeblock %}

The first two practices from the [book](https://amzn.to/2OETBHN) is like this:
{% codeblock %}
  windc <- circular(wind, type = "angles", units = "radians", template = "geographics",
                  rotation = "clock", zero = pi/2)
  plot(wind, xlab = "Observation number", ylab = "Wind direction (in radians)")
  plot(windc, cex = 1, bins = 720, stack = TRUE, sep = 0.035, shrink = 1.3)
  axis.circular(at = circular(seq(0,7*pi/4, pi/4), rotation = "clock", zero = pi/2), labels = c("N","NE","E","SE","S","SW","W","Nw"),
              zero = pi/2, rotation = 'counter', cex = 1.1)
  ticks.circular(circular(seq(0.2*pi, pi/8)), zero = pi/2, rotation = "clock", tcl = 0.075)

  ## Rose Diagram (histogram for circular)
  rose.diag(windc, bins = 16, cex = 1.5, prop = 1.3, shrink = 1.3, add = T, axes = F)
{% endcodeblock %}


Here is how I use for all the peakflow in Hawaii:
{% codeblock %}
  plot(allGage.circular, cex = 0.5, bins = 720, stack = TRUE, sep = 0.035, shrink = 1.8, axes = F,
       col = "grey50")
  axis.circular(at = circular(seq(0,330,30), rotation = "clock", zero = pi/2, units = "degrees"), labels = month.ab,
                zero = pi/2, units = "degrees", rotation = 'counter', cex = 1)
  ticks.circular(circular(seq(0,330,30), rotation = "clock", zero = pi/2, units = "degrees"), zero = pi/2, rotation = "clock", tcl = 0.075)
  lines(density.circular(na.omit(allGage.circular), bw = 10), lwd = 2, lty = 2, col = "red")
  legend("bottomleft", legend = c("Peakflow Date for all gages", "Kernal Density Estimates"), col = c("grey50", "red"),
         lwd = c(NA,2), lty = c(NA,2), pch = c(16,NA))
{% endcodeblock %}

![When does peakflow occur in Hawaii?](/attachments/circular_pk_all.jpg)

It also can be use to comparing different sources or different time block, for instance, I compared all the gages by island and by time. I only have figures here since the original code for these including other calculation with loops, and it's too much to show here.

Peakflow occurrence time by island:
![Peakflow occurrence time by island](/attachments/circular_pk_islands.jpg)

Peakflow occurrence time by years:
![Peakflow occurrence time by every 9 years](/attachments/circular_pk_bytimes.jpg)

Feel free to leave your comments or questions. Here is the amazon link to the book if you need:

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=vindy0530-20&marketplace=amazon&region=US&placement=0199671133&asins=0199671133&linkId=6fc3493d1e032e79f3c1ac2d6618ce6f&show_border=true&link_opens_in_new_window=true"></iframe>
