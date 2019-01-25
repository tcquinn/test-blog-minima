---
layout: post
title:  "Bayesian vs. non-Bayesian statistics"
author: Ted Quinn
categories: ted
math: true
---

The following is my attempt to lay out what I think I understand about the distinction between Bayesian and non-Bayesian approaches to probability and statistics, in order to get the discussion started. There are a few important caveats here. First, I'm going to overexplain a bunch of basic concepts that you already know well. My intention in doing this is to try to be explicit about the way I think about these basic concepts in order to illuminate any differences in how we each think about these things and/or mistakes in my own thinking.

Second, although I will try to be clear about the distinctions between axioms, definitions, assumptions, theorems, interpretations, etc., I'm going to be pretty loose with the mathematical details. My aim is to try to get the _logic_ straight without slowing down to make the notation clear and consistent or to consider all of the edge cases and complexities of things like infinite sets, etc. In doing so, it's definitely possible that I'll end up glossing over something that ends up being important.

Third, and most importantly, some portion of what follows is almost certainly wrong or at least confused. I say this because, having read and thought a bit about this topic at a few different points over the last year or so, I can't for the life of me figure out what all of the fuss is about. As far as I can tell, the Bayesian and non-Bayesian approaches are two equally valid approaches to a wide range of common problems. They ask different sorts of questions, they make different sorts of mappings between the real world and the underlying mathematical structure, they make different sorts of assumptions, and they use different language. In the end, though, it seems to me that Bayesian approaches work better for certain types of questions with certain combinations of known and unknown quantities when certain kinds of assumptions are valid, and non-Bayesian approaches work better for different types of questions with different combinations of known and unknown quantities when different kinds of assumptions are valid.

However, I must be wrong about this, because lots of people who know a lot more than I do about probability and statistics come at this distinction as if it's a kind of holy war. The Bayesian people talk about the non-Bayesian people as if they're aging relics, still stuck in the dark ages of statistics, sitting in their dusty offices debating the minutiae of competing explanations for why the sun goes around the earth. The non-Bayesian people talk as if the Bayesian people are foolish children, ignorant of history and of all of the layers of knowledge that have been built up over centuries, running around and shouting about the Next Big Thing without bothering to understand the old thing, and making absurd claims as a result. These are not just undereducated and overcaffeinated internet trolls. These are people with PhDs in statistics.

So I must be wrong. _Caveat lector_.

## Shared foundations

This section is my attempt to lay out all of the things that are the _same_ between the Bayesian and non-Bayesian approaches. As far as I can tell, these are the things that the Bayesian people and the non-Bayesian people _agree_ on.

### Definitions and axioms of probability theory

At the core of both approaches is a set of mathematical structures that we use to reason about probabilities. Roughly (and I'm going to try to stop adding qualifying adverbs like this; just see caveats above), the structures are the following. We consider a set $$\Omega$$ and a mapping $$\mathbf{P}$$ from subsets of $$\Omega$$ to $$\mathbb{R}$$ that satisfies three properties:

* $$\mathbf{P}(A) \geq 0$$ for any subset $$A$$
* $$\mathbf{P}(A \cup B) = \mathbf{P}(A) + \mathbf{P}(B)$$ for any disjoint subsets $$A$$ and $$B$$.
* \$$\mathbf{P}(\Omega) = 1$$

That's basically it. People use lots of different language when describing these objects, but most people tend to call call $$\Omega$$ the **sample space** and most people refer to the elements of $$\Omega$$ as **outcomes**. Often we then refer to the subsets of $$\Omega$$ (the things like $$A$$ and $$B$$ above) as **events**. We call $$\mathbf{P}$$ a **probability law** and the real number that you get when you apply $$\mathbf{P}$$ to an event is called the **probability** of that event.

The intuition here is that $$\Omega$$ is a set of possible outcomes in some real-world situation and $$\mathbf{P}$$ describes the relative likelihood of these outcomes. The axioms above try to capture a minimal things we need to be true to ensure that the output of $$\mathbf{P}$$ behaves the way we want likelihoods to behave. Note that, based on the three axioms above, you can pretty quickly derive lots of other things you'd want to be true of things we'd call probabilities, such as:

* \$$\mathbf{P}(\emptyset) = 0$$
* If $$A \subset B$$, then $$\mathbf{P}(A) \leq \mathbf{P}(B)$$
* \$$\mathbf{P}(A \cup B) \leq \mathbf{P}(A) + \mathbf{P}(B)$$

and so on.

Now, of course, this isn't the most robust, elegant mathematical formulation of all of this stuff. The "real" formulation uses measure theory (i.e., the sample space is a measure space, the probability law is a measure, etc.). The "real" formulation has some significant advantages over the informal formulation above (e.g., it's much easier to handle discrete and continuous variables in a single set of axioms and to extend all of the machinery above to uncountably infinite sets), but I think the formulation above captures everything that's important for this discussion. I'm happy to be proven wrong about this.

### Conditional probabilities

Having established the structures above and verified that they behave the way we want probabilities to behave, we would now like to define another structure that captures the concept of the likelihood of $$A$$ occurring (or having occurred) given that we know that $$B$$ is occurring (or has occurred). That is, we want to define the **conditional probability of** $$A$$ **given** $$B$$, which we want to write as $$\mathbf{P}(A \vert B)$$. It turns out that the following definition captures this concept well. Given a sample space $$\Omega$$, a probability law $$\mathbf{P}$$, and two events $$A$$ and $$B$$, we define $$\mathbf{P}(A \vert B)$$ as:

$$
\mathbf{P}(A \vert B) \equiv \frac{\mathbf{P}(A \cap B)}{\mathbf{P}(B)}
$$

I won't do it here, but it's pretty easy to verify two things. First, this definition produces the "right" answer for the conditional probability in all those cases for which we have a strong intuition for what the "right" answer should be. Second, defined in this way, $$\mathbf{P}(A \vert B)$$ is a legitimate probability law in its own right. That is, if $$\mathbf{P}(A)$$ satisfies all of the axioms above, then so does $$\mathbf{P}(A \vert B)$$. This is important, because we can just carry over all of our theorems about probability laws to conditional probabilities.

### Bayes' rule

Now we come to Bayes' rule, which is one of the first places that the false controversies appear to start. A direct consequence of the definition of conditional probability above is the so-called chain rule,

$$
\mathbf{P}(A \cap B) = \mathbf{P}(A \vert B) \mathbf{P}(B)
$$

This is fairly intuitive (i.e., the probability of $$A$$ and $$B$$ occurring is the conditional probability of $$A$$ occurring given $$B$$ times the probability of $$B$$ occurring) and extensions to three or more variables are obvious. If we just apply this rule twice (alternating the roles of $$A$$ and $$B$$) and rearrange, we get Bayes' rule:

$$
\mathbf{P}(A \vert B) = \frac{\mathbf{P}(A) \mathbf{P}(B \vert A)}{\mathbf{P}(B)}
$$

This is a very useful rule, because it allows you to calculate the conditional probability of $$A$$ given $$B$$ from the conditional probability of $$B$$ given $$A$$ and the overall probabilities of $$A$$ and $$B$$. This rule takes an especially useful form when $$A$$ is one of a collection of mutually exclusive and collectively exhaustive possible outcomes (i.e., when $$A_1 \cap A_2 \cap \cdots \cap A_n = 0$$ and $$A_1 \cup A_2 \cup \cdots \cup A_n = \Omega)$$. Then we can expand the denominator of Bayes' rule to write:

$$
\mathbf{P}(A_i \vert B) = \frac{\mathbf{P}(A_i) \mathbf{P}(B \vert A_i)}{\mathbf{P}(A_1) \mathbf{P}(B \vert A_1) + \cdots + \mathbf{P}(A_n) \mathbf{P}(B \vert A_n)}
$$

A very common (and highly consequential!) application of this rule is medical tests. Let's say we know that a particular rare disease afflicts one out of 100,000 people. We have a test for this disease which is 99% accurate in the following sense: if a patient has the disease, the test is 99% likely to come out positive, while if a patient does not have the disease, the test is 99% likely to come out negative. Let's say a particular patient tests positive. How likely is it that the patient has the disease? This is a straightforward application of Bayes' rule:

$$
\begin{align*}
\mathbf{P}(\text{Disease} \vert \text{Positive}) & = \frac{\mathbf{P}(\text{Disease}) \mathbf{P}(\text{Positive} \vert \text{Disease})}{\mathbf{P}(\text{Disease}) \mathbf{P}(\text{Positive} \vert \text{Disease}) + \mathbf{P}(\text{No disease}) \mathbf{P}(\text{Positive} \vert \text{No disease})} \\
& = 0.00099
\end{align*}
$$

Even though the patient tested positive and the test is 99% accurate, there is still less than a tenth of a percent chance that the patient has the disease because having the disease is so unlikely in the first place. This is the kind of thing that Bayes' rule often helps us calculate: updating our understanding of the likelihood of something based on new information.
The important point is that, despite what Nate Silver may want you to think, none of the preceding is unique to "Bayesian statistics". It's not like non-Bayesian statisticians deny the truth of Bayes' rule. Bayes' rule is a direct consequence of the axioms of probability and the definition of conditional probability. It's totally fundamental. All statisticians, Bayesian and non-Bayesian alike, would follow the same logic above if you asked them the question about the patient and the medical test. None of them is going to claim that the patient has a 99% chance of having the disease. That's not non-Bayesian statistics. That's just wrong. If there are distinctions between Bayesian and non-Bayesian approaches, they are more subtle than this.

### Statistical inference

Finally, both Bayesian and non-Bayesian statisticians do statistical inference. That is, given a set of data, they will both try to _infer_ something about the underlying probability law from which the data is drawn. The most common form this takes is **statistical modeling**. In a statistical modeling approach, one makes a set of assumptions (often very strong assumptions) about the structure of the underlying probability law (e.g., "Let's assume the data is drawn from a normal distribution with mean $$\mu$$ and standard deviation $$\sigma$$...") and then, under these assumptions, one tries to say something about the unknown parameters in the model (e.g., the $$\mu$$ and the $$\sigma$$). Importantly, in both Bayesian and non-Bayesian approaches, these claims about the underlying probability law are always probabilistic claims. That is, we use the data to try to quantify our confidence in our conclusions about the underlying probability law. It's important to point this out because it's another caricature of non-Bayesian approaches that only Bayesian approaches are capable of producing such probabilistic conclusions and that non-Bayesians don't have the language or the machinery to do so.

## Bayesian vs. non-Bayesian interpretations of probability

As far as I know, everything above is common to both Bayesian and non-Bayesian approaches. Now I'm finally going to get to my understanding of the differences between the two approaches. As far as I can tell, the first difference is in how Bayesians and non-Bayesians _interpret_ all of the machinery above. Based on the axioms above (or their more formal, well-defined counterparts), all of the actual mathematical machinery of probability is well-defined, unambiguous and shared by Bayesians and non-Bayesians. However, the interpretation is notoriously slippery (whenever I say "interpretation", I mean the same thing that I mean in physics: the non-well-defined mapping between the real world and the well-defined mathematical machinery). It turns out to be kind of hard to say exactly what one means by the statement, "There is a 40% chance of rain today." What claim exactly is one making? In what sense might we regard such a statement as being true or false?

The most common non-Bayesian interpretation seems to be the **frequentist** interpretation. In the frequentist interpretation, all probabilistic statements are taken to be statements about large ensembles of identical systems. In other words, when you say that there's a 40% chance of rain today, what you're really saying is that, in a collection of days exactly like today, 40% of them will contain rain. This kind of interpretation is fairly natural in at least two kinds of situations. The first situation is when you're sampling data from a large population. When you say that a randomly chosen person in the U.S. has a 52% chance of being a Democrat, what you're really saying is that 52% of people in the U.S. are Democrats (the extension to conditional probabilities is obvious). The second situation is in experimental settings. When you say that a given experiment result has a 10% chance of being positive, you're really saying that if you do the experiment exactly the same way a very large number of times, 10% of the experiments will have a positive result.

This interpretation has some clear advantages. When it works well, it's clear and intuitive, and it can help you figure out what you mean when things get complicated with lots of different variables and cause and effect get entangled (i.e., you can always step back, get clear on the sample space, and start counting). However, it also has some obvious drawbacks. For example, what exactly does one mean by an ensemble of identical systems? After all, in a classical physics world (let's not bring in quantum mechanics here), if you prepare the experiment _exactly_ the same way, you'll get exactly the same result every time. The uncertainty you're trying to capture evaporates. So one must mean something like a collection of systems in which some set of things are identical (i.e., the things we control) and another set of things are allowed to vary (i.e., the things we don't control), but then things get murky fast. It's the same with the seemingly innocuous statement above about a 40% chance of rain. One must mean something like, "In a large collection of days that are exactly the same as today in terms of all of the data I have observed but possibly different on all kinds of smaller scales, 40% of them have rain." Oof.

In the Bayesian interpretation, one dispenses with all of these awkward ensembles and just interprets probabilistic statements as degrees of belief. In other words, when you say there's a 40% chance of rain, you're just making a statement about your level of confidence that it will rain (e.g., you're half as confident as when you say that there's an 80% of rain). If this sounds a bit vague, it is. However, if one starts from the notion that one wants some number to represent one's degree of belief about things, one can pretty quickly come up with a bunch of axioms that such a number should satisfy (e.g., one's belief that $$A$$ and $$B $$are both true will always be less than or equal one's degree of belief that just $$A$$ is true, etc.) If you start going down this path and defining more properties, you basically end up at precisely the probability axioms above. In other words, it's roughly true that probabilities as they are defined above are more or less the unique mathematical structures that behave the way we expect degrees of belief to behave (I think it's a bit more complicated than this because there are different sets of properties one could demand, etc., but the probability axioms above are one pretty natural place one can land).

This interpretation has some obvious advantages, too. You get to ditch all of those awkward ensembles, and it's much easier to extend probabilistic interpretations to situations where it's unclear what the ensemble is (e.g., what is the probability of going to war with North Korea this year?). The downside is that the interpretation is kind of thin. It's nearly just a restatement of the axioms themselves. It doesn't give one much intuitive purchase on the world. Once you define your degree of belief about various things, the machinery above will then let you calculate your degrees of belief about lots of other things, but it's hard to know how to define your ingoing degrees of belief. It seems kind of arbitrary. There's nothing you can just count.

So the two interpretations above are really different conceptually, but still, my sense is that it's a bit like competing interpretations of quantum mechanics. They represent totally different ways of thinking about what one actually means by probability, but these differences just aren't that important at the level of actual calculations. Once they agree on a question, Bayesians and frequentists get exactly the same answers. The just talk about the answers differently.

However, your interpretation of what probability means _can_ impact the kinds of questions one asks, and I think this is maybe where the holy war starts. See the next section.

## Bayesian vs. non-Bayesian inference

As we said above, both Bayesians and non-Bayesians want to be able to infer truths about an underlying probability law by examining data that is drawn from that probability law, and they both want those truths to be framed in probabilistic terms.

However, if you're using a frequentist interpretation of probabilities, then it's a bit tricky to make say what you mean by probabilistic claims about the underlying probability law. Given a particular probability law, it's easy to imagine an ensemble of different data sets drawn from that probability law, but it's harder to imagine an ensemble of different probability laws. They're not like experiments you can do the same way over and over and there is no clear population from which they're drawn. Put another way, it's very unnatural to think of $$\mu$$ and $$\sigma$$ as random variables in their own right. Perhaps for this reason, or perhaps for other good reasons I don't understand, frequentists are very careful never to make direct probabilistic claims about features of the underlying probability law like $$\mu$$ and $$\sigma$$ above. They never say, "Based on this data, there is a 95% chance that $$\mu$$ is between 2.4 and 2.5." Instead, when they're being precise (and Lord know they aren't always precise, which is part of the problem here), they're careful to always make statements that run the other way: "If $$\mu$$ is 0 and our assumptions are true, then there is less than a 5% probability that data drawn from the distribution will have a mean that's as far from 0 as the data we observed..." and so on.

This has the advantage of that you stay grounded in the frequentist interpretation interpretation so you never confuse yourself about the meaning of your probabilistic claims, but it's all a bit tortured. As a very simple example, try saying exactly what one means by the statement that the 95% confidence interval for $$\mu$$ is between 2.4 and 2.5, using this kind of frequentist language. It's clear that what you really want to say is something about the probability that $$\mu$$ is in the interval, but frequentist approaches just don't allow you to do that. That _question_ is just not the question you ask in a frequentist approach.

By contrast, if you're using a Bayesian interpretation of probabilities, it's very easy to make probabilistic claims about the underlying probability law. You don't need to imagine ensembles of things. You can just think of the parameters of the underlying probability law like $$\mu$$ and $$\sigma$$ as random variables in their own right, things you can have degrees of belief about. Perhaps for this reason (or perhaps for other good reasons I don't understand), Bayesians have no problem making direct probabilistic claims about features of the underlying probability law. They can say, "Based on this data, if our assumptions are true, there is a 95% chance that $$\mu$$is between 2.4 and 2.5."

This is great because it's exactly the sort of thing you want to say. However, there's a catch. Remember, in the Bayesian interpretation, the machinery of probability tells you how to manipulate degrees of belief and to calculate degrees of belief about one thing from degrees of belief about another related thing. However, unlike the frequentist interpretation, the interpretation doesn't give you any obvious way to come up with the degrees of belief in the first place. Therefore, you always have to have an ingoing set of beliefs about things like $$\mu$$ and $$\sigma$$ in order to get started. That is, the _question_ is different.

As far as I can tell, that's the whole holy war. Bayesians and non-Bayesians just ask slightly different questions. Frequentists make a set of parameterized assumptions about the underlying probability law and then, for each possible value of the parameters, they ask, "If my assumptions are true, what is the likelihood that I would produce data like the data I observed?" Bayesians make a set of parameterized assumptions about the underlying probability law _and_ a set of ingoing assumptions about the likelihood of the parameters assuming various values and then, for each possible value of the parameters, they ask, "If my assumptions are true, given that I have observed this data, what is my new belief about the likelihood that these parameters assume these values."

That's it. They're just different, perfectly legitimate questions. What am I missing?
