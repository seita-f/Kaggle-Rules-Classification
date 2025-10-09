# Reddit Comment Rules Classification
This is the log for the Kaggle competiton [Jigsaw - Agile Community Rules Classification](https://www.kaggle.com/competitions/jigsaw-agile-community-rules) <br>
The task is to create a model that predicts whether Reddit comments violate specific rules. The labels for the training data are binary (violated or not violated), but during test inference, the model must output a numerical probability indicating the likelihood of violation.
Furthermore, the test data contains a large amount of data that includes rules not present in the training data.


## Dataset
| Name | Details |
| --- | --- | 
| row_id | index |
| body | the text of the comment |
| rule | the rule the comment is judged to be in violation of |
| subreddit	| the forum the comment was made in |
| positive_example_1 | examples of comments that violate the rule |
| positive_example_2 | examples of comments that violate the rule |
| negative_example_1 | examples of comments that do not violate the rule |
| negative_example_2 | examples of comments that do not violate the rule |
| rule_violation | the binary target (0=Negative, 1=Positive)

<!-- ## Result

| notebook| score | details |
| --- | --- | --- |
| 01 | doing | --- |  -->

<!-- ## Paper
| No. | Status | Name | Detail | Date | Url |
| --- | --- | --- | --- | --- | --- | 
| 01 | doing | --- | --- | --- | --- | -->

## Links
| Source | Link | Details | 
| --- | --- | --- | 
| kaggle topic | [The rules are revealed](https://www.kaggle.com/competitions/jigsaw-agile-community-rules/discussion/607941) | The list of hiden rules (in test data) |
| kaggle topic | [Grey Area in the Rules](https://www.kaggle.com/competitions/jigsaw-agile-community-rules/discussion/598099#3267150) | Using test data for training | 
| hugging face | [text-generation models](https://huggingface.co/models?pipeline_tag=text-generation&sort=downloads) | Reasonable models that can be used in this competition |
| paper | [Chain-of-Thought (CoT) fine-tuning](https://arxiv.org/pdf/2508.11281) | To-Do |

## Note
(might use MLFlow later)

dd-mm-yyyy
#### 05-10-2025 (Joined!)
- Build a base model with <b>Bert</b> and just passed body data
- <b>f1</b> metric for evaluating
- No internet is allowed, so pre-trained model should be added to the dataset on kaggle
- First submission --> Score: 0.584 (The public best score 0.931 seems the ceiling)

#### 06-10-2025
- Tuned RoBERTa model (an upgraded version of BERT) using body, but the score slightly dropped
- Top players seem to use part of the test data for training and it boosts score significantly even without large LLMs. <br>
In test.csv there are columns positive_example_1/2 and negative_example_1/2.
For each rule in the test set, these contain labeled example comments that either violate or do not violate that rule.
- Score: 0.761 (used 20% test data)

#### 08-10-2025
- Not only test data, but using positive_example_1/2 and negative_example_1/2 in train data as well. (Score: 0.767)  
- Hiden rules exisiting in the test data are revealed by experts.
<img width="700" height="400" alt="Screen Shot 2025-10-08 at 20 56 48" src="https://github.com/user-attachments/assets/737170d2-f344-46fc-bf3c-7a7aac0ae1b0" />
  
#### 09-10-2025
- Added rule and subreddit data (finally)
```
X = "Rule: " + df_train["rule"] +
    " Subreddit: " + df_train["subreddit"] + \
    " Comment: " + df_train['body']
```

- text mining
- synthetic data for tuning?
- switch to prompt engineering? 



