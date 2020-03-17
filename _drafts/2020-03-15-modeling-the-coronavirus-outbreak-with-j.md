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

These states and variables all have differential equations which correlate them. There are still few notes to be made, though. Firstly, this model doesn't represent mortality in the population due to the disease. Because of that, the [basic reproductive rate](https://en.wikipedia.org/wiki/Basic_reproduction_number)<sup>4,7</sup> (that is, the amount of people one person will infect, on average) of the disease is quite simple to calculate: \\(R_0 =  \frac{\beta}{\gamma}\\).

We're going to complicate things a little bit, though. Just looking at disease numbers is boring and unimpactful! Instead of analyzing the differential equations, we're going to take a look at an actual discrete time simulation which uses those 4 variables. But first, we have to figure out what those variables should be.

Let's take our "unit of time" to be one day. Here's some of the information that we have for the virus:

* We know that \\(R_0\\) is about [2.28](https://www.ncbi.nlm.nih.gov/pubmed/32097725)<sup>8</sup> for COVID-19.

* We know that it has an incubation period of about 10-ish days ([2-14, as per the CDC](https://www.cdc.gov/coronavirus/2019-ncov/symptoms-testing/symptoms.html)).

* We know that once symptoms emerge, they persist about two weeks.<sup>9</sup>

* We know that 14%<sup>6</sup> of infected people become re-infected.

We can calculate all the variables we need from these, starting with \\(\sigma\\). Each day, there's a certain chance \\(\sigma\\) for COVID-19 to begin showing symptoms. Each day, the chance that it cumulatively _hasn't_ shown symptoms is \\((1 - \sigma)^N\\), where \\(N\\) is the number of days. This is a simple binomial distribution, and for it to begin showing symptoms after 10 days on average means that we must simply solve for \\((1 - \sigma)^{10} = 0.5\\). So, \\(\sigma = 0.066967\\)

Once symptoms emerge, they persist about two weeks. Let's do the same process we did above: \\((1 - \gamma)^{14} = 0.5\\) means we have \\(\gamma = 0.0483048\\).

We can calculate \\(\beta\\) using \\(R_0 =  \frac{\beta}{\gamma}\\) with the \\(R_0\\) and \\(\gamma\\) values established above<sup>8</sup>. \\(2.28 = \frac{\beta}{0.0483048}\\) means \\(\beta = 0.110134944\\).

All's left is to calculate \\(\xi\\). I have no clue how we could do this, but let's just say (as a completely random, reasonable guess) that it takes about 30 days to lose immunity to COVID-19. \\((1 - \xi)^{30} = 0.5\\) gives us \\(\xi = 0.02284\\).

Now that we have all our variables, let's boot up our J session and start writing this simulation!

First, numeric definitions.

```j
ppl =: 10 NB. How many people are in our simulation?

Beta  =: 0.110134944 NB. The rate at which susceptible people become exposed.
Sigma =: 0.066967    NB. The rate at which exposed people become infectious.
Gamma =: 0.0483048   NB. The rate at which infectious people recover.
Xi    =: 0.02284     NB. The rate at which recovered people lose immunity and become susceptible.
R     =: 2.28        NB. How many people will 1 person infect?
CFR   =: 0.02        NB. Case Fatality Rate
```

Note that we added one extra parameter, the [Case Fatality Rate](https://wwwnc.cdc.gov/eid/article/26/6/20-0320_article)<sup>9</sup>. This is the percent of COVID-19 cases which end in death. The CDC suggests using 0.25% to 3%, so I'd say 2% is a solid medium, especially with as overwhelmed our US medical systems will be.

If we want to simulate the disease's spread, we have to simulate social contact. So, let's make a [hermitian matrix](https://en.wikipedia.org/wiki/Hermitian_matrix) representing how close people are.

```j
   ] closeness =: 2 %~ (+ |:) ? (ppl,ppl) $ 0
0.989266 0.433774 0.587923 0.860137 0.423383 0.669393 0.296438  0.658781 0.253612 0.327771
0.433774 0.797844 0.412043 0.734478 0.693995 0.452715 0.585249   0.67874 0.591025 0.897202
0.587923 0.412043 0.916071 0.336036 0.652928 0.531987 0.541966  0.477779 0.522089 0.293315
0.860137 0.734478 0.336036 0.125376 0.438783 0.451785 0.695368  0.468632 0.508739 0.603744
0.423383 0.693995 0.652928 0.438783 0.557975 0.450049 0.722475  0.560327 0.676762 0.809449
0.669393 0.452715 0.531987 0.451785 0.450049 0.435358 0.839259  0.223557 0.835785 0.611344
0.296438 0.585249 0.541966 0.695368 0.722475 0.839259 0.380239   0.36727 0.321142 0.258312
0.658781  0.67874 0.477779 0.468632 0.560327 0.223557  0.36727 0.0571881  0.41249  0.63065
0.253612 0.591025 0.522089 0.508739 0.676762 0.835785 0.321142   0.41249 0.483952 0.657467
0.327771 0.897202 0.293315 0.603744 0.809449 0.611344 0.258312   0.63065 0.657467  0.83782
```

This matrix represents how likely the \\(i\\)th and \\(j\\)th person are to be in social contact with each other. Since it doesn't make sense for someone to give the virus to themselves, let's get rid of the middle diagonal.

```j
   ] risk =: closeness - closeness * =i.ppl 
       0 0.433774 0.587923 0.860137 0.423383 0.669393 0.296438 0.658781 0.253612 0.327771
0.433774        0 0.412043 0.734478 0.693995 0.452715 0.585249  0.67874 0.591025 0.897202
0.587923 0.412043        0 0.336036 0.652928 0.531987 0.541966 0.477779 0.522089 0.293315
0.860137 0.734478 0.336036        0 0.438783 0.451785 0.695368 0.468632 0.508739 0.603744
0.423383 0.693995 0.652928 0.438783        0 0.450049 0.722475 0.560327 0.676762 0.809449
0.669393 0.452715 0.531987 0.451785 0.450049        0 0.839259 0.223557 0.835785 0.611344
0.296438 0.585249 0.541966 0.695368 0.722475 0.839259        0  0.36727 0.321142 0.258312
0.658781  0.67874 0.477779 0.468632 0.560327 0.223557  0.36727        0  0.41249  0.63065
0.253612 0.591025 0.522089 0.508739 0.676762 0.835785 0.321142  0.41249        0 0.657467
0.327771 0.897202 0.293315 0.603744 0.809449 0.611344 0.258312  0.63065 0.657467        0
```

Now, let's make our people! Let `0` represent a dead person, `1` represents a susceptible person, `2` represents an exposed person, `3` represents an infectious person, and finally `4` represents a recovered person.

Our population can then be 10 susceptible people with one infected person.

```j
   ] starting_pop =: 2 , (ppl - 1) $ 1
2 1 1 1 1 1 1 1 1 1
```

Let's then define the `infect` function which, given a population, advances the disease by one timestep. It'll be a monadic (one-argument) function of the population:

```j
infect =: 3 : 0
```

We're now in the definition for `infect`. Let's get a boolean vector representing who's infectious in the population.

```j 
 can_spread =. (1&< *. 4&>) y
```

Let's use use this vector on the risk matrix, so that each row represents the collective social contact that the `n`th person in the `j`th column has with an infected person.

```j
 infectiousness =. can_spread ,./ . * risk
```

Let's look at what we've got at this point (output statements added for clarity).

```j
   infect =: 3 : 0
    smoutput ] can_spread =. (1&< *. 4&>) y
    smoutput ] infectiousness =. can_spread ,./ . * risk
   )

   starting_pop
2 1 1 1 1 1 1 1 1 1

   risk
        0 0.320427 0.628024  0.856909 0.705072  0.162919 0.684089 0.0256438 0.531406  0.88479
 0.320427        0 0.653424  0.152774 0.607565  0.589595 0.232512  0.534223 0.724831 0.739889
 0.628024 0.653424        0  0.485708 0.693178  0.307283 0.455467  0.381945 0.193344 0.431928
 0.856909 0.152774 0.485708         0 0.748798 0.0610626 0.490139  0.803612 0.724202 0.437314
 0.705072 0.607565 0.693178  0.748798        0  0.658209 0.722431  0.330002 0.355295  0.69828
 0.162919 0.589595 0.307283 0.0610626 0.658209         0 0.306342  0.590838 0.779528 0.632452
 0.684089 0.232512 0.455467  0.490139 0.722431  0.306342        0  0.820687 0.481649 0.315347
0.0256438 0.534223 0.381945  0.803612 0.330002  0.590838 0.820687         0 0.638455 0.561562
 0.531406 0.724831 0.193344  0.724202 0.355295  0.779528 0.481649  0.638455        0 0.770651
  0.88479 0.739889 0.431928  0.437314  0.69828  0.632452 0.315347  0.561562 0.770651        0

   infect starting_pop
1 0 0 0 0 0 0 0 0 0
        0 0 0 0 0 0 0 0 0 0
 0.320427 0 0 0 0 0 0 0 0 0
 0.628024 0 0 0 0 0 0 0 0 0
 0.856909 0 0 0 0 0 0 0 0 0
 0.705072 0 0 0 0 0 0 0 0 0
 0.162919 0 0 0 0 0 0 0 0 0
 0.684089 0 0 0 0 0 0 0 0 0
0.0256438 0 0 0 0 0 0 0 0 0
 0.531406 0 0 0 0 0 0 0 0 0
  0.88479 0 0 0 0 0 0 0 0 0
```

We see now that `infect starting_pop` prints that person 1 is infectious, and they're putting a couple people at very large risk -- namely, person 4 with contact level `0.856909` and person 10 with contact level `0.88479`.

In our simulation, human contact averages a level of `0.5`, so we'll use that as a baseline. Anything above `0.5` increases \\(\beta\\), and anything below `0.5` decreases \\(\beta\\). This simulates reality, with people tending to transfer disease to people they're closer to more readily. 

Let's now calculate risk for every person in the population seperately. There are 4 risks we're working with here, and we can represent each of them with vectors of their respective probabilities. Let's place people into those groups, first:

```j
infect =: 3 : 0
 can_spread =. (1&< *. 4&>) y
 susceptible =. y = 1
 exposed     =. y = 2
 infectious  =. y = 3
 recovered   =. y = 4

 smoutput ] infectiousness =. can_spread ,./ . * risk
)
``` 

Now let's find each person's level of at risk social contact, and clamp that somehow. This gets a little hand wavy... what happens if all of a person's sick friends have a closeness rating of 1? Epidemiological models usually don't need to take social closeness into consideration when simulating populations, because they generally don't simulate individual people themselves. So, I'm instead going to average all of the values of someone's social closeness over the amount of possible people that can infect. This will effectively turn someone's social closeness into a binomial distribution, with a maximum value of `1` and a minimum value of `0` and a mode of `0.5`. I'd rather scale this down though, so it has a minimum of `-0.2`ish, a maximum of `0.2`ish, and a mode of `0`, so that being social/asocial is not the only defining aspect of whether someone falls ill. This looks like:

```j
5 %~ 0.5 -~ (+/ can_spread) %~ +/ |: susceptible * infectiousness
```

The rest of the function is simple enough, we just add in the rates for people in each category, then we calculate some random numbers for each person per day and advance them through the illness if they are so unlucky. This is now the full `infect` function.

```j
infect =: 3 : 0
 can_spread =. (1&< *. 4&>) y
 susceptible =. y = 1
 exposed     =. y = 2
 infectious  =. y = 3
 recovered   =. y = 4

 infectiousness =. can_spread ,./ . * risk

 susceptible_to_exposed =. (Beta * susceptible) + 5 %~ 0.5 -~ (+/ can_spread) %~ +/ |: susceptible * infectiousness
 exposed_to_infectious =. Sigma * exposed
 infectious_to_recovered =. Gamma * infectious
 infectious_to_dead =. CFR * infectious
 recovered_to_susceptible =. Xi * infectious
 
 1 (I.recovered_to_susceptible>?ppl$0)} 4 (I.infectious_to_recovered>?ppl$0)} 0 (I.infectious_to_dead>?ppl$0)} 3 (I.exposed_to_infectious>?ppl$0)} 2 (I.susceptible_to_exposed>?ppl$0)} y
)
```

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

9. https://wwwnc.cdc.gov/eid/article/26/6/20-0320_article

---

Footnotes:

*: The basic SEIRS model is not actually the model used for COVID-19. The model used is much closer to the one presented in Li, Fuxiang, and Xiao-Qiang Zhao. "A Periodic SEIRS Epidemic Model with a Time-dependent Latent Period." Journal of Mathematical Biology 78.5 (2019): 1553-579. Web.