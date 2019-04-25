# [Question Answering via Integer Programming over Semi-Structured Knowledge](https://arxiv.org/pdf/1604.06076.pdf)

- Answering real science questions is a challenging task because they are posed in natural language, require extensive domain knowledge, and often require combining multiple facts together. They presented TableILP, a system that can answer such questions, using a semi-structured knowledge base.
- They treat QA as a subgraph selection problem and then formulate this as an ILP optimization. Most importantly, this formulation allows multiple, semi-formally expressed facts to be combined to answer questions, a capability outside the scope of IR-based QA systems.
- In their experiments, this approach significantly outperforms both the previous best attempt at structured reasoning for this task, and an IR engine provided with the same knowledge. It also significantly boosts performance when combined with unstructured methods (IR and PMI). These results suggest that the approach is both viable and promising for natural language question answering.