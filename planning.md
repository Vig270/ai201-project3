1. Community

I selected the r/Wizard101 community, a subreddit centered around the online game Wizard101. This community is a strong fit for a text classification task because it contains a wide variety of post types, including structured gameplay guides, advice-seeking questions, and opinion-based discussions.

The discourse is naturally varied: some users write detailed optimization guides and ranked lists, while others ask for help with specific in-game problems or share emotional reactions and “hot takes” about game balance or monetization. This diversity makes it suitable for a classification task because different linguistic patterns clearly correspond to different types of intent and structure.

2. Labels

I defined three labels for classification:

1. guide_analysis

Posts that provide structured explanations, guides, rankings, or systematic breakdowns of game mechanics.
These posts are informative and usually contain step-by-step reasoning or comparisons.

Examples:

“Updated gear guides per level”
“Comprehensive guide to pets and optimization”

2. help_question

Posts where a user is directly asking for help, advice, or clarification about gameplay.
These are typically problem-oriented and include personal situations or requests.

Examples:

“What gear should I use at level 80 life?”
“Help me beat this boss / returning player advice”
3. opinion_reaction

Posts that express subjective opinions, hot takes, complaints, or community discussions without structured reasoning.
These posts are emotional or conversational rather than instructional.

Examples:

“Unpopular opinion: the monetization system is outdated”
“What is your favorite spell in the game?”

3. Hard Edge Cases

Some posts are ambiguous between opinion_reaction and guide_analysis.

Example:
A post like:

“Ranking schools by soloing ability”

This could be guide_analysis because it is structured and comparative, but it can also be opinion-based depending on whether the ranking is justified with evidence or personal preference.

Decision rule:
If the post uses structured reasoning, comparisons, or clear criteria → label as guide_analysis
If it is mostly subjective preference or lacks clear justification → label as opinion_reaction

For borderline cases, I prioritize whether the content could stand as an informational guide without the author’s opinion framing.

4. Data Collection Plan

I collected data manually from the r/Wizard101 subreddit using direct post sampling and keyword-based search (e.g., “guide,” “help,” “unpopular opinion,” “tier list”).

I aim for approximately:

10 posts per label initially (minimum viable dataset)
Expanded to 15–20 per label for final model training (≈45–60 total posts)

If a label becomes underrepresented after 200 total samples (future scaling):

I will perform targeted keyword searches to balance the dataset
Example: if help_question is underrepresented, I will search “help”, “boss”, “how do I”, “returning player”

5. Evaluation Metrics

Accuracy alone is not sufficient because class imbalance and overlapping language patterns are expected.

I will use:

Precision, Recall, and F1-score (macro-averaged)

Reason:

Precision ensures we do not incorrectly label opinion posts as guides
Recall ensures we capture most relevant posts per category
Macro F1 ensures performance is balanced across all three classes regardless of class frequency

I will also use a confusion matrix to analyze which classes are most frequently misclassified (especially guide_analysis vs opinion_reaction).

6. Definition of Success

A successful model should achieve:

≥ 80% accuracy overall
Macro F1-score ≥ 0.75

More importantly, the model should:

Clearly distinguish structured guides from opinion-based posts
Correctly identify help-seeking posts with minimal confusion
Be reliable enough to support basic community moderation or content tagging

A model below these thresholds would not be considered reliable enough for practical use, as misclassification between advice and opinion content would reduce trust in predictions.

AI TOOL PLAN
1. Label Stress-Testing

I will use an LLM to generate 5–10 borderline posts between:

guide_analysis vs opinion_reaction
help_question vs opinion_reaction

I will test whether my labeling rules can classify these clearly. If not, I will refine definitions before annotation.

2. Annotation Assistance

I may use an LLM to pre-label a subset of collected posts to speed up annotation. However, I will manually verify all labels to ensure consistency and correct any incorrect predictions.

I will track pre-labeled examples separately to ensure transparency in AI usage.

3. Failure Analysis

After training the model, I will provide incorrectly classified examples to an LLM to identify patterns in misclassification.

I will verify these patterns manually by checking whether errors come from:

ambiguous wording
overlapping label definitions
insufficient training data

This will help refine both the dataset and labeling rules in future iterations.