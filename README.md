This repo runs two stance/stance-like classifiers fine-tuned from mlburnham/Political_DEBATE_DeBERTa_large_v1.1 on newspaper headlines to estimate the tone of coverage for named political figures.
Hypotheses (labels inferred with natural-language prompts):
Author Support: “The author of this text supports {candidate}.”
Author Performance: “The author of this text believes {candidate} is performing/performed/will perform well/poorly.”
Note on validation:A working paper showing high agreement with human-coded benchmarks is in progress. When it’s public, we’ll link it here. For now, the paper is not yet available.


Base model: mlburnham/Political_DEBATE_DeBERTa_large_v1.1 (Hugging Face)
Fine-tuned checkpoints used here:
zkava01/AuthorSupport_Oct29 (hypothesis set #1: support vs. not)
zkava01/AuthorPerformance_Oct29 (hypothesis set #2: performance good vs. poor)
Both are used via the Hugging Face transformers zero-shot-classification pipeline with natural-language hypotheses.

What the code does:
1. Input a CSV of news headlines (and dates). Required columns: title (the headline text), date (YYYY-MM-DD)
2. Detects the political figure mentioned in each headline:
3. Dictionary maps last names (ex. “Biden”, “Harris”, “Trump”, etc.) to full names (ex. “Joe Biden” → Joe Biden).
4. Code creates a column figure with the full candidate name.
4. You can restrict to a set of key_figures (ex ["Barack Obama"]).
5. Runs two classifiers over each headline that includes a target figure:

AuthorSupport → outputs debate_support in {1 (support), 0 (neutral/uncertain), -1 (oppose)}
Current thresholds in the notebook:
support if P(support) > 0.99
oppose if P(oppose) > 0.99
otherwise 0

AuthorPerformance → outputs debate_performance in {1 (good), 0 (neutral/uncertain), -1 (poor)}
Current thresholds in the notebook:
good if P(good) > 0.50
poor if P(poor) > 0.50
otherwise 0

Chunked processing & outputs:
Processes headlines in chunks (default 500) 
Produces a merged CSV 
title, date, figure, debate_support, debate_performance 
