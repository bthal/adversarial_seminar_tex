# Notes: Goodfellow et al. — The Linear Explanation of Adversarial Examples

Source paper: *Explaining and Harnessing Adversarial Examples*, Goodfellow, Shlens & Szegedy, ICLR 2015 (arXiv 1412.6572, 2014). Bib key: `goodfellow2014explaining`.

Purpose of these notes: raw material for our survey's section on Goodfellow's explanation attempt. We are the intro group, so this should read as the *foundational reframing of why adversarial examples exist*, setting up the narrower groups (attacks on LLMs, defenses, robustness definitions).

---

## 1. Starting point: the non-linearity hypothesis (Szegedy et al., 2013)

Bib key: `szegedy2013intriguing`.

- Szegedy et al. *discovered* adversarial examples: imperceptibly perturbed inputs that get confidently misclassified. They also noted the puzzling transfer across models.
- The first, speculative explanation attributed the phenomenon to the **extreme non-linearity of deep networks**, possibly compounded by insufficient model averaging and insufficient regularization of the supervised objective (i.e. an **overfitting**-flavoured story).
- Mental picture to convey: deep nets carve up input space into highly **contorted** decision surfaces; adversarial examples are **rare, low-probability "blind spots" / pockets** sitting very close to correctly classified points. Goodfellow's own phrasing for this view: adversarial examples "finely tile space like the rational numbers among the reals" — dense but occurring only at very precise locations.
- Implicit consequences of that view (worth stating, because Goodfellow overturns them): the pockets are model-specific accidents of training; the suggested cure is *more* regularization / *less* capacity / smoothing.

Transition sentence for the section: Goodfellow et al. argue these hypotheses are *unnecessary* — you do not need non-linearity or overfitting to get adversarial examples.

---

## 2. Core idea: high-dimensional linearity is *sufficient*

The central claim: **linear behaviour in high-dimensional spaces is enough to produce adversarial examples.** Modern nets (ReLU, maxout, LSTM; sigmoids kept in their non-saturating regime) are deliberately built to behave near-linearly for easy optimization, so the argument transfers from strictly linear models to real networks.

### 2.1 The high-dimensional "bloating" effect (with a little math)

Setup — finite feature precision motivates a max-norm (per-coordinate) tolerance:

- Real inputs have limited precision (e.g. 8-bit images discard everything below 1/255 of the dynamic range). So a sensible classifier *should* give the same label to `x` and to a perturbed `x̃ = x + η` whenever every coordinate of the perturbation is below the precision, i.e. `‖η‖_∞ < ε`.

The linear model's response to the perturbation:

```
wᵀx̃ = wᵀx + wᵀη
```

- The perturbation shifts the activation by exactly `wᵀη`.
- Maximize that shift subject to the max-norm budget `‖η‖_∞ ≤ ε`  →  choose `η = ε · sign(w)`.
- With `w ∈ ℝⁿ` and average weight magnitude `m`, this gives:

```
wᵀη = ε · Σ|wᵢ| = ε · m · n
```

**The punchline (this is the whole argument):** the perturbation's per-coordinate size `‖η‖_∞ = ε` is *fixed* and does **not** grow with the dimension `n` — it stays imperceptible. But the induced change in the activation, `ε·m·n`, grows **linearly with `n`**. In high dimensions, many individually-imperceptible changes, all aligned with the weights, **add up to one large change in the output**. Goodfellow calls this "accidental steganography."

Useful framings to reuse in prose:
- **Margin vs. budget.** The classifier's margin (distance from `x` to the boundary) is set by the data and does *not* grow with `n`; the attacker's available push `ε·m·n` *does*. Enough dimensions ⇒ budget overwhelms margin.
- **Intuition gap.** We live in 3D and have poor intuition for how thousands of tiny, coordinated nudges accumulate — hence the phenomenon *feels* like it must require something exotic (non-linearity), when it doesn't.
- **What matters is direction, not location.** Adversarial regions are broad contiguous half-spaces, not fine pockets; `η` only needs a positive dot product with the gradient and `ε` "large enough."

### 2.2 Framing correction — linearity is NOT claimed to be *necessary*

This is important and often mis-stated in secondary summaries. The paper is sometimes read as "adversarial examples are *caused by* linearity" / "the explanation requires the model to be linear." That is a misreading.

- What Goodfellow actually shows: **non-linearity is not *necessary* to explain adversarial examples.** The simplest possible model — plain logistic / softmax regression, no hidden layers, zero non-linearity — *already* exhibits them, with the clean `ε·m·n` account above.
- So the logical status is a **sufficiency / Occam argument**: since linearity already suffices, invoking extreme non-linearity is an unnecessary extra assumption. "These speculative hypotheses are unnecessary" (paper's wording).
- It is *not* a claim that networks are purely linear, nor that curvature plays no role.
- Balancing caveat to include (keeps us honest, cites the modern view): the refined post-2015 picture is that *both* dimensionality and local geometry matter — e.g. iterative attacks (PGD, `madry2017towards`) beat single-step FGSM precisely because the surface is not perfectly linear, and minimum-distance attacks like DeepFool (`moosavi2016deepfool`) exploit boundary geometry. Goodfellow's lasting contribution is dislodging *extreme non-linearity* as the default/necessary cause, not proving linearity is the sole cause.

---

## 3. First explanation of transferability

The abstract flags transferability as "the most intriguing fact." Goodfellow's is the **first explanation to account for it** (Szegedy only observed it). See paper §8, "Why do adversarial examples generalize?".

The puzzle: an example crafted on model A is often misclassified by model B — different architecture, or trained on a disjoint dataset — and the models frequently **agree on the wrong label**. The non-linearity/overfitting view can't explain this: why would independent, model-specific contortions land on the *same* out-of-distribution point *and* the same wrong class?

The linear view answers it in **two parts**:

**(a) Why the mistake transfers at all — a volume argument.**
Under linearity, adversarial examples fill **broad contiguous half-spaces**, not fine pockets (Fig. 4: the wrong-class logits are piecewise-linear in `ε` and the misclassification is *stable across a wide range* of `ε`; correct classifications live only on a thin data manifold). Each model's adversarial region is therefore a huge fraction of input space, so two models' regions **overlap heavily** — an example bad for A has a high prior probability of being bad for B.

**(b) Why they agree on the class — aligned weight vectors.**
The perturbation `η = ε·sign(∇)` is aligned with A's weight vector by construction. It transfers *in class* because different models learn **similar weight vectors**: the discriminative direction is a property of the **data/task**, not of the model, so any accurate model trained on the same task (even a different subset) recovers approximately the same weights. Stable underlying weights ⇒ stable adversarial direction ⇒ same wrong class. High dimensionality makes this **forgiving**: `η` only needs a *positive* dot product with B's weights, and even partial correlation accumulates over `n` coordinates into a large aligned push.

**Supporting evidence (good to cite concretely):** adversarial examples generated on a deep maxout net; restricting to cases where both models err, a *linear* softmax model reproduces maxout's wrong label **84.6%** of the time, while a *non-linear, saturating* RBF network agrees only **54.3%**. → the amount of transfer tracks how *linear* the second model is, exactly as the hypothesis predicts.

(Optional forward-reference: transferability later became the basis for black-box attacks, `papernot2016transferability`.)

---

## 4. FGSM — introduced to demonstrate the idea (mechanics out of scope here)

The linear view suggests a fast, cheap way to *generate* adversarial examples: the **Fast Gradient Sign Method**,

```
η = ε · sign(∇ₓ J(θ, x, y))     — one backprop step
```

For our overview section, keep this brief and **do not** unpack the sign / max-norm derivation (that belongs to the attacks group). The point to make is *why FGSM matters for the argument*:

- It is **evidence for** the linearity hypothesis, not just a tool. If a single, cheap, first-order (i.e. *linear*) step reliably fools many different models, those models must be behaving near-linearly over the perturbation — which is the thesis. "Simple, cheap algorithms are able to generate misclassified examples" is offered as support for the linear interpretation.
- It is what made **adversarial training** practical (previously bottlenecked by expensive L-BFGS in Szegedy et al.).

---

## Section-writing checklist / narrative arc

Suggested arc for the subsection:
1. Szegedy discovery + non-linearity/overfitting guess (`szegedy2013intriguing`).
2. Goodfellow's reframe: high-dimensional linearity is *sufficient* — the `ε·m·n` bloating argument.
3. Framing correction: non-linearity shown *unnecessary*, not linearity *necessary*.
4. Payoff: first explanation of transferability (the two-part argument).
5. Brief FGSM note as demonstration + enabler of adversarial training; defer mechanics to the attacks group.
6. One-sentence balancing caveat toward the modern "both matter" view (`madry2017towards`, `moosavi2016deepfool`).

Key numbers/phrases to keep handy: `ε = .007` panda→gibbon (GoogLeNet, ImageNet); `‖η‖_∞ = ε` fixed vs. `wᵀη = ε·m·n` growing; "accidental steganography"; half-spaces vs. "rationals among the reals"; 84.6% (softmax) vs. 54.3% (RBF) class agreement.
