# Reddit Comment Rules Classification
This is the log for the Kaggle competiton [Jigsaw - Agile Community Rules Classification](https://www.kaggle.com/competitions/jigsaw-agile-community-rules) <br>
The task is to create a model that predicts whether Reddit comments violate specific rules. The labels for the training data are binary (violated or not violated), but during test inference, the model must output a numerical probability indicating the likelihood of violation.
Furthermore, the test data contains a large amount of data that includes rules not present in the training data.

## Result
- Rank: 1031/2437
- Public Score: 0.90903
- Private Score: 0.90354

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
- No internet is allowed, so pre-trained model should be added to the dataset on kaggle 
- Hiden test data is 45-50k  
### Data Augumentation
In train.csv and test.csv, there are columns positive_example_1/2 (violated) and negative_example_1/2 (non violated). <br>
Using this data, I augmented the dataset to make it 5 times larger. Although the training data contained only two rules, it was discovered (thanks to expert Kagglers) that the test data included four additional rules. So I applied the same processing to the test data as well. <br><br>
<img width="961" height="463" alt="Screen Shot 2025-10-28 at 2 40 36" src="https://github.com/user-attachments/assets/1f859c06-a543-4991-a0c2-9a01e899cbb5" />
<img width="700" height="400" alt="Screen Shot 2025-10-08 at 20 56 48" src="https://github.com/user-attachments/assets/737170d2-f344-46fc-bf3c-7a7aac0ae1b0" /><br>
This improved score from 0.584 to 0.767 <br><br>
Added rule and subreddit data --> Score: 0.815
```
X = "Rule: " + df_train["rule"] +
    " Subreddit: " + df_train["subreddit"] + \
    " Comment: " + df_train['body']
```

### Data Analysis
- Increased the data taken from the test set from 20% to 40%. However the result was almost the same. --> 20% of data is enough for trainig.
- There is a chance that comments breaking the specific rules tend to have more capital letters? --> No huge difference <br>
- Another hypothisis is that comments breaking the specifc rules tend to have more urls? --> No huge diffenrence 
However, after training the dataset with URLs removed, the results got worse, indicating that <b>URLs are important features</b>. <br>
<p align="center">
  <img width="45%" alt="Screen Shot 2025-10-12 at 14 16 27" src="https://github.com/user-attachments/assets/93717880-5def-4232-b3bc-0e0378a2b7d8" /> 
  <img width="45%" alt="Screen Shot 2025-10-12 at 14 18 25" src="https://github.com/user-attachments/assets/e6fe0d08-7761-4e4b-a481-a9343a49cc3d" /> 
</p>

### URLs
- Parsed URLs and removed emoji / unnecessary space --> Score: 0.864
```
# Before
for you and your friend 2 codes for 4 dollars https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=UN4E27AG7BWKS

# After
for you and your friend 2 codes for 4 dollars (URL Keywords: domain:paypal path:button)
```

- Tried prompt engineering for parsing urls such that 
```
prompt = (
        f"Please list 3 to 5 keywords that are likely related to the content of this URL: {url}. "
        f"Answer the keywords only, separated by commas."
    )

url = "https://onlyfans.com/user123"
print(build_prompt(url))

['onlyfans', 'user123', 'content', 'entertainment', 'social', 'media']
```
However, this processing took even more time and wasted resources which is not ideal

### Bert Models

### LLM

### Ensembling

#### 15-10-2025
- Removed noise from URLs such that "wwww" and replaced URL string to
```
feature_str = f"<URL:domain={domain}, shortener={is_shortener}, social={is_social}, ip={has_ip}>"
```
However, the model's accuracy has decreased. --> path are also imortant features?

- Found Python library <b>Unsloth</b> that provides a high-speed and memory-efficient way to fine-tune LLM. <br>
This might enable ensemble learning (3 models on 25% of the data each) without running into time out error(?) 


## To-do
- synthetic data for tuning?
- switch to prompt engineering?
- prompt tuning, fine-tuning
- using vLLM / unsloth for better performance? <br>
--> vLLM prompt engineering was the best solution

