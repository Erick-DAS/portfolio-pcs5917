# AGENTS.md

## Context

Individual portfolio for **PCS5917 – IA Adversarial** (MSc, Escola Politécnica – USP, prof. Victor Takashi Hayashi).
The repository is **graded by the professor**, so it must read as the work of a careful student, not as
AI-generated material. Every piece of code and every result must be easy to explain to someone else in a
few sentences: if it is hard to explain, simplify it.

The course rules in `README.md` apply:

- Only the `main` branch is graded; other branches are ignored.
- Weekly, incremental commits: the evolution over the weeks is part of the grade.
- The README is objective, shows evidence of results (tables, figures from `images/`) and cites the references used.
- The README follows the course template and stays in **Portuguese**; keep its section structure and the ethics disclaimer.
- When a new notebook is added the README must be updated with a brief summary about it (what is, results, link to the notebook)

## README sections per class

A new class section copies the shape and the length of the previous ones. It does not invent a new structure,
and it is never a summary of the group report: the report lives in the group repository.

The section is, in this order and nothing else:

1. `## (Aula XX) <topic>` and, when the class had one, `### Atividade em grupo` or `### Atividade para casa`;
2. one or two lines pointing to the notebook and to the result files in `resultados/`;
3. a short `Configuração do experimento:` bullet list, one line per bullet;
4. the results: one table, and a figure from `images/` when it adds something the table does not;
5. at most three sentences of analysis, plus one line of limitations;
6. `Referências usadas:` with the references cited in that section.

Hard limits: the whole section stays around 40 lines, the analysis never repeats numbers already in the table,
and no section is longer than the longest existing one. Cut before adding.

## Repository layout

```text
.
├── README.md                 # results per class, in Portuguese (course template)
├── notebooks/
│   └── aula_XX/              # one folder per class
│       └── aula-XX-<topic>.ipynb
├── images/                   # figures referenced by the README
├── src/pcs5917/              # internal library, only once code is shared (see below)
├── pyproject.toml / uv.lock  # environment
└── AGENTS.md
```

- One notebook per class, named `aula-XX-<topic>.ipynb` (e.g. `aula-02-llm-jailbreaks.ipynb`), inside `notebooks/aula_XX/`.
  Helper scripts or small data files used only by that class live in the same folder.
- Never commit `*_output.ipynb` files, credentials, `.env`, model weights or large datasets.

## Language

- **English** for all code, identifiers, comments, docstrings, printed messages and **notebook markdown**.
- **Portuguese** only for the README and for experiment data that is Portuguese on purpose (e.g. pt-BR versions of prompts).
- Commit messages in English, conventional style (`feat(aula_02): ...`, `fix: ...`, `chore: ...`).
- **No AI attribution in commits**: never add `Co-Authored-By` trailers or "Generated with" lines. Commits are authored by the student only.

## Code style

The notebooks are academic submissions. Write them as a careful student would, not as tutorial material.

- **Self-explanatory code over comments.** Prefer clear structure and descriptive names to explanation.
  Comments are a last resort, one line at most, and only where the code cannot state the intent itself
  (e.g. a hardware constraint). No comments that restate the code, and no decorative banner comments.
- **Docstrings short.** One line for functions where possible. State what the function does, not how.
- **Impersonal prose everywhere** (code, docstrings, printed text, notebook markdown). No second person
  ("you", "your"), no marketing or casual tone. First-person plural ("we") is acceptable in explanatory text.
- **Names.** Descriptive, unabbreviated identifiers in the domain's terms (`human_prompt`, `system_prompt_override`, `attack_id`).
- **No emojis** in code or markdown, and no AI-assistant phrasing ("Let's dive in", "Great question", "robust", "seamless").
- **Simple over clever.** Plain functions and at most one small class per concern. No speculative options,
  wrappers, abstractions or configuration that the experiment does not use.
- **Error and status messages** are terse and factual.

## Notebooks

Each notebook is self-contained and runs top to bottom on a fresh Colab runtime.

1. Title, one-paragraph goal, and an "Open in Colab" badge pointing to the file on `main`
   (`https://colab.research.google.com/github/Andrew1302/portfolio-pcs5917/blob/main/notebooks/aula_XX/<file>.ipynb`).
2. Setup cell: detects Colab and installs only what Colab lacks (`COLAB_EXTRA_PACKAGES`).
3. Shared helpers (functions/classes) in one cell, then the experiments, one cell per experiment or attack.
4. A results section: a summary table (`DataFrame`) and, when useful, a figure.
5. References in markdown next to the experiment that uses them (paper, dataset repository and commit).

- Every experiment prints exactly what was sent to the model and what came back, so the output alone is enough
  to explain it to the professor.
- Results are reproducible: greedy decoding by default, fixed seeds, datasets pinned to a commit or version.
- The committed notebook **keeps its outputs**: they are the evidence cited by the README.

## Internal library

Code moves to `src/pcs5917/` only when a second notebook needs it; until then it stays in the notebook,
which is easier to read and explain. When the library exists:

- Keep it small, with one module per concern (e.g. `pcs5917/attacks.py`, `pcs5917/evaluators.py`).
- Locally it is installed by `uv sync` (the project becomes a package).
- On Colab it is installed from GitHub in the setup cell:
  `%pip install -q "git+https://github.com/Andrew1302/portfolio-pcs5917@main"` (requires the change to be pushed).

## Environment

- Managed with `uv`; Python is pinned to the Colab runtime version (currently 3.13). Check it with
  `!python --version` on Colab when a new runtime image is released.
- Add dependencies with `uv add <package>` (never by editing `pyproject.toml` by hand).
- Colab already ships torch, transformers, pandas, matplotlib and openpyxl; do not reinstall them there,
  reinstalling torch breaks its CUDA build.

## Running on Colab

Three ways, all on the same Colab runtime:

- **Browser:** the "Open in Colab" badge.
- **VS Code:** Google Colab extension, *Select Kernel* → *Colab*.
- **Terminal / agents:** the `colab` CLI ([googlecolab/google-colab-cli](https://github.com/googlecolab/google-colab-cli)),
  installed in WSL Ubuntu (it does not support Windows). Call it as `wsl.exe -d Ubuntu -- bash -lc 'colab ...'`;
  the repository is at `/mnt/c/Users/Andrew/Msc/disciplinas/PCS5917/portfolio-pcs5917`.

CLI workflow:

```bash
colab usage                                   # compute balance before running
colab new -s aula02 --gpu T4                  # omit --gpu when a CPU is enough
colab exec -s aula02 --timeout 1800 --env HF_HUB_DISABLE_PROGRESS_BARS=1 -f notebooks/aula_02/aula-02-llm-jailbreaks.ipynb
colab stop -s aula02                          # always, even after a failure
colab sessions && colab usage                 # confirm nothing is left running
```

- `colab exec -f` writes `<notebook>_output.ipynb` next to the notebook: move it over the original notebook
  (so the committed file keeps its outputs) and never leave the `_output` file behind.
- `colab run --gpu T4 script.py` creates, runs and releases a runtime in one step: preferred for one-off scripts.
- Long jobs: start them on the VM with `nohup ... > /content/<job>.log 2>&1 &` and poll the log; long `colab exec`
  connections from WSL sometimes drop.
- **Use as little Colab compute as possible**: free tier, T4 only. Prefer a CPU runtime when no GPU is needed,
  test logic locally first (stub the model), run the notebook once on the GPU, and stop the session right away.
- T4 limits: 15 GB VRAM, no bf16 (use float16). vLLM does not run Gemma 4 on a T4 (attention kernels exceed its
  shared memory); use Hugging Face `transformers`.

## Data and models

- Small datasets: load from a URL pinned to a commit (e.g. `raw.githubusercontent.com/<repo>/<commit>/...`).
- Large datasets (hundreds of MB or more): keep them in Google Drive under `MyDrive/pcs5917/data/<dataset>/` and
  mount Drive in the notebook (`from google.colab import drive; drive.mount("/content/drive")`, or
  `colab drivemount` from the CLI). Download them once to Drive, never on every run, and never commit them.
- Models come from the Hugging Face Hub. Gated models need an `HF_TOKEN` stored as a Colab secret
  (`google.colab.userdata.get("HF_TOKEN")`), never written in a notebook.

## Ethics

Follow the README's ethics disclaimer. Attacks target models we run ourselves; prefer harmless canaries
(e.g. a secret password in the system prompt) to measure success instead of harmful content.
