TakeMeter: Wizard101 Post Classification

Project Overview

This project builds a text classification system for Wizard101 community posts. The goal is to classify each post into one of three categories:

- guide_analysis → gameplay guides, strategies, explanations
- help_question → questions asking for help or advice
- opinion_reaction → opinions, complaints, praise, or reactions

The system compares:

1. A zero-shot baseline (Groq LLaMA 3.3 70B)
2. A fine-tuned DistilBERT model

The dataset contains 200 labeled posts, and performance is evaluated using accuracy, precision, recall, F1-score, and a confusion matrix.

Dataset
- Total examples: 200
- Train/Validation/Test split: 140 / 30 / 30
- Labels:
    guide_analysis (71)
    help_question (66)
    opinion_reaction (63)

Label Distribution (Train/Test)
Train (140):

- guide_analysis: 50
- help_question: 46
- opinion_reaction: 44

Test (30):

- guide_analysis: 11
- help_question: 10
- opinion_reaction: 9


Label Taxonomy
guide_analysis

Posts that explain systems, strategies, leveling, gear optimization, farming routes, or combat mechanics. 

ex - “best farming seed route for beastmoon gear”


help_question

Posts asking direct gameplay questions or requesting help.

ex - “what gear should I use at level 60”

opinion_reaction

Posts expressing opinions, complaints, praise, or reactions.

Ex - “pets are too RNG based”


Models
1. Baseline Model (Groq Zero-Shot)
- Model: llama-3.3-70b-versatile
- Prompt-based classification
- No training required
- Uses strict label prompting

2. Fine-Tuned Model
- Base model: distilbert-base-uncased
- Fine-tuned using HuggingFace Transformers
- Training platform: Google Colab (T4 GPU)
- Epochs: 3
- Learning rate: 2e-5
- Batch size: 16

Training Details
- Loss function: Cross-entropy
- Optimizer: AdamW (default HuggingFace Trainer)
- Evaluation: accuracy per epoch
- Best model selected automatically using validation accuracy

Final validation accuracy reached: 0.80

Results Comparisons
| Model                     | Accuracy |
| ------------------------- | -------- |
| Zero-shot baseline (Groq) | 0.933    |
| Fine-tuned DistilBERT     | 0.933    |


Improvement:

No improvement (0.000), meaning:

- The baseline was already very strong
- Dataset is highly separable
- Task is well-defined and low ambiguity

Fine-Tuned Model Evaluation
Overall Performance -> Accuracy: 0.933

Per-Class Metrics
| Class            | Precision | Recall | F1-score |
| ---------------- | --------- | ------ | -------- |
| guide_analysis   | 0.85      | 1.00   | 0.92     |
| help_question    | 1.00      | 0.90   | 0.95     |
| opinion_reaction | 1.00      | 0.89   | 0.94     |

Confusion Matrix (Test Set)
Rows = true labels, Columns = predicted labels
| True \ Pred      | guide_analysis | help_question | opinion_reaction |
| ---------------- | -------------- | ------------- | ---------------- |
| guide_analysis   | 11             | 0             | 0                |
| help_question    | 1              | 9             | 0                |
| opinion_reaction | 1              | 0             | 8                |

Key Insight:

Most confusion occurs between:
- help_question → guide_analysis
- opinion_reaction → guide_analysis

This shows the model sometimes treats informative statements as guides even when they are questions or opinions.




Error Analysis (Fine-Tuned Model)
Mistake 1

Text: “what is team-up system used for”

- True: help_question
- Predicted: guide_analysis

Why it failed:
The model treated the structured phrase “system used for” as a guide/explanation rather than a question.


Mistake 2

Text: “boss cheats are unfair design”

- True: opinion_reaction
- Predicted: guide_analysis

Why it failed:
The statement sounds descriptive and system-focused, causing confusion with informational guide-style text.

Key Failure Pattern

The model struggles with:
Distinguishing informational statements vs. opinions/questions when phrasing is declarative

This is especially true when:

- questions are short and lack question marks
- opinions are written like factual statements
- “system/game mechanic” language appears in multiple classes

Sample Predictions
| Post                        | Prediction       | Confidence | Correct? |
| --------------------------- | ---------------- | ---------- | -------- |
| best farming route for gold | guide_analysis   | 0.91       | ✅        |
| what gear should I use      | help_question    | 0.88       | ✅        |
| pets are RNG based          | opinion_reaction | 0.86       | ✅        |

Correct predictions show the model strongly associates:

- “best / route / guide” → guide_analysis
- “what / how / should I” → help_question
- “too / unfair / overrated” → opinion_reaction

Reflection: Model vs Intent

The model performs well but reveals a key insight:

- It relies heavily on surface-level linguistic cues
- It does not deeply understand intent differences in borderline cases
- It overgeneralizes “system-related language” into guide_analysis

What it captured well:
- Clear guide vs opinion separation
- Strong detection of help questions with explicit structure

What it missed:
- Subtle intent differences in short declarative sentences
- Ambiguous posts without explicit question markers


Spec Reflection
How the spec helped:

The structured pipeline (baseline → fine-tune → evaluate) ensured:

- consistent evaluation on identical test sets
- fair comparison between models
- clear separation of training vs evaluation logic

Where implementation diverged:
I initially underestimated dataset size requirements and had to:
- adjust dataset to reach 200 examples
- refine label definitions for consistency
This improved model stability and reduced ambiguity.