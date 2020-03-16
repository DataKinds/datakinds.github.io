---
layout: post
title: Modeling the COVID-19 Outbreak with J
date: 2020-03-15 16:01 -0700
---

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>

_Foreword: I am not an epidemiologist and I don't claim to be. I am, however, a math major and an academic and I feel like I've done my due dilligence in reporting this accurately and correctly._

If you've been watching the news, I assume you've heard about the COVID-19 pandemic. In light of [the CDC releasing a model predicting 62% of Americans will become infected](https://thehill.com/homenews/state-watch/487489-worst-case-coronavirus-models-show-massive-us-toll)<sup>1</sup>, I became interested in how they came to those numbers. In fact, how do we come to any conclusions as to the spread of disease at all?

There are plenty of well established models for disease spread. The simplest model is the [SIR model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SIR_model)<sup>5</sup>, but it comes with a few caveats. The first caveat is that it assumes that once a disease runs its course in a single person, that person will now be permanently immune to the disease. The second caveat is that the model assumes that all infectious people immediately begin to show symptoms. Neither of these assumptions are accurate for COVID-19: [the disease has a 14% reinfection rate](https://www.forbes.com/sites/brucelee/2020/03/15/can-you-get-infected-by-coronavirus-twice-how-does-covid-19-immunity-work/)<sup>6</sup>, and it takes 10 to 14 days for symptoms to appear. 

The model that has been used for the current COVID-19 pandemic is the [SEIRS model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SEIR_model)<sup>*,4,5</sup>. [With the SEIR model, we have 4 states that any particular person can be in: "Susceptible", "Exposed", "Infectious", and "Recovered"](https://www.idmod.org/docs/hiv/model-seir.html)<sup>7</sup>.

* A person is **susceptible** if they are at risk for catching a disease.
* A person is **exposed** if they have caught the disease or are carrying the disease *without* any symptoms. This happens during the incubation period.
* A person is **infectious** if they have now caught the disease and are experiencing symptoms. This is when people go to the hospital, require intensive care, etc.
* A person is **recovered** if they have gotten over the disease and now have some semblance of immunity.

We have 4 variables we can tweak: \\(\beta, \sigma, \gamma, \xi \\). They are all rates over a generic unit of time.

* \\(\beta\\) is the rate at which susceptible people become exposed.
* \\(\sigma\\) is the rate at which exposed people become infectious.
* \\(\gamma\\) is the rate at which infectious people recover.
* \\(\xi\\) is the rate at which recovered people lose immunity and become susceptible.

![States and variables in the SEIRS model.](/assets/imgs/covid-19/SEIR-SEIRS.png "States and variables in the SEIRS model.")

_Used with attribution from [https://www.idmod.org/docs/hiv/_images/SEIR-SEIRS.png](https://www.idmod.org/docs/hiv/_images/SEIR-SEIRS.png). Copyright 2019, Intellectual Ventures Management, LLC (IVM). All rights reserved._ 

These states and variables all correspond directly to differential equations which correlate them. There are still few notes to be made, though. Firstly, this model doesn't represent mortality in the population due to the disease. Because of that, the [basic reproductive rate](https://en.wikipedia.org/wiki/Basic_reproduction_number)<sup>4,7</sup> (that is, the amount of people one person will infect, on average) of the disease is quite simple to calculate: \\(R_0 =  \frac{\beta}{\gamma}\\).

We're going to complicate things a little bit, though. Looking at disease numbers is boring and unimpactful! Instead of analyzing the differential equations, we're going to take a look at an actual simulation which uses those 4 variables. Let's pull up J and get cracking.

Let's take our "unit of time" to be one day. Let's look at some of the information that we have for the virus:

* We know that \\(R_0\\) is about [2.28](https://www.ncbi.nlm.nih.gov/pubmed/32097725)<sup>8</sup> for COVID-19.

* We know that it has an incubation period of about 10 days -- this corresponds to a \\(\sigma\\) of about 

* We know that once symptoms emerge, they persist about two weeks -- this corresponds to a \\(\gamma\\) of about 

* We know that 14%<sup>6</sup> of infected people become re-infected -- this corresponds to a \\(\xi\\) of about 




I want to run a little simulation to figure it out, and to see the physical effects of social distancing and other 

---

Sources:
1. https://thehill.com/homenews/state-watch/487489-worst-case-coronavirus-models-show-massive-us-toll

2. https://www.cdc.gov/csels/dsepd/ss1978/lesson3/section3.html

3. https://www.open.edu/openlearn/health-sports-psychology/health/epidemiology-introduction/content-section-2.1.1

4. https://en.wikipedia.org/wiki/Basic_reproduction_number

5. https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology

6. https://www.forbes.com/sites/brucelee/2020/03/15/can-you-get-infected-by-coronavirus-twice-how-does-covid-19-immunity-work/

7. https://www.idmod.org/docs/hiv/model-seir.html

8. https://www.ncbi.nlm.nih.gov/pubmed/32097725

---

Footnotes:

*: The basic SEIRS model is not actually the model used for COVID-19. The model used is much closer to the one presented in Li, Fuxiang, and Xiao-Qiang Zhao. "A Periodic SEIRS Epidemic Model with a Time-dependent Latent Period." Journal of Mathematical Biology 78.5 (2019): 1553-579. Web.