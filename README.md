# contstimlang
Code for generating controversial sentence pairs and supporting material for **["Testing the limits of natural language models for predicting human language judgments"](http://arxiv.org/abs/2204.03592)**.

Tested under Python 3.7.6, PyTorch 1.3.1, and 2.9.0 (but might work also with later versions).

## How to install (Anaconda, recommended)

```git clone https://github.com/dpmlab/contstimlang.git```

```cd contstimlang```

```conda env create -f environment.yml```

```conda activate contstimlang```

```python download_checkpoints.py```
(This will download the checkpoints for the following models from Zenodo: BIGRAM, TRIGRAM, RNN, LSTM, BILSTM. The transformer models will be automatically downloaded when the sentence generation code is first run.)

if you don't use Anaconda, you can use ```pip install requirements.txt``` within your virtual environment, but you will have to deal with installing a PyTorch build that matches your installed cudatoolkit version.

## How to generate a single controversial synthetic sentence pair
Use the file `synthesize_one_controversial_sentence_pair` to generate controversial sentence pairs. For a quick example, run

```python synthesize_one_controversial_sentence_pair.py --model_accept bigram --model_reject trigram --initial_sentence "Life’s preference for symmetry is like a new law of nature"```

This generates a synthetic sentence whose probability according to the 3-gram is lower than the probability of the natural sentence, but is as likely according to the 2-gram model.

To invert model roles, run:

```python synthesize_one_controversial_sentence_pair.py --model_accept trigram --model_reject bigram --initial_sentence "Life’s preference for symmetry is like a new law of nature"```

Next, we can compare the trigram with GPT-2 (this requires a GPU)

```python synthesize_one_controversial_sentence_pair.py --model_accept trigram --model_reject gpt2 --initial_sentence "Life’s preference for symmetry is like a new law of nature"```

To compare BERT and GPT-2, run 

```python synthesize_one_controversial_sentence_pair.py --model_accept bert --model_reject gpt2 --initial_sentence "Life’s preference for symmetry is like a new law of nature"```
and grab a cup of coffee. Running this code might require two GPUs.

Type `python synthesize_one_controversial_sentence_pair.py -help` for more info. Note that the bi-directional models are slow to run due to the need to average sentence probabilities across conditional probability chains

## How to generate an entire set of synthetic controversial sentence pairs
Run `python batch_synthesize_controversial_pairs.py`. This script is designed to be run in parallel by multiple HPC nodes/workers. It communicating between concurrent processes through an sqlite database.

To generate a set of sentences as big as we used in the preprint, you would need an HPC environment since the generation of each sentence pair can take a few minutes (depending on the models). Each compute node should have two GPUs.

Once you have generated a set of synthetic sentences, you can select an optimal subset for human testing using
`python select_synthetic_controversial_sentences_for_behav_exp.py`. This code requires the installation of CPLEX (`conda install -c ibmdecisionoptimization cplex=1.2`).

## How to generate an entire set of natural controversial sentence pairs
First, install [GUROBI](https://www.gurobi.com/). The free academic license is sufficient.

Then, run `python select_natural_controversial_pairs.py`.

The code takes about an hour on a modern workstation and may require high RAM (tested on a 128GB machine).

## How to reproduce the paper's figures from raw behavioral data
Run `python behav_exp_analysis.py`.

## Currently included models
GPT2, BERT, ROBERTA, ELECTRA, XLM, LSTM, RNN, TRIGRAM, BIGRAM

models implemented but not currently used: BILSTM, BERT_WHOLE_WORD

## Cite:
```bibtex
@misc{golan2022testing,
      title={Testing the limits of natural language models for predicting human language judgments}, 
      author={Tal Golan and Matthew Siegelman and Nikolaus Kriegeskorte and Christopher Baldassano},
      year={2022},
      eprint={2204.03592},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
