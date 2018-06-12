---

layout: post
title: Cache Side-Channel Attacks
tags: [Scientific Research in Cyber Security]

---

While Meltdown and Spectre both gained popularity in last couple of months, they leveraged on other attacks for their success which never gained such popularity. Today we will discuss about cache side-channel attacks that make attacks such as Meltdown, a reality.

Before going into some specific details, lets first talk about some background.

## Cache Architecture

Modern processors use three level of caches where Level 1 is the fastest, smallest and closest to the processor whereas Level 3 cache is largest, slowest and shared between all the processors. On the other hand Level 2 cache is not shared between processors.

These cache consists of storage cells called cache lines, each consisting of *B* bytes. The cache is organized into *S* cache sets, each containing *W* cache lines, so overall the cache contains *S.W.B* bytes. The mapping of memory into cache is limited such that:
1. The cache holds copies of aligned blocks of *B* bytes in the main memory which is called as memory blocks.
2. Each memory block in memory can be mapped to a specific cache set , for instance, memory block starting at address *a* can be cached only in the *W* cache lines belonging to cache set *\[a/B\] mod S*.

## Cache Hit and Cache Miss

A cache hit is a state in which data requested for processing by a component or application is found in the cache memory. It is a faster means of delivering data to the processor, as the cache already contains the requested data.

Cache miss is a state where the data requested for processing by a component or application is not found in the cache memory. It causes execution delays by requiring the program or application to fetch the data from other cache levels or the main memory.

## Page Sharing

A page is a fixed-length contiguous block of virtual memory, described by a single entry in the page table. It is the smallest unit of data for memory management in a virtual memory operating system. Sharing memory between processes is generally done for two purposes:
1. It can be used as an inter-process communication mechanism between two different processes.
2. On the other hand, it can also be used to reduce memory foot print by avoiding multiple copies of identical content.

---
## Main Idea

In my previous post we talked about Meltdown, where researchers were able to execute instructions and get data to the cache from kernel address space without adequate previlages. But, this data is useless to the attacker if she cannot identify where the secret stoled information is stored in the cache. So, the attacker needs to pinpoint where exactly the secret is stored in the cache. That is where cache side-channel comes in. Meltdown and Spectre use cache side-channel attacks for exactly locating the secret information in the cache.

## EVICT+TIME
One method of extracting information from cache is to manipulate the cache before each operation of the victim software, and observe the execution time of subsequent victim software execution.

Step 1 is to trigger the execution of victim software operation. This ensures that all the memory blocks accessed during its operation are cached. In step 2 is the spy program accesses memory blocks that atleast completely replace the prior contents of the cache. Specifically the memory blocks of the victim program. In step 3, the spy program records the time required by the victim software to fetch the previously cached values. There are two possibilities:
1. The data can be present in the cache and the access time to the data would be short which implies a cache hit.
2. The data has beed flushed out of cache by the the spy program and the data retrieval takes considerably more time. In this case the data is being fetched from the memory due to a cache miss.

Using these three steps the attacker can use access latency information for finding out the location of the secret data in cache. The granularity of information retrieved depends on the type of cache-side channel attack performed \[[1]\].

![21-1](/assets/img/posts/ScientificResearch/21.png)

## PRIME+PROBE

This method tries to discover the memory blocks read by victim program after the operation by the victim program has completed.

In step 1, the attacker first completely fills the complete cache with her own data. Then in step 2 allows the victim program to complete execution. Then in step 3 the attacker checks each cache sets, if her data is still present or replaced by victim program. This can be identified by cache misses. If the victim program replaces the data stored by the spy program, then it casuses cache miss leading to additional fetching latency as compared to when the data is directly fetched from the cache \[[2]\].

![11-1](/assets/img/posts/ScientificResearch/11.png)

## FLUSH+RELOAD

In step 1, spy program flushes the monitored memory line from cache hierarchy. In step 2, attacker allows the victim program time to access the memory lines and store secret information in cache. In step 3, spy program reloads the memory line and measures the time to load it. If during step 2, the victim program cached some data, then corresponding to those cache sets, the latency of access would be more for the spy program in step 3. Hence using this latency of access, the spy program can identify the cache sets that have been changed by the victim program \[[3]\].

---
## Conclusion

Three attacks proposed above are all methods to steal information from the cache using side-channel information which in this case was access latency. Cache side-channel attacks have been ignored by Intel's threat model for a long time but after the widespread vulnerability Meltdown was disclosed to public, I think it's time for Intel to reconsider.

---
## References
\[1\] Osvik A. D., Shamir A., and Tromer E., "Cache Attacks and Countermeasures: the Case of AES".
\[2\] Percival C., "Cache Missing for Fun and Profit".
\[3\] Yarom. Y, and Falkner K., "FLUSH+RELOAD: a High Resolution, Low Noise, L3 Cache Side-Channel Attack".

[1]: https://eprint.iacr.org/2005/271.pdf "EVICT+TIME"
[2]: https://www.cs.ucsb.edu/~chong/290N-F06/covert-cache.pdf "PRIME+PROBE"
[3]: https://eprint.iacr.org/2013/448.pdf "FLUSH+RELOAD"
---
