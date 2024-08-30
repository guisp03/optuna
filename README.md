<div align="center"><img src="https://raw.githubusercontent.com/optuna/optuna/master/docs/image/optuna-logo.png" width="800"/></div>

# Optuna: A hyperparameter optimization framework

[![Python](https://img.shields.io/badge/python-3.7%20%7C%203.8%20%7C%203.9%20%7C%203.10%20%7C%203.11%20%7C%203.12-blue)](https://www.python.org)
[![pypi](https://img.shields.io/pypi/v/optuna.svg)](https://pypi.python.org/pypi/optuna)
[![conda](https://img.shields.io/conda/vn/conda-forge/optuna.svg)](https://anaconda.org/conda-forge/optuna)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/optuna/optuna)
[![Read the Docs](https://readthedocs.org/projects/optuna/badge/?version=stable)](https://optuna.readthedocs.io/en/stable/)
[![Codecov](https://codecov.io/gh/optuna/optuna/branch/master/graph/badge.svg)](https://codecov.io/gh/optuna/optuna)

:link: [**Website**](https://optuna.org/)
| :page_with_curl: [**Docs**](https://optuna.readthedocs.io/en/stable/)
| :gear: [**Install Guide**](https://optuna.readthedocs.io/en/stable/installation.html)
| :pencil: [**Tutorial**](https://optuna.readthedocs.io/en/stable/tutorial/index.html)
| :bulb: [**Examples**](https://github.com/optuna/optuna-examples)
| [**Twitter**](https://twitter.com/OptunaAutoML)
| [**LinkedIn**](https://www.linkedin.com/showcase/optuna/)
| [**Medium**](https://medium.com/optuna)

*Optuna* is an automatic hyperparameter optimization software framework, particularly designed
for machine learning. It features an imperative, *define-by-run* style user API. Thanks to our
*define-by-run* API, the code written with Optuna enjoys high modularity, and the user of
Optuna can dynamically construct the search spaces for the hyperparameters.

## :loudspeaker: News

* **Aug 30, 2024**: We posted [an article](https://medium.com/optuna/optunahub-a-feature-sharing-platform-for-optuna-now-available-in-official-release-4b99efe9934d) about the official release of [OptunaHub](https://hub.optuna.org/).
* **Aug 28, 2024**: We posted [an article](https://medium.com/optuna/a-natural-gradient-based-optimization-algorithm-registered-on-optunahub-0dbe17cb0f7d) about [implicit natural gradient optimization (`INGO`)](https://hub.optuna.org/samplers/implicit_natural_gradient/), a sampler newly supported in [OptunaHub](https://hub.optuna.org/).
* **Aug 20, 2024**: We posted [an article](https://medium.com/optuna/file-management-during-llm-large-language-model-trainings-by-optuna-v4-0-0-artifact-store-5bdd5112f3c7) about [`ArtifactStore`](https://optuna.readthedocs.io/en/stable/reference/artifacts.html) officially supported from Optuna 4.0.
* **Aug 15, 2024**: We posted [an article](https://medium.com/optuna/introduction-to-catcma-in-optunahub-ffa316309cb8) about [`CatCMA`](https://hub.optuna.org/samplers/catcma/), a sampler newly supported in [OptunaHub](https://hub.optuna.org/).
* **Aug 7, 2024**: We posted [an article](https://medium.com/optuna/significant-speed-up-of-multi-objective-tpesampler-in-optuna-v4-0-0-2bacdcd1d99b) about the significant (300x) speed up of multi-objective [`TPESampler`](https://optuna.readthedocs.io/en/stable/reference/samplers/generated/optuna.samplers.TPESampler.html) in Optuna 4.0.
* **Jul 16, 2024**: The beta version of [OptunaHub](https://hub.optuna.org/), a feature-sharing platform for Optuna is available! Please check the detail in [our article](https://medium.com/optuna/announcing-optunahub-0-1-0-%CE%B2-69b35bb3e95e).
* **Jul 12, 2024**: The beta version of Optuna 4.0 is available!  You can install the beta version by `pip install -U --pre optuna`. Find the latest [here](https://github.com/optuna/optuna/releases).


## :fire: Key Features

Optuna has modern functionalities as follows:

- [Lightweight, versatile, and platform agnostic architecture](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/001_first.html)
  - Handle a wide variety of tasks with a simple installation that has few requirements.
- [Pythonic search spaces](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/002_configurations.html)
  - Define search spaces using familiar Python syntax including conditionals and loops.
- [Efficient optimization algorithms](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/003_efficient_optimization_algorithms.html)
  - Adopt state-of-the-art algorithms for sampling hyperparameters and efficiently pruning unpromising trials.
- [Easy parallelization](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/004_distributed.html)
  - Scale studies to tens or hundreds of workers with little or no changes to the code.
- [Quick visualization](https://optuna.readthedocs.io/en/stable/tutorial/10_key_features/005_visualization.html)
  - Inspect optimization histories from a variety of plotting functions.


## Basic Concepts

We use the terms *study* and *trial* as follows:

- Study: optimization based on an objective function
- Trial: a single execution of the objective function

Please refer to the sample code below. The goal of a *study* is to find out the optimal set of
hyperparameter values (e.g., `regressor` and `svr_c`) through multiple *trials* (e.g.,
`n_trials=100`). Optuna is a framework designed for automation and acceleration of
optimization *studies*.

<details open>
<summary>Sample code with scikit-learn</summary>

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](http://colab.research.google.com/github/optuna/optuna-examples/blob/main/quickstart.ipynb)

```python
import ...

# Define an objective function to be minimized.
def objective(trial):

    # Invoke suggest methods of a Trial object to generate hyperparameters.
    regressor_name = trial.suggest_categorical('regressor', ['SVR', 'RandomForest'])
    if regressor_name == 'SVR':
        svr_c = trial.suggest_float('svr_c', 1e-10, 1e10, log=True)
        regressor_obj = sklearn.svm.SVR(C=svr_c)
    else:
        rf_max_depth = trial.suggest_int('rf_max_depth', 2, 32)
        regressor_obj = sklearn.ensemble.RandomForestRegressor(max_depth=rf_max_depth)

    X, y = sklearn.datasets.fetch_california_housing(return_X_y=True)
    X_train, X_val, y_train, y_val = sklearn.model_selection.train_test_split(X, y, random_state=0)

    regressor_obj.fit(X_train, y_train)
    y_pred = regressor_obj.predict(X_val)

    error = sklearn.metrics.mean_squared_error(y_val, y_pred)

    return error  # An objective value linked with the Trial object.

study = optuna.create_study()  # Create a new study.
study.optimize(objective, n_trials=100)  # Invoke optimization of the objective function.
```
</details>

> [!NOTE]
> More examples can be found in [optuna/optuna-examples](https://github.com/optuna/optuna-examples).
>
> The examples cover diverse problem setups such as multi-objective optimization, constrained optimization, pruning, and distributed optimization.

## Installation

Optuna is available at [the Python Package Index](https://pypi.org/project/optuna/) and on [Anaconda Cloud](https://anaconda.org/conda-forge/optuna).

```bash
# PyPI
$ pip install optuna
```

```bash
# Anaconda Cloud
$ conda install -c conda-forge optuna
```

> [!IMPORTANT]
> Optuna supports Python 3.7 or newer.
>
> Also, we provide Optuna docker images on [DockerHub](https://hub.docker.com/r/optuna/optuna).

## Integrations

Optuna has integration features with various third-party libraries. Integrations can be found in [optuna/optuna-integration](https://github.com/optuna/optuna-integration) and the document is available [here](https://optuna-integration.readthedocs.io/en/stable/index.html).

<details>
<summary>Supported integration libraries</summary>

* [Catboost](https://github.com/optuna/optuna-examples/tree/main/catboost/catboost_pruning.py)
* [Dask](https://github.com/optuna/optuna-examples/tree/main/dask/dask_simple.py)
* [fastai](https://github.com/optuna/optuna-examples/tree/main/fastai/fastai_simple.py)
* [Keras](https://github.com/optuna/optuna-examples/tree/main/keras/keras_integration.py)
* [LightGBM](https://github.com/optuna/optuna-examples/tree/main/lightgbm/lightgbm_integration.py)
* [MLflow](https://github.com/optuna/optuna-examples/tree/main/mlflow/keras_mlflow.py)
* [PyTorch](https://github.com/optuna/optuna-examples/tree/main/pytorch/pytorch_simple.py)
* [PyTorch Ignite](https://github.com/optuna/optuna-examples/tree/main/pytorch/pytorch_ignite_simple.py)
* [PyTorch Lightning](https://github.com/optuna/optuna-examples/tree/main/pytorch/pytorch_lightning_simple.py)
* [TensorBoard](https://github.com/optuna/optuna-examples/tree/main/tensorboard/tensorboard_simple.py)
* [TensorFlow](https://github.com/optuna/optuna-examples/tree/main/tensorflow/tensorflow_estimator_integration.py)
* [tf.keras](https://github.com/optuna/optuna-examples/tree/main/tfkeras/tfkeras_integration.py)
* [Weights & Biases](https://github.com/optuna/optuna-examples/tree/main/wandb/wandb_integration.py)
* [XGBoost](https://github.com/optuna/optuna-examples/tree/main/xgboost/xgboost_integration.py)
</details>

## Web Dashboard

[Optuna Dashboard](https://github.com/optuna/optuna-dashboard) is a real-time web dashboard for Optuna.
You can check the optimization history, hyperparameter importance, etc. in graphs and tables.
You don't need to create a Python script to call [Optuna's visualization](https://optuna.readthedocs.io/en/stable/reference/visualization/index.html) functions.
Feature requests and bug reports are welcome!

![optuna-dashboard](https://user-images.githubusercontent.com/5564044/204975098-95c2cb8c-0fb5-4388-abc4-da32f56cb4e5.gif)

`optuna-dashboard` can be installed via pip:

```shell
$ pip install optuna-dashboard
```

> [!TIP]
> Please check out the convenience of Optuna Dashboard using the sample code below.

<details>
<summary>Sample code to launch Optuna Dashboard</summary>

Save the following code as `optimize_toy.py`.

```python
import optuna


def objective(trial):
    x1 = trial.suggest_float("x1", -100, 100)
    x2 = trial.suggest_float("x2", -100, 100)
    return x1 ** 2 + 0.01 * x2 ** 2


study = optuna.create_study(storage="sqlite:///db.sqlite3")  # Create a new study with database.
study.optimize(objective, n_trials=100)
```

Then try the commands below:

```shell
# Run the study specified above
$ python optimize_toy.py

# Launch the dashboard based on the storage `sqlite:///db.sqlite3`
$ optuna-dashboard sqlite:///db.sqlite3
...
Listening on http://localhost:8080/
Hit Ctrl-C to quit.
```

</details>


## OptunaHub

[OptunaHub](https://hub.optuna.org/) is a feature-sharing platform for Optuna.
You can use the registered features and publish your packages.

### Use registered features

`optunahub` can be installed via pip:

```shell
$ pip install optunahub
```

You can load registered module with `optunahub.load_module`.

```python
import optuna
import optunahub


def objective(trial: optuna.Trial) -> float:
    x = trial.suggest_float("x", 0, 1)

    return x


mod = optunahub.load_module("samplers/simulated_annealing")

study = optuna.create_study(sampler=mod.SimulatedAnnealingSampler())
study.optimize(objective, n_trials=20)

print(study.best_trial.value, study.best_trial.params)
```

For more details, please refer to [the optunahub documentation](https://optuna.github.io/optunahub/).

### Publish your packages

You can publish your package via [optunahub-registry](https://github.com/optuna/optunahub-registry).
See the [OptunaHub tutorial](https://optuna.github.io/optunahub-registry/index.html).


## Communication

- [GitHub Discussions] for questions.
- [GitHub Issues] for bug reports and feature requests.

[GitHub Discussions]: https://github.com/optuna/optuna/discussions
[GitHub issues]: https://github.com/optuna/optuna/issues


## Contribution

Any contributions to Optuna are more than welcome!

If you are new to Optuna, please check the [good first issues](https://github.com/optuna/optuna/labels/good%20first%20issue). They are relatively simple, well-defined, and often good starting points for you to get familiar with the contribution workflow and other developers.

If you already have contributed to Optuna, we recommend the other [contribution-welcome issues](https://github.com/optuna/optuna/labels/contribution-welcome).

For general guidelines on how to contribute to the project, take a look at [CONTRIBUTING.md](./CONTRIBUTING.md).


## Reference

If you use Optuna in one of your research projects, please cite [our KDD paper](https://doi.org/10.1145/3292500.3330701) "Optuna: A Next-generation Hyperparameter Optimization Framework":

<details open>
<summary>BibTeX</summary>

```bibtex
@inproceedings{akiba2019optuna,
  title={{O}ptuna: A Next-Generation Hyperparameter Optimization Framework},
  author={Akiba, Takuya and Sano, Shotaro and Yanase, Toshihiko and Ohta, Takeru and Koyama, Masanori},
  booktitle={The 25th ACM SIGKDD International Conference on Knowledge Discovery \& Data Mining},
  pages={2623--2631},
  year={2019}
}
```
</details>


## License

MIT License (see [LICENSE](./LICENSE)).

Optuna uses the codes from SciPy and fdlibm projects (see [LICENSE_THIRD_PARTY](./LICENSE_THIRD_PARTY)).
