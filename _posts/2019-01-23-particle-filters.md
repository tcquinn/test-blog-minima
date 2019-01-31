---
layout: post
title:  "Bayesian filtering using a hidden Markov model"
author: Ted Quinn
categories: ted
math: true
comments: true
---

Supposed we have a system that's evolving over time, and we're trying to track its state by taking a series of measurements or sensor readings. Just to pick two illustrative examples, consider an airplane moving through the sky or a student learning a new subject.

We know something about how the system evolves, but only approximately. In the case of the airplane, if we know the position and the velocity now, we can roughly predict the position and velocity 10 seconds from now, but the plane is subject to unpredictable gusts of wind, we don't know exactly what the pilot is going to do, etc. In the case of the student, if we know what she knows today, we can roughly predict what she's likely to know in a week, but students learn at many different speeds, we don't know what lessons she might receive, etc.

Similarly, we know how our sensors or measurement instruments respond to the state of the system, but only approximately. In the case of the airplane, we have sensors measuring the wind speed and the distance from the airplane to the three nearest radio beacons, but these measurements are very noisy. For the student, we're administering a test each week, but the test only roughly captures what the student knows. It's possible that the student will get a question wrong even if she has the relevant knowledge and it's possible that she'll get a question right even if she lacks the relevant knowledge.

For this kind of general system, we want to be able to calculate what we know (and don't know) about the state of the system at each moment, based on all of the sensor readings or measurements up to that moment. This is often called the _filtering_ problem. [Footnote: The _filtering_ name arises from the special case in which the measurement variables are identical to the state variables (e.g., the state variables are position and velocity and the measurement variables are also position and velocity). In this special case, the problem of inferring the (uncertain) state of the system from the (noisy) measurement variables reduces to the problem of finding the _filter_ that optimally smooths out the measurement data based on what we know about the evolution of the system and the sensor response. However, for our purposes, we won't make any such assumptions. The measurement variables can be any variables at all, as long as they have some known relationship to the underlying state variables, however complex and uncertain. For example, for the student, the state variables could be the student's actual knowledge of a set of specified concepts and the measurement variables could be the student's stress level while taking the test as measured by galvanic skin response.]

We propose to study the special case when two additional conditions are true. First, we want to assume that the evolution rule is memoryless in the sense that the state of the system now depends only on the state of the system at the previous moment. Put another way, if we know the state of the system at the previous moment, the likely state of the system now is independent of the state of the system at all earlier moments. Knowledge of the state of the system at those earlier moments won't give us any more information about the likely state of the system now. This is often called the _Markov condition_ or _Markov assumption_, and such a system is said to be _Markovian_.

On its face, this is a rather strong assumption. After all, we might imagine that the plane's position at a given time depends on the the plane's position at many previous times in the sense that knowing the position at those previous times would allow us to refine our estimate of the current position by observing how the plane is moving. However, in practice, we can often capture what we want to know about those previous times by adding additional state variables to the model. In the case of the plane, if we consider velocity as part of the state of the system, the Markov assumption becomes much more reasonable because the velocity at the previous time step is capturing the most important information about what was happening at the earlier time steps.

Second, we want to assume that the sensor readings we observe at any given moment depend only on the state of the system at that moment. Put another way, if we know the state of the system now, the sensor readings we are likely to observe now are indendent of the state of the system and the sensor readings we observed at all earlier moments (and, for that matter, all future moments). Knowledge of those earlier (or later) states or measurements won't give us any additional information about the sensor readings we are likely to observe now.

Again, this may seem like a rather strong assumption. After all, we might imagine that a sensor reading like velocity or  acceleration depends on the state of the system at several previous moments  in the sense that the measured velocity depends on the _change_ in position and the measured acceleration depends on the _change_ in velocity. However, as with the Markov assumption above, we can often make this assumption more realistic for a given real world problem by adding additional state variables to the model. In the example we were just considering, if we add velocity and acceleration to the state variables, then it becomes very natural to assume that the velocity and acceleration measurements depend only on the current state.

A system with the structure above that satsfies these two conditions is called a _hidden Markov model_: "Markov" because the evolution satisfies the Markov condition and "hidden" because we never observe the state directly, we only observe the measurements. [Footnote: Sometimes the name _hidden Markov model_ is reserved for the special case where the evolution rule is also time invariant (i.e., the relationship between the previous state and the current state is the same at every time step), but we're not going to make that restriction yet. For example, we want to allow for the possiibility of some known outside influence on the system, like the pilot exerting control on the airplane.]

For such a system, our intuition tells us that we might be able to solve the filtering problem iteratively. That is, we should be able to the calculate the state of the system at any given moment by combining our knowledge of the system at the previous moment, our knowledge of how the system evolves, the current set of sensor readings, and our knowledge of how the sensors respond. Then we can apply the same logic again to infer the state of the system at the next moment after that, and so on.

In the next section, we will simply restate all of the above slightly more precisely using the language of probability distributions. Then we will use this machinery to validate our intuition  by calculating an iterative solution to the filtering problem of precisely the form suggested by our intuition.

## The model

A very natural way to represent our uncertain knowledge about the system is to use the language of probability distributions. For example, if the state of our system is described by a set of variables $$\mathbf{X} = X_1, \ldots, X_m$$, then our knowledge of the state of the system at time $$t$$ can be described by the probability distribution $$P(\mathbf{X}_t) = P(X_{1t}, \ldots, X_{mt})$$. Similarly, if the set of all possible sensor readings is described by a set of variables $$\mathbf{Y} = Y_1, \ldots, Y_n$$, then our knowledge of the likely possible sensor readings at time $$t$$ can be described by the probability distribution $$P(\mathbf{Y}_t) = P(Y_{1t}, \ldots, Y_{nt})$$.

Using this language, the filtering problem can be expressed as a conditional probability. Specifically, we would like to know $$P(\mathbf{X}_t  \vert  \mathbf{Y}_0,\ldots,\mathbf{Y}_t) $$, the likelihood of the system being in any particular state $$\mathbf{X}$$ at time $$t$$, conditional on the fact that we observed a particular sequence of sensor readings $$\mathbf{Y}_0, \ldots, \mathbf{Y}_t$$.

We can also express our two assumptions above in the language of conditional probabilities. For example, our Markov assumption becomes

$$
\begin{equation}
P(\mathbf{X}_t  \vert  \mathbf{X}_0, \ldots, \mathbf{X}_{t-1};\mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) = P(\mathbf{X}_t  \vert  \mathbf{X}_{t-1}) \label{eq:markovind}
\end{equation}
$$

That is, $$\mathbf{X}_t$$ is conditionally independent of $$\mathbf{X}_{t'}$$ for all $$t' < t - 1$$ and $$\mathbf{Y}_{t'}$$ for all $$t' < t$$, given $$\mathbf{X}_{t-1}$$. [Footnote: Note that we're treating time here as a series of discrete time steps. This assumption simplifies our analysis enormously because can model the system as an infinite, but countable, set of random variables rather than using the much more complicated machinery of stochastic calculus. In practice, we can make these time steps small enough to realistically capture a wide range of time phenomenon.]

Similarly, our assumption about the behavior of our measurement variables becomes

$$
\begin{equation}
P(\mathbf{Y}_t  \vert  \mathbf{X}_0, \ldots;\mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}, \mathbf{Y}_{t+1},\ldots) = P(\mathbf{Y}_t  \vert  \mathbf{X}_{t}) \label{eq:measind}
\end{equation}
$$

That is, $$\mathbf{Y}_t$$ is conditionally independent of $$\mathbf{X}_{t'}$$ and $$\mathbf{Y}_{t'}$$ for all $$t' \neq t$$.

The figure below is an intutive way of visualizig these independence relationships. For more on the semantics of such graphs, which are widely used in modeling complex sytems, see _Probabilistic Graphical Models_ by Dahpne Koller and Nir Friedman.

![Figure 1]({{ "/assets/images/bayesfilter_fig01.png" | relative_url }}){:width="500px"}

Using this language, our intuition tells us that we should be able to express our knowledge of the current state $$P(\mathbf{X}_t)$$ in terms of our knowledge of the previous state $$P(\mathbf{X}_{t-1})$$, our knowledge of how the system evolves $$P(\mathbf{X}_t  \vert  \mathbf{X}_{t-1})$$, the current set of sensor readings $$\mathbf{Y}_t$$, and our knowledge of how the sensors respond $$P(\mathbf{Y}_t  \vert  \mathbf{X}_t)$$. In the next section, we derive precisely this result.

## Discrete case

Let us assume for the moment that each variable $$X_i$$ and $$Y_i$$ takes on only a finite, discrete set of values. For this special case, we can directly calculate the probability distribution of $$\mathbf{X}_t$$ given a particular set of observed measurements $$\mathbf{Y}_0, \ldots, \mathbf{Y}_t$$. We have

$$
\begin{align}
P(\mathbf{X}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_t) &= \frac{P(\mathbf{Y}_t  \vert  \mathbf{X}_t; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})P(\mathbf{X}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) }{P(\mathbf{Y}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})} \label{eq:bayesrule} \\
&= \frac{P(\mathbf{Y}_t  \vert  \mathbf{X}_t; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})P(\mathbf{X}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) }{\sum_{\mathbf{X}'_t} P(\mathbf{Y}_t  \vert  \mathbf{X}'_t; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})P(\mathbf{X}'_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})} \label{eq:expden} \\
&= \frac{P(\mathbf{Y}_t  \vert  \mathbf{X}_t; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) \sum_{\mathbf{X}'_{t-1}} P(\mathbf{X}_t  \vert  \mathbf{X}'_{t-1}; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) P(\mathbf{X}'_{t-1}  \vert \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})}{\sum_{\mathbf{X}'_t} P(\mathbf{Y}_t  \vert  \mathbf{X}'_t; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) \sum_{\mathbf{X}'_{t-1}} P(\mathbf{X}'_t  \vert  \mathbf{X}'_{t-1}; \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1}) P(\mathbf{X}'_{t-1}  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})} \label{eq:totalprob}\\
&= \frac{P(\mathbf{Y}_t  \vert  \mathbf{X}_t) \sum_{\mathbf{X}'_{t-1}} P(\mathbf{X}_t  \vert  \mathbf{X}'_{t-1}) P(\mathbf{X}'_{t-1}  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})}{\sum_{\mathbf{X}'_t} P(\mathbf{Y}_t  \vert  \mathbf{X}'_t) \sum_{\mathbf{X}'_{t-1}} P(\mathbf{X}'_t  \vert  \mathbf{X}'_{t-1}) P(\mathbf{X}'_{t-1}  \vert \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})} \label{eq:indep}
\end{align}
$$

To get Eq. $$\ref{eq:bayesrule}$$, we used Bayes rule to express our knowledge of the current state given the current measurement $$P(\mathbf{X}_t  \vert  \mathbf{Y}_t)$$ in terms of the sensor response $$P(\mathbf{Y}_t  \vert  \mathbf{X}_t)$$ and the prior distributions of $$P(\mathbf{X}_t)$$ and $$P(\mathbf{Y}_t)$$.

To get Eq. $$\ref{eq:expden}$$ from Eq. $$\ref{eq:bayesrule}$$, we wrote the prior distribution $$P(\mathbf{Y}_t)$$ as a sum over all possible states of the system $$\mathbf{X}_t$$.

To go from Eq. $$\ref{eq:expden}$$ to Eq. $$\ref{eq:totalprob}$$, we wrote the prior distribution of $$P(\mathbf{X}_t)$$ as a sum over all possible previous states of the system $$P(\mathbf{X}_{t-1})$$.

To from Eq. $$\ref{eq:totalprob}$$ to Eq. $$\ref{eq:indep}$$ we used our independence assumptions (Eq. $$\ref{eq:markovind}$$ and Eq. $$\ref{eq:measind}$$) to simplify the expression.

Note that the denominator is just the sum of the numerator over all possible values of $$\mathbf{X}_t$$. This is reassuring because it ensures that the sum of $$P(\mathbf{X}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_t)$$ over all possible values of $$\mathbf{X}_t$$ is equal to 1. It also suggests that we don't really need to calculate the denominator at all. Instead, we can just calculate the numerator for every possible value of $$\mathbf{X}_t$$ and then normalize. This approach is taken in many derivations of Eq.  \ref{eq:indep} and it is the approach we'll take when we implement Eq.  \ref{eq:indep} computationally. However, by writing everything out, we can verify that everything makes sense and we haven't missed anything.

We see that Eq. $$\ref{eq:indep}$$ aligns with our intuition by giving us an iterative rule for calculating $$P(\mathbf{X}_t  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_t)$$ from the observed sensor readings $$\mathbf{Y}_t$$, the sensor response $$P(\mathbf{Y}_t  \vert  \mathbf{X}_t)$$, the evolution rule $$P(\mathbf{X}_t  \vert  \mathbf{X}_{t-1})$$, and our knowledge of the previous state $$P(\mathbf{X}_{t-1}  \vert  \mathbf{Y}_0, \ldots, \mathbf{Y}_{t-1})$$. All we need to do is make some assumption about the state of the system $$P(\mathbf{X}_0)$$ prior to our first measurement and then apply this rule iteratively to update our knowledge of the system as receive each set of sensor readings.

## Linear Gaussian case: the Kalman filter

[TBD]

## General case: approximate methods

[TBD]
