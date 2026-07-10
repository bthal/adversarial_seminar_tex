# Subsection: Real-World and Physical Adversarial Attacks

*For Section 3 (Discussion), to be placed **above** the "Explaining Adversarial Examples" subsection. Target ~half a page. LaTeX-ready (`\cite{}` keys); new bib entries to add are listed at the bottom. `biggio2018wild` is already in `bib.bib`.*

---

\subsection{Real-World and Physical Adversarial Attacks}

One might expect the study of adversarial attacks to be driven primarily by security---the need to defend deployed systems against real attackers. In practice, confirmed \emph{malicious} adversarial-example attacks against real institutions or individuals remain strikingly rare.\cite{biggio2018wild, raff2023dont} Two explanations suggest themselves. The first is that such attacks slip through unnoticed: a successful adversarial input may be logged as an ordinary misclassification, ascribed to generic model error, or filed under an unrelated incident category, so that its true incidence is undercounted. The second, which we find more plausible, is that adversarial examples are simply of little use to an attacker. Crafting them reliably demands specialised machine-learning and security expertise, whereas cruder techniques---distributed denial-of-service, character obfuscation to slip past spam filters, or merely packing a malware binary---remain cheap, fast, and effective.\cite{apruzzese2023real, raff2023dont} Building an adversarial example to defeat a system that a simpler attack would already defeat is like learning to pick a lock when the crowbar is cheaper, quicker, and works just as well; tellingly, the few confirmed real-world cases, such as commercial CAPTCHA-solving services or the prompt injection of deployed chatbots, tend to be those where no such simpler alternative exists. The relevance of adversarial attacks as an object of study is therefore less that of an urgent, widespread security threat than an \emph{epistemic} one: they serve as a lens on how machine perception diverges from human perception.

This does not render the phenomenon academic in the pejorative sense. As machine learning is embedded ever more deeply into the physical world---autonomous vehicles, automated border control, biometric surveillance---the question of whether an input that is semantically unchanged to a human can nonetheless fool a sensor pipeline becomes a central engineering concern. The body of work on \emph{physical} adversarial attacks shows that it can: inconspicuous stickers on a stop sign reliably cause targeted misclassification, both in the laboratory and from a moving vehicle;\cite{eykholt2018robust} printable adversarial patches force a chosen label onto almost any scene they appear in;\cite{brown2017adversarial} adversarial eyeglass frames defeat face recognition;\cite{sharif2016accessorize} and spoofed sensor returns or crafted road-surface markings can mislead the perception stack of a production self-driving system.\cite{cao2019adversarial, tencent2019tesla}

Such results matter because robustness is a core requirement for any safety-relevant deployment---and, as the following subsection develops, it is precisely the study of adversarial examples that reshaped what robustness is understood to mean in the first place.

---

### New bib entries to add to `bib.bib`

```bibtex
@inproceedings{eykholt2018robust,
  title={Robust Physical-World Attacks on Deep Learning Visual Classification},
  author={Eykholt, Kevin and Evtimov, Ivan and Fernandes, Earlence and Li, Bo and Rahmati, Amir and Xiao, Chaowei and Prakash, Atul and Kohno, Tadayoshi and Song, Dawn},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
  pages={1625--1634},
  year={2018}
}

@inproceedings{brown2017adversarial,
  title={Adversarial Patch},
  author={Brown, Tom B. and Man{\'e}, Dandelion and Roy, Aurko and Abadi, Mart{\'\i}n and Gilmer, Justin},
  booktitle={NIPS 2017 Workshop on Machine Learning and Computer Security},
  year={2017},
  note={arXiv:1712.09665}
}

@inproceedings{sharif2016accessorize,
  title={Accessorize to a Crime: Real and Stealthy Attacks on State-of-the-Art Face Recognition},
  author={Sharif, Mahmood and Bhagavatula, Sruti and Bauer, Lujo and Reiter, Michael K.},
  booktitle={Proceedings of the 2016 ACM SIGSAC Conference on Computer and Communications Security (CCS)},
  pages={1528--1540},
  year={2016}
}

@inproceedings{cao2019adversarial,
  title={Adversarial Sensor Attack on {LiDAR}-based Perception in Autonomous Driving},
  author={Cao, Yulong and Xiao, Chaowei and Cyr, Benjamin and Zhou, Yimeng and Park, Won and Rampazzi, Sara and Chen, Qi Alfred and Fu, Kevin and Mao, Z. Morley},
  booktitle={Proceedings of the 2019 ACM SIGSAC Conference on Computer and Communications Security (CCS)},
  pages={2267--2281},
  year={2019}
}

@misc{tencent2019tesla,
  title={Experimental Security Research of Tesla Autopilot},
  author={{Tencent Keen Security Lab}},
  year={2019},
  howpublished={\url{https://keenlab.tencent.com/en/2019/03/29/Tencent-Keen-Security-Lab-Experimental-Security-Research-of-Tesla-Autopilot/}}
}

@inproceedings{apruzzese2023real,
  title={{``Real Attackers Don't Compute Gradients''}: Bridging the Gap Between Adversarial ML Research and Practice},
  author={Apruzzese, Giovanni and Anderson, Hyrum S. and Dambra, Savino and Freeman, David and Pierazzi, Fabio and Roundy, Kevin Alejandro},
  booktitle={2023 IEEE Conference on Secure and Trustworthy Machine Learning (SaTML)},
  pages={339--364},
  year={2023},
  organization={IEEE}
}

@article{raff2023dont,
  title={You Don't Need Robust Machine Learning to Manage Adversarial Attack Risks},
  author={Raff, Edward and Benaroch, Michel and Farris, Andrew L.},
  journal={arXiv preprint arXiv:2306.09951},
  year={2023}
}
```

### Notes
- The "so few attacks in the wild" argument is anchored on two directly-relevant, verified sources: Apruzzese et al. (SaTML 2023), whose title *"Real Attackers Don't Compute Gradients"* is itself the thesis, and Raff, Benaroch & Farris (2023), who argue real-world adversarial attacks are rare and that simpler non-ML mitigations/attacks dominate. (The earlier research pass mislabeled Raff et al. as "Grosse et al." — corrected here.)
- Seven citations total now (up from five) because of the two added in paragraph 1. If space is tight, `biggio2018wild` can be dropped from the first `\cite` since Raff et al. covers the same claim more pointedly.
- For a person-detection / surveillance example, `thys2019fooling` (adversarial patch vs. person detectors, CVPRW 2019) slots naturally after the eyeglasses clause.
- The closing sentence forward-references the Explanation subsection; to cross-link explicitly, add `\label{subsec:explaining}` there and replace "the following subsection" with `Subsection~\ref{subsec:explaining}`.
- `apruzzese2023real`: verified as SaTML 2023, pp. 339--364 (arXiv:2212.14315 is the common preprint id if you prefer to cite that). `raff2023dont`: arXiv:2306.09951, also appeared at AAAI/ACM AIES — the arXiv form is used here.
