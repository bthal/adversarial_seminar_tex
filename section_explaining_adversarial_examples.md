# Explaining Adversarial Examples

*Section base — target length ~1–1.5 pages. Prose is draft-level; `\cite{}` keys match `bib.bib` (one entry still to add, see bottom).*

We trace how the discussion around adversarial examples has developed since their discovery in 2013, and in particular how successive attempts to *explain* the phenomenon have replaced one another. As we will see, the history of adversarial examples is also a history of how we think about machine learners — the relationship between human and machine learning, and the very notion of *robustness* in machine learning.

## Szegedy et al. (2013): the discovery

\cite{szegedy2013intriguing} did not yet offer a concrete explanation, but established two base observations that every later account had to reckon with. First, adversarial examples are not random artifacts or isolated anomalies: they occupy structured regions of the input space — "pockets" in the data manifold. They are sparse, and therefore hard to hit by random sampling, yet systematic, and can be generated reliably by optimization. Second, the effect is not merely overfitting. The same adversarial input often fools different models — with different hyperparameters, and even different architectures — a property later called *transferability*, which already argued against the idea that adversarial examples are just the artefact of one particular model overfitting.

## Goodfellow et al. (2014): the linear explanation

The account of \cite{goodfellow2014explaining} became enormously influential — one of the most cited works in the area (on the order of tens of thousands of citations). It reframes the intuition, inherited from \cite{szegedy2013intriguing}, that adversarial examples stem from the *extreme non-linearity* of deep networks (highly contorted decision surfaces with rare, low-probability "blind spots"), possibly compounded by overfitting. Goodfellow et al. argue the opposite: **linear behaviour in high-dimensional spaces is already sufficient** to produce adversarial examples. Modern networks (ReLU, maxout, LSTM) are deliberately built to behave near-linearly for ease of optimization, so the argument carries from strictly linear models to real networks.

The mechanism is a high-dimensional "bloating" effect. Feature precision is limited (e.g. 8-bit pixels discard everything below 1/255 of the dynamic range), so a classifier should be insensitive to any perturbation with $\lVert\eta\rVert_\infty < \epsilon$. For a linear model, $w^\top\tilde{x} = w^\top x + w^\top\eta$: the perturbation shifts the activation by $w^\top\eta$. Maximising this under the max-norm budget yields $\eta = \epsilon\,\mathrm{sign}(w)$, so with $n$ dimensions and mean weight magnitude $m$, $w^\top\eta = \epsilon\sum_i|w_i| = \epsilon\, m\, n$. The per-coordinate change $\epsilon$ stays fixed and imperceptible, but the induced activation shift grows *linearly with the dimension $n$*: many tiny, weight-aligned changes add up to one large change in the output — what Goodfellow calls "accidental steganography."

One framing point is worth stating precisely, since it is frequently garbled in secondary summaries: Goodfellow does **not** claim that networks *are* linear, nor that linearity is a *necessary* ingredient of the explanation. He shows that non-linearity is *unnecessary* — the simplest possible model, plain logistic/softmax regression with no hidden layer, already exhibits adversarial examples — so by an Occam argument the exotic non-linearity hypothesis can be dropped. (The refined, post-2015 view is that *both* dimensionality and local curvature matter, as reflected in stronger iterative and minimum-norm attacks, e.g. \cite{madry2017towards, moosavi2016deepfool}.)

This was also the first account to explain transferability. Under the linear view, adversarial examples occupy broad, contiguous half-spaces rather than fine pockets, so different models' adversarial regions overlap heavily — which is why an example crafted for one model tends to fool another at all. And because the discriminative direction is a property of the data rather than the model, models trained on the same task learn similar weight vectors, so the same perturbation pushes them toward the same wrong class — which is why they tend to *agree* on the mistaken label. Empirically, the degree of agreement tracks how linear the second model is: a linear softmax reproduces a maxout network's wrong label 84.6% of the time, versus only 54.3% for a non-linear RBF network.

To demonstrate the idea, they introduced the Fast Gradient Sign Method, $\eta = \epsilon\,\mathrm{sign}(\nabla_x J(\theta,x,y))$, a single-step attack. Its exact mechanics belong to the discussion of attacks; here its significance is as *evidence for the explanation* — that so cheap a first-order (linear) step reliably fools many different models is itself support for the linearity thesis, and it made adversarial training practical.

## Ilyas et al. (2019): features, not bugs

\cite{ilyas2019adversarial} shift the framing once more: adversarial examples are not pathological model failures but a *mismatch between machine and human perception*. Models exploit patterns in the data that are genuinely predictive, but that humans cannot perceive and find unintuitive. The paper introduces two notions: *predictive features* (features that correlate with the label) and *robust features* (features that remain predictive under perturbation). Since empirical risk minimisation rewards any signal that improves accuracy, a learner will happily rely on predictive but *non-robust* features — so adversarial vulnerability is the expected consequence of standard training, rooted in a gap between the human sense of similarity and the statistical structure of the data.

Their striking demonstration makes this palpable: they build an adversarial version of CIFAR-10 in which every image is perturbed toward a (random) target class and relabelled accordingly. To a human the images look unchanged and hence completely mislabelled, yet a standard model trained on this dataset generalises well to the *clean, unmodified* test set. The model has evidently latched onto real, transferable features that are simply imperceptible to us — not arbitrary noise. This view also recasts interpretability: if a model succeeds by relying on human-imperceptible features, there may be little in its decisions for a human to "understand."

## Synthesis (arc of the section)

Across the three works, the object of explanation moves inward: from the model's geometry (Szegedy's pockets), to the interplay of linearity and dimensionality (Goodfellow), to the structure of the data itself and the human–machine perceptual gap (Ilyas). With it, *robustness* shifts from something we implicitly expect a well-trained model to possess, to a property that must be deliberately engineered — often in tension with ordinary predictive accuracy.

---

### Bib entry to add to `bib.bib`

```bibtex
@article{ilyas2019adversarial,
  title={Adversarial examples are not bugs, they are features},
  author={Ilyas, Andrew and Santurkar, Shibani and Tsipras, Dimitris and Engstrom, Logan and Tran, Brandon and Madry, Aleksander},
  journal={Advances in Neural Information Processing Systems},
  volume={32},
  year={2019}
}
```

### Notes / open choices
- Length is close to budget; if it runs long in LaTeX, the Goodfellow math paragraph can be compressed to just the `\epsilon m n` result, and the transferability paragraph shortened to its two clauses (overlap + shared direction).
- The detailed Goodfellow-only notes remain in `notes_goodfellow_linear_explanation.md` if more depth is needed for any claim.
- Verify the exact citation count for Goodfellow before final (phrased loosely here on purpose).
