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
| rule_violation | the binary target (0=not violated, 1=violated)

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
| kaggle notebook | [Fine-tuning LLMs using Unsloth](https://www.kaggle.com/code/kingabzpro/fine-tuning-llms-using-unsloth) | Train faster using Unsloth |
| hugging face | [text-generation models](https://huggingface.co/models?pipeline_tag=text-generation&sort=downloads) | Reasonable models that can be used in this competition |
| paper | [Chain-of-Thought (CoT) fine-tuning](https://arxiv.org/pdf/2508.11281) | To-Do |
| paper | [URL に基づく機械学習を用いたフィッシングサイト判別の精度向上](https://www.jc.u-aizu.ac.jp/news/management/gr/2024/03.pdf) |Features of phising URLs. Although it may not apply much to this case, the <b>urllib.parse</b> module introduced in the paper can be used |

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
- Added rule and subreddit data (finally) --> Score: 0.864
```
X = "Rule: " + df_train["rule"] +
    " Subreddit: " + df_train["subreddit"] + \
    " Comment: " + df_train['body']
```
- Increased the data taken from the test set from 20% to 40%. However the result was almost the same. --> 20% of data is enough for trainig.
- There is a chance that comments breaking the specific rules tend to have more capital letters? --> No huge difference <br>
- Another hypothisis is that comments breaking the specifc rules tend to have more urls? --> No huge diffenrence 
However, after training the dataset with URLs removed, the results got worse, indicating that <b>URLs are important features</b>. <br>
<p align="center">
  <img width="45%" alt="Screen Shot 2025-10-12 at 14 16 27" src="https://github.com/user-attachments/assets/93717880-5def-4232-b3bc-0e0378a2b7d8" /> 
  <img width="45%" alt="Screen Shot 2025-10-12 at 14 18 25" src="https://github.com/user-attachments/assets/e6fe0d08-7761-4e4b-a481-a9343a49cc3d" /> 
</p>

#### 11-10-2025
- Tried prompt engineering for urls such that 
```
prompt = (
        f"Please list 3 to 5 keywords that are likely related to the content of this URL: {url}. "
        f"Answer the keywords only, separated by commas."
    )

url = "https://onlyfans.com/user123"
print(build_prompt(url))

['onlyfans', 'user123', 'content', 'entertainment', 'social', 'media']
```
However, this processing took even more time and wasted resources which is not ideal --> <b>unsloth</b> can be applied?
- Switched to AdamW for optimization and AUC-ROC for evaluating but the result got worse. --> Each rule is distributed by 16% and labels are balanced. Hence f1 might suit this case

#### 15-10-2025
- Found Python library <b>Unsloth</b> that provides a high-speed and memory-efficient way to fine-tune LLM. <br>
This might enable ensemble learning (3 models on 25% of the data each) without running into time out error(?) 

----------------------------------------------
- text mining
- synthetic data for tuning?
- switch to prompt engineering?
- switch to multi classification?
- prompt tuning, fine-tuning
- using unsloth for better performance?


