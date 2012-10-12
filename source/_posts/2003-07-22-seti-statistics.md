---
title: SETI statistics
layout: post
categories:
- Personal
  - Technical
---
For those who have been through my archives or just know me, you’ll know that I participate in the [SETI@home experiment][1]. I know that other projects out there exist, such as the [Folding project][2], but I find SETI to be a fascinating project, and it’s also the more ubiquitous geeky distributed computing project, so I naturally have to participate in it.

One of the things that is interesting about the SETI project is that it’s not just about looking for extraterrestrial life, but about how much that you as an individual contributes. I personally like to know how many units I’ve processed, and my relatively new computer seems to crank out about six units a day, plsu I’ve got another one or two computers running the project, so it’s always interesting for me to see where my stats are. The more astute observer will note that there is now a “Brian’s SETI stats” section in the sidebar. I use the Moveable Type system to blog, and could have written an MT plugin to accomplish the statistics on the side, but that’s not fast enough for me. I don’t post enough for it to be updated quickly, and I’d really like real-time statistics. Fortunately for me, sometime back I decided to change the default extensions of all of my pages to be PHP instead of HTML, so that I could insert some simple PHP here and there if desired.

One of the real blessings of PHP is that it is very easy to include code from other files very easily. I’m now taking advantage of that inclusion factor to display my SETI statistics. PHP (well, the PHP install I’ve got going on this server) has full support for XML, and SETI provides me with an XML stream, so using some fun PHP and XML trickery, any time that my page is loaded, the XML stream is grabbed, parsed, and the relevant pieces of information are extracted and printed out as HTML to the browser. For those interested, here is a link to a text file containing my source code. This source code is open source, a modified twist on some XML parsing code I wrote some months ago, and is totally free for use by anyone. I’ve obviously modified what I’m presenting to you from what I actually use, as I do not wish to divulge the e-mail address that I provide to SETI. After all, if you really want to see my stats, just look to the sidebar now.

[SETI statistics code][3]

In my blog template, I simply added the line <? include ‘setistats.txt’ ?> – substituting setistats.txt for the actual file that I used, of course – and now the server automatically grabs, parses, and displays my SETI stats every time the page is loaded. It’s a very convenient system. Enjoy!

 [1]: http://setiathome.ssl.berkeley.edu/
 [2]: http://folding.stanford.edu
 [3]: http://www.randomthink.net/setistats.txt