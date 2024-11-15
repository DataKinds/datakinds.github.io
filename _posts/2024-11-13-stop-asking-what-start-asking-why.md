---
layout: post
title: Stop Asking What, Start Asking Why
date: 2024-11-13 15:03 -0700
tags: software
---

It tends to be the case that doing something professionally can kill one's drive to do so recreationally. Despite this, I still enjoy hobby programming and participating in online programming communities in my off-time. In these communities I have found a wealth and breadth of knowledge I would not have encountered otherwise. However, that comes with a risk: it is very easy to waste your time online. Even worse, the variance in how much you will get out of any given conversation is massive. 

There is a lot of precedent for how to maximize the value of your time when you _ask questions_ online. "[Don't ask to ask](https://solhsa.com/dontask.html)" and (perhaps more humorously) [Cunningham's Law](https://en.wikipedia.org/wiki/Ward_Cunningham#Law) are what immediately comes to my mind in this context. There is much less precedent for how to answer questions -- or even why we choose to spend our time answering questions online.

Eric S. Raymond [has written at length about asking good questions](http://catb.org/~esr/faqs/smart-questions.html)[^1] and yet left the "*How To Answer Questions in a Helpful Way*" section stunted at 9 suggestions. Stack Overflow is the same: the page [describing how to ask questions](https://stackoverflow.com/help/how-to-ask) is rich with direct suggestions on how to make the best use of your time asking questions. Most of the given advice is generally applicable to any development forum with tags and post titles. The [page describing how to answer them](https://stackoverflow.com/help/how-to-answer) is about half the length and full of softer advice dedicated to SO's specific position as a community question-archival site: writing with good grammar, providing context to avoid link bitrot, using the bounty system to ask someone to continue your partial answer. 

There is an asymmetry here. Advice given to question-askers involves getting the most value out of the time you and the community spend on said question. This *is* ultimately the goal of the asker: to learn, to gain value from the question at hand. Contrast this with advice given to question-answerers, which is more scatter-shot and generally revolves around *producing a satisfactory answer*. This is unmotivating as *this is ultimately not the goal of the answerer!* I claim, supported by the text above, that there are really two reasons someone would want to answer questions:

1. **To learn.**

    Common knowledge dictates that one does not understand something if they cannot explain it. Explaining things online is a fantastic way to find one's own knowledge blind spots; collaborative problem-solving gives a very low-risk opportunity to dive deeper into topics just outside of one's reach. 

    Learning also happens at a community level. Raymond notes that a helpful answer will "*[h]elp your community learn from the question.* When you field a good question, ask yourself 'How would the relevant documentation or FAQ have to change so that nobody has to answer this again?' Then send a patch to the document maintainer.".

2. **To grow their community.**

    Raymond notes that "[Hackers] like answering questions for people who have demonstrated they can learn from the answers". A person who grew after interacting with a community is going to come back for more. This is a positive feedback loop, and it's multiplied by the near-realtime responses and casual atmosphere given by platforms such as Discord or IRC. Strong learners with interesting, insightful, novel questions make for great community members!

I am excluding any communities that set up gamified or otherwise perverse objectives to encourage participation (upvote this post if you agree!)

This brings me to my ultimate point: **how do we maximize these incentives as question-answerers**? What can we do to go above and beyond producing helpful answers, making the time we spend answering more valuable to us? Here are a couple of things that I have personally found to make my time participating online more valuable:

## The great, nonexhaustive framework for online participation

* Use realtime platforms to your advantage
    * As mentioned above, instant messaging platforms allow for instant feedback between question-askers and question-answerers. Many of the concerns about asking good questions come from a time of email lists and traditional forums, where a lack of information in the original post could take days to be rectified. Over Discord, getting clarifications from askers is often a matter of minutes. This drastically limits the frustration and wasted time associated with badly-framed questions.
    * The conversational nature of chat platforms allows troubleshooting and stating a problem space to naturally become brainstorming. This is not the case on Q&A platforms, for example, as there is a strict distinction between "question" and "answer". Intermediate brainstorming and conversation is relegated to a comments section or a secondary chat room. On an IM platform this distinction does not exist -- to maximize learning, one must take advantage of this.
* Assume good faith
    * This is the titular "stop asking what, start asking why".
        * Bad actors or low-effort posters in online communities are inevitable. That being said, 
        * I have seen enough instances in programming communities where someone will come in with a conversational style or set of ideas that resemble bad-faith posting. They'll use ALL CAPS or a twyping qwirk or send / messages / with / one / word. They might be stubborn or rash or stuck on their own ideas. 
        * These posters often present some of the most unique ideas available, and I have found that engaging can often maximize learning (sometimes at the cost of frustration). As a question-answerer, maximizing learning mandates that one must assume good-faith in these situations and dig into the "why"s of their choices rather than being stuck on the "what"s of their possibly absurd current situation. This *does* often require skill in analyzing XY problems.
    * Assuming a given person is a bad actor is a losing-sum game. Consider the cases:
        * A good-faith question is met with an assumption of good-faith ➡️ everyone is happy, the question may get answered
        * A bad-faith question is met with an assumption of good-faith ➡️ either the asker improves their question, gets bored and leaves, or they escalate, at which point you can disengage
    * Or, on the contrary:
        * A good-faith question is met with an assumption of bad-faith ➡️ a potential community member is turned away
        * A bad-faith question is met with an assumption of bad-faith ➡️ time is saved as you may disengage immediately
    * To maximize community growth, we must therefore assume good-faith to avoid turning away potential community members. 
    * To maximize learning as a result of these interactions, we must consider that nothing is learned upon turning away community members or immediately disengaging with bad-faith questions. *Something* may be learned if the is answered or if the badly-posed question is improved. Attempting to assist with the low-effort poster's issue may give the answerer an opportunity to gain a deeper knowledge of the system being badly asked about. 
    * Efficient moderation allows active community participants to more effectively gauge when to disengage with an actual bad actor, minimizing the time wasted.
* Address XY problems through risk analysis
    * To address an XY problem, one must first learn to identify an XY problem. 
        * Look for questions where the asker wants to know how to carry out a task using tooling that isn't made for that task
        * Look for questions where the asker insists there is only one way forward 
        * Look for questions where the asker thinks a process is *supposed* to work.
    * Address the problem fully if you choose to address it. Ask the asker what they're trying to accomplish instead of entertaining their solution. Note that they only see trees where you may see the forest. Learn from the aspects of the situation that they've missed, and consider if there are any similar workflows that you have that are suffering from the same blind spots.
    * Raymond states the fundamental point well: "*If you're going to answer the question at all, give good value.* Don't suggest kludgy workarounds when somebody is using the wrong tool or approach. Suggest good tools. Reframe the question." 
* Askers must answer. Answerers must ask.
    * To maximize learning, you must be willing to have a conversation!
    * Make use of the resource that other question-answerers provide.
    * There must be no hierarchical distinction between those who ask and those who answer in a community. Maximization of learning requires that everyone is comfortable to both seek knowledge and to share their subset of knowledge. 
        * The effect where longer standing members of a community are afforded more technical weight than newer members needs to be explicitly considered when the goal is the breakdown of hierarchy.

I hope that you're able to get something out of the suggestions I've made above. If not, hopefully the reframing of online discussion as something potentially valuable encourages you to go out and participate :)


---

[^1]: Shout out to the footnote at <https://dontasktoask.com/> for linking Raymond's essay, I hadn't seen it before and it's a complete treasure trove of info. Without that this whole blog post was just going to be the `Assume good faith` bullet point.