---
layout: post
title: Spectre Attack without Shared Memory
# subtitle: Excerpt from Soulshaping by Jeff Brown
cover-img: /projects/spring-2023/unshared-spectre/spectre.png
# thumbnail-img: /assets/img/thumb.png
# share-img: /assets/img/path.jpg
# tags: [books, test]
author: Hrishikesh Jedhe Deshmukh
date: December 5, 2023
---

### Description
We implemented Spectre attack without any shared memory as part of CS 683 - Advanced Computer Architecture course project. 

Original proof of concept code has a victim function
in which transient instructions are exploited by attacker to
retrieve secret data. The function is as follows:
{% highlight javascript linenos %}
void victim_function(size_t x) {
    if (x < array1_size) {
        temp &= array2[array1[x] * 512];
    }
}
{% endhighlight %}
Branch predictor is initially mistrained to always predict
that the if statement is true by providing valid inputs
of `x`. Then attacker provides an invalid input of `x` to
the victim function. This causes the branch predictor to
mispredict and speculatively execute the if statement. The
transient instruction `temp &= array2[array1[x] *
512]` brings the data at `array2[array1[x] * 512]`
in the cache hierarchy. Attacker then accesses `array2`
indices in a loop and measures the time taken to access each
index. Note that `array2` is shared while `array1` is not.
If the time taken to access an index is less than a threshold,
it means that the data at that index was brought in the cache
hierarchy by the transient instruction. This way attacker can
retrieve the secret data.

Original code had both attacker and victim in the same
process. We separated them both into different processes
while retaining shared memory. Instead of `array2`, which
acted as shared memory in the original proof of concept
code, we mapped a file to both processes. This file now
acts as shared memory, having the exact same functionality
of `array2`.
Both attacker and victim are forked and execed by a
parent process. Both processes need to be mapped to the
same core of the CPU for this attack to work as we are
detecting the secret data by measuring its latency of access.
In the victim process, `array2` points to the mapped file.
In the attacker process, `array2` points to the same mapped
file. Thus we can use the memory of the file as shared
memory to probe for lines which are brought into cache
speculatively.

We modelled spectre attack without shared memory by
keeping attacker and victim in same process. Attacker and
victim do not access each other’s data structures. Attacker
first primes the cache by accessing a certain number of
linked list nodes. Then attacker calls the victim function with
valid input. Then attacker probes the cache by accessing the
same linked list nodes. This is done repeatedly to mistrain
the branch predictor. Then on the last iteration, attacker
calls the victim function with invalid input. This causes the
branch predictor to mispredict and speculatively execute the
if statement. Attacker then probes the cache to see which
cache lines have been accesses by the victim.
Prime and probe is done on L1D cache here. This is
possible due to the fact that attacker and victim reside in
the same process.

### Papers
[Spectre](https://ieeexplore.ieee.org/document/8835233)

### Links
[Report](./report.pdf) <br/>
[Github Repo](https://github.com/khushangsingla/The-UNSHARED-Spectre)

### Contributors
[Khushang Singla](https://www.cse.iitb.ac.in/~khushangsingla/) <br/>
[Sankalan Baidya](https://github.com/PrrsnCrVS4sr)