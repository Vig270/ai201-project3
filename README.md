Just mental note - skip to overview dont worry
The zero-shot Groq baseline significantly outperformed the fine-tuned DistilBERT model, achieving 0.929 accuracy compared to 0.679. This suggests that for small datasets with semantically overlapping labels, large language models retain a strong advantage due to broader pretraining. The fine-tuned model struggled particularly with distinguishing opinion_reaction from help_question, indicating that subjective statements and implicit advice-seeking language are difficult to separate with limited training data.



Overview

This project builds a 3-class text classification system for Wizard101 community content using:

- A fine-tuned DistilBERT model
- A zero-shot LLM baseline (Groq LLaMA 3.3 70B)

The goal is to classify posts into:

- guide_analysis
- help_question
- opinion_reaction

We compare a trained transformer model against a large language model baseline and analyze where each succeeds or fails.

Dataset
- Total examples: 182
- Labels:
    guide_analysis (65)
    help_question (60)
    opinion_reaction (57)
Data Source

Manually collected Wizard101 community-style posts including:

gameplay guides
player help questions
opinions and reactions
Labeling Process

Each post was manually labeled based on intent:

instructional content → guide_analysis
questions → help_question
opinions/complaints/reactions → opinion_reaction

No label exceeds 70% of the dataset, ensuring balance.


Label Definitions
guide_analysis

Posts that explain systems, strategies, leveling paths, gear optimization, or tutorials.

help_question

Posts that ask for help or clarification about gameplay, bosses, gear, pets, or progression.

opinion_reaction

Posts expressing opinions, complaints, praise, or reactions about the game or community.


Model Training
Fine-Tuned Model
Base model: distilbert-base-uncased
Platform: Google Colab (T4 GPU)
Epochs: 3
Learning rate: 2e-5
Batch size: 16
Why these settings?
3 epochs prevents overfitting on a small dataset (~182 samples)
2e-5 is standard for BERT fine-tuning stability
Batch size 16 fits GPU memory constraints



| Model                          | Accuracy  |
| ------------------------------ | --------- |
| Zero-shot Groq (LLaMA 3.3 70B) | **0.929** |
| Fine-tuned DistilBERT          | **0.679** |


Key Insight

The LLM baseline outperforms the fine-tuned model, showing that:

small datasets limit transformer learning
LLMs generalize better on short text classification
fine-tuning struggles with subjective labels like opinions


Confusion Matrix (Fine-tuned Model)
Actual (rows) vs Predicted (columns)

|                  | guide_analysis | help_question | opinion_reaction |
| ---------------- | -------------- | ------------- | ---------------- |
| guide_analysis   | 9              | 0             | 1                |
| help_question    | 0              | 9             | 0                |
| opinion_reaction | 2              | 6             | 1                |


Key Pattern

The model heavily confuses:

opinion_reaction → help_question

This suggests:

opinion posts are often phrased like questions or explanations
the model relies on surface structure (“question-like text”) instead of intent


❌ Error Analysis (3 Examples)
Example 1
Text: “AOE meta makes fights boring”
True: opinion_reaction
Predicted: guide_analysis (0.34)

Why it failed:
Model misinterprets “meta” and system-like language as instructional content.

Example 2
Text: “pets are too RNG dependent”
True: opinion_reaction
Predicted: help_question (0.35)

Why it failed:
Short complaint phrased as a statement resembles a question context in training data.

Example 3
Text: “KI should rebalance PvP completely”
True: opinion_reaction
Predicted: help_question (0.35)

Why it failed:
Directive tone (“should”) confused model into interpreting as request/help.

Sample Model Predictions
| Text                            | True             | Predicted     | Confidence |
| ------------------------------- | ---------------- | ------------- | ---------- |
| “best farming team composition” | guide_analysis   | help_question | 0.38       |
| “game is too grind heavy”       | opinion_reaction | help_question | 0.37       |
| “fastest way to level up”       | help_question    | help_question | 0.91       |


Correct Prediction Example
Text: “fastest way to level up”
Prediction: help_question

Why correct:
Clear question structure and explicit request for guidance.


Key Findings
1. What the model learned well
Strong separation of help vs guide
Performs well on structured instructional text
2. What it struggled with
Opinion vs help boundary
Short emotional statements
Ambiguous phrasing without question marks
3. Root cause
Dataset is small (~182 samples)
Opinion class is linguistically diverse
Many posts lack strong structural cues


Baseline vs Fine-tuned Insight
LLM baseline performs better due to:
world knowledge
strong zero-shot reasoning
Fine-tuned model:
learns dataset-specific patterns
struggles with generalization


Spec Reflection
What the spec helped with:
Clear label definitions forced consistent annotation
Required separation of intent types improved dataset structure
What diverged in practice:
Real posts are more ambiguous than label definitions suggest
Many “opinion” posts overlap with “help-like” phrasing


AI Usage
1. Prompt Engineering (Groq baseline)
AI was used to design a strict classification prompt
Output format was constrained to label-only responses
I refined it to improve parsing accuracy and reduce invalid outputs
2. Error Analysis Support
AI was used to group misclassified examples
Helped identify confusion between:
opinion_reaction ↔ help_question
Final interpretation and reasoning were manually verified