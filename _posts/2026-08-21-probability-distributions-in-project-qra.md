---
layout: post
title: "Probability Distributions in Project QRA: Why the Choice Matters"
date: 2026-08-19 18:06 +0100
author: b3rdma
description: How probability distributions are used in project QRA, why distribution choice matters, and how Monte Carlo simulation models uncertainty.
categories:
  - Risk Management
  - QRA
tags:
  - qra
  - risk management
  - qsra
  - qcra
  - monte carlo
  - probability distributions
math: true
---

Quantitative Risk Analysis (QRA) helps turn uncertainty into information that
can support better project decisions.

Rather than relying solely on deterministic estimates such as “the project will
finish in December” or “the final cost will be £100 million”, QRA allows us to
consider a range of possible outcomes and the probability associated with
achieving them.

In project risk management, this is particularly valuable when assessing
uncertainty around cost and schedule. Quantitative Cost Risk Analysis (QCRA) and
Quantitative Schedule Risk Analysis (QSRA) can help decision-makers understand
not only the expected outcome, but also the confidence they should place in it.

The Association for Project Management (APM) defines quantitative risk analysis
as the estimation of numerical values for the probability and impact of risks,
using actual or estimated values, known relationships, modelling and statistical
techniques.[^1]

However, the usefulness of any quantitative analysis depends heavily on the
assumptions that sit behind it.

One of those assumptions is the choice of probability distribution.

The familiar bell curve, or normal distribution, is mathematically convenient
and appropriate in some circumstances. But when it is applied to uncertainty
that is not actually symmetrical, it can create a misleading picture of project
risk.

The problem is therefore not the bell curve itself. It is using the wrong
distribution for the uncertainty being modelled.

## What QRA Is Trying to Tell Us

Projects are inherently uncertain.

An activity scheduled to take ten days might take eight, ten, twelve, or perhaps
twenty. A procurement package might arrive as planned, or a specific risk event
might occur and delay the project by several weeks. Costs may fall slightly
below estimate, but there may be considerably more scope for them to increase.

Traditional deterministic planning represents these uncertainties using single
values. QRA instead attempts to represent the range of plausible outcomes and
their associated probabilities.

APM describes quantitative project analysis as involving the measurement of
uncertainty in cost and time estimates and the probabilistic combination of
those individual uncertainties.[^2]

This distinction is important because QRA is not simply about attaching numbers
to risks in a register. It is about understanding their combined effect on
project objectives.

For schedule analysis, that might mean determining the probability of achieving
a key milestone by a particular date. For cost analysis, it might mean
understanding the level of funding required to achieve a specified confidence
level.

Instead of saying:

> “The project will finish on 30 September.”

QRA allows us to say something more useful:

> “There is a 50% probability of finishing by 30 September, making that the P50,
> or 50th percentile, completion date. An 80% confidence date, or P80, would be
> 24 October.”

That is a fundamentally different quality of information for decision-making.

## Where the Bell Curve Comes In

The normal distribution is probably the most recognisable probability
distribution.

It is symmetrical around its mean. Outcomes close to the centre are more likely,
while progressively more extreme outcomes become less likely in either
direction.

There are many situations where this is entirely reasonable.

If an uncertain quantity is genuinely just as likely to vary above its expected
value as below it, and the degree of variation is approximately symmetrical, a
normal distribution may represent it adequately.

The difficulty arises when project uncertainty does not behave like that.

Consider an activity estimated to take ten days.

Perhaps excellent performance could reduce its duration to eight days. However,
poor productivity, technical difficulties, rework, or resource constraints could
conceivably extend it to twenty days.

The uncertainty is clearly not symmetrical.

A normal distribution centred on ten days would imply that unusually short
durations were possible in much the same way that unusually long durations were
possible. Because the normal distribution is unbounded, it also assigns some
non-zero probability to values below zero, including physically impossible
negative durations or costs, although that probability may be negligible
depending on the mean and standard deviation used.

The mathematics may be perfectly correct.

The model of reality is not.

## Project Risk Is Often Skewed

Many cost and schedule uncertainties have limited upside but considerably
greater downside.

There may be a practical minimum duration below which an activity simply cannot
be completed. There may similarly be a minimum cost dictated by labour,
material, or contractual requirements.

The upper boundary can be very different.

An activity can be delayed by design problems, supply-chain disruption, rework,
resource shortages or unexpected technical complexity. Costs can increase
significantly beyond their original estimate.

The result is often a distribution with a longer right-hand tail.

The US Government Accountability Office's (GAO) cost-estimating guidance notes
that the normal distribution is less useful for defining risk because it
requires symmetrical data, while risk is usually asymmetrical.[^3]

This matters because forcing symmetrical uncertainty into a symmetrical
distribution can underestimate the probability of substantial overruns.

It can also conceal another important statistical distinction.

In a symmetrical normal distribution, the mean, median, and mode coincide. In a
skewed distribution they do not necessarily do so.

The “most likely” outcome therefore need not be the same as the average outcome
produced across thousands of simulations.

That distinction becomes important when interpreting QRA results.

## Three-Point Estimates

One common way of representing uncertainty in project QRA is through a
three-point estimate.

Instead of asking for a single duration or cost, the analyst might ask for:

- an optimistic value;
- a most likely value; and
- a pessimistic value.

For example, an activity might be assessed as:

- **Optimistic:** 8 days
- **Most likely:** 10 days
- **Pessimistic:** 20 days

The values themselves already tell us something significant.

The distance from 10 days to the optimistic estimate is only two days. The
distance to the pessimistic estimate is ten days.

The uncertainty is therefore strongly skewed towards delay.

APM defines a three-point estimate as one in which optimistic, pessimistic, and
most likely values are given.[^1] These values can then be used to help define
the range and shape of the probability distribution applied in the analysis.

The next question is which distribution best represents those estimates.

### A Simple Comparison

Using the same 8, 10 and 20-day activity illustrates why that choice matters.

A **normal distribution** centred on the ten-day estimate would impose symmetry.
Shorter and longer durations would be represented in broadly the same way,
despite the assessment telling us that there are only two days of plausible
improvement but ten days of plausible delay. A normal distribution is also
unbounded, so unless it is constrained it can theoretically generate values
outside the range considered credible.

A **triangular distribution** can use the three estimates directly. Eight days
becomes the minimum, ten days the most likely value and twenty days the maximum.
The resulting distribution therefore reflects the greater uncertainty on the
delay side while remaining bounded by the estimates provided.

A **PERT-type distribution** can use the same three estimates while placing more
probability around the most likely value and progressively less towards the
extremes.

Interestingly, the choice of distribution can materially affect the resulting
statistics even when the same three-point estimates are used.

For a triangular distribution, the mean is:

$$
\mu_{\text{triangular}} = \frac{a + m + b}{3}
= \frac{8 + 10 + 20}{3}
\approx 12.7
$$

For the traditional PERT weighted mean:

$$
\mu_{\text{PERT}} = \frac{a + 4m + b}{6}
= \frac{8 + (4 \times 10) + 20}{6}
\approx 11.3
$$

where $$ a $$ is the optimistic estimate, $$ m $$ is the most likely estimate
and $$ b $$ is the pessimistic estimate.

The most likely estimate remains 10 days, yet the two approaches produce
different mean values from exactly the same three-point inputs.

This illustrates two important points. First, where uncertainty is skewed, the
most likely outcome and the average outcome are not necessarily the same.
Second, choosing a probability distribution is not merely a cosmetic modelling
decision. **The choice changes the model and can therefore change the
conclusions drawn from it.**

None of these observations proves that one distribution is automatically
correct.

They show why the shape of the uncertainty matters.

## Triangular Distributions

The triangular distribution is one of the simplest approaches.

It uses the optimistic, most likely and pessimistic values directly, producing a
distribution with its peak at the most likely value and fixed minimum and
maximum boundaries.

This makes it intuitive and transparent.

It can also represent skew.

Our 8, 10 and 20-day example would therefore produce a distribution weighted
towards the ten-day estimate but with considerably more uncertainty on the delay
side.

The triangular distribution is particularly useful when information is limited
and the three-point estimates are primarily based on expert judgement.

Its simplicity is also its weakness.

The distribution changes linearly between the three points, which may not be a
particularly realistic representation of how likely the values between them
actually are.

## Program Evaluation and Review Technique (PERT) and Beta Distributions

PERT-type distributions provide another way of using three-point estimates.

Like the triangular distribution, they can be bounded by optimistic and
pessimistic values and shaped around a most likely estimate. However, the
resulting curve is smoother and can place greater concentration around the most
likely value.

This can sometimes provide a better representation where the analyst believes
that values close to the most likely estimate are significantly more probable
than those close to the extremes.

Neither triangular nor PERT is automatically correct.

The distribution should follow the uncertainty, rather than the uncertainty
being forced to fit whichever distribution happens to be easiest to configure in
the software.

## Lognormal Distributions

A lognormal distribution can be useful where values have a practical lower
boundary but a potentially long upper tail.

Unlike the normal distribution, it is positively skewed.

That makes its shape intuitively relevant to some project uncertainties where
small variations around the expected value are common, but increasingly large
overruns remain possible.

This does not mean that every cost or duration should be modelled using a
lognormal distribution.

It means that the characteristics of the uncertainty should determine the
distribution.

## Uniform Distributions

A uniform distribution assumes that every value between its lower and upper
limits is equally likely.

This can be useful where a plausible range is known but there is genuinely no
basis for believing that one value within that range is more likely than
another.

It should not, however, simply become the default response to poor information.

“There is insufficient information to distinguish between these values” and
“every value is equally likely” are not necessarily the same statement.

That difference matters in quantitative modelling.

## Risk Events Are Different from Uncertainty

Another important distinction is the difference between general uncertainty and
discrete risk events.

Suppose an activity has an uncertain duration of between eight and twenty days.
That uncertainty exists regardless of whether any particular identified risk
occurs.

Now consider a separate risk:

> There is a 20% probability that a specialist component will arrive late. If it
> does, it could delay the activity by between 10 and 25 days.

That is different.

The delay should not necessarily be blended into the underlying duration
uncertainty. The event has a probability of occurrence and, conditional upon
occurring, a range of possible impacts.

Care is also needed to avoid double counting. If the pessimistic duration
already includes the effect of the late-component scenario, modelling the same
risk event separately would represent that uncertainty twice.

A quantitative model can represent both.

This distinction prevents general estimating uncertainty from being confused
with identified risk events and makes it easier to understand which risks are
actually driving the project outcome.[^4]

## Monte Carlo Simulation Is Not a Distribution

HM Treasury's Green Book recognises Monte Carlo analysis as an advanced
risk-assessment technique that may be appropriate where multiple variables are
subject to significant uncertainty.[^5]

This is where Monte Carlo simulation becomes important.

Monte Carlo is sometimes discussed alongside distributions such as normal,
triangular or PERT as though it were another distribution from which the analyst
chooses.

It is not.

Monte Carlo simulation is the mechanism used to repeatedly sample from the
probability distributions in the model.

During one simulation the ten-day activity might take nine days. During another
it might take eleven. During another it might take eighteen.

At the same time, discrete risks may or may not occur according to their
assigned probabilities.

This process is repeated hundreds, thousands or tens of thousands of times.

The result is not one deterministic answer but a distribution of possible
project outcomes.

APM describes probabilistic analysis as specifying probability distributions and
considering their combined effect, with Monte Carlo simulation being the most
common form of sampling technique used for this purpose.[^2]

Monte Carlo therefore does not solve the distribution problem.

It amplifies whatever assumptions have been placed into the model.

If those assumptions are reasonable, it can provide powerful insight.

If they are poor, the simulation merely processes poor assumptions thousands of
times.

## The Danger of False Precision

This is one of the greatest traps in QRA.

The output of a quantitative model can look extraordinarily authoritative.

It may produce an exact P50 date, an exact P80 cost, cumulative probability
curves, histograms, and sensitivity analysis.

But mathematical precision is not the same as accuracy.

If an activity owner has provided an optimistic estimate without considering
what would genuinely need to happen to achieve it, the lower bound may be wrong.

If the pessimistic estimate merely represents “a bad day” rather than a credible
extreme, the upper tail may be understated.

If important risk events have been omitted, the model will not magically
discover them.

If risks or activities affected by the same underlying drivers are treated as
completely independent, the combined uncertainty may also be distorted.

The Infrastructure and Projects Authority (IPA) similarly warns that
probabilistic approaches rely heavily on input data and assumptions around
probability, distribution, and correlation, and can provide false levels of
confidence when applied to immature data.[^4]

Good QRA therefore begins long before the simulation button is pressed.

## Expert Judgement Still Matters

Project QRA often deals with situations where perfect historical data do not
exist.

Projects are not identical repetitions of one another. Technology changes,
organisations change, suppliers change and the environment in which work is
undertaken changes.

Expert judgement therefore remains important.

That does not mean asking someone for three numbers and accepting them without
challenge.

The analyst should understand the basis of estimate behind them.

Why is ten days considered most likely?

What would need to go particularly well for eight days to be achieved?

What circumstances could drive the duration towards twenty days?

Is twenty genuinely a credible pessimistic outcome, or merely an arbitrary
number?

Are there identifiable risk events hidden inside that range that should instead
be modelled separately?

Good data collection is therefore as much about structured conversation and
challenge as it is about statistics.

## Choosing the Distribution to Fit the Risk

There is no universally correct probability distribution for project risk.

Normal distributions can be appropriate.

Triangular distributions can be appropriate.

PERT or beta distributions can be appropriate.

Lognormal distributions can be appropriate.

Uniform distributions can be appropriate.

Discrete probabilistic events can be appropriate.

The important question is not:

> “Which distribution do we normally use?”

It is:

> “What does the uncertainty we are trying to represent actually look like?”

The model should follow the risk.

The risk should not be forced to follow the model.

## Conclusion

Quantitative Risk Analysis can provide project decision-makers with something
that deterministic estimates cannot: an understanding of the range of possible
outcomes and the confidence associated with them.

But that capability depends on the quality of the assumptions within the model.

The bell curve is not inherently wrong. If the underlying uncertainty is
genuinely symmetrical, a normal distribution may be entirely appropriate.

The mistake is assuming that uncertainty must be symmetrical simply because a
normal distribution is familiar or convenient.

Project cost and schedule uncertainty can be bounded, skewed and affected by
discrete events. Different uncertainties may therefore require different
distributions and different modelling approaches.

Monte Carlo simulation can then combine those uncertainties and risk events to
show their potential effect on the project as a whole.

Ultimately, effective QRA is not about producing the most sophisticated
mathematical model.

It is about representing uncertainty honestly enough to support better
decisions.

A precise answer produced from unrealistic assumptions is still the wrong
answer.

[^1]: [APM Project Management Glossary](https://www.apm.org.uk/resources/glossary/)

[^2]: [APM - Project Risk Analysis and Management](https://www.apm.org.uk/resources/whitepapers/project-risk-analysis-and-management/)

[^3]: [US Government Accountability Office - Cost Estimating and Assessment Guide](https://www.gao.gov/products/gao-20-195g)

[^4]: [Infrastructure and Projects Authority - Cost Estimating Guidance](https://www.gov.uk/government/publications/cost-estimating-guidance/cost-estimating-guidance)

[^5]: [HM Treasury - The Green Book (2026)](https://www.gov.uk/government/publications/the-green-book-appraisal-and-evaluation-in-central-government/the-green-book-2026)
