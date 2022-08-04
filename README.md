## Data Exploration

- Data is heavily skewed towards A class.
    - May need to balance with sampling/loss function depending on performance.
- Data has numerous classes with only 1 example.
    - Unclear whether there is a relation between groups with similar names (i.e. A, AA, A***8, AC, AV)
    - Will default to including them in the training set.
- Max sequence lengths are short enough for a standard transformer input at a 1:1 Amino Acid:Token ratio.


## Model

### Hypothesis & Model Selection

I selected the pretrained RITA model from HuggingFace [1]. It is a protein generative model,
trained autoregressively to predict amino acid structure. The architecture is a typical
encoder-decoder transformer. I hypothesis that this model will transfer well to this problem due to the domain similarity, and will show better performance than transfer learning from a standard transformer.

I will use the small version of RITA as it has 1/3 parameters of the current most popular HuggingFace protein model and is also smaller than the standard BERT model. This makes it an ideal choice given the time constraints for this submission.

### Model Training

Trained for 2 epochs w/ batch size = 8, learning_rate=2e-5, weight_decay=0.01


## Discussion

### Results

The model performs very well on the evaluation set with an overall accuracy of 98.8%.

The three errors:
Group_ID	Predicted
B_098	        C
C_157	        B
B_080	        C

Classification metrics per class:

Group: C, Accuracy: 0.988, Recall: 0.979, Precision: 0.959, Specificity: 0.990, FPR: 0.010
Group: A, Accuracy: 1.000, Recall: 1.000, Precision: 1.000, Specificity: 1.000, FPR: 0.000
Group: B, Accuracy: 0.988, Recall: 0.920, Precision: 0.958, Specificity: 0.995, FPR: 0.005


### Predictions

For the unknown proteins the following predictions were made:

ID
01 | Prediction: C
02 | Prediction: A
03 | Prediction: A
04 | Prediction: C
05 | Prediction: B
06 | Prediction: B
07 | Prediction: B
08 | Prediction: B
09 | Prediction: C
10 | Prediction: A

Softmaxed probabilities available in results/rita_predictions.csv

### Limitations/Next Steps

#### Class imbalance

Unaccounted for in my approach. Didn't seem to have too much of a determinental effect on learning for the main 3 classes, but correcting for it could further improve performance.

Single class examples - I did not attempt to create an accurator predictor for the rarer classes. I could have tried one-shot or zero-shot learning approaches. Although without at least a few examples to evaluate performance, it would be hard to determine how effective they were.

#### Other

- Subword (sub-amino acid) tokenization as in [2] has been shown to improve performance in some protein group classifiers.
- Pretraining on sequence completion.
- Training on reversed sequences.
- Ensembling.


## References

[1] RITA: a Study on Scaling Up Generative Protein Sequence Models. Hesslow et al., 2022.
    https://arxiv.org/abs/2205.05789 

[2] SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing. Taku Kudo, John Richardson, 2018.
    https://arxiv.org/abs/1808.06226
