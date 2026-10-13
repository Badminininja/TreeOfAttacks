# Tree of Attacks (TAP): A Reproduction Study

Joseph Mangapit

📄 **[Read the paper](TreeOfAttacksFinal/CS222_TAP_Paper.pdf)**

## Overview

This project reproduces and examines *Tree of Attacks with Pruning* (TAP), from
[Tree of Attacks: Jailbreaking Black-Box LLMs Automatically](https://arxiv.org/abs/2312.02119)
(Mehrotra et al., 2023). TAP is an automated red-teaming method. One language
model iteratively proposes and refines test prompts against a target model, and
a judge model scores the responses. Unpromising branches of the search tree are
pruned along the way.

Studying attacks like this is how model developers find and fix safety gaps.
The aim here was to see how well the published results hold up on a
student's budget and what the method actually costs to run.

## Setup

| Component | Choice |
|---|---|
| Attacker | GPT-3.5 Turbo |
| Judge / evaluator | GPT-3.5 Turbo |
| Target | Vicuna-7B v1.5 |
| Tree parameters | branching factor 4, depth 10, width 5 |
| Prompts | 25 behaviors sampled from the AdvBench benchmark |
| Hardware | Google Colab (A100 when available, otherwise V100) |

I also tried Llama-2-7B-Chat as a target, but it ran out of GPU memory after a
few iterations.

## Results

- **11 of 25 prompts (44%)** reached the maximum judge score of 10 against
  Vicuna-7B.
- The 25-prompt run took about **2.5 hours** and roughly **a third of my Colab
  compute units**. That runs against the original paper's framing of TAP as a
  low-cost attack.
- With GPT-3.5 as the judge, some responses were scored as successful even
  though the target didn't really comply. A weaker judge inflates success
  rates, which may also explain some of the original paper's results.

![Max judge score per prompt](TreeOfAttacksFinal/MaxJudgeScores.png)

## Repository contents

| File | What it is |
|---|---|
| `TreeOfAttacksFinal/CS222_TAP_Paper.pdf` | The write-up: method, results, discussion |
| `TreeOfAttacksFinal/CS222FinalTAP.ipynb` | The Colab notebook used to launch the experiments, with its run log |
| `TreeOfAttacksFinal/VicunaFullCSV.csv` | Every logged attack attempt from the Vicuna runs (229 rows) |
| `TreeOfAttacksFinal/SuccessfulVicunaCSV.csv` | The attempts that scored 9–10 |
| `TreeOfAttacksFinal/MaxJudgeScores.png` | Max judge score per prompt (the chart above) |

**Note on redaction:** where the target model complied with a harmful request,
its response has been replaced with a placeholder in the CSVs and notebook
output. The prompts, judge scores and all other columns are unchanged.

The TAP implementation itself is the authors' code,
[RICommunity/TAP](https://github.com/RICommunity/TAP) (MIT license), with small
changes to run in Colab. It isn't included in this repository.

## Acknowledgments

- Mehrotra et al., [*Tree of Attacks: Jailbreaking Black-Box LLMs Automatically*](https://arxiv.org/abs/2312.02119), and their [reference implementation](https://github.com/RICommunity/TAP)
- Chao et al., [*Jailbreaking Black Box Large Language Models in Twenty Queries*](https://arxiv.org/abs/2310.08419) (PAIR), which TAP builds on
- Zou et al., [AdvBench](https://github.com/llm-attacks/llm-attacks)
