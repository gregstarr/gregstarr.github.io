---
title: Age of Empires 2 Counters Poster
author: gstarr
categories:
  - Age of Empires
date: 2019-10-09T00:06:00.106Z
thumbnail: /assets/uploads/post-picture.jpg
---
## [Link to Poster](https://github.com/gregstarr/aoe2chart/blob/master/resources/chart.pdf)

![poster preview](/assets/uploads/poster-preview.png)

## Overview

I have been playing a lot of _Age of Empires 2: HD_ lately. An important aspect of the game is creating the correct unit to counter your opponent's units. Since there are many units, it can be hard to remember which unit you are supposed to be building! To serve as both a wall decoration and a quick reminder what all the various counters are for every unit in _AOE2HD_, I created the **Age of Empires 2 Counter Poster**. For every unit in the game, it lists effective counters. The information was scraped from [the AOE2 wiki](https://ageofempires.fandom.com/wiki/Age_of_Empires_II:Portal), then I edited the counter info based on my personal experience. I struggled for a while trying to decide which units should actually be considered "effective counters". Should it be units that trade well in even numbers or should it be units that absolutely demolish the countered unit? What about units that are an effective counter only when massed? Unit counters can be highly situational and so aren't perfectly suited to being put on a chart. For this reason, I decided just to defer to the wiki for the most part, making edits where I think they are needed. I made the chart using python and **matplotlib**, it honestly seemed like the best option, I don't know what other tool I would use to do this.

## Process

### Get Unit Names

First I copied a list of all the units from some list I found on the wiki into the python file "get_unit_names.py". I split up the list into an actual python list and wrote it to a text file. Then I manually edited the text file to remove anything that wasn't a name and to make sure there was only valid names and only one per line.

![unit list](/assets/uploads/unit-list.png)

### Get Unit Data

Then, in the file "get_unit_data.py", I use the [requests](https://requests.kennethreitz.org/en/master/) library to pull the page of each unit according to the list I created in the last step. I use [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/)  to download the thumbnail for the unit as well as its counters (this step is hacky). I create a dictionary who's keys are unit names and who's values contain the thumbnail path, the list of counters and a display name. This dictionary is saved as a YAML file so I can easily edit it later. 

![unit data](/assets/uploads/unit_data.png)

### Narrow list down

I manually created a new text file which only contained the units I wanted to be included in the chart. The same counters will work for everything in the heavy cavalry line so it is pointless to include Knights and Cavaliers, I just include Paladin with the understanding that Paladin means anything in the Knight line of units. This is where I organize the units into their respective classes, determining which section they will go in. 

![units to include](/assets/uploads/units_to_include.png)

### Create Chart

I opened up the `units_to_include.txt` and `unit_data.yml` files and collected two dictionaries. One mapping the unit type to a list of all the units I want to include from that type, and the other mapping unit name to the information I have on that unit. This is the tricky part. I then calculate the "width" and "height" of each section (Infantry, Cavalry ...) in terms of cells. By "cells", I mean cells of my [GridSpec layout](https://matplotlib.org/3.1.1/tutorials/intermediate/gridspec.html). For the width, it is just the maximum number of counters a unit has in that section, plus 2, 1 for the unit thumbnail itself and 1 for a gap between the unit and its counters. The height is simpler, it is just the number of units in that unit type plus 1 for a title. From these I got the total width and height of the chart, the height being the maximum of the sums of the section heights for each column, and the width being the sum of the maximums of the section widths for each column. Then I used nested gridspecs in order to seperate the main title from the chart, the main chart into columns and each column into sections. From there it was pretty simple to plot one thumbnail in each of the axes I created and add the `display_name` to each one (image path and display name pulled from the `unit_data.yml` file mentioned earlier). Matplotlib has no problem saving figures as pdf. 

![grid spec layout](/assets/uploads/layout.png)
