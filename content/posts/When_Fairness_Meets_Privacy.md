+++
title = 'When Fairness Meets Privacy'
date = 2025-03-10T12:22:37+01:00
draft = false
+++

<style TYPE="text/css">
 code.has-jax {font: inherit; font-size: 100%; background: inherit; border: inherit;}
 </style>
 <script type="text/x-mathjax-config">
 MathJax.Hub.Config({
     tex2jax: {
         inlineMath: [['$','$'], ['\\(','\\)']],
         skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'] // removed 'code' entry
     }
 });
 MathJax.Hub.Queue(function() {
     var all = MathJax.Hub.getAllJax(), i;
     for(i = 0; i < all.length; i += 1) {
         all[i].SourceElement().parentNode.className += ' has-jax';
     }
 });
 </script>
 <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/MathJax.js?config=TeX-AMS_HTML-full"></script>

<h1 style="font-size: 36px;">When Fairness Meets Privacy: A Double-Edged Sword in Machine Learning</h1>

<h1 style="font-size: 24px;">Authors: Vincent Lagarde & Boyenval Thibaut</h1>

## Table of Contents
1. [Introduction: Fairness or Privacy—Pick Your Poison?](#1-introduction-fairness-or-privacy-pick-your-poison)
2. [Algorithmic Fairness: A Noble Goal That Cuts Both Ways](#2-algorithmic-fairness-a-noble-goal-that-cuts-both-ways)
3. [Membership Inference Attacks: The Silent Thief of Privacy](#3-membership-inference-attacks-the-silent-thief-of-privacy)
4. [The Birth of a New Threat: Fairness Discrepancy Membership Inference Attacks (FD-MIA)](#4-the-birth-of-a-new-threat-fairness-discrepancy-membership-inference-attacks-fd-mia)
5. [Experimental Findings: How Fairness Opens the Door to Attackers](#5-experimental-findings-how-fairness-opens-the-door-to-attackers)
6. [The Future of Fairness and Privacy: Can We Have Both?](#6-the-future-of-fairness-and-privacy-can-we-have-both)
7. [Conclusion: Walking the Razor’s Edge](#7-conclusion-walking-the-razors-edge)

---

## 1. Introduction: Fairness or Privacy—Pick Your Poison?

Imagine stepping into a high-tech courtroom in 2024. The AI judge, designed to be perfectly fair, renders unbiased decisions. But then, a hacker in the back row smirks—because that same fairness-enhancing mechanism just leaked private data about every case it trained on. 

Fairness and privacy in AI are like the two ends of a seesaw: push too hard on one side, and the other rises uncontrollably. **Recent research reveals a disturbing paradox: making a model fairer can also make it leak more private information.**

This blog explores how fairness in machine learning, despite its good intentions, can introduce **Membership Inference Attacks (MIAs)**. Worse still, it uncovers a devastating new attack—**Fairness Discrepancy Membership Inference Attack (FD-MIA)**—that exploits fairness interventions to **make privacy breaches even more effective**. 

---

## 2. Algorithmic Fairness: A Noble Goal That Cuts Both Ways

Fairness in AI is like forging a perfect sword—it must be balanced, precise, and just. Researchers have developed **in-processing fairness interventions**, which modify the training process to remove biases in model predictions. These methods act like master swordsmiths, hammering out the unwanted imperfections in AI decision-making.

However, every sword has two edges. These fairness techniques do not just eliminate biases—they also alter how models respond to data. This change in behavior can create exploitable patterns that adversaries can use to infer whether a specific individual was part of the training data. In short, while fairness dulls one blade (bias), it sharpens another (privacy risk).

Mathematically, fairness interventions often involve introducing constraints into the loss function: $ \mathcal{L}_{\text{fair}} = \mathcal{L}_{\text{orig}} + \lambda \cdot \mathcal{L}_{\text{fairness}} $

where:
- $ \mathcal{L}_{\text{orig}} $ is the original loss function (e.g., cross-entropy loss for classification tasks).
- $ \mathcal{L}_{\text{fairness}} $ is a fairness penalty term, which ensures that predictions are balanced across different demographic groups.
- $ \lambda $ is a hyperparameter controlling the trade-off between accuracy and fairness.

Common fairness constraints include **Equalized Odds**, which ensures that true positive and false positive rates are equal across groups:

$$
P(\hat{Y} = 1 | Y = 1, S = s_0) = P(\hat{Y} = 1 | Y = 1, S = s_1)
$$

where $ S $ represents a sensitive attribute (e.g., gender or race).

While these interventions improve fairness, they also alter the confidence distribution of model predictions—**a fact that attackers can exploit**.

---

## 3. Membership Inference Attacks: The Silent Thief of Privacy

Imagine a thief who can tell, just by looking at a house, whether you've recently installed a new security system. **Membership Inference Attacks (MIAs)** work the same way: they analyze a model’s outputs to determine if a given data point was part of its training set.

A traditional MIA exploits **confidence scores**—the probabilities that a model assigns to different predictions. The intuition is simple: models tend to be more confident on data they have seen during training. Given a target model $ T $ and a queried sample $ x $, an attacker computes:

$$
M(x) = 1 \text{ if } A(T(x)) > \tau
$$

where:
- $ A(T(x)) $ is a decision function (often a threshold on the confidence score).
- $ \tau $ is a predefined threshold.

### Why Traditional MIAs Fail on Fair Models

Fairness interventions introduce **more uncertainty** into the model’s predictions. This causes:
- **Lower confidence scores overall**, making it harder for attackers to distinguish between training and non-training samples.
- **More uniform confidence distributions**, which means attackers lose their key signal.

Thus, fairness-enhanced models resist traditional MIAs. But this protection is not foolproof—**a new, more dangerous attack lurks in the shadows**.

---

## 4. The Birth of a New Threat: Fairness Discrepancy Membership Inference Attacks (FD-MIA)

If traditional MIAs are blunt weapons, **FD-MIA is a scalpel.** It exploits the discrepancies between a biased model and a fairness-enhanced one. 

### How does FD-MIA work?

Fairness interventions shift model predictions differently for training and non-training data. This creates a **gap** between how biased and fair models behave for the same inputs. An attacker, armed with knowledge of both models, can exploit this difference to infer membership with high accuracy.

Mathematically, FD-MIA extends membership prediction by comparing prediction shifts between biased and fair models:

$$
M(x) = 1 \text{ if } |T_{\text{bias}}(x) - T_{\text{fair}}(x)| > \tau
$$

where:
- $ T_{\text{bias}}(x) $ and $ T_{\text{fair}}(x) $ are the predictions from the biased and fair models, respectively.
- $ \tau $ is a threshold chosen by the attacker.

The key insight is that **fairness interventions cause systematic shifts** in model confidence, creating a measurable pattern that attackers can exploit.

---

## 5. Experimental Findings: How Fairness Opens the Door to Attackers

The study conducted extensive experiments across **six datasets, three attack methods, and five fairness approaches**, testing over **160 models**. The results were shocking:

- **Fair models were significantly harder to attack using traditional MIAs.**
- **FD-MIA, however, dramatically increased attack success rates—fairness actually made models more vulnerable!**
- The greater the fairness intervention, the wider the discrepancy between biased and fair models, making FD-MIA even more effective.

In simple terms: **making a model fairer may paradoxically make it leak more private information.** A cruel irony for those trying to do the right thing.

---

## 6. The Future of Fairness and Privacy: Can We Have Both?

The million-dollar question: **Can we balance fairness and privacy without sacrificing one for the other?**

The researchers propose two key defenses:

1. **Restricting Information Access**  
   - Limiting confidence score outputs reduces the information available to attackers.
   
2. **Differential Privacy (DP)**  
   - By injecting noise into model training, DP-SGD (Differentially Private Stochastic Gradient Descent) helps obscure membership information:

   $$
   \tilde{g}_t = g_t + \mathcal{N}(0, \sigma^2 I)
   $$

   where $ g_t $ is the original gradient and $ \mathcal{N}(0, \sigma^2 I) $ is Gaussian noise added to prevent membership inference.

While these methods help, they come with trade-offs: **too much privacy protection can lower fairness and accuracy, while too little leaves models vulnerable.** The challenge ahead is designing AI systems that can balance both.

---


