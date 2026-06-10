---
title: "Relational AutoML in the Era of Agentic AI"
description: "ID:P0070"
---

# Overview

Most of the world's structured data lives in relational databases. Banks, hospitals,
retailers, and governments keep their records in collections of tables linked by keys:
customers point to orders, orders point to products, products point to suppliers. This
is where the data actually is, and it rarely moves.

Machine learning, however, expects a single flat table: one row per example, one column
per feature. To bridge the gap, practitioners spend most of their effort flattening the
database into that shape, joining tables together, aggregating across relationships
(*"how many orders did this customer place in the last 30 days?"*), and hand-crafting the
features that carry the relational structure into a form a model can read. This step is
laborious, it discards much of the structure that made the database useful in the first
place, and it forces the data out of the system it lives in, which is expensive and
often impossible when privacy or governance rules keep the data in place.

Relational machine learning offers a different path: learn directly on the relational
structure, without flattening and without moving the data. Recent relational deep
learning work ([Fey et al. 2024](#ref-Fey2024rdl); [Robinson et al. 2024](#ref-Robinson2024relbench))
treats the database as a graph, the rows are nodes and the foreign keys are edges, and
trains a model over that graph in place. The promise is to skip the brittle, manual
feature-engineering step entirely and let the model exploit the schema as it stands.

This reframes a long-standing problem. Automated Machine Learning (AutoML) was built to
take the burden of model selection and hyperparameter tuning off the human
([Thornton et al. 2013](#ref-Thornton2013autoweka); [Feurer et al. 2015](#ref-Feurer2015autosklearn); [Hutter, Kotthoff, and Vanschoren 2019](#ref-Hutter2019automlbook)).
But today's AutoML tools still assume the data arrives as a fixed, single table. They
automate the search over algorithms; they do not reason about the *data* — how it could
be reshaped, joined, aggregated, or enriched, which is where real performance gains
usually come from ([Kanter and Veeramachaneni 2015](#ref-Kanter2015dfs)). On a relational
database, the data side *is* the problem: which tables to bring in, which relationships to
traverse, which aggregates to compute. An AutoML system that understands relational
structure has a far richer space of moves available to it than one staring at a single
table.

This is where the current wave of agentic AI becomes interesting. An agent can plan, take
actions, and call tools on its own. Pointed at a relational database, an agent could in
principle read the schema, decide which relationships are worth exploring, derive new
features across tables, augment or enrich the data, and then run the model search, all
without a human flattening the database by hand and without copying the data out. But to
do this in a principled way, we need to ask a basic question: what is the right way to
describe and organize this whole process? What formalism lets an agent reason about
schema traversal, feature enrichment, and model search together, rather than as
disconnected steps?

A second, very practical question is the infrastructure underneath. When an agent
experiments with many versions of a relational dataset (materializing a join, adding a
derived table, augmenting rows, trying a transformation) it needs a fast and cheap way to
create, modify, and discard these versions, ideally *in place* so the data never leaves
the database. Database clones, lightweight copy-on-write branches of a database that can
be spun up and thrown away, may be a natural fit. Each experiment becomes a clone the
agent can play with without disturbing the original or moving anything. Whether database
clones are actually a good backbone for agentic relational AutoML is an open and practical
question.

# Research Questions

We organize the investigation around three questions:

- **RQ1 (Landscape).** What do AutoML and relational machine learning look like today?
  Which problems do current tools solve well, and where do they fall short, especially in
  doing ML on relational data without flattening or moving it, and in feature enrichment
  across tables?

- **RQ2 (Formalism).** Is there a clean way to describe the combined process of schema
  exploration, feature enrichment, and model search so that an agentic system can reason
  about it directly over a relational database? What would such a formalism need to
  capture?

- **RQ3 (Infrastructure).** Are database clones a sensible foundation for managing the
  many dataset versions an agentic relational AutoML system would generate, in place and
  without copying the data out? What are the trade-offs in speed, storage, and
  convenience?

# Approach

The following is a tentative plan that can be refined as the project moves forward.

#### Survey first.

The core deliverable is a literature review, essentially a small survey paper, mapping out
the state of AutoML and relational machine learning. The student would read and organize
recent work on automated model selection, hyperparameter optimization, automated feature
engineering, relational deep learning, and data augmentation, and place agentic approaches
in that context.

#### Identify the gaps.

From the survey, we want to pin down concretely where today's tools stop short, with
attention to the data side (relational feature enrichment, augmentation, learning in place)
rather than only the algorithm side.

#### Sketch the formalism.

Based on what the survey reveals, we will try to outline what a formal description of
agentic relational AutoML might look like. This is exploratory and conceptual; the goal is
a clear sketch, not a finished theory.

#### Consider the infrastructure angle.

Where time allows, we will examine database clones as a possible substrate for managing
relational dataset experiments in place, and discuss the practical trade-offs.

# Feasibility and Resources

The bulk of the project is reading, organizing, and writing, which is very tractable for a
motivated student. Some lightweight hands-on experimentation with existing AutoML and
relational learning libraries and database tooling may be involved. Access to modest
computing power will be needed for any experiments.

# Logistics and Collaboration

- **Time commitment:** 3 months. Around 3 hours per week from the student.

- **Meetings:** roughly 1 hour per week.

- **Team size:** 2 to 3 students.

- **Mode:** Remote.

- **Authorship:** Co-authorship on the resulting survey paper, with credit reflecting
  contribution.

# Minimum Requirements

An ideal candidate should meet the following:

- Interest in research, including reading literature, organizing ideas, and writing them
  up clearly.

- Comfort reading CS-related scientific papers.

- A working knowledge of classical machine learning (what algorithms do, how training and
  evaluation work).

- Familiarity with relational database management systems and basic SQL.

- Good Python skills.

# Nice to Have

The following can be picked up during the project:

- Familiarity with the software development lifecycle (SDLC).

- Some exposure to CUDA or parallel programming.

- Prior use of an AutoML library (for example auto-sklearn, AutoGluon, or H2O), or a
  relational/graph learning library.

# References

- <a id="ref-Fey2024rdl"></a>Fey, Matthias, Weihua Hu, Kexin Huang, Jan Eric Lenssen, Rishabh Ranjan, Joshua Robinson, Rex Ying, Jiaxuan You, and Jure Leskovec. 2024. "Relational Deep Learning: Graph Representation Learning on Relational Databases." arXiv:2312.04615. <https://arxiv.org/abs/2312.04615>.

- <a id="ref-Feurer2015autosklearn"></a>Feurer, Matthias, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel Blum, and Frank Hutter. 2015. "Efficient and Robust Automated Machine Learning." In *Advances in Neural Information Processing Systems (NeurIPS)* 28, 2962–70. <https://papers.nips.cc/paper/2015/hash/11d0e6287202fced83f79975ec59a3a6-Abstract.html>.

- <a id="ref-Hutter2019automlbook"></a>Hutter, Frank, Lars Kotthoff, and Joaquin Vanschoren, eds. 2019. *Automated Machine Learning: Methods, Systems, Challenges*. Springer. <https://www.automl.org/book/>.

- <a id="ref-Kanter2015dfs"></a>Kanter, James Max, and Kalyan Veeramachaneni. 2015. "Deep Feature Synthesis: Towards Automating Data Science Endeavors." In *2015 IEEE International Conference on Data Science and Advanced Analytics (DSAA)*, 1–10. <https://doi.org/10.1109/DSAA.2015.7344858>.

- <a id="ref-Robinson2024relbench"></a>Robinson, Joshua, Rishabh Ranjan, Weihua Hu, Kexin Huang, Jiaqi Han, Alejandro Dobles, Matthias Fey, Jan Eric Lenssen, Yiwen Yuan, Zecheng Zhang, Xinwei He, and Jure Leskovec. 2024. "RelBench: A Benchmark for Deep Learning on Relational Databases." In *Advances in Neural Information Processing Systems (NeurIPS)*. arXiv:2407.20060. <https://arxiv.org/abs/2407.20060>.

- <a id="ref-Thornton2013autoweka"></a>Thornton, Chris, Frank Hutter, Holger H. Hoos, and Kevin Leyton-Brown. 2013. "Auto-WEKA: Combined Selection and Hyperparameter Optimization of Classification Algorithms." In *Proceedings of the 19th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD)*, 847–55. <https://doi.org/10.1145/2487575.2487629>.
