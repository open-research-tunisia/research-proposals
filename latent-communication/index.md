---
title: "Exploring Venues in the Topic of Latent Communication Between Language Models"
description: "ID:P0067"
---

# Overview

When two language models collaborate, *e.g.,* two agents communicating, they exchange information in natural language, one token at a time. This is a natural interface, but a lossy one. To emit a token, a model reduces its full distribution over the vocabulary into a single choice, discarding the uncertainty and structure that its intermediate activations encode. A model's richest representation of a problem lives in those activations. The final decoding step discards most of it, because next-token prediction does not require it. However, that discarded information can be valuable.

A recent line of work replaces text with the direct exchange of internal state and reports better coordination, stronger reasoning, and substantially lower compute. CIPHER ([Pham et al. 2024](#ref-Pham2024cipher)) lets debating models exchange a probability-weighted average of token embeddings rather than a sampled token, communicating a belief over the whole vocabulary instead of a single guess. Activation communication ([Ramesh and Li 2025](#ref-Ramesh2025activation)) pauses one model mid-forward-pass and grafts another model's intermediate activation into it, transmitting the full enriched representation of the context. Interlat ([Du et al. 2026](#ref-Du2026interlat)) goes furthest, transmitting the entire trajectory of last-layer hidden states as a model's "thought," and compressing it to a handful of latent steps.

These methods share the same principle where information loss occurs at the moment a distribution is reduced into a token, and latent communication reduces such a loss in information. Each method preserves a different amount of the uncollapsed state, and that choice determines how far the transmitted signal moves the receiving model from the activations it normally produces, and therefore how much, if any, training is required. Small perturbations (a single grafted activation, or a convex combination of real embeddings) stay close to states the receiver already knows how to read and work zero-shot, even across model families. Large perturbations (a whole hidden-state trajectory) is distant from the receiver's manifold and require a lightweight trained adapter, together with a curriculum that gradually shifts the receiver from text onto latents.

# Directions

We outline three exploratory directions, rather than concrete research questions.

- **Direction 1 (Redundancy-aware merging).** Existing methods combine two models' activations by summing, averaging, replacing, or learning a fusion layer. However, none keeps what is distinctive in each model while dropping what is redundant. This can be seen as the activation-space version of weight-merging methods such as task arithmetic ([Ilharco et al. 2023](#ref-Ilharco2023)) and TIES ([Yadav et al. 2023](#ref-Yadav2023)). The question is whether we can inject only the part of model A's state that model B does not already encode. One approach is geometric and needs no training where we project out the shared component and keep the orthogonal residual. Another is statistical where we remove from A whatever is linearly predictable from B over a corpus. Either way, the test is whether the merged state stays close enough to the receiver's normal activations for it to use. Of course these approaches rest on many assumptions, for example, it assumes that both models A and B live in comparable space. If they don't, well, we have to figure it out.

- **Direction 2 (Reverse cascade).** The usual cost-saving pattern runs a cheap model first and escalates to an expensive one. We propose testing the inverse: a large model produces the opening of the reasoning (framing the problem and choosing an approach), then hands off to a small model to finish. The large model produces the first $n$ reasoning tokens, the small model completes the rest. A latent version hands off internal state instead of text. In both cases we have to determine $n$, i.e., at what point should the offloading take place. We can do a sweep over $n$, or we could rely on a signal that determines that the reasoning has taken shape, such as a drop in the large model's next-token entropy.

- **Direction 3 (Cross-modal communication).** Existing work is entirely text-to-text. If latent communication preserves what decoding to text discards, its advantage should be largest where text loses the most information. Describing an image in words is an extreme case, since a caption captures only a fraction of what an image contains. The question is whether a multimodal model can pass its visual understanding to a text-only reasoner through latent state rather than a caption. The Platonic Representation Hypothesis ([Huh et al. 2024](#ref-Huh2024platonic)) suggests this is feasible. It posits that models trained on different modalities converge toward a shared representation, and that alignment grows with scale, so a vision latent need not be unintelligible to a text model.

# Feasibility and Resources

We can target small open-weight models (up to 32B). In case of heavy calculations (e.g. the sweeping over $n$ in the second direction), GPUs can be made available.

# Logistics and Collaboration

- **Time commitment:** $6$ months. Number of hours is up to the candidate.

- **Target venue:** To be determined.

- **Mode:** Remote.

- **Authorship:** Ideally, we want the candidate to be the first author. However, that will depend later on their contribution.

# Minimum Requirements

The project is intended to be accessible, and curiosity about how models represent and exchange information matters more than prior research experience.
An ideal collaborator should meet the following minimum requirements:

- Comfort with Python and basic PyTorch or the Hugging Face transformers
  library.

- Interest in conducting research, including reading literature, implementation, running experiments, and writing. The collaborator will not carry out all of these end-to-end, but will play a role in each.

# Nice to Have

The following are qualifications that are nice-to-have, but can be acquired throughout the project:

- Familiarity with the interpretability toolkit for inspecting and intervening on activations.

- Exposure to latent or continuous-space reasoning (e.g., Coconut ([Hao et al. 2024](#ref-Hao2024coconut))), representation alignment across models, or weight-space model merging, which is the parameter-space counterpart of the activation-space merges in Direction 1.

- Basic conceptual understanding of the Transformer architecture.

# References

- <a id="ref-Du2026interlat"></a>Du, Zhuoyun, et al. 2026. "Enabling Agents to Communicate Entirely in Latent Space (Interlat)." arXiv:2511.09149. <https://arxiv.org/abs/2511.09149>. Code: <https://github.com/XiaoDu-flying/Interlat>.

- <a id="ref-Hao2024coconut"></a>Hao, Shibo, Sainbayar Sukhbaatar, DiJia Su, Xian Li, Zhiting Hu, Jason Weston, and Yuandong Tian. 2024. "Training Large Language Models to Reason in a Continuous Latent Space." arXiv:2412.06769. <https://arxiv.org/abs/2412.06769>.

- <a id="ref-Huh2024platonic"></a>Huh, Minyoung, Brian Cheung, Tongzhou Wang, and Phillip Isola. 2024. "The Platonic Representation Hypothesis." In *Proceedings of the 41st International Conference on Machine Learning (ICML)*. arXiv:2405.07987. <https://arxiv.org/abs/2405.07987>.

- <a id="ref-Ilharco2023"></a>Ilharco, Gabriel, Marco Tulio Ribeiro, Mitchell Wortsman, Suchin Gururangan, Ludwig Schmidt, Hannaneh Hajishirzi, and Ali Farhadi. 2023. "Editing Models with Task Arithmetic." In *The Eleventh International Conference on Learning Representations (ICLR)*. arXiv:2212.04089. <https://arxiv.org/abs/2212.04089>.

- <a id="ref-Pham2024cipher"></a>Pham, Chau, et al. 2024. "Let Models Speak Ciphers: Multiagent Debate Through Embeddings." In *The Twelfth International Conference on Learning Representations (ICLR)*. arXiv:2310.06272. <https://arxiv.org/abs/2310.06272>.

- <a id="ref-Ramesh2025activation"></a>Ramesh, Vignav, and Kenneth Li. 2025. "Communicating Activations Between Language Model Agents." In *Proceedings of the 42nd International Conference on Machine Learning (ICML)*. arXiv:2501.14082. <https://arxiv.org/abs/2501.14082>.

- <a id="ref-Yadav2023"></a>Yadav, Prateek, Derek Tam, Leshem Choshen, Colin Raffel, and Mohit Bansal. 2023. "TIES-Merging: Resolving Interference When Merging Models." In *Advances in Neural Information Processing Systems (NeurIPS)*. arXiv:2306.01708. <https://arxiv.org/abs/2306.01708>.
