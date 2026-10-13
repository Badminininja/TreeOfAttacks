# Tree of Attacks (TAP): A Reproduction & Safety Study

<<<<<<< HEAD
A solo reproduction of a published LLM-safety method, built for a graduate
course. I rebuilt the *Tree of Attacks with Pruning* red-teaming pipeline,
ran it against an open target model, and tested how well the paper's headline
claims actually hold up on a student's budget.
=======
Joseph Mangapit
>>>>>>> cefb0b683438cbffa79182bc30e98722bc13e759

**CS222 — University of California, Riverside — Winter 2024**
Joseph Mangapit · 📄 **[Full write-up (PDF)](TreeOfAttacksFinal/CS222_TAP_Paper.pdf)**

*Tech stack: Python, Google Colab, Hugging Face Transformers, the OpenAI API,
Weights & Biases.*

## What I found

- **The "low-cost attack" framing didn't hold.** A single 25-prompt run took
  ~2.5 hours and about a third of my monthly Colab compute budget. The original
  paper presents TAP as cheap and easy; at my scale it was neither.
- **A weak judge inflates the success rate.** Using GPT-3.5 as the evaluator, I
  found cases scored as successful "jailbreaks" where the target model hadn't
  actually complied. This suggests reported success numbers (mine *and* the
  original paper's) depend heavily on how capable the judge model is.
- **Reproduced result:** 11 of 25 prompts (44%) reached the maximum judge score
  against Vicuna-7B — in the same ballpark as the paper, but see the caveat above.

![Max judge score per prompt — most prompts land at the extremes (1 or 10), rarely in between](TreeOfAttacksFinal/MaxJudgeScores.png)

## About the method

TAP is an automated red-teaming technique (Mehrotra et al., 2023). One language
model proposes and iteratively refines test prompts against a target model; a
judge model scores each response; and unpromising branches of the search tree
are pruned along the way. Techniques like this are how model developers surface
and then close safety gaps. My goal was to reproduce it independently and
pressure-test its cost and evaluation claims.

## Experiment setup

| Component | Choice |
|---|---|
| Attacker | GPT-3.5 Turbo |
| Judge / evaluator | GPT-3.5 Turbo |
| Target | Vicuna-7B v1.5 |
| Tree parameters | branching factor 4, depth 10, width 5 |
| Prompts | 25 behaviors sampled from the AdvBench benchmark |
| Hardware | Google Colab (A100 when available, otherwise V100) |

I also tried Llama-2-7B-Chat as a target, but it exhausted GPU memory after a
few iterations — itself a useful data point on the method's resource demands.

## Limitations & what I'd do differently

- A stronger judge model (e.g. GPT-4) would give more trustworthy success
  numbers; GPT-3.5 was what the budget allowed.
- The run drew 25 prompts from the full AdvBench set rather than the authors'
  curated 50-prompt subset, so results aren't a like-for-like comparison.
- Single run per configuration — no averaging over seeds, so per-prompt
  outcomes carry some noise.

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
