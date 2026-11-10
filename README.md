# CS781 Capstone Project

I built a legal language model from scratch, then adapted the Phase 1 models for six downstream legal NLP tasks. This repository contains the model notebooks I ran or submitted for each phase, the shared tokenizer file, and the official leaderboard scores. I have not included any submission ZIPs, result ZIPs, model weights, or checkpoints.

## Repository layout

```text
phase1/   Four pretraining runs and the shared BPE tokenizer
phase2/   The one NER model I submitted
phase3/   The three rhetorical-role models I submitted
phase4/   Four judgment-prediction model families
phase5/   Four statute-identification model families
phase6/   Four summarization model families
phase7/   Four prior-case retrieval model families
```

The Phase 1 Model 3 notebook's original filename mentions encoder-decoder, but its saved run output shows that it selected `ours_glru` for the final training run. I named the copy `model3_glru_run.ipynb` here to match what it actually ran. Model 3 still includes the encoder-decoder ablation code.

I made 5 Phase 1 uploads (4 distinct model variants, with Model 2 uploaded twice by mistake), 1 Phase 2 upload, 3 Phase 3 uploads, and 24, 20, 20, and 20 uploads in Phases 4–7. The Phase 4 screenshot shows one submission still marked Submitted, and one Phase 6 finished row has no score.

## Phase 1: Legal language model pretraining

I trained the models from scratch on the supplied legal corpus with a custom byte-level BPE tokenizer. The notebooks compare attention, gated recurrence, hybrid, encoder-decoder, and masked-diffusion designs. They include training, validation, decoding, and the local generation-metric code.

| Model | Final selected run | Official score | Rank |
|---|---|---:|---:|
| Model 1 | `ours_hybrid_gate` | 0.7909 |  |
| Model 2 | `ours_glru` | 0.8239 |  |
| Model 3 | `ours_glru` | 0.8265 | 110 |
| Model 4 | `ours_mdlm_adaln_hybrid` | 0.8121 |  |

I uploaded Model 2 twice by mistake; both uploads had the same score. The instructor-provided starter scored 0.8010 and is not included as one of my models.

## Phase 2: Legal named-entity recognition

I submitted Model 1, an attention-decoder tagger. It predicts BIO labels in overlapping windows and maps predictions back to character offsets for strict span evaluation. The notebook includes full fine-tuning and a half-depth knowledge-distillation student.

| Model | Official strict macro-F1 | Rank | Submissions |
|---|---:|---:|---:|
| Model 1 | 0.4348 | 97 | 1 |

## Phase 3: Rhetorical-role prediction

This phase predicts one of 13 roles for each sentence. I trained jointly on the Corporate Law and Information Technology domains and evaluated them separately. The model notebooks compare context windows and sentence-only inputs, and include LoRMA+, LoRA, and knowledge distillation experiments where implemented.

| Model | Backbone | Official macro-F1 | Rank |
|---|---|---:|---:|
| Model 1 | Attention decoder | 0.4792 | 79 |
| Model 2 | G-LRU | 0.4757 |  |
| Model 3 | Encoder-decoder | 0.3744 |  |

The Model 2 notebook includes its saved execution outputs. The Model 3 notebook is included because I submitted it, but its saved local copy does not contain cell outputs.

## Phase 4: Court judgment prediction

I treated judgment prediction as binary classification. Each notebook uses a Phase 1 backbone, chunks long judgments, and trains a hierarchical classifier. The runs compare LoRMA, knowledge distillation (KD), full fine-tuning, BitFit, adapters, and AdaLoRA.

Official macro-F1 by model and adaptation:

| Model | LoRMA | KD | Full | BitFit | Adapter | AdaLoRA |
|---|---:|---:|---:|---:|---:|---:|
| 4 | 0.7638 | 0.7601 | 0.6406 | 0.6082 | 0.7464 | 0.6082 |
| 3 | **0.7798** | 0.7636 | 0.7517 | 0.6923 | 0.7604 | 0.5741 |
| 2 | 0.7661 | 0.7640 | 0.7422 | 0.6063 | 0.7614 | 0.5931 |
| 1 | 0.7698 | 0.7534 | 0.7692 | 0.6274 | N/A | 0.5777 |

Model 3 with LoRMA ranked 25th. The Model 1 adapter submission was still marked Submitted in the leaderboard screenshot, with no score shown.

## Phase 5: Legal statute identification

This is a multi-label classification task. The notebooks use chunked document representations, binary cross-entropy with positive-class weights, and validation-selected thresholds. I compared LoRMA, KD, full fine-tuning, adapters, and AdaLoRA.

Official macro-F1:

| Model | LoRMA | KD | Full | Adapter | AdaLoRA |
|---|---:|---:|---:|---:|---:|
| 4 | 0.3670 | 0.3646 | 0.3575 | 0.3538 | 0.3273 |
| 3 | 0.3519 | 0.3442 | 0.3467 | 0.3454 | 0.3154 |
| 2 | 0.3711 | 0.3692 | 0.3683 | 0.3577 | 0.3356 |
| 1 | **0.3744** | 0.3714 | 0.3629 | 0.3584 | 0.3359 |

Model 1 with LoRMA ranked 66th.

## Phase 6: Abstractive legal summarization

The notebooks condition on a judgment and generate a summary. They use compressed views of long documents, a prefix-language-model format, and compare LoRMA, KD, full fine-tuning, adapters, and AdaLoRA. The Model 2 KD entry was my best official result.

Official BERTScore F1:

| Model | LoRMA | KD | Full | Adapter | AdaLoRA |
|---|---:|---:|---:|---:|---:|
| 4 | 0.7506 | 0.7847 | 0.7578 | 0.7501 | 0.7458 |
| 3 | 0.8359 | 0.8343 | 0.8320 | 0.8330 | 0.8311 |
| 2 | 0.8422 | **0.8445** | 0.8365 | 0.8430 | 0.8436 |
| 1 | 0.8424 | N/A | 0.8350 | 0.8425 | 0.8398 |

Model 2 with KD ranked 48th. One Model 1 KD row was finished but had no score shown.

## Phase 7: Prior case retrieval

I trained a shared-weight bi-encoder with chunked case representations and contrastive learning. The experiments use in-batch examples and TF-IDF hard negatives, then return the top seven candidate cases. The notebooks compare partial and full fine-tuning, LoRMA, IA$^3$, and KD.

Official micro-F1@7:

| Model | Partial | LoRMA | KD | IA$^3$ | Full |
|---|---:|---:|---:|---:|---:|
| 4 | 0.3839 | 0.3946 | 0.3975 | 0.3739 | 0.3981 |
| 3 | 0.3721 | 0.3904 | 0.3898 | 0.3727 | **0.3999** |
| 2 | 0.3940 | 0.3916 | 0.3916 | 0.3733 | 0.3887 |
| 1 | 0.3952 | 0.3916 | 0.3928 | 0.3739 | 0.3898 |

Model 3 with full fine-tuning ranked 61st.

## Running the notebooks

I ran the notebooks in Kaggle-style environments. Phase 1 training uses JAX and a TPU; Phases 2–7 use PyTorch and a GPU. The notebooks contain the model configuration, data preprocessing, training or inference code, and evaluation/submission-generation steps. Install any missing packages listed or installed by the notebook cells, including NumPy, `regex`, JAX, PyTorch, and Hugging Face Hub as needed.

The notebooks download the official phase data from the `Exploration-Lab/CS-781-Capstone` Hugging Face dataset. If access requires authentication, set a Kaggle Secret named `HF`. The Phase 1 Model 2 copy in this repository reads that secret; it does not contain a hard-coded access token.

Phases 2–7 initialize from Phase 1 model weights and use `phase1/bpe_legal.json`. Since weights and checkpoints are not included, reproduce a downstream run by first running the matching Phase 1 training notebook and generating its `final_params.pkl`. Then attach that checkpoint and the tokenizer to the Kaggle notebook input. When more than one checkpoint is mounted, set the notebook's `CK_DIR` or `PIN` to the intended checkpoint rather than relying on automatic selection. Phase 3 Model 3 expects an encoder-decoder Phase 1 checkpoint, while the recorded final checkpoint from the Phase 1 Model 3 run is G-LRU; reproduce that Phase 3 entry by generating/saving the encoder-decoder ablation checkpoint. The recurrent variants use the corresponding G-LRU checkpoint.

The notebooks can create submission archives when run, but I have not placed any submission or results ZIPs in this repository. The repository contains source notebooks and the shared tokenizer only; it does not contain trained weights, checkpoints, or downloaded datasets.
