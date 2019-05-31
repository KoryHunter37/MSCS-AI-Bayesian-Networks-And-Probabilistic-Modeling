# MSCS Project

This repository overviews an Artificial Intelligence design project which I completed while completing my Master's in Computer Science at Georgia Tech. The source code for this project is not publically available, in compliance with the Georgia Institute of Technology Academic Honor Code<sup><a href="https://policylibrary.gatech.edu/student-affairs/academic-honor-code">[1]</a></sup>.

Instead, I have substituted my raw code with a guided walkthrough of the project itself, the steps I went through to complete it, and the skills I acquired by solving it.

# Bayesian Networks

Bayesian Networks<sup><a href="https://en.wikipedia.org/wiki/Bayesian_network">[2]</a></sup> are a form of predictive analysis which focuses on the probability of events which are dependent on other events.

<p align="center"><img width="400" height="200" src=images/bayesian-network.png></img></p>
<div align="center"><b>Fig 1. Basic Bayesian Network</b></div>

In the figure above, you can see that the probability of the "grass being wet" is influenced by "if the sprinkler is on" and "if it is raining". Additionally, the chances of "if the sprinkler is on" is influenced by "if it is raining."

When calculating the probability that the grass is wet, the Bayesian network not only takes into consideration the effects of the sprinkler and the rain directly on the grass, but also how the rain will effect the odds of the sprinkler being on, which in turn effects the grass.

While that may seem like a lot of things to consider all at once, especially if you are doing these calculations by hand, the Bayesian network makes it easy to automate the process once implemented! Before implementing this, I was required to calculate numerous interwoven probability networks like this by hand, and gained a thorough understanding of the intricacices of the process. This was ultimately very helpful to my implementation (although completely exhausting to do on paper!)

# The Nuclear Power Plant

To start, I was given a series of information snippets, in plain English:

> 1. The temperature gauge reads the correct temperature with 95% probability when it is not faulty and 20% probability when it is faulty. For simplicity, say that the gauge's "true" value corresponds with its "hot" reading and "false" with its "normal" reading, so the gauge would have a 95% chance of returning "true" when the temperature is hot and it is not faulty.
> 2. The alarm is faulty 15% of the time.
> 3. The temperature is hot (call this "true") 20% of the time.
> 4. When the temperature is hot, the gauge is faulty 80% of the time. Otherwise, the gauge is faulty 5% of the time.
> 5. The alarm responds correctly to the gauge 55% of the time when the alarm is faulty, and it responds correctly to the gauge 90% of the time when the alarm is not faulty. For instance, when it is faulty, the alarm sounds 55% of the time that the gauge is "hot" and remains silent 55% of the time that the gauge is "normal."

I was tasked with using Bayesian networks to calculate a variety of probabilities based on these pieces of information. Additionally, we received extremely limited attempts to get the right answers, so I had to perform extremely careful and meticulous calculations by hand to help ensure my implementation was accurate.

<p align="center"><img width="614" height="401" src=images/power-plant.PNG></img></p>
<div align="center"><b>Fig 2. Nuclear Power Plant Network</b></div>

Above is the Bayesian network I designed based on the aforementioned information. I also used the percentages to create discrete distributions, which clarified exactly what sort of probabilistic relationship and effect each event would have on its children nodes.

I performed inference by implementing a custom variation of the Junction Tree algorithm<sup><a href="https://en.wikipedia.org/wiki/Junction_tree_algorithm">[3]</a></sup>. This also allowed me to compute marginal probability, after I supplied evidence into the engine which was operating off the Junction Tree algorithm. 

With respect to any given piece of evidence, I could now find out a resulting probability! For example, If given the status of "Temperature" and "Faulty Guage", I could tell you probability for "Guage". Notice that the relationship between these three is the same as in the sprinkler diagram of Figure 1. This pattern, and those similar to it, arise very often in problems of interrelated probabilities-- which is why solutions like the Bayesian Network are so powerful and helpful!

# Sampling

In this section, I was given a new series of sample data, now involving more factors than ever before. The data represented groups of sports teams, their skill level, their win or loss status, etc.

| variable name | description|
|---------|:------:|
|A| A's skill level|
|B | B's skill level|
|C | C's skill level|
|AvB | the outcome of A vs. B <br> (0 = A wins, 1 = B wins, 2 = tie)|
|BvC | the outcome of B vs. C <br> (0 = B wins, 1 = C wins, 2 = tie)|
|CvA | the outcome of C vs. A <br> (0 = C wins, 1 = A wins, 2 = tie)|

Each team has the following prior distribution of skill levels:

|skill level|P(skill level)|
|----|:----:|
|0|0.15|
|1|0.45|
|2|0.30|
|3|0.10|

Differences in skill levels correspond to the following probabilities of winning:

| skill difference <br> (T2 - T1) | T1 wins | T2 wins| Tie |
|------------|----------|---|:--------:|
|0|0.10|0.10|0.80|
|1|0.20|0.60|0.20|
|2|0.15|0.75|0.10|
|3|0.05|0.90|0.05|

It's a ton of information! But, suppose you know a small bit of information, like the outcome of two of the three games...

It's actually possibly to perform inference using the same Junction Tree algorithm as in the previous part of the problem. After a very, very lengthy setup process and ensuring all this data was entered correctly with respect to my implementation of the Junction Tree algorithm, I was able to perform inference on this part of the problem as well!

However, if such tools were not available to you, a sampling approach might be necessary. I was tasked with implementing two different forms of sampling, both Gibbs sampling<sup><a href="https://en.wikipedia.org/wiki/Gibbs_sampling">[4]</a></sup> and Metropolis-Hastings sampling<sup><a href="https://en.wikipedia.org/wiki/Metropolis-Hastings_algorithm">[5]</a></sup>. The results of these sampling processes should be nearly identical to the previously calculated inference, but they would arrive there by an entirely different means.

### Gibbs

To imlement the Gibbs algorithm, I took in a Bayesian network and returned a sample state drawn from the network's distribution tables. The returned state would always differ by at most one variable, which was chosen arbitrarily. This generated large quantities of samples very neatly, and randomly.

### Metropolis-Hastings

I built a latent probability distribution by repeatedly generating a "candidate" value for each random variable in the system, and then probabilistically accepting or rejecting the candidate value based on an underlying acceptance function. Unlike in Gibbs, the returned state could differ from the initial state at more than one variable. This created a lot more variance in my samples.

### Comparison

The goal of both these sampling methods was to achieve convergence-- such that the resulting calculated probabilities began moving by so little with each sampling iteration that no further samples would be necessary. Both did arrive at the same results, to a stunning degree of accuracy, which matched the results of the Junction Tree engine calculations as well.

However, I did find that Metropolis-Hastings converged, at worst, 10% faster than Gibbs. There was no situation in which I found Gibbs to be more effective than Metropolis-Hastings for this particular problem, and this was substantiated by the similar results my peers shared after the assignment's completion.

# Reflection

This assignment was extremely math-heavy, and made me carefully consider each step of my algorithm implementation by first having me solve these sorts of statistical problems by hand. In my undergraduate AI course, these tools were given to me, and I was told what to do with them, and what they did. However, I was not given a deep understanding of why they worked, or what the underlying mathematical principles behind these algorithms entailed.

In challenging problems I've faced since solving this assignment, especially ones which are high-level and often loose sight of fundamentals, I've been able to take a step back and focus on the foundational guiding principles. This assignment helped me grow as a programmer by reminding me that every fancy technique we have to solve a problem is grounded in some sort of logical, rational idea. Sometimes, when reaching a point of stagnation while trying to solve a problem, stepping back and approaching it from a foundational level can help reveal a solution hiding in plain sight.

I've taken this to heart, and it has become a frequently cited technique for me, both in my workplace and in my personal projects.
