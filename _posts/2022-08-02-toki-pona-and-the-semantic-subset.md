---
layout: post
title: "[2/100] toki pona and the semantic subset"
date: 2022-08-02 22:38 -0700
tags: toki-pona 100-blog-posts
---

[toki pona is a tiny conlang created by Sonja Lang](https://tokipona.org/). I am not here to write a toki pona tutorial, [that has been done a hundred times over](https://devurandom.xyz/tokipona/) [by many people a hundred times smarter than I am](https://en.wikibooks.org/wiki/Updated_jan_Pije%27s_lessons). 

No, I am here to talk about sets and spaces. 

toki pona has only around 120 words. Each of these words has a very general meaning. Take the word "soweli" for instance. [lipu linku](https://lipu-linku.github.io/) notes that the definition of soweli is "animal, beast, land mammal". But we rarely use soweli to refer to the group of all animals. Instead, soweli is often used to refer to specific animals. Such as large cows. Or my small cat Raven. soweli pimeja li pu! 

![soweli pimeja](/assets/imgs/cats/raven.png)

This means you must combine words with very general meanings if you want to refer to specific objects. English will have a single word to refer to my "home", but in toki pona it could be the 

<table class="tp-gloss">
    <tr>
        <td>tomo</td><td>pi</td><td>supa</td><td>lape</td>
    </tr>
    <tr>
        <td>BUILDING</td><td>(</td><td>SURFACE</td><td>SLEEP</td>
    </tr>
    <tr>
        <td colspan="4"><i>Home? Bedroom? Blanket drawer?</i></td>
    </tr>
</table>

or perhaps depending on the context it could be  

<div style="margin: 0 auto;">
    <table class="tp-gloss">
        <tr>
            <td>tomo</td><td>lili</td><td>pi</td><td>jan</td><td>olin</td>
        </tr>
        <tr>
            <td>BUILDING</td><td>SMALL</td><td>(</td><td>PERSON</td><td>LOVE</td>
        </tr>
        <tr>
            <td colspan="4"><i>Home? Little bachelor pad? Lover's hut?</i></td>
        </tr>
    </table>
</div>

In both cases, the phrase began with the word "tomo". Note that toki pona puts modifiers after the noun (unlike English*) so we will consider "tomo" to be the main noun of the noun phrase, called the head noun.

Both these noun phrases are referring to the same object, my home. These noun phrases are only distinct in that I've described my home differently. I might use the first phrase talking about my home to my parents and the second phrase talking about my home to my friends. Although they take two different perspectives, we may say these noun phrases refer to the same object -- they have the same *referent*.

The head noun gives the reader the first clue as to what the referent of the noun phrase might be. Sometimes, the noun phrase ends there and you'll have to infer the referent from context. Let \\(\operatorname{ref}(T)\\) be the set of all concrete things that the toki pona phrase \\(T\\) can refer to. We may start playing this game immediately: \\(\operatorname{ref}(\text{tomo}) \\) could be my house, it could be your house, it could be the restaurant down the street or the kitchen in a ship at sea. All of these possible referents exist within the _semantic space_ of "tomo". 

In order to narrow the semantic space of "tomo", additional modifiers can be used. The set \\(\operatorname{ref}(\text{tomo lili}) \\) is approximately equal to the set of all small buildings. A naive observer (me, intially) would now jump to a false conclusion: appending modifiers to noun phrases always restricts their semantic space. Or, to use the notation we've just created:

\\[(\forall A,B \in \text{NounPhrases})\;\;\operatorname{ref}(A\,B) \subseteq \operatorname{ref}(A)\\]

This works for large classes of noun phrases. We may deconstruct the noun phrase "tomo lili pi jan olin" word by word, as grammar allows, and show that this concatenation rule holds.

\\(\operatorname{ref}(\text{tomo}) \\) is roughly the set of buildings. \\(\operatorname{ref}(\text{tomo lili})\\) is roughly the set of small buildings -- a subset of the set of buildings. \\(\operatorname{ref}(\text{tomo lili pi jan}) \\) is roughly the set of small buildings associated with people -- a subset of the set of small buildings. \\(\operatorname{ref}(\text{tomo lili pi jan olin}) \\) is roughly the set of small buildings associated with lovers -- a subset of the set of small buildings associated with people.

These noun phrases, which I will call _restrictive_ noun phrases, have modifiers which only serve to narrow down the set of possible referents. Each new modifier concatenated on the end shrinks the possible semantic space of the phrase, carving out and removing referents which don't align with the meaning of the modifier. 

toki pona's equivalent of the proper noun uses restrictive noun phrases. My toki ponized name is "Tala", but to simply be "Tala" is ungrammatical. I must instead be "jan Tala": the person whom is Tala. "Tala" thus serves as a restrictive modifier on the head noun "jan", such that \\(\operatorname{ref}(\text{jan Tala}) \subseteq \operatorname{ref}(\text{jan}) \\). A popular nasin toki allows for an entire noun phrase to stand in as a head noun, allowing something like "kili sike Lemon" (FRUIT CIRCLE "Lemon"). This is, too, a restrictive noun phrase. The proof is left to the reader.

As elegant as they are, not every noun phrase is restrictive. For example, the emphatic particle "a" does not change the referent of a noun phrase, such that \\(\operatorname{ref}(\text{tomo a}) = \operatorname{ref}(\text{tomo}) \implies \operatorname{ref}(\text{tomo a}) \not\subseteq \operatorname{ref}(\text{tomo}) \\). 

One might think that "ala", which roughly translates to "NOT", can produce a non-restrictive noun phrase. However, consider the following sentence:

<div style="margin: 0 auto;">
    <table class="tp-gloss">
        <tr>
            <td>jan</td><td>ala</td><td>li</td><td>kama</td><td>musi</td>
        </tr>
        <tr>
            <td>PERSON</td><td>NOT</td><td>(verb)</td><td>BECOME</td><td>FUN</td>
        </tr>
        <tr>
            <td colspan="5"><i>Nobody is going to start having fun.</i></td>
        </tr>
    </table>
</div>

I contest that \\(\operatorname{ref}(\text{jan ala}) \\) is equivalent to \\(\operatorname{ref}(\text{jan}) \\) in this context. This sentence makes the claim \\((\forall p \in \text{People})\, p \text{ will not have fun}\\). The direct negation of this preposition is \\((\exists p \in \text{People})\, p \text{ will have fun}\\). This corresponds to the toki pona sentence "jan li kama musi" (*People will start having fun*), which is the direct negation of "jan ala li kama musi". Vitally, using "jan ala" as a subject or a direct object makes a logical claim spanning all people. Therefore, \\(\operatorname{ref}(\text{jan ala}) \\) is the set of all people and must be equivalent to \\(\operatorname{ref}(\text{jan}) \\)


I suppose I leave this blog post searching for a more satisfying counterexample to the restrictive noun phrase. I don't think that "ala" actually produces a non-restrictive noun phrase. If anything it produces an effect something similar to "a" where "ala" does not change the semantic space of the noun phrase at all. I additionally think that using the emphatic particle "a" is a cop out since "a" is treated specially in the grammar. Me and other toki ponaists across the Discords talked over some other interesting rules to consider, such as 

\\[(\forall A,B \in \text{NounPhrases})\;\;\operatorname{ref}(A\;B) = \operatorname{ref}(A) \cap \bigcup_{\forall x \in \text{NounPhrases}}\operatorname{ref}(X\;B) \\]

but these rules and their (similarly surprising) effects may be outside the scope of this blog post.

o tawa pona.

---
\* Any noun phrases flipped like this would be the devil reincarnate.