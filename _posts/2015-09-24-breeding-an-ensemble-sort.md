---
layout: post
category : java
tagline: "Developing a fast ensemble sort in Java"
tags : [sorting, sort algorithms, optimization, java, java 8, bleedsort, treesort, sampling, AI, Paavo Toivanen]
---
{% include JB/setup %}

After I blogged about the possibility of [beating Java sort performance](/java/2015/08/17/beating-java-sort-performance/),
I got really obsessed to prove my case with **integers**!
Some new versions
[2](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort2.java)
[3](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort3.java)
[4](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort4.java)
[4b](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort4b.java)
[5](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort5.java)
of bleedsort followed,
along with quick benchmarks
[3](https://github.com/pvto/java-sort-experiments/blob/master/beedsort-3-times.txt)
[3-2](https://github.com/pvto/java-sort-experiments/blob/master/bleedsort-3-times-phase-2.txt)
[4](https://github.com/pvto/java-sort-experiments/blob/master/bleedsort-4-times.txt)
[4b](https://github.com/pvto/java-sort-experiments/blob/master/bleedsort-4b-times.txt)
[4b-2](https://github.com/pvto/java-sort-experiments/blob/master/bleedsort-4b2-times.txt)
[5](https://github.com/pvto/java-sort-experiments/blob/master/bleedsort-5-times.txt)
on different kinds of datasets.  If you look at those, there are many kinds of errors
and biases in them.  I wanted quick insight into my developing algorithms,
not an absolute proof of their value, yet.

Here's a series of plots of binomial benchmarks
with three
successive generations
(plots: [R](https://www.r-project.org/)).

![~bin(0.1, 10000), bleedsort4](/assets/img/bleedsort4/bin-01-n.png)

![~bin(0.1, 10000), bleedsort4b](/assets/img/bleedsort4/bs4-bin-01-n.png)

![~bin(0.1, 10000), bleedsort5](/assets/img/bleedsort4/bs5-bin-01-n.png)

I have ```20*x``` (x≥1) measurements for each data point in all pictures.

#Benchmarks: fallibility and other concerns

As of earlier I was running benchmarks with JMH.  It is an established
microbenchmark framework backed by the expertise of Oracle's
engineers.
It was soon clear that I couldn't test as much and
as fast as I needed to.
My algorithms evolved very quickly and a JMH run for a single datapoint
could take an hour or more if data was hard to prepare.
I wrote versions 3 and 4 of bleedsort
on two successive days, to contrast with these.
I could shorten my JMH sampling times, but the results then had blatant variance.
Also JMH enforced some restrictions in building supportive structures,
recycling data, etc.

I switched away from JMH to hand-written JUnit benchmarks because of these limitations.
JUnit was also more tightly integrated with my IDE, which was nice for explorative
testing.

If I should write a paper about these, then my JUnit tests would not be good enough.
Above pictures illustrate a very
Java-like fault that ensued.
Namely I found JUnit times varying hugely over days.
I run on two different machines, but it had nothing to do with that.
What was I not noticing?

In the end I deduced this unwanted semirandom bias down to the benchmark cycle.
I was benchmarking three algorithms against same data in successive loops.
This was very nice for ad hoc analysis (I could track down performance issues
to the exact form of random data when I needed to),
but running different things together allows them affect each other.
Because small datasets were so fast to sort, I had created extra wight by running
20*50 or 20*100 sorts instead of 20.

When I ran bleedsort3 x times, then bleedsort4 x times, then Arrays.sort x times,
repeat,
I found that with some data sizes bleedsort performance degraded sharply.

I guessed that my benchmarks collided badly with the gc cycle.
Bleedsorts as distribution sorts do make relatively large ad hoc memory reservations.
They were also the sorts that were slowing down,
whereas Arrays.sort had much less variance.
This could be like 100% slowdown in a bad case.
There is a blue datapoint at
array size 4e4 in the last picture that illustrates this.
I could repeat performance degradation simply by re-running a test with specific
parameters, so it was not totally random.

I tried driving gc by hand, but it only made things weird.
However varying the number of inner-loop executions seemed to modify the degradation pattern,
even to the point of removing degradation.  Arrays.sort times remained more stable
throughout.


Now by some manual variation I could find a nice cycle for each measurement.
I switched away from plotting lines (breaking that fun illusion of continuity)
and started plotting individual datapoints.

(This all could be methodized as a search over cycle space,
which is perhaps one of the things that JMH does under the hood – haven't looked there.)

My JUnit tests do some priming too,
not as correct as in JMH, I'm sure, but it helps a little anyway.

So this much about measuring at this stage.


#Explaining my algorithm-specific sampling techniques

I was a bit misleading to speak about bleedsort above.
My sorting turned into an orchestrated ensemble of sort strategies.

From [bleedsort3](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/BleedSort3.java)
upwards, my sorts sample a target array in multiple ways which I'll explain shortly.

For bleedsorts, as [distribution sorts](),
the **amount of repetition** in an array is a key question.  A relatively
unique array will map neatly on a scale, but if there are many densely packed
places and duplicate items, bleedsorts will plunge to suboptimal pit.

This is why I needed to estimate the amount of repetition found in a dataset.
My original idea was to take **twenty** patches of **100** items
from the target array
and calculate average repetition per value from this sample.
It was not enough.

I needed to sample **also longer runs of 1/4 array size**.
The short runs had relatively low likelihood of hitting duplicates in large arrays.

All this sampling means that I can not compete with sorting small or already
sorted arrays.  So I'm not doing that, as stated [before](/java/2015/08/17/beating-java-sort-performance/).
As a teaser however I wrote a
[naive monotonous mergesort](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/MergeSort.java)
that beats Arrays.sort when sorting sine waves of small frequences.

The second thing that I sampled was quite naturally data distribution.
In bleedsort1 I started simply from **three quantiles**, 0th, 50th, and 100th.
This of course meant a very bad estimate for many kinds of data.
In bleedsort2 I used **five quantiles** 0, 25, 50, 75, and 100.
In bleedsort3 I switched to **nine quantiles** 0, 12.5, 25, ... , 100
and have stuck with them since.

I sample a batch of 160 random items from the array and order them
to get nine quantile estimates.
They provide some fast solutions for algorithm choice.
They are also used by bleedsorts.

#Algorithms

Here's a picture of the sort paths of the bleedsort4 ensemble.

![bleedsort4 ensemble](/assets/img/bleedsort4/bs_alg2.png)

Bleedsort3/4 themselves are [distribution sorts](https://en.wikipedia.org/wiki/Sorting_algorithm#Distribution_sort)
based on 9 quantiles that map to eight buckets
in temporary space.
Bleedsort4 is a [counting sort](https://en.wikipedia.org/wiki/Sorting_algorithm#Counting_sort),
and it uses half of
its temporary space for storing item counts, whereas bleedsort3 moves items around
individually.  Both have runtime heuristics to terminate execution when they run to trouble.
Trouble for them means too dense items, making them bleed and slow down.
It means that they can leave an array to a halfway sorted state with the mentality
"OK, I'm sorry but I couldn't do more now".  They then delegate their work
from that state on to another algorithm.

In the code, I tried to keep sampling as cost-effective as possible, but standard
library sort performance is still much better with small target arrays.
This is due to an initial cost brought by sampling and of course because
Java library sorts are quite robust.  With larger arrays, the cost of
sampling gets gradually insignificant and its benefits start to weigh more.

Bleedsort4 and 3 are explained above.
[InntTree](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/InntTree.java)
is a tailored ordered static-depth counting tree
with ```O(1)``` puts and gets within theoretical "clean" complexity, not taking
memory management into account. Iteration is not ```O(1)``` but it is still in practise
very fast.  InntTree is robust in some problems but must be used with some caution.
A big and evenly spread data range increases
memory costs and slows down both storage and retrieval.

As a variation I also implemented
[SmallRangeInntTree](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/SmallRangeInntTree.java),
which has even more
flat a structure with only two node levels and autoexpanding array
buckets to keep memory footprint initially as low as possible.

The following flow chart gives a more detailed explanation of
sampling and selection in bleedsort4.

![bleedsort4 ensemble](/assets/img/bleedsort4/bs_sampling.png)

#Data

I describe here what kinds of distributions and arrangements I tested so far.
I refrained to sorting 32 bit signed integers, as said,
which enables me to use common statistical methods while poking the data.
It means that bleedsort could be extended to longs (64 bit int) and floating point numbers, but not to
arbitrary objects, in particular never to objects that only implement the Java interface [Comparable]()
but do not possess more powerful properties for mapping to a linear space
(ascii strings, for instance, could be mapped in base 128 by their 4 first digits).

I must stress that these benchmarks are very tricky with respect to extrapolating trends, so I would
not recommend that.

I also have insufficient records of what code path is
responsible for each result.
The decision is nowadays stored by the benchmark script,
but it was not when I ran some of these benchmarks.

##Binomial distributions

My first mission was to get binomial distributions very fast.  There is a normal distribution
near to a binomial distribution with sufficiently large values of *n*, so binomial
performance correlates with normal performance.  This makes binomial distributions
nice to be good at.

My main result is graph 1 plotted earlier, and some supplementary benchmarks follow.

Bleedsort performance is very good and also degrades more slowly than that of Arrays.sort
when the binomial base *n* increases
(illustrated in the following picture).
Bleedsort4 slope is especially favorable
towards large *n*'s.  Array size remains 100,000 throughout the following benchmarks:

![bin-05-ie5](/assets/img/bleedsort4/bin-05-ie5.png)
*Chart 2: Binomial distribution sort times with respect to n*

With small *n*'s and array size 100,000, Arrays.sort dominates up to n=15,
but bleedsorts give more constant performance when n grows:

![bin-025-1e5](/assets/img/bleedsort4/bin-025-1e5.png)
*Chart 3: Binomial distribution, moderate array, small n's*

To create data that is more difficult to sort, I benchmarked some
[bimodal binomial distributions](https://en.wikipedia.org/wiki/Multimodal_distribution).

Already with small arrays of 5000 items with
n growing, Arrays.sort starts to slow down in comparison with more constantly performing
bleedsorts.  Arrays.sort however is recommendable for
```bin(p,n)+bin(1-p,n)``` when n < 3000 and array size < 10000:

![bin2-p010-5000](/assets/img/bleedsort4/bin2-p010-5000.png)
*Chart 4: Sorting bimodal binomial distribution ```bin(0.1,n)+bin(0.9,n)``` for various n, array size 5000*

With somewhat larger arrays bleedsorts start to win with much smaller *n*'s,
around n=15:

![bin2-p020-5e4](/assets/img/bleedsort4/bin2-p020-5e4.png)
*Chart 5: Sorting bimodal binomial distribution  ```bin(0.2,n)+bin(0.8,n)``` for various n, array size 50,000*

Sort times of big arrays can be three to four times faster with bleedsorts than with Arrays.sort,
when n is moderately big.  

On the other hand when n is very small (array consists of a few distinct values like
in the following example), Arrays.sort performs better or nearly same.
Bleedsort5 performs better though due to
[SmallRangeInntTree](https://github.com/pvto/java-sort-experiments/blob/master/src/main/java/util/sort/SmallRangeInntTree.java).

![bin2-p025.png](/assets/img/bleedsort4/bin2-p025.png)
*Chart 6: Sorting bimodal binomial distribution ```bin(0.25,10)+bin(0.75,10)```*

![bs5-bin2-025-10.png](/assets/img/bleedsort4/bs5-bin2-025-10.png)

It is an interesting fact to note that Arrays.sort (quicksort) finds a de-centered bimodal distribution
easier to sort than a cluttered one, at least when data is randomly placed:

![bin2-n5000-5e6](/assets/img/bleedsort4/bin2-n5000-5e6.png)
*Chart 7: Bimodal binomial distribution, effect of shifting the peaks*

##Uniform distributions

Uniform distributions are easy to generate.  Sorting might be needed with 3D scenes or
other spatially or temporally ordered series of events.

Bleedsort performance seemed competitive up to 2e7 arrays,
but then something strange happened:

![rnd-u.png](/assets/img/bleedsort4/rnd-u.png)
*Chart 8: Sorting uniform distribution ```U(0,x)``` where x = array size*

Actually there is a bug in sampling code that is frozen
with Bleedsort4 for this post.  I have a [fix](https://github.com/pvto/java-sort-experiments/commit/4490bdf11f3bf27d43f0d45bb534fd6b9d29b335)
for this in an upper version, but it is not tested yet.  I will release at least
one new version, since there are some further ideas pending on my mind.

To get back to uniform distributions, the following two charts show how
when U distributes over a wider range, bleedsort4 heuristics shines over bleedsort3
and Arrays.sort:

![rnd-scatter-1e6.png](/assets/img/bleedsort4/rnd-scatter-1e6.png)
*Chart 9: Sorting scattered uniform distribution ```U(0,1e^x * 1e6)```, array size 1,000,000*

![rnd-scatter-1e5.png](/assets/img/bleedsort4/rnd-scatter-1e5.png)
*Chart 10: Sorting scattered uniform distribution ```U(0,1e^x * 1e5)```, array size 100,000*

Next I wanted to vary the simple uniform distribution to make it appear more like
some real datasets.

I increased redundancy in test distributions,
replacing items by randomly chosen source items from the same array.
This increases repetition in the array.

In these kinds of problems, Arrays.sort won clearly over bleedsorts.
I analyzed this a bit and found that it is a sampling problem.
Bleedsort could pick another sort strategy, but signs of repetition are lost
into the haystack of a big array.  For this I have a fix waiting for new tests.

It is interesting to note that both bleedsorts and Arrays.sort start to perform better,
when data reduplication increases.

![rnd-simil-1e6.png](/assets/img/bleedsort4/rnd-simil-1e6.png)
*Chart 11: Sorting uniform distribution U(0,1e6), array size 1e6, with x\*1e6 random reduplications*

I made also experiments with ```U^x``` type of distributions, which are plotted next.
I will not discuss them here though.

![rnd-simil-exp-4-ie5.png](/assets/img/bleedsort4/rnd-simil-exp-4-ie5.png)
*Chart 12: Sorting U^4 distribution*

![rnd-exp-dupl-2e4-3e4.png](/assets/img/bleedsort4/rnd-exp-dupl-2e4-3e4.png)
*Chart 13: Sorting U^x distributions (experimental feature pending)*

##Monotonously decreasing arrays, with added noise

Sorting decreasing arrays is one classical benchmark for sort robustness.  Some
otherwise performant algorithms are reduced to an awful mess by this problem.
Quicksort in its classical and two-pivot versions handles them elegantly.

Here are some benchmarks from these kinds of tasks.

![decr-1e4.png](/assets/img/bleedsort4/decr-1e4.png)

![decr-1e5.png](/assets/img/bleedsort4/decr-1e5.png)

![decr-1e6.png](/assets/img/bleedsort4/decr-1e6.png)

![decr-2e7.png](/assets/img/bleedsort4/decr-2e7.png)

##Sinusoidal waves
