---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
layout: single
author_profile: true
permalink: /
excerpt: "Choice of Plausible Alternatives without superficial cues (hopefully)"
title: "Balanced COPA"
header:
  overlay_color: "#333"
  caption: ""
  actions:
    - label: "Download"
      url: "/downloads/balanced_copa.tar.bz2"
---

# Summary

<blockquote>
Pretrained language models, such as BERT and RoBERTa, have shown large improvements in the commonsense reasoning benchmark COPA. However, recent work found that many improvements in benchmarks of natural language understanding are not due to models learning the task, but due to their increasing ability to exploit superficial cues, such as tokens that occur more often in the correct answer than the wrong one. Are BERT's and RoBERTa's good performance on COPA also caused by this?

We find superficial cues in COPA, as well as evidence that BERT exploits these cues. To remedy this problem, we introduce Balanced COPA an extension of COPA that does not suffer from easy-to-exploit single token cues.

We analyze BERT's and RoBERTa's performance on original and Balanced COPA, finding that BERT relies on superficial cues when they are present, but still achieves comparable performance once they are made ineffective, suggesting that BERT learns the task to a certain degree when forced to. In contrast, RoBERTa does not appear to rely on superficial cues.

</blockquote>

# COPA: Choice of Plausible Alternatives

Given a premise, such as _The man broke his toe_,
<a href="http://people.ict.usc.edu/~gordon/copa.html" rel="nofollow" target="_blank">COPA</a> requires choosing the more plausible, causally related alternative, in this case either: because _He got a hole in his sock_ (wrong) or because _He dropped a hammer on his foot_ (correct). To test whether COPA contains superficial cues, we conduct a dataset ablation in which we provide only partial input to the model.

Specifically, we provide only the two alternatives but not the premise, which makes solving the task impossible and hence should reduce the model to random performance. However, we observe that a model trained only on alternatives performs considerably better than random chance and trace this result to an unbalanced distribution of tokens between correct and wrong alternatives.

Further analysis reveals that finetuned BERT and RoBERTa perform very well on _easy_ instances containing superficial cues, but worse on _hard_ instances without such simple cues.

To prevent models from exploiting superficial cues in COPA, we introduce **Balanced COPA**

# Balanced COPA

![](/assets/images/cues.png)

Balanced COPA contains one additional, _mirrored_ instance for each original training instance.
This mirrored instance uses the same alternatives as the corresponding original instance, but introduces a new premise which matches the _wrong_ alternative of the original instance, e.g. _The man hid his feet_, for which the correct alternative is now because _He got a hole in his sock_.
Since each alternative occurs exactly once as correct answer and exactly once as wrong answer in Balanced COPA, the lexical distribution between correct and wrong answers is perfectly balanced, i.e., superficial cues in the original alternatives have become uninformative.

Balanced COPA allows us to study the impact of the presence or absence of
superficial cues on model performance.
**Download <a href="{{site.url}}/downloads/balanced_copa.tar.bz2" 
  rel="nofollow" target="_blank">
balanced_copa.tar.bz2 (27K)</a>**

# Balanced COPA Examples

**Original instance**

The stain came out of the shirt. What was the CAUSE of this?<br>
&#10004; I bleached the shirt. <br>
&#10007; I patched the shirt. <!-- correct -->

**Mirrored instance**

The shirt did not have a hole anymore. What was the CAUSE of this?<br>
&#10007; I bleached the shirt. <br>
&#10004; I patched the shirt.

**Original instance**

The woman hummed to herself. What was the CAUSE for this? <br>
&#10004; She was in a good mood. <br>
&#10007; She was nervous.

**Mirrored instance**

The woman trembled. What was the CAUSE for this? <br>
&#10007; She was in a good mood. <br>
&#10004; She was nervous.

# Superficial Cues (Annotation Artifacts)

<!-- img src="{{site.url}}/assets/images/single_token_cues.png" alt="Superficial Cues" -->

One of the simplest types of superficial cues are unbalanced token distributions, i.e tokens appearing more often or less frequently with one particular instance label than with other labels.
COPA contains single token cues, such as a, was, went, that are predictive of the correct alternative.

# Easy/Hard Subsets

To investigate the behaviour of models trained on the original COPA, which contains superficial cues, we split the test set into an _Easy_ subset and a _Hard_ subset.
The _Easy subset_ consists of instances that are correctly solved by the premise-oblivious model, a model trained on the alternatives only.
This results in the _Easy_ subset with 190 instances and the _Hard_ subset comprising the remaining 310 instances. You can download the
<a href="{{site.url}}/downloads/easy_hard_subsets.json" 
 rel="nofollow" target="_blank">Easy and Hard Subsets IDs here (6.4K)</a>

# Results

## Evaluation on Easy and Hard Subsets

<img src="{{site.url}}/assets/images/easy_hard_eval.png" alt="Easy/Hard Evaluation">

Prediction keys for BERT and RoBERTa are available <a href="{{site.url}}/downloads/predictions.tar.bz2" rel="nofollow" target="_blank">Here predictions.tar.bz2 (2.2K)</a>

Previous models perform similarly on both _Easy_ and _Hard_ subsets, with the exception of Sasakiet al. (2017). Overall both BERT and RoBERTa considerably outperform the best previous model. However, BERT's improvements over previous work can be almost entirely attributed to high accuracy on the _Easy_ subset: on this subset.
This indicates that BERT relies on superficial cues.
The difference between accuracy on _Easy_ and _Hard_ is less pronounced for RoBERTa, but still suggests some reliance on superficial cues.
We speculate that superficial cues in the COPA training set prevented BERT and RoBERTa from focusing on task-related non-superficial cues such as causally related event pairs.

## Effect of Balanced COPA

<img src="{{site.url}}/assets/images/bal_eval.png" alt="Easy/Hard Evaluation">

Once superficial cues are removed, the models are able to learn the task to a high degree.

The smaller performance gap between _Easy_ and _Hard_ subsets indicates that training on Balanced COPA encourages BERT and RoBERTa to rely less on superficial cues.
Moreover, training on Balanced COPA improves performance on the Hard subset.

## BERT/RoBERTa Sensitivity to Superficial Cues

<img src="{{site.url}}/assets/images/gradient_sensitivity_prod.png" alt="Gradient Sensitivity">

We observe that BERT trained on Balanced COPA is less sensitive to a few highly productive superficial cues than BERT trained on original COPA.
Note the decrease in the sensitivity for cues of productivity from 0.7 to 0.9.

However, for cues with lower productivity, the picture is less clear, in case of RoBERTa, there are no noticeable trends in the change of sensitivity.

## BERT Difference Embedding

<img src="{{site.url}}/assets/images/embeddings_BERT.png" alt="BERT Embedding PCA">

## RoBERTa Difference Embedding

<img src="{{site.url}}/assets/images/embeddings_RoB.png" alt="RoBERTa Embedding PCA">
