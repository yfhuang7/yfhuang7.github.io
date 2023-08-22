---
title: "Tips for batch in bash"
date: 2021-01-05
draft: false
tags: [batch, bash shell]
---

I'm currently writing a batch script for running [LROSE](https://ncar.github.io/lrose-core/), a software that allows me to convert raw radar reflectivity into rainrate and accumulated rainfall. The software is working on Linux system, and it takes multiple steps to achieve the goal. Thus, I wrote a batch script in bash. It's not my first time writing bash script, but it was the first time I used more statement and loop. I would like to share some useful lines here with you.

&nbsp;  


# Know where the run is and debug - `echo`  

`echo` is a very common and simple command in bash, and it's usually the first few commands you learn for programming (the same in other programming languages, they don't use `echo`, but either `print` or something similar). `echo` outputs the strings, therefore, it can show you the status and what the value of the valuable at the time. I used `echo` to know which part of script has completed, where it stopped and gave errors, which variable might have problem.

&nbsp;    



example of echo for status with the variable `$RADAR_NAME`:
```
echo "Converting NEXRAD RAW data for $RADAR_NAME to CfRadial format now"
```
  
&nbsp;  

If the script finished its run without showing the "Converting...", then I know something wrong before this part, and this part actually not started running yet. If the script stopped at one of the `$RADAR_NAME`, then there might be some problem for the raw radar data of certain radar. If it printed out all the lines with each radar name, then I know this part has successfully run.

&nbsp;  


# From the past to the future - Adding days/months/years on a date  

In my batch, I would like the script to run day by day, or an event period (+- a few days with a date), I thought I will need to list out all the days in a month; set the if statement for nonleap year, and write the script of adding the day, month, and year based on the date. It was so much work, but I found out that I can use this instead all I mentioned before for dealing with the accumulated date:

&nbsp;  



```
$(date +%Y -d "$case_date + $i day"
```

&nbsp;  

Here, it's saying I want to get the year (`+%Y`) of the date (`$case_date`) that is i days after (`+ $i day`). Isn't it neat? 

&nbsp;  

# Detecting if the command is still running on the background - `pgrep`  

The last tip I would like to share today, is how to know if the command is still running and when to start the next command. In my workflow, some of the command can be run at the same time, but they might take a few hours to finish, and the next commands can only be run when the results and products of current command exist. For running the command at the same time, I will put the run in the background by using `&` in the end of the command line. For checking if the command is still running, I used `$(pgrep -x "<Your command>")`. For example, I've run a command earlier, `RadxConvert ... &`, in the script, then I can ask the script to sleep for a while, then wake up and check if those command is still running in the background (check the PID of "RadxConvert" in my case), if it is still running (with some PIDs returned), then take a nap and check later; if the run is finished (no PID returned), then go to the next command/line in the script:
&nbsp; 

```
echo "Waiting for RadxConvert, sleep for 8 hours"
sleep 8h
PID=$(pgrep -x "RadxConvert")
while [ ! -z "$PID" ]; do
  echo "sleep for 15 mins"
  sleep 15m
done
```

&nbsp;  

This is what I found useful today doing research, hope you found something exciting and useful as well! Aloha!

&nbsp;