# Week 2 Technical & Scenario Questions

---

## 15 Technical Questions

### EDA & Data Understanding

#### Q1. What is EDA (Exploratory Data Analysis)? Why is it the first step before building any ML model?
**Ans:** EDA means analyzing and visualizing data to understand its patterns. It is the first step because it helps find missing values, clean bad data, and choose the right algorithm.

#### Q2. What is class imbalance? Look at your ticket priority distribution — which class has the least tickets and why does that create a problem for the ML model?
**Ans:** Class imbalance means some categories have many samples while others have very few. The **Critical** priority class usually has the least tickets, which causes the model to ignore minority classes and make biased predictions.

#### Q3. What does `df.describe()` show you? Name at least 4 things you can learn from its output.
**Ans:** `df.describe()` provides basic statistical summaries of numerical columns:
1. **Count:** Total number of non-null values.
2. **Mean:** Average value.
3. **Min/Max:** Minimum and maximum values.
4. **Std:** Standard deviation.

#### Q4. What is the difference between `value_counts()` and `value_counts(normalize=True)`? When would you use each one?
**Ans:** `value_counts()` gives exact row counts per category, while `value_counts(normalize=True)` gives percentages/proportions. Use `value_counts()` to see raw numbers and `normalize=True` to observe class distribution ratios.

#### Q5. You ran `df.dtypes` and found that the "Date of Purchase" column shows "object" instead of "datetime". What does this mean and how do you fix it?
**Ans:** It means the column is treated as plain text (string) rather than a date object. You fix it using:
```python
df['Date of Purchase'] = pd.to_datetime(df['Date of Purchase'])

### Feature Engineering

#### Q6. What is Feature Engineering? Why can't you directly feed raw ticket text like "VPN is not working" into an XGBoost model?
**Ans:** Feature Engineering is creating new, useful inputs from raw data to help the model learn better. XGBoost only understands mathematical numbers and matrices, so raw text strings cannot be fed directly into it.

#### Q7. What is TF-IDF? Explain it simply — what does TF mean and what does IDF mean?
**Ans:** TF-IDF converts text into numerical scores based on word importance:
* **TF (Term Frequency):** Measures how often a word appears in a single ticket.
* **IDF (Inverse Document Frequency):** Reduces the importance of common words (like "the", "is") that appear across all tickets.

#### Q8. In your code you used `TfidfVectorizer(max_features=100)`. What does `max_features=100` mean? What happens if you increase it to 500?
**Ans:** `max_features=100` keeps only the top 100 most important words as numerical features. Increasing it to 500 captures more vocabulary, but increases memory usage and model complexity.

#### Q9. You created a column called `word_count`. How does the number of words in a ticket description help the model predict priority?
**Ans:** Word count gives context on detail level. Extremely short tickets often indicate basic queries, while longer ticket descriptions usually detail complex technical outages or error logs.

#### Q10. What is the difference between `fit_transform()` and `transform()`? When do you use each one?
**Ans:**
* **`fit_transform()`:** Learns the vocabulary and transforms text into numbers (used **only on Training data**).
* **`transform()`:** Converts text into numbers using the previously learned vocabulary without re-learning (used on **Test data and new live tickets**).

### ML Model — XGBoost

#### Q11. What is XGBoost? What does "boosting" mean in simple terms?
**Ans:** XGBoost is a fast, high-performance machine learning algorithm based on decision trees. "Boosting" means building decision trees sequentially, where each new tree corrects the errors made by previous ones.

#### Q12. You split your data 80% train and 20% test using `train_test_split`. What is the purpose of the test set — why don't you just train on all the data?
**Ans:** The test set acts as unseen data to evaluate how well the model predicts real-world cases. Training on 100% of the data prevents honest evaluation and leads to overfitting.

#### Q13. In your model you used `stratify=y` in the train_test_split. What does stratify do and why is it important when you have class imbalance?
**Ans:** `stratify=y` ensures that both train and test splits keep the exact same percentage ratio of each class (Critical, High, Medium, Low), preventing rare classes from being left out of the test set.

#### Q14. What is a Label Encoder? Why did you use it to convert "High", "Low", "Critical" into numbers?
**Ans:** A Label Encoder maps text labels to numbers (e.g., Low=0, Medium=1, High=2, Critical=3) because machine learning algorithms require numerical target values for training.

#### Q15. What does `n_estimators=100` mean in XGBoost? What happens if you set it too high or too low?
**Ans:** `n_estimators=100` means building 100 decision trees. Setting it too low leads to underfitting (poor learning), while setting it too high causes overfitting and slow execution.

### 10 Scenario Based Questions

#### Q1. You trained your XGBoost model and got 90% accuracy. Your mentor says — *"This accuracy is fake, do not trust it."* Why would your mentor say that and what is the real problem?
**Ans:** Because of severe **class imbalance**. If 90% of tickets in the dataset are "Medium", the model gets 90% accuracy simply by predicting "Medium" for every ticket without learning actual patterns.

#### Q2. Your confusion matrix shows that the model almost never predicts "Critical" correctly — it keeps classifying Critical tickets as High. What are 2 reasons this might be happening and what would you do to fix it?
**Ans:**
* **Reasons:** Very few "Critical" samples in training data and similar vocabulary between High and Critical descriptions.
* **Fix:** Oversample Critical tickets using SMOTE or adjust class weights (`sample_weight` / `scale_pos_weight`) in XGBoost.

#### Q3. A new employee joins the company and creates a ticket saying — *"urgent pls fix asap"* — just 4 words. Your model predicts it as Low priority. Is the model wrong? Why did this happen and how would you improve it?
**Ans:**
* **Why it happened:** A 4-word description provides very little TF-IDF signal for the model to learn context.
* **How to improve:** Combine description with subject line features, user designation, or add custom urgency keyword features.

#### Q4. You saved your model as a `.pkl` file to S3. Next week your teammate tries to load it and gets an error — *"XGBoost version mismatch"*. What happened and how do you prevent this in a real project?
**Ans:**
* **What happened:** Different library versions installed on different machines.
* **Prevention:** Lock and export exact library versions using a `requirements.txt` file alongside saved model artifacts.

#### Q5. Your mentor asks — *"Why did you use TF-IDF and not just count how many times each word appears?"* How do you answer?
**Ans:** Simple word counting treats all words equally. TF-IDF penalizes common non-informative words (e.g., "the", "please") and highlights rare, high-value words like "VPN" or "outage".

#### Q6. You trained the model on Monday. On Friday a new batch of 500 tickets arrives. Can you use the same TF-IDF vectorizer to process these new tickets or do you need to retrain it? Explain why.
**Ans:** Use the same saved vectorizer with `transform()`. Do **not** retrain it (`fit_transform()`); refitting changes vocabulary indexing, making features incompatible with your trained model.

#### Q7. Your model has 75% F1 score on test data but when you test it on 10 real tickets manually, it gets 4 out of 10 wrong. What could be causing this gap between test score and real-world performance?
**Ans:** **Data drift**. Live production tickets may use different phrasing, newly introduced software terms, or seasonal issues not present in historical test data.

#### Q8. Your manager asks — *"Can we use this model in production right now to automatically close tickets?"* What do you tell them and what would you recommend instead?
**Ans:** No, automated ticket actions carry high risk if predictions fail. Recommend a **Human-in-the-Loop** approach where AI suggests priority, but a human agent approves final actions.

#### Q9. You realise you forgot to save the TF-IDF vectorizer `.pkl` file to S3 — you only saved the model. The next day you restart your notebook and the vectorizer is gone. What happens when you try to use the model and what do you do to fix it?
**Ans:** The model fails because raw text cannot be transformed into the expected input features. Fix it by re-fitting the vectorizer on the exact same original training data, and always save `model.pkl`, `vectorizer.pkl`, and `label_encoder.pkl` together.

#### Q10. Your intern teammate says — *"I got 95% accuracy so my model is better than yours which got 78%."* But you look at their confusion matrix and see they never predicted Critical or High correctly. How do you explain to them why their model is actually worse?
**Ans:** High accuracy on imbalanced data is misleading because predicting only majority classes inflates accuracy. A model that misses all Critical issues fails business goals, making **per-class F1-score** the true performance metric.