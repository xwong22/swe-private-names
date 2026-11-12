# Private-name flags for SWE-rebench and SWE-bench

A task is **private-name flagged** when its hidden tests import, call, access or pass by keyword an identifier that the gold patch introduces in non-test code and that appears neither in the issue text nor anywhere in the repository at the base commit (names inside strings and comments do not count on either side). Such a task can only be passed by guessing the author's name choice. This repository holds the per-task flags and the cleaned id lists; the paper and the detector are in the companion repository (see Citation).

## Numbers

| dataset | tasks analysed | flagged | flagged % | clean ids |
| --- | --- | --- | --- | --- |
| `swe_rebench_v2_python` (nebius/SWE-rebench-V2, python) | 7,203 | 1,749 | 24.3 | 5,454 |
| `swe_rebench_v2_otherlang_sample` (go, ts, js, rust, java, php, kotlin; 25 task-richest repos each) | 8,542 | 1,629 | 19.1 | 6,913 |
| `swe_rebench_v1_openhands_traj` (nebius/SWE-rebench tasks with Nebius OpenHands trajectories) | 5,557 | 278 | 5.0 | 5,279 |
| `swe_bench_full` (princeton-nlp/SWE-bench test) | 2,294 | 115 | 5.0 | 2,179 |
| `swe_bench_verified` | 500 | 8 | 1.6 | 492 |
| `swe_bench_lite` | 300 | 2 | 0.7 | 298 |
| `kaggle_public_107` (gemma-4-developer-agent public set; flags only) | 107 | 10 | 9.3 | 97 |

Flagged tasks are solved far less often by every agent we could measure: Qwen3-Coder-480B (OpenHands, about 11 attempts per task) resolves flagged SWE-rebench V1 tasks at a mean rate of 0.196 against 0.499 for unflagged ones; across 24 SWE-bench submissions on the full test split the rates are 0.033 against 0.201; across 135 SWE-bench Verified submissions 0.109 against 0.523.

## Files

`private_names/<name>.flags.jsonl`: one record per analysed task.

- `strict` (bool): flagged. `strict_names`: the identifiers that trigger the flag.
- `private`: the broader any-mention list (names the gold patch introduces and the test patch mentions anywhere, kept for reference, not used for the lists).
- SWE-rebench-V2 records also carry `in_interface` / `interface_resolves` (do all flagged names appear in the dataset's `interface` field) and the dataset's own LLM labels `v2_code`, `v2_intent`, `v2_B2`.

`private_names/<name>.clean_ids.txt`: instance ids not flagged (the cleaned set). `private_names/<name>.flagged_ids.txt`: flagged ids. `private_names/summary.json`: counts. Tasks whose repository could not be cloned are absent, not marked clean.

`gemma_benchmark/`: the cleaned SWE-rebench-V2 Python sets used in our Gemma 4 31B experiments, in SWE-bench task format (issue, gold patch, test patch, base commit, install config).

- `tasks_valid_64.jsonl`: 64-task out-of-distribution benchmark, one task per repository, gold patch verified in Docker, private-name tasks removed.
- `tasks_train_valid_98.jsonl`: 98-task verified training pool, private-name tasks removed.
- `private_name_drops.json`: the ids removed from each set.
- `exclusion/`: id and repository lists for leakage control (`exclude_*` are the ids and repos to keep out of training when evaluating on the 64-task benchmark or the competition public set; `ood_*`, `train_*`, `kaggle_*` describe the sets).

## Recommended use

For evaluation with issue-only prompts, use `clean_ids`. For training data, drop `flagged_ids`; if the agent will be shown the SWE-rebench-V2 `interface` field, drop only records with `strict` true and `interface_resolves` false.

## Sources

Task ids refer to nebius/SWE-rebench-V2, nebius/SWE-rebench and princeton-nlp/SWE-bench on Hugging Face; the Qwen resolve labels come from nebius/SWE-rebench-openhands-trajectories; SWE-bench submission results come from the SWE-bench/experiments repository. The task files under `gemma_benchmark/` are derived from nebius/SWE-rebench-V2 and keep its fields.

## Citation

*A Mechanical Test for SWE Tasks That Cannot Be Solved From the Issue*. Kaggle gemma-4-developer-agent competition, paper track, November 2026. The paper gives the definition, the detector, the evidence and the limitations behind these lists.
