---
layout: page
title: articles
tags: [probabilistic data structure]
modified: 2014-08-08T20:53:07.573882-04:00
comments: false
image:
  feature: texture-feature-05.jpg
  credit: Texture Lovers
  creditlink: http://texturelovers.com
---

<h2>Introduction to Probabilistic Data Structures</h2>
<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

When processing large data sets, we often want to do some simple checks, such as number of unique items, most frequent items, and whether some items exist in the data set. The common approach is to use some kind of deterministic data structure like HashSet or Hashtable for such purposes. But when the data set we are dealing with becomes very large, such data structures are simply not feasible because the data is too big to fit in the memory. It becomes even more difficult for streaming applications which typically require data to be processed in one pass and perform incremental updates.  

Probabilistic data structures are a group of data structures that are extremely useful for big data and streaming applications. Generally speaking, these data structures use hash functions to randomize and compactly represent a set of items. Collisions are ignored but errors can be well-controlled under certain threshold. Comparing with error-free approaches, these algorithms use much less memory and have constant query time. They usually support union and intersection operations and therefore can be easily parallelized.

This article will introduce three commonly used probabilistic data structures: bloom filter, HyperLogLog, and Count-Min sketch.

## Bloom Filter

A bloom filter is a bit array of m bits initialized to 0. To add an element, feed it to k hash functions to get k array position and set the bits at these positions to 1. To query an element, feed it to k hash functions to obtain k array positions. If any of the bits at these positions is 0, then the element is definitely not in the set. If the bits are all 1, then the element might be in the set. A bloom filter with 1% false positive rate only requires 9.6 bits per element regardless of the size of the elements.

![Bloom Filter Image]({{ site.url }}/images/bloomfilter.png)

For example, if we have inserted x, y, z into the bloom filter, with k=3 hash functions like the picture above. Each of these three elements has three bits each set to 1 in the bit array. When we look up for w in the set, because one of the bits is not set to 1, the bloom filter will tell us that it is not in the set.

Bloom Filter has the following properties:

- False positive is possible when the queried positions are already set to 1. But false negative is impossible.
- Query time is O(k)
- Union and intersection of bloom filters with same size and hash functions can be implemented with bitwise OR and AND operations.
- Cannot remove an element from the set

Bloom filter requires the following inputs:
m: size of the bit array
n: estimated insertion
p: false positive probability

The optimum number of hash functions k can be determined using the formula: 
	
	𝑘=𝑚/𝑙𝑛2

Given false positive probability p and the estimated number of insertions n, the length of the bit array can be calculated as:

	m=𝑛𝑙𝑛𝑝(𝑙𝑛2)^2

The hash functions used for bloom filter should generally be faster than cryptographic hash algorithms with good distribution and collision resistance. Commonly used hash functions for bloom filter include Murmur hash, fnv series of hashes, and Jenkins hashes. Murmur hash is the fastest among them. MurmurHash3 is used by Google Guava library’s bloom filter implementation.


## HyperLogLog

HyperLogLog is a streaming algorithm used for estimating the number of distinct elements (the cardinality) of very large data sets. HyperLogLog counter can count one billion distinct items with an accuracy of 2% using only 1.5 KB of memory. It is based on the bit pattern observation that for a stream of randomly distributed numbers, if there is a number x with the maximum of leading 0 bits k, the cardinality of the stream is very likely equal to 2^k.

For each element 𝑠𝑖 in the stream, hash function ℎ(𝑠𝑖) transforms 𝑠𝑖 into string of random bits (0 or 1 with probability of 1/2): 	
	
	ℎ:𝐷→{0,1}^∞ 
	
	𝑆=𝑠1𝑠2𝑠3… => ℎ(𝑠1)ℎ(𝑠2)ℎ(𝑠3)…

The probability P of the bit patterns:

	0xxxx… → P = 1/2
	01xxx… → P = 1/4
	001xx… → P = 1/8

The intuition is that when we are seeing prefix 0𝑘1…, it’s likely there are 𝑛≥2𝑘+1 different strings. By keeping track of prefixes 0𝑘1… that have appeared in the data stream, we can estimate the cardinality to be 2𝑝, where p is the length of the largest prefix.

Because the variance is very high when using single counter, in order to get a better estimation, data is split into m sub-streams using the first few bits of the hash. The counters are maintained by m registers each has memory space of multiple of 4 bytes. If the standard deviation for each sub-stream is 𝜎, then the standard deviation for the averaged value is only 𝜎√𝑚. This is called stochastic averaging.

For instance for m=4,

	ℎ(𝑣)=𝑏1𝑏2𝑏3𝑏4𝑏5…

The elements are split into m stream using the first 2 bits (00, 01, 10, 11) which are then discarded. Each of the register stores the rest of the hash bits that contains the largest 0𝑘1 prefix. The values in the m registers are then averaged to obtain the cardinality estimate.

HyperLogLog algorithm uses harmonic mean to normalize result. The algorithm also makes adjustment for small and very large values. The resulting error is equal to 1.04√𝑚.

Each of the m registers uses at most log2log2 n + O(1) bits when cardinalities ≤𝑛 need to be estimated.

Union of two HyperLogLog counters can be calculated by first taking the maximum value of the two counters for each of the m registers, and then calculate the estimated cardinality.

## Count-Min Sketch

Count-Min sketch is a probabilistic sub-linear space streaming algorithm. It is somewhat similar to bloom filter. The main difference is that bloom filter represents a set as a bitmap, while Count-min sketch represents a multi-set which keeps a frequency distribution summary.

The basic data structure is a two dimensional 𝑑×𝑤 array of counters with 𝑑 pairwise independent hash functions ℎ1…ℎ𝑑 of range 𝑤. Given parameters (𝜀,𝛿), set 𝑤=[𝑒/𝜀], and 𝑑=[𝑙𝑛1/𝛿]. 𝜀 is the accuracy we want to have and 𝛿 is the certainty with which we reach the accuracy. The two dimensional array consists of 𝑤𝑑 counts. To increment the counts, calculate the hash positions with the d hash functions and update the counts at those positions.

![Bloom Filter Image]({{ site.url }}/images/count-min-sketch.png)

The estimate of the counts for an item is the minimum value of the counts at the array positions determined by the d hash functions.

The space used by Count-Min sketch is the array of w*d counters. By choosing appropriate values for d and w, very small error and high probability can be achieved.

Example of Count-Min sketch sizes for different error and probability combination:

|𝜺     | 𝟏−𝜹   |    𝒘 |    d |   wd |
|-----:|------:|-----:|-----:|-----:|
|0.1   | 0.9   |   28 |    3 |    84|
|0.1   | 0.99  |   28 |    5 |   140|
|0.1   | 0.999 |   28 |    7 |   196|
|0.01  | 0.9   |  272 |    3 |   816|
|0.01  | 0.99  |  272 |    5 |  1360|
|0.01  | 0.999 |  272 |    7 |  1904|
|0.001 | 0.999 | 2719 |    7 | 19033|

Count-min sketch has the following properties:

- Union can be performed by cell-wise ADD operation
- O(k) query time
- Better accuracy for higher frequency items (heavy hitters)
- Can only cause over-counting but not under-counting

Count-Min sketch can be used for querying single item count or “heavy hitters” which can be obtained by keeping a heap structure of all the counts.

## Summary

Probabilistic data structures have many applications in modern web and data applications where the data arrives in a streaming fashion and needs to be processed on the fly using limited memory. Bloom filter, HyperLogLog, and Count-Min sketch are the most commonly used probabilistic data structures. There are a lot of research on various streaming algorithms, synopsis data structures and optimization techniques that are worth investigating and studying.

If you haven’t tried these data structures, you will be amazed how powerful they can be once you start using them. It may be a little bit intimidating to understand the concept initially, but the implementation is actually quite simple. Google Guava has bloom filter implementation using murmur hash. Clearspring’s Java library stream-lib and Twitter’s Scala library Algebird have implementation for all three data structures and other useful data structures that you can play with. I have included the links below.

## Links

1. [Probabilistic Data Structures for Data Analytics](http://bigsnarf.wordpress.com/2013/02/08/probabilistic-data-structures-for-data-analytics/)
2. [Bloom Filter Wikipedia](http://en.wikipedia.org/wiki/Bloom_filter) 
3. [HyperLogLog Paper](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf) 
4. [Google HyperLogLog Paper](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/40671.pdf) 
5. [HyperLogLog - Cornerstone of a Big Data Infrastructure](http://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/)
6. [Count-Min Sketch Paper](http://dimacs.rutgers.edu/~graham/pubs/papers/cm-full.pdf)
7. [Sketch Techniques for Approximate Query Processing](http://people.cs.umass.edu/~mcgregor/711S12/sketches1.pdf)
8. [stream-lib](https://github.com/addthis/stream-lib) 
9. [algebird](https://github.com/twitter/algebird)
