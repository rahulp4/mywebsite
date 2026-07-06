# Machine Learning from First Principles

> An interactive chapter from Groundwork. This is a plain-text version of the
> full article — paste it into your own AI assistant to ask follow-up
> questions, get quizzed on it, or have it explained a different way. The
> live interactive version (sliders, animated demos, a loss-bowl
> visualization, optional voice narration) is at the URL this file came from.

## 1. What is Machine Learning, really?

Traditional programming means a human writes explicit rules: "if this, then
that." Machine Learning flips that around. Instead of writing the rules
ourselves, we show the computer many examples of inputs and correct outputs,
and let it discover the rule — a mathematical function — connecting them.
Once learned, that function predicts answers for new inputs it has never
seen.

A spam filter isn't told "the phrase 'free money' means spam" — spammers
adapt in a week. It's shown thousands of labeled emails and learns which
patterns correlate with spam. In every case the common thread is the same:
we don't know the rule ourselves, but we have examples of it in action — so
we let the data reveal the rule.

This chapter covers **supervised learning**: we have both inputs and correct
answers. (Unsupervised learning finds structure without answers; reinforcement
learning learns from rewards — different chapters.)

In symbols: f(x) ≈ y — we're hunting for a function f that turns an input
into an approximately-right output. The "≈" matters: we rarely find a rule
that's perfectly right, only one that's close enough on average.

*(On the live page: an animated demo cycles through five candidate lines
fitted to three houses' size-vs-price data, ending on the one that fits
perfectly.)*

## 2. The model: a line with two knobs

The simplest possible rule assumes price grows in a straight line with size.
A straight line has exactly two knobs: how *steep* it is, and where it
*starts*. Learning, at its core, is tuning these two knobs until the line
fits the data well.

**The model equation:** ŷ = w·x + b

- **ŷ** ("y-hat") — the model's predicted answer
- **x** — the input (house size)
- **w** — the *weight*, knob #1: the slope, how much price rises per unit of size
- **b** — the *bias*, knob #2: the starting height, the base price when size is zero

Training a model is nothing more than searching for the best pair of numbers,
w and b.

**Analogy — two colleges, same students, opposite verdicts:** imagine a
college admissions score built from GPA, a test score, and extracurriculars,
each weighted: Score = w₁·GPA + w₂·Test + w₃·Extracurricular + b. Student A
(GPA 9.5, Test 9.0, Extracurricular 3.0) and Student B (GPA 7.0, Test 7.0,
Extracurricular 9.5) never change — only the weights do. An academic-focused
scheme (weights 0.6, 0.35, 0.05) scores A at 9.00 vs. B at 7.13 — A wins. A
holistic scheme (weights 0.3, 0.2, 0.5) scores B at 8.25 vs. A at 6.15 — B
wins. That's the whole point of a weight: how much influence one input has
on the final answer, relative to the others.

*(On the live page: sliders let you steer the line over the houses; buttons
flip the admissions weighting scheme and watch the ranking reverse.)*

## 3. How do we know a model is good? The loss function

Once we have a line — even a terrible guess — we need to measure how wrong
it is. For each house, compare the prediction to the actual price; the
difference is that house's *error*. We want one number summarizing how
wrong the model is across all houses. Smaller is better — that's the
**loss**.

Why square each error? First, raw errors can be positive or negative — if we
just averaged them, a +10 miss and a −10 miss would cancel to zero, falsely
claiming a perfect model. Squaring makes every error positive. Second,
squaring punishes big misses much more than small ones — being off by 4
costs 16, being off by 1 costs only 1.

**Analogy — a dartboard where big misses hurt extra:** score darts by the
area of a square drawn on your miss distance. Miss by 1 cm — a tiny 1 cm²
penalty. Miss by 5 cm — a 25 cm² penalty. One wild throw costs more than many
small wobbles, so the safest strategy is to keep every throw reasonably
close — exactly the behavior we want from our line.

**The loss equation (Mean Squared Error):** L(w, b) = (1/n) · Σ (ŷᵢ − yᵢ)²

- **L** — the loss, one number for "how wrong overall"
- **Σ** ("sigma") — add these up, one term per data point
- **n** — how many data points (here, 3 houses)
- **ŷᵢ − yᵢ** — the error on house i: prediction minus truth
- **1/n** — divide by the count, i.e. take the average

**Worked example** on houses with size x = [1, 2, 3] and price y = [3, 5, 7]:
the "bad line" w=0.5, b=0 gives loss L = (1/3)(52.50) = **17.50**. The "good
line" w=2, b=1 passes through every point exactly: loss = **0**.

## 4. Derivatives: which way is downhill?

Plot the loss against every possible (w, b) pair and you get a bowl-shaped
surface — the best model sits at the bottom. So: how do you find the bottom
of a bowl you can't see all at once?

A **derivative** answers one question: at the spot where you're standing,
how steeply is the ground tilting, and which way? Slope pointing up to the
right means downhill is to the left. A slope of zero means you're on flat
ground — possibly the very bottom.

**Analogy — the blindfolded hiker:** standing somewhere on the bowl,
blindfolded, you can't see the shape but you can feel which way the ground
slopes under your feet. Take a small step downhill, feel again, step again.
A derivative is that feeling-with-your-feet, turned into a number.

**Definition:** f′(x) = lim(h→0) [f(x+h) − f(x)] / h — nudge the input a
hair, see how much the output moves, divide; for an infinitely small nudge,
that ratio is the slope at that exact spot.

Two rules cover everything we need:
1. **Power rule** — for f(x) = xⁿ, the slope is f′(x) = n·xⁿ⁻¹. For f(x) = x²
   (a bowl shape), the slope is f′(x) = 2x.
2. **Chain rule** — when one function sits inside another, f(x) = g(h(x)),
   the slope is f′(x) = g′(h(x)) · h′(x): differentiate the outer function
   first, then multiply by the derivative of the inner one. Our loss,
   (ŷ − y)², is exactly this shape — a square (outer) wrapped around the
   line ŷ = wx + b (inner).

## 5. The gradient: a slope for each knob

With two knobs, we ask the slope question twice — how does loss change if I
nudge w? If I nudge b? Together those two answers are the **gradient**, a
pair of numbers pointing toward where loss climbs *fastest*. We move exactly
the opposite way.

Using the chain rule on one data point's loss, (ŷ − y)² where ŷ = wx + b:
the derivative with respect to w is 2(ŷ − y)·x (outer square gives
2(ŷ−y), multiplied by the inner slope with respect to w, which is x).
Averaged over all n points, and doing the same for b (whose inner slope is
just 1):

- **∂L/∂w = (2/n) · Σ (ŷᵢ − yᵢ)·xᵢ**
- **∂L/∂b = (2/n) · Σ (ŷᵢ − yᵢ)**

**∂** ("partial") means a derivative taken for one knob while the other
holds still. In plain words: the w-slope is the average error *scaled by the
input x* — larger inputs demand larger weight adjustments. The b-slope is
simply the average error itself — no scaling, since b shifts every
prediction equally regardless of x.

**Analogy — square feet vs. bedrooms, an unfair tug-of-war:** because the
w-slope is error × x, the raw size of x sneaks into the gradient. If a model
uses size (x=2000) and bedrooms (x=3) and is off by 5, the gradients are
2×5×2000=20,000 and 2×5×3=30 respectively — over 600× larger for size, not
because size truly matters 600× more, but purely because its numbers are
bigger. This is why features are scaled to similar ranges before training.

## 6. Gradient descent: the update rule

Take a small step opposite the gradient, re-measure the slope, repeat. This
"step downhill, feel again, step again" ritual is **gradient descent** — the
core learning mechanism behind almost all of modern ML.

**The update rule:** w := w − α·∂L/∂w  and  b := b − α·∂L/∂b

- **:=** — "update to," replace the old value with the new one
- **α** ("alpha") — the *learning rate*, a small positive number setting the step size

Choosing α well matters enormously. Too small → thousands of timid steps,
training crawls. Too large → each step flies past the bottom, bouncing back
and forth or even diverging. Just right → steady, efficient descent.

*(On the live page: three simulated hikers with α = 0.02, 0.35, and 1.15
race down the same bowl — one crawls, one converges cleanly, one diverges.)*

## 7. The training loop: four steps on repeat

Training a model is repeating four steps: **predict, measure the mistake,
figure out which direction reduces it, adjust — just a little.**

**Analogy — seasoning a soup:** a cook tastes the soup (predict), notices
it's bland (measure the error), decides salt is what's missing and roughly
how much (the gradient), adds a pinch rather than the whole box (a small
step, the learning rate), then tastes again. Round and round until it's
right.

The algorithm, spelled out: initialize w and b (often to zero). Repeat: (a)
predict ŷᵢ = w·xᵢ + b for every point, (b) measure L = (1/n)Σ(ŷᵢ−yᵢ)²,
(c) compute both gradients ∂L/∂w and ∂L/∂b, (d) update both knobs. Return the
final w and b — that pair *is* the trained model.

## 8. The worked example, running live

Dataset: three houses, size x = [1, 2, 3], price y = [3, 5, 7] (secretly
y = 2x + 1, though the model doesn't know that). Setup: w = 0, b = 0, α = 0.1.

**Iteration 1 by hand:** with both knobs at zero, every prediction is 0, so
errors are [−3, −5, −7] and loss = (1/3)(9+25+49) = **27.67**. Gradients:
∂L/∂w = (2/3)(−3·1 − 5·2 − 7·3) = **−22.67**, ∂L/∂b = (2/3)(−15) = **−10**.
Both slopes are steeply negative, so updates push both knobs up: w becomes
0 − 0.1(−22.67) = **2.267**, b becomes 0 − 0.1(−10) = **1.0**. Loss
collapses from 27.67 to about 0.33 in one step.

**Iteration 2** refines the slight overshoot: new predictions [3.267, 5.533,
7.8] are all a touch high, gradients turn small and positive, knobs ease
back to w ≈ **2.018**, b ≈ **0.893**. Loss keeps shrinking as w → 2 and
b → 1 — exactly the true rule y = 2x + 1.

## 9. Visual intuition: the bowl

Plotting the loss for every (w, b) pair produces a bowl-shaped surface —
high walls where the line fits badly, one lowest point (at w=2, b=1) where
it fits perfectly. Training never sees the whole bowl, only the local slope
underfoot — yet the path bends steadily inward toward the bottom, loss
shrinking with every step.

*(On the live page: a contour map and a 3D wireframe of this exact bowl,
with the real descent path animated from the starting point (0,0) down to
the minimum.)*

## 10. From simple to general

Everything here — a model with weights and biases, a loss measuring error,
gradients from calculus, an update rule stepping downhill — is the exact
same machinery used in logistic regression and deep neural networks. Only
two things change in fancier models: the shape of f(x), and the loss used.

**Logistic regression** squeezes wx+b through a sigmoid: ŷ = 1 / (1 + e^−(wx+b)),
producing a probability between 0 and 1 instead of a raw number — trained by
the same gradient descent, with a different loss (cross-entropy).

**Neural networks** are many of these equations stacked in layers, each
feeding the next, with a non-linear bend between layers so the network can
learn curves, not just lines. Training still is predict → measure → gradient
→ update; the chain rule is just applied once per layer — a process called
**backpropagation**.

## 11. Summary and key takeaways

**The five formulas that ran this whole chapter:**
- Model: ŷ = w·x + b
- Loss (MSE): L = (1/n)Σ(ŷᵢ−yᵢ)²
- Gradient for w: ∂L/∂w = (2/n)Σ(ŷᵢ−yᵢ)·xᵢ
- Gradient for b: ∂L/∂b = (2/n)Σ(ŷᵢ−yᵢ)
- Update rule: w := w − α·∂L/∂w,  b := b − α·∂L/∂b

**Common pitfalls:**
- **Learning rate too high** — loss oscillates or diverges instead of shrinking.
- **Learning rate too low** — training works but takes far too long.
- **Unscaled features** — very different input ranges cause imbalanced
  gradients and unstable training; normalize features first.
- **Local minima** — this chapter's loss is a perfect single-bottomed bowl,
  but more complex models have bumpy surfaces where descent can settle into
  a valley that isn't the lowest one.

**In one sentence:** Machine Learning is adjusting weights and biases in the
direction that reduces prediction error, guided step-by-step by derivatives
— and gradient descent is the disciplined, repeated application of that idea
until the predictions are as accurate as possible.
