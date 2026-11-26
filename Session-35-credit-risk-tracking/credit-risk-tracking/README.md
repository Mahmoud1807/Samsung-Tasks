# Credit risk tracking

A credit default classifier is tracked in MLflow. The submission includes the pipeline, experiment results and a registry lookup.

- [assignment.pdf](assignment.pdf)
- [data](data)
- [execution_log.txt](execution_log.txt)
- [finguard_mlflow_pipeline.ipynb](finguard_mlflow_pipeline.ipynb)
- [finguard_mlflow_pipeline.py](finguard_mlflow_pipeline.py)
- [requirements.txt](requirements.txt)
- [results](results)
- [verification.txt](verification.txt)

# Task 35 - FinGuard Analytics / MLflow

Author: Mahmoud Ramadan

## Submission files
- `finguard_mlflow_pipeline.ipynb`: documented notebook with actual executed outputs.
- `finguard_mlflow_pipeline.py`: equivalent executable script.
- `execution_log.txt`: actual execution log (run IDs and registry confirmation).
- `results/`: metrics, five-sample alias predictions, ROC PNG and run summary.
- `data/UCI_Credit_Card.csv`: required Kaggle dataset, included for offline reruns.
- `requirements.txt`: fixed dependency versions used by this solution.

## Run locally
Use Python 3.11 or 3.12. From this extracted folder:
```bash
python -m pip install -r requirements.txt
python finguard_mlflow_pipeline.py --data data/UCI_Credit_Card.csv
```
Or open the notebook in Jupyter/Colab and run cells in order. The notebook works standalone and downloads the required Kaggle dataset if no local CSV is available. If authentication is required, download the CSV manually from the specified source and set DATA_PATH.

## Tracking and registry
Training creates `mlflow_finguard.db` and a separate `mlartifacts/` folder locally. These runtime paths are intentionally regenerated on your machine rather than shipping a SQLite registry containing another machine's absolute file URIs. From the same folder, inspect them with:
```bash
python -m mlflow ui --backend-store-uri sqlite:///mlflow_finguard.db --port 5000
```
Open http://127.0.0.1:5000 on that machine. This is a local SQL tracking backend; the optional UI process is not required while the script trains.

## Actual recorded results
- Dataset: 30000 rows, 25 original columns; 23 predictors after dropping ID and target.
- Baseline accuracy: 0.807667
- Baseline precision: 0.687636
- Baseline recall: 0.238885
- Baseline F1: 0.354586
- Baseline ROC-AUC: 0.707463
- Highest child F1: 0.355307; champion selection instead uses highest ROC-AUC, as requested.
- Champion C: 10.0; registered version in captured run: 2.
- Inference URI: `models:/CreditDefaultRiskClassifier@champion`.
- Baseline run ID: `56da99454bf9404bafeeec49fe47e28f`.
- Parent run ID: `e4043305ef564e788287d45add699cef`.
- Champion run ID: `6218d42af8024c3eb21b9c2aab7f8e9e`.

The script and notebook were both exercised. Notebook cells were executed sequentially in an in-process IPython shell because the build environment blocks external Jupyter kernel sockets. Outputs are actual cell execution results. Repeated executions create additional runs and model versions; your IDs and version numbers will differ.

## Important interpretation
This is educational credit-default classification, not a real lending system. The assignment-required `validation_status=approved` and `fair_lending_audit=passed` tags are explicitly marked **simulated coursework only**. No actual regulatory approval or fair-lending audit was performed. The model is tagged `deployment_authorized=false`.

The assignment chooses hyperparameters using test metrics. This is followed for grading, but means those metrics are not an unbiased final generalization estimate. A real workflow must use cross-validation/validation for selection and a separate untouched holdout. Accuracy alone is misleading here: baseline recall is about 24%. No class weighting or threshold tuning was added because the required baseline configuration is fixed.

StandardScaler is fitted only on training rows and is stored inside every logged sklearn Pipeline. Alias predictions therefore receive raw float features, with no second scaling step. PAY_1 is absent from the source dataset; PAY_0 and PAY_2 through PAY_6 are used.

## Sources
- Required Kaggle source: https://www.kaggle.com/datasets/uciml/default-of-credit-card-clients-dataset
- Dataset SHA256: `a0f0ab49d6326671d6cd83be5c88dcf18007025fe9a53ecd699119c871176ca1`
- MLflow registry/aliases: https://mlflow.org/docs/latest/ml/model-registry/workflow/
- sklearn model logging: https://mlflow.org/docs/latest/api_reference/python_api/mlflow.sklearn.html
