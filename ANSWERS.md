# Midterm 1: answers

GitHub username: sunkim06
Session code: kettle-62
Fingerprint: cc2eb4f2
> Fill in the three lines above from `my_data/DATA.md`. Do not rename headings or table columns: a script reads the numbers.
> Write only in the empty cells and lines. Percentages as 0.78 or 78 %, money as plain numbers.

## AI-use declaration
During this exam, I used Gemini as AI assistants primarily to quickly understand the core structure, objectives, and requirements of the assignment. I also relied on AI throughout the calculation process to process data files, calculate retrieval metrics, compute Wilson confidence intervals, and evaluate annual cost models. Additionally, I used the AI to analyze the legal and regulatory implications of transferring sensitive personal data, such as customer IINs and names, abroad under Kazakhstan Law No. 94 for the executive memo. All calculations, code, and narrative answers were thoroughly audited and verified by me before submitting.
(About half a page: which assistants or tools you used and for what. If you used none, say so.)

## Part 1: Retrieval and cost decision

### 1a. Metrics (all 60 questions)

| config | recall@1 | recall@3 | recall@5 | MRR@10 |
|---|---|---|---|---|
| A |0.5000 | 0.7000| 0.8000| 0.6153|
| B |0.3000|0.4667| 0.7167 |0.4345|
### 1b. Recall and 95 % Wilson interval of each candidate (recall at its own k)

| candidate | recall@k | lower | upper |
|---|---|---|---|
| A, k=3 |0.7000|0.5749|0.8010|
| A, k=5 |0.8000|0.6822|0.8817| 
| B, k=3 |0.4667|0.3463|0.5911|
| B, k=5 |0.7167|0.5923|0.8149|

### 1c. Annual model cost in USD

| candidate | annual cost |
|---|---|
| A, k=3 |17241.86|
| A, k=5 |22599.77|
| B, k=3 |11486.72|
| B, k=5 |13007.87|

### 1d. Decision (at most 150 words)

Chosen candidate:B, k=5
I selected candidate B, k=5. Candidates under Config A ($17,241.86 for k=3 and $22,599.77 for k=5) exceed the annual budget of $15,100 and must be rejected. Between the two feasible budget options, (B, k=5) costs $13,007.87 per year remaining within budget while significantly outperforming (B, k=3) in overall recall (0.7167 with 95% Wilson interval [0.5923, 0.8149] vs 0.4667 [0.3463, 0.5911]).
EN:0.6500 (65.0%)
RU:0.6500 (65.0%)
KK:0.8500 (85.0%)
Increasing k to 5 under Config B recovers necessary retrieval context while respecting financial constraint.
(Decision, using your intervals and the budget. Give the recall of your choice for EN, RU and KK separately.)

### 1e. Next measurement (at most 60 words)
Run an end-to-end evaluation measuring LLM answer accuracy (using human grading or LLM-as-a-judge) and user latency on 100 real support queries using candidate (B, k=5). If answer quality degrades compared to Config A due to fragmented sentence-window context, we would pivot back to Config A with a smaller k=2 to meet the budget.

## Part 2: A document pipeline you can defend

### 2a. Numbers

| pipeline | overall accuracy | weakest cell (language, condition) | accuracy there | lower | upper | annual cost |
|---|---|---|---|---|---|---|
| H |0.8778|RU, degraded|0.8000|0.6269|0.9050|24133.80|
| L |0.8444|RU, degraded|0.6667|0.4878|0.8077|10800.00|

Personal-data pages per year sent abroad under H:273750
### 2b. Memo (at most 300 words)
I recommend going with Pipeline L instead of the overseas cloud service Pipeline H. Looking at overall numbers, H seems slightly better with 87.8% accuracy compared to L’s 84.4%, but overall figures hide significant performance drops. Both systems struggle most with degraded Russian scans (RU, degraded). In this worst-served case, H drops to 0.8000 (95% Wilson interval [0.6269, 0.9050]) and L drops to 0.6667 [0.4878, 0.8077], though L still performs very well on clean documents (90–97%).The main issue with Pipeline H is data privacy and handling sensitive info. Under H, around 273,750 pages containing personal details like customer names and IINs would be sent out of the country every year. Looking at where the scans go, who stores them, and for how long, H sends data to a foreign data center where an external vendor holds it for an unclear period. Pipeline L completely avoids this risk by keeping all data on our own local server in Kazakhstan, where we can set up clear, checkable safeguards like local disk encryption, network air-gapping, and internal access logs.On top of privacy, Pipeline L is much cheaper, costing $10,800 per year compared to $24,133.80 for H. The main remaining risk with Pipeline L is lower accuracy on blurry documents and server maintenance, which we can easily manage by routing low-confidence scans to staff for quick manual review.

## Part 3: Explain your own numbers

### Q1

Number: 2.23
Why: Output tokens cost 5 times more than input tokens. Kazakh has a smaller proportion of output tokens relative to input tokens than English (output share: KK 7.7% vs EN 10.6%), making its overall cost multiplier lower than its token ratio.

### Q2

Number: 13.5%
Why: Only English is cut because its e5 tokenizer expands 576 cl100k tokens into 591.8 e5 tokens, exceeding the 512 limit. The trailing 79.8 tokens are discarded, causing semantic context loss at chunk endings.

### Q3

Number: 76.9%
Why: Top-p dynamically cuts low-probability tail tokens once cumulative probability hits 0.8 while preserving relative likelihoods. Lowering temperature globally sharpens the entire probability distribution without explicitly excluding any tokens from selection.

### Q4

Number: 36
Why: The public leaderboard prefers Model X (73 vs 69) because unpenalized scoring rewards guessing. This teaches models to hallucinate guesses rather than abstain with "I don't know" when uncertain, whereas penalization favors Model Y's cautious behavior.

### Q5

Number: 18.05
Why: The model is heavily over-trained (18.05x Chinchilla ratio). Chinchilla showed models like GPT-3 were severely under-trained on tokens relative to parameter size, proving compute should be split equally between parameters and tokens during pre-training.
