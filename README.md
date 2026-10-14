# AI Interview & Candidate Evaluation System

Explainable, bias-aware screening: resume/JD matching, adaptive questions, rubric-based answer scoring,
a recruiter dashboard and a fairness audit. A human always makes the final hiring decision.

## Pipeline
Resume + JD -> PII masking -> skill extraction + TF-IDF match -> adaptive questions -> answer evaluation
(rubric, optional LLM judge) -> follow-ups -> weighted final score (30% resume fit, 70% interview) -> dashboard.

## Run
```bash
pip install -r requirements.txt
uvicorn app.main:app --reload        # open http://localhost:8000
pytest                                # unit + API tests
python -m scripts.evaluate            # AI vs human correlation
docker build -t interviewer . && docker run -p 8000:8000 interviewer
```
Optional: `export ANTHROPIC_API_KEY=...` blends an LLM-as-judge score 50/50 with the rubric score.

## Scoring rubric (0-10)
Concept coverage 45%, semantic similarity to reference 25%, depth 15%, clarity 15%. Each answer returns covered/missing concepts as its explanation.

## Validation (put real numbers on your CV)
`data/human_scores.json` has 19 sample answers (Pearson 0.95, Spearman 0.93, MAE 0.85 on this small set). These are demo data:
replace them with 50-100 answers rated by humans (ideally two raters) and report your own figures.

## Fairness
Names, emails, phones, URLs, institutions and gendered words are masked before scoring. `/api/fairness/test`
prepends name/gender/college cues and checks the score shift stays within 0.25. Extend with Fairlearn on a larger dataset.

## Extend next
Sentence-transformer embeddings, Whisper speech analysis, Judge0 coding round, Postgres + Celery, fine-tuned DistilBERT scorer vs LLM-as-judge, auth and consent screens.
