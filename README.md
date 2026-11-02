# Causal Logic and DAGs: Diagnosing Bad Controls

## Objective

I use simulated data, causal graphs and a job-training study to examine when regression controls help identify a causal effect and when they create bias or change the question.

## Methodology

- I compare regressions with and without a collider and derive the sign of the resulting association.
- I examine a pre-treatment collider to show why timing alone is not enough to choose controls.
- I separate a training programme’s total effect from its direct effect by comparing models with and without a skill mediator.
- I compare the LaLonde experimental benchmark with observational estimates using CPS controls.
- I use the provided `src/dag_utils.py` module to identify backdoor paths and find the smallest valid adjustment sets under an assumed causal graph.
- I check two AI adjustment-set recommendations against the job-training DAG and revise the prompt to make the causal assumptions explicit.
- I complete an optional sensitivity simulation and compare two possible energy-market DAGs.

## Key Findings

### 1. Controlling for a collider creates bias

My estimate changes from −0.012 without the collider to −0.593 after controlling for it, even though the true causal effect is zero. The controlled model has an R-squared of 0.585.

I find that better fit and a smaller p-value do not establish that a regression gives a better causal estimate.

### 2. Pre-treatment variables can still be bad controls

My estimate becomes −0.201 after controlling for a pre-treatment collider, against a true effect of zero.

I find that a variable’s measurement date does not establish whether it is safe to control for. Its position in the causal graph matters.

### 3. Total and direct effects answer different questions

My estimated total effect of training is 3.978, compared with a true total effect of 4.0.

After controlling for skill, my estimated direct effect is 0.988, compared with a true direct effect of 1.0.

I interpret the difference as the effect operating through skill. For a question about the programme’s overall earnings benefit, I would use the total effect.

### 4. Observational adjustment does not recreate random assignment automatically

In the LaLonde exercise, I obtain:

- Experimental benchmark: $1,794.
- Unadjusted observational estimate: −$8,498.
- Covariate-adjusted observational estimate: $699.

Adjustment moves my estimate toward the experimental benchmark, but this movement alone does not validate the adjustment strategy. I would still need to defend assumptions about confounding, overlap and model specification.

### 5. AI recommendations need a causal check

Both AI recommendations use:

`["prior_earnings"]`

Both pass the DAG check, leave zero backdoor paths open and produce an estimate of 4.047 against a true total effect of 4.0.

My revised prompt makes the causal structure explicit. I exclude risk_score because it is a collider, skill because it is a mediator, and rating because it is a collider and a descendant of training.

### 6. Sensitivity depends on the confounding scenario

In my optional simulation, the estimate doubles relative to the true effect of 0.5 at a confounding strength of approximately 1.001. At that point, the unobserved confounder explains approximately 33.7% of the residual outcome variation after accounting for treatment.

This threshold applies to my simulation, where one parameter controls the confounder’s influence on both treatment and outcome. It is not a general outcome-only bound.

## Repository Files

- `lab_ch10_diagnostic.ipynb`: completed explanations, executed code, AI-response records and optional exercises.
- `src/dag_utils.py`: the graph utility module supplied in the lab and written by the notebook.
- `figures/confounding_sensitivity.png`: my optional sensitivity chart.
- `requirements.txt`: Python packages used to run the notebook.

## Reproducing My Results

I can open the notebook in Google Colab and run all cells in order.

The setup uses seed 42. Diagnoses 1–3 share a random generator, so I restart the runtime before rerunning the entire notebook.

The notebook downloads two public NBER Stata datasets. Internet access is required, but no API key is needed. The Ship It cell automatically writes `src/dag_utils.py`.

The graph utilities evaluate the stated backdoor criterion. They cannot determine whether my causal assumptions are correct. The `minimal_adjustment_sets` function returns the valid sets with the fewest variables.

## AI Assistance

I used ChatGPT to help draft the explanations, complete the optional code and prepare this README.

The two adjustment-set responses were generated in the same preparation session rather than two independent new chats. My notebook retains both recommendations, the revised prompt and the DAG checks.

My written diagnoses are completed explanations rather than a record of unaided answers written before seeing the reveals.

## Data Sources

- [NSW experimental sample](https://users.nber.org/~rdehejia/data/nsw_dw.dta)
- [CPS comparison sample](https://users.nber.org/~rdehejia/data/cps_controls.dta)

I use the Dehejia–Wahba files linked in the lab instructions. The other datasets are simulated within the notebook.
