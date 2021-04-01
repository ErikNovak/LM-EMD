# LM-EMD
The repository containing the experiment files for the explainable
cross-lingual document ranking model using multilingual language model
and Regularized Earth Movers Distance.


## ✔️ Requirements
Before starting the project make sure these requirements are available:
- [conda][conda]. For setting up your research environment and python dependencies.
- [git][git]. For versioning your code.
- [dvc][dvc]. For versioning your data (part of conda environment).

## 💻 Setup

### Install the conda environment

First create the new conda environment:

```bash
conda env create -f environment.yml
```

### Activate the environment

To activate the newly set environment run:

```bash
conda activate lm-emd
```

### Install the CUDA version of PyTorch

Use conda to install the appropriate version of PyTorch. **NOTE:** Be
sure you install the right CUDA version.

```bash
conda install pytorch cudatoolkit=11.1 -c pytorch -c conda-forge
```

### Deactivate the environment

When the environment is not in use anymore deactivate it by running:

```bash
conda deactivate
```

## 💾 Data Preparation

First download the data. Go into the `/data` folder and run

```bash
sh ./download.sh
```

This will download the data used in the experiments.

## 🥼 Experiment Setup

To run the experiments one can manually change the `params.yaml` file with
different parameters. Then, simply run the following commands:

```bash
# trains the model with the provided parameters
python src/train.py
# evaluates the model
python src/evaluate.py
```

### Using DVC

We use DVC to automatically run experiments with different parameters. The `dvc`
is installed with `conda`. To run multiple experiments we execute the following
command:

```bash
# prepare the queue of experiments
dvc exp run --queue -S model.ranking=cls -S model.reg=None -S model.nit=None
dvc exp run --queue -S model.ranking=max -S model.reg=None -S model.nit=None
dvc exp run --queue -S model.ranking=mean -S model.reg=None -S model.nit=None
dvc exp run --queue -S model.ranking=emd -S model.reg=0.1
dvc exp run --queue -S model.ranking=emd -S model.reg=1
dvc exp run --queue -S model.ranking=emd -S model.reg=10

# execute all queued experiments (run 3 jobs in parallel)
dvc exp run --run-all --jobs 3
```

Afterwards, we can compare the performance of the models by running:

```bash
dvc exp show
```

## 📋 Experiment Results

The above experiments yield the following results.

| Model 	    | P@1 (EN → DE) | MAP (EN → DE) | P@1 (EN → FR) | MAP (EN → FR) | P@1 (EN → TL) | MAP (EN → TL) | P@1 (EN → JA) | MAP (EN → JA) | P@1 (EN → SW) | MAP (EN → SW) |
|-------------- |:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|
| BERT-CLS  	| .978  | .987  | .978  | .987  | .851  | .912  | .955  | .973  | .913  | .947  |
| BERT-MAX  	| .941  | .964  | .948  | .969  | .798  | .874  | .912  | .946  | .824  | .886  |
| BERT-MEAN  	| .967  | .980  | .958  | .976  | .786  | .874  | .941  | .965  | .835  | .897  |
| LM-EMD  	    | .977  | .986  | .974  | .985  | .801  | .874  | .955  | .973  | .890  | .932  |


When comparing the performance of the LM-EMD model using the cross-entropy and pairwise ranking loss at different
regularization factors, we got the following results.

| Params  | Loss             | P@1 (EN → DE) | MAP (EN → DE) | P@1 (EN → FR) | MAP (EN → FR) | P@1 (EN → TL) | MAP (EN → TL) | P@1 (EN → JA) | MAP (EN → JA) | P@1 (EN → SW) | MAP (EN → SW) |
|---------|----------------- |:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|
| γ = 0.1 | cross-entropy    | .865  | .921  | .830  | .902  | .699  | .822  | -     |  -    | -     | -     |
|         | pairwise ranking | .977  | .986  | .974  | .985  | .801  | .874  | .955  | .973  | .890  | .932  |
| γ = 1   | cross-entropy    | .862  | .919  | .823  | .898  | .701  | .824  | -     | -     | -     | -     |
|         | pairwise ranking | .970  | .982  | .968  | .981  | .809  | .883  | .910  | .946  | .859  | .913  |
| γ = 10  | cross-entropy    | .874  | .926  | .838  | .907  | .712  | .830  | -     | -     | -     | -     |
|         | pairwise ranking | .965  | .979  | .961  | .978  | .805  | .881  | .899  | .941  | .835  | .899  |






# 🏬 Acknowledgments
This work is developed by [Department of Artificial Intelligence][ailab] at [Jozef Stefan Institute][ijs].

The work is supported by the following EU Horizon 2020 projects:
- [Envirolens][elens] (grant no. 761758). The project demonstrates and promotes the use of Earth observation as direct evidence for environmental law enforcement,
including in a court of law and in related contractual negotiations.
- [X5GON][x5gon] (grant no. 821918). The projects goal is to connect different Open Educational Resources (OER) providers around the globe and to provide meaningful
 AI tools to support the learning process.









[git]: https://git-scm.com/
[dvc]: https://dvc.org/
[conda]: https://docs.conda.io/en/latest/

[ailab]: http://ailab.ijs.si/
[ijs]: https://www.ijs.si/
[elens]: https://envirolens.eu/
[x5gon]: https://www.x5gon.org/
