---
title: "Explainable AI (XAI): A systematic meta-survey of ..."
date: 2024-05-25



categories: ["Study"]
math: true
tags: ["paper"]
---

![preface](https://cdn.jsdelivr.net/gh/MiaoHN/image-host@master/images/ink-20240525123959.png)

## Introduction

Current AI technologies (before 2021) have achieved great success in various fields, but the increasing complexity of models and the opacity of black-box models have followed. In response to this problem, XAI was proposed. Although some research has identified the challenges and potential research directions faced by XAI, these studies are scattered and lack a systematic review. This paper conducts a systematic meta-survey of the challenges and future research directions of XAI, divided into two main themes:

1. General challenges and research directions of XAI
2. Challenges and research directions of XAI based on the machine learning lifecycle: design, development, and deployment

## Why XAI is Needed?

Actually, not all black-box AI systems need to be explained[^1] because of reducing systems efficiency and development cost. Generally, there are two situations where XAI is not needed:

1. **When the system is not critical**: For example, when the system is used for entertainment or non-critical applications, such as games or social media platforms.

2. **When the problem has been studied in-depth and well-tested**: For example, when the system is used in a well-understood domain, such as chess or Go.

Based on the retrieved surveys, the need for XAI can be discussed from various perspectives as shown in Fig. 1.

![picture1]fhttps://cdn.jsdelivr.net/gh/MiaoHN/image-host@master/images/ink-20240525131443.png)
_Fig. 1. The five main perspectives for the need for XAI._

## From Explainability to Interpretability

To contribute to a better distinction between explainability and interpretability, this paper attempts to present a distinction between these terms as follows:

> Explainability provides **insights** to a **targeted audience** to fulfill a **need**, whereas interpretability is the degree to which the provided **insights** can make sense for the **targeted audience**'s domain knowledge.
{: .prompt-info }

- **Insights**: The output from explainability techniques (e.g., text explanations, feature relevance, local explanation)
- **Targeted audience**: domain experts, end-users, developers, or regulators
- **Need**: the reason for providing insights (e.g., trust, accountability, transparency, fairness, privacy, security)

## Discussion

![picture2](https://cdn.jsdelivr.net/gh/MiaoHN/image-host@master/images/ink-20240525133724.png)

### General Challenges and Research Directions of XAI

1. **Towards more formalism**
   - The explainability problem must be generalized and formulated rigorously, and this will improve the SOTA for identifying, classifying, and evaluating sub-issues of explainablity
   - Extablishing formalized rigorous evaluation metrics need to be considered as well
2. **Multidisciplinary research collaborations**
   - It is essential to consider the domain of application, the users, type of explanations, and how to provide the explanations for the user
3. **Explanations and the nature of user experience and expertise**
   - In general, users have varying backgrounds, knowledge, and communication styles
   - Abstraction can be used to simplify the explanations
   - DL models often use concepts that are unintelligible to predict outcomes
   - Explanations should be provided differently to different users in different contexts
4. **XAI for trustworthiness AI**
   - Increasing the use of AI in everyday life applications will increase the need for AI trustworthiness, especially in situations where undesirable decisions may have severe consequences
   - With regards to fairness, ML algorithms must not be biased or discriminatory in the decisions they provide. However, with the increased usage of ML techniques, new ethical, policy, and legal challenges have also emerged
   - Having accountability means having someone responsible for the results of AI decisions if harm occurs
   - The use of XAI can enhance understanding, increase trust, and uncover potential risks
5. **Interpretability vs. performance trade-off**
   - The belief that complicated models provide more accurate outcomes is not necessarily correct
   - Models need to be explained in enough detail and in a way that matches the audience for whom they are intended while keeping in mind that explanations reflect the model and do not oversimplify its essential features
6. **Causal explanations**
   - There can be conflicts between predicting performance and causality
   - Causal explanations are anticipated to be the next frontier of ML research and to become an essential part of the XAI literature
7. **Contrastive and counterfactual explanations**
   - Contrastive and counterfactual explanations help improve the interaction between humans and machines and personalize the explanation of algorithms
   - One of the significant barriers towards a fair assessment of new frameworks is the lack of standardization of evaluation methods
8. **XAI for non-image, non-text, and heterogeneous data**
9. **Explainability methods composition**
   - Some overlap exists between explainability methods, but for the most part, each seems to address a different question
   - It is argued that enabling composability in XAI may contribute to enhancing both explainability and accuracy
10. **Challenges in the existing XAI models/methods**
11. **Natural language generation**
12. **Analyzing models, not data**
13. **Communicating uncertainties**
14. **Time constraints**
15. **Reproducibility**
16. **The economics of explanations**

### Challenges and Research Directions of XAI in the Design Phase

1. **Communicating data quality**
2. **Data sharing**

### Challenges and Research Directions of XAI in the Development Phase

1. **Knowledge infusion**
2. **developing apporaches supporting explaining the training process**
3. **Developing model debugging techniques**
4. **Using interpretability/explainability for models/architectures comparison**
5. **Developing visual analytics apporaches for advanced DL architectures**
6. **Sparsity of analysis**
7. **Model innovation**
8. **Rules extraction**
9. **Bayesian approach to interpretability**
10. **Explaining competencies**

### Challenges and Research Directions of XAI in the Deployment Phase

1. **Improving explanations with ontologies**
2. **XAI and privacy**
3. **XAI and security**
4. **XAI and safety**
5. **Human-machine teaming**
6. **Explainable agency**
7. **Machine-to-machine explanation**
8. **XAI and reinforcement learning**
9. **Explainable AI planning (XAIP)**
10. **Explainable recommendation**
11. **XAI as a service**

## Conclusion

In this systematic meta-survey paper, we present two main contributions to the literature of XAI. First, we propose an attempt to present a distinction between explainability and interpretability terms. Second, we shed light on the significant challenges and future research directions of XAI resulting from the selected 58 papers, which guide future exploration in the XAI area. Even though they are presented individually in 39 points, they can overlap and combine them based on researchers’ backgrounds and interests, resulting in new research opportunities where XAI can play an important role. This meta-survey has three limitations. First, because we cannot ensure that the selected keywords are complete, we could miss some very recent papers. Second, to avoid listing the challenges and future research directions per each paper, we come up with the reported 39 points, which are the results of combining what was reported in the selected papers based on the authors’ point of view. Third, we believe that more challenges and future research directions can be added where XAI can play an important role in some domains, such as IoT, 5G, and digital forensic. However, related surveys did not exist at the time of writing this meta-survey

[^1]: Amina Adadi and Mohammed Berrada. Peeking inside the black-box: A survey on explainable artificial intelligence (XAI). IEEE Access, 6:52138–52160, 2018. doi:10.1109/ACCESS.2018.2870052.
