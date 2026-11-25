# DSCI 552 - Homework 8

- **Notebook:** [Fulcher_Jesse_HW8.ipynb](./notebook/Fulcher_Jesse_HW8.ipynb)
- **Assignment:** [Homework8.pdf](./Homework8.pdf)

## Setup

Use Python 3.11. From the project root, create a virtual environment and
install the dependencies:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows, use `py -3.11 -m venv .venv` and activate with
`.venv\Scripts\activate` instead.

[requirements.txt](./requirements.txt) lists the notebook's direct
third-party dependencies, plus JupyterLab and the Python kernel.
Versions are not pinned, so this is not an exact environment lockfile.

## Run the Notebook

With the environment activated, start JupyterLab from the notebook folder:

```bash
cd notebook
python -m jupyterlab Fulcher_Jesse_HW8.ipynb
```

In VS Code, open the notebook and select the `.venv` Python interpreter as
the kernel. Run cells from top to bottom because later sections reuse
imports and variables from earlier cells.

The notebook expects its working directory to be `notebook/`: the breast
cancer data is loaded from `../data/wdbc.data`. If loading fails, check the
kernel's working directory and the data file location.

The cross-validation searches, self-training simulation, and 50-run
active/passive learning simulation can take considerable time.
Allow every simulation to finish before interpreting its averages. Resolve
any convergence warnings, rerun affected cells, and update written results
if they change. Save the notebook with its tables and figures.

## Data

The professor-provided data files are included in [data/](./data/):

| File | Dataset |
| --- | --- |
| `wdbc.data` | Breast Cancer Wisconsin (Diagnostic): 569 observations, an ID, a diagnosis, and 30 numeric features |
| `wdbc.names` | Breast cancer dataset documentation |
| `data_banknote_authentication.txt` | Banknote Authentication: 1,372 observations, four numeric features, and a binary class |

Both data files are comma-separated and have no header row. The breast
cancer ID is excluded from the predictors; benign (`B`) is encoded as 0
and malignant (`M`) as 1.

Dataset sources:

- [Breast Cancer Wisconsin (Diagnostic), UCI](https://archive.ics.uci.edu/dataset/17/breast+cancer+wisconsin+diagnostic)
- [Banknote Authentication, UCI](https://archive.ics.uci.edu/dataset/267/banknote+authentication)

## Assignment Overview

### 1. Supervised, Semi-Supervised, and Unsupervised Learning

The notebook compares four methods across 30 Monte Carlo runs:

- L1-penalized linear SVM with five-fold cross-validation.
- Self-training SVM, starting with approximately half of each training
  class labeled and adding pseudo-labeled observations one at a time.
- K-means with two clusters and multiple random initializations.
- RBF spectral clustering with two clusters and `gamma=1`.

Each run uses a stratified 80/20 training/test split. Normalization is
fitted on training data only. The notebook reports average accuracy,
precision, recall, F1-score, and AUC for training and test data, plus ROC
curves and confusion matrices for one run.

**Spectral clustering caveat:** The current implementation is transductive:
it clusters training and test features together using `fit_predict`.
Cluster class assignments and ROC scores use training labels only; test
labels are used only for evaluation. This is not the same as a strictly
held-out prediction experiment, and instructor confirmation of this
interpretation may be needed.

### 2. Active Learning Using Support Vector Machines

The assignment calls for comparing passive and active L1-SVM learning on
banknote data across 50 runs. Each run reserves 472 test observations and
uses the remaining 900 as a training pool, adding 10 observations per step
and plotting average test error against training-set size.

The notebook implements both passive and active learning across 50 runs.
Each method trains at 90 pool sizes, from 10 to 900 observations, using
five-fold cross-validation to select C. The average test errors are plotted
together to compare the learning curves.