# NERQual: Evaluating the Robustness of Named Entity Recognition Models to Data Quality Issues

This repository contains the code of our paper:  
**[NERQual: Evaluating the Robustness of Named Entity Recognition Models to Data Quality Issues](https://aclanthology.org/2026.wnut-1.14/)**, published at the *11th Workshop on Noisy and User-generated Text (W-NUT 2026)* (co-located with EMNLP 2026).

This project investigates how **data quality** influences the performance of different **Named Entity Recognition (NER)** models. It systematically introduces **noise** into datasets (train, validation, and test sets) at varying **noise rates** and **stages** to measure robustness and generalization of various NER architectures.

---

## 🗂️ Repository Structure

```bash
NERQual/
│
├── notebooks/                # Example notebooks
├── scripts/                  # Execution scripts
│   ├── run_profile.sh
│   └── run_profile_pegasus.sh
├── src/                      # Core source code
│   ├── noise/                # Noise generation modules
│   │   ├── utils/            # Helper utilities for noise generation
│   │   ├── label_noise.py    # Injects noise into labels/entities
│   │   ├── orthographic.py   # Orthographic (character-level) noise
│   │   ├── registry.py       # Registry for available noise types
│   │   ├── semantic.py       # Semantic-level noise (word meaning)
│   │   └── syntactic.py      # Syntactic noise (structure-based)
│   ├── profiles/             # Experiment configurations
│   ├── data_preprocessing.py # Data loading and preprocessing
│   ├── metrics.py            # Evaluation and scoring metrics
│   └── train.py              # Training loop and orchestration
├── requirements.txt          # Dependencies
├── sweep_config_*.yaml       # W&B sweep configurations
├── LICENSE                   # MIT License
└── README.md
```

---

## ⚙️ Installation

Clone the repository and install the dependencies:

```bash
git clone https://github.com/DFKI-NLP/NERQual.git
cd NERQual
pip install -r requirements.txt
```

---

## 🤖 Models & Datasets

### Datasets
- **Evaluated in the Paper / Default**: [CoNLL-2003](https://huggingface.co/datasets/eriktks/conll2003) (automatically loaded via Hugging Face `datasets`).
- **Custom Datasets**: You can pass any Hugging Face dataset name or path via the `--dataset` CLI argument (default: `conll2003`). Any dataset providing `tokens`, `ner_tags`, and `pos_tags` across standard splits (`train`, `validation`, `test`) is supported.

### Models
Our experiments cover a diverse set of transformer architectures:
- `bert-base-cased`
- `roberta-base`
- `microsoft/deberta-base`
- `distilbert-base-uncased`
- `xlnet-base-cased`
- `google/canine-c` *(Character-level token classification)*

Since all models are loaded via Hugging Face's `AutoModelForTokenClassification` and `AutoTokenizer`, any compatible pre-trained model from the [Hugging Face Hub](https://huggingface.co/models) can be passed directly via the `--model` argument.

---

## 🚀 Running Experiments

You can run experiments in multiple ways depending on your setup.

### 1️⃣ Manual Execution
```bash
python -m src.train \
    --model bert-base-cased \
    --dataset conll2003 \
    --profile src/profiles/<PROFILE> \
    --epochs 5 \
    --batch_size 16 \
    --lr 3e-5 \
    --max_length 256 \
    --seed 42
```

---

### 2️⃣ Using Provided Shell Scripts

**💻 Local Execution**
```bash
bash scripts/run_profile.sh bert-base-cased src/profiles/orthographic/orthographic_p0.1_test_all.yaml
```

**🦄 [Pegasus](https://pegasus.dfki.de/) Cluster**
```bash
bash scripts/run_profile_pegasus.sh bert-base-cased src/profiles/orthographic/orthographic_p0.1_test_all.yaml
```

---

### 3️⃣ Running W&B Sweeps
This project uses **grid search sweeps** with **Weights & Biases (W&B)** to automate structured experiments.  
Each agent executes **one training run after another**, iterating through all defined configurations in sequence.

Example sweep config files:
- `sweep_config_baseline.yaml`
- `sweep_config_test.yaml`
- `sweep_config_train_validation_test.yaml`
- `sweep_config_train_validation.yaml`

Run a sweep:

```bash
wandb sweep sweep_config_baseline.yaml
wandb agent <YOUR_SWEEP_ID>
```

Example for using on cluster (using [Pegasus Bridle Wrapper](https://github.com/DFKI-NLP/pegasus-bridle)):
```bash
bash /home/dtrautner/dev/pegasus-bridle/wrapper.sh wandb agent <YOUR_SWEEP_ID>
```

---

## 📖 Citation

```bibtex
@inproceedings{trautner-thomas-2026-nerqual,
    title = "{NERQ}ual: Evaluating the Robustness of Named Entity Recognition Models to Data Quality Issues",
    author = "Trautner, Denis  and
      Thomas, Philippe",
    booktitle = "Proceedings of the 11th Workshop on Noisy and User-generated Text",
    month = oct,
    year = "2026",
    address = "Budapest, Hungary",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2026.wnut-1.14/",
    pages = "195--209",
    ISBN = "979-8-89176-470-5"
}
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
