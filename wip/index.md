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

Their findings are not quite surprising: 

- abnormal option trading volumne, particularly for OTM calls, sometimes ITM puts(synthetic long stock)
- volume is significantly higher for short and medium-dated options 
- increased volatility
- higher bid-ask spread
- decreate of term structure of implied vol

There are some methods that they used for data processing are quite useful. For example, they classified options based on moneyness: DOTM, OTM, ATM, ITM, DITM and time to expirations: less than or equal to 30 days, greater than 30 days but less than or equal to 60 days, and more than 60 days. They also investigated zero-volume options in relation to the anouncement date. 

It will be interesting to build an option screening program. The problem with option data is that it's difficult to obtain the data and for each stock at any given date, there will be a lot of option data for different strikes and expiration dates. 

So far, I've only finished the data scraping part. There are lots of things need to be done. Python is great for quick prototyping. I tried MongoDB first because I started with Google finance API and the data was already in json format. But google blocks you if you send requests with many threads. So I had to switch to webpage scraping. Also Google finance does not show options for ETFs, very strange. I was able to download daily data in about 10 minutes. However, the Mongo DB size grows so fast that I am looking to switch to mysql, flat file or pytable. Simple is good. 
