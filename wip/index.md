---
layout: page
title: work in progress
tags: [stock option, trading, python]
modified: 2014-08-08T20:53:07.573882-04:00
comments: false
image:
  feature: texture-feature-04.jpg
  credit: Texture Lovers
  creditlink: http://texturelovers.com
---

In this paper "[Informed Options Trading prior to M&A Announcements:Insider Trading?](http://irrcinstitute.org/pdf/Informed-Options-Trading_June-12-2014.pdf)", the authors investigated informed trading activity in equity options prior to the announcement of corporate mergers and acquisitions (M&A).

Their finding are not quite surprising: 

- abnormal option trading volumne, particularly for OTM calls, sometimes ITM puts(synthetic long stock)
- volume is significantly higher for short and medium-dated options 
- increased volatility
- higher bid-ask spread
- decreate of term structure of implied vol

There are some methods that they used for data processing are quite useful. For example, they classified options based on moneyness: DOTM, OTM, ATM, ITM, DITM and time to expirations: less than or equal to 30 days, greater than 30 days but less than or equal to 60 days, and more than 60 days. They also investigated zero-volume options in relation to the anouncement date. 

It will be interesting to build an option screening program. The problem with option data is that it's difficult to obtain the data and for each stock at any given date, there will be a lot of option data for different strikes and expiration dates. 

So far, I've only finished the data scraping part. There are lots of things need to be done. It's easy to get lost and give up when you work on your own...
