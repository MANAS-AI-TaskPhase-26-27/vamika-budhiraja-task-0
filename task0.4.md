# Machine Learning Fundamentals 

basic report over machine learning by taking into account a few resources on ML basics, supervised and unsupervised learning, and data cleaning.

Sources used:
- Google's Intro to ML (developers.google.com)
- IBM's explainer on Supervised Learning
- IBM's explainer on Unsupervised Learning
- Kaggle's Data Cleaning micro-course
- A YouTube explainer video on ML basics

---

## 1. What Machine Learning Actually Is

At the core, ML is the process of training a piece of software (called a model) to make useful predictions or generate content, based on data, instead of writing explicit rules for every situation.

Example that stuck with me: predicting rainfall.
- Traditional approach: build a physics based model of the atmosphere and run fluid dynamics equations. Extremely hard.
- ML approach: feed the model huge amounts of past weather data until it learns the relationship between weather patterns and rainfall on its own. Then give it current conditions and it predicts the output.

So a "model" is basically a mathematical relationship the system has derived from data, not a piece of hardware and not a small representation of the thing being studied.

### The four broad categories of ML systems
1. **Supervised learning** - learns from labeled data (data that already has the correct answers).
2. **Unsupervised learning** - finds patterns in data that has no labels.
3. **Reinforcement learning** - learns by getting rewards or penalties for actions taken in an environment.
4. **Generative AI** - creates new content (text, images, audio, video) by learning patterns from existing data and mimicking them.

---

## 2. Supervised Learning

Supervised learning models learn by seeing lots of examples that already come with the correct answer, and then figuring out the connection between the inputs and those answers. It is compared to a student studying old exam papers that have both the questions and the answers, then using that pattern to do well on a new exam.

### Core building blocks
- **Data** - stored as datasets, made up of individual examples.
- **Features** - the input values used to predict something (e.g. temperature, humidity, square footage).
- **Label** - the actual answer or value you want the model to predict (e.g. rainfall amount, house price).
- **Labeled example** - an example that has both features and a label. Used for training.
- **Unlabeled example** - has features but no label. This is what the trained model is eventually used on.

### Two dataset qualities that matter a lot
- **Size** - number of examples.
- **Diversity** - how wide a range of situations the examples cover.

A dataset can be large but not diverse (bad), small but diverse (still not great, not enough examples to be confident), or large and diverse (ideal). A dataset covering 100 years of July weather won't help predict January rainfall. More features generally help the model find patterns, but not always, since some features may have no real relationship to the label.

### The actual workflow
1. **Model** - the collection of learned parameters/numbers that represent the relationship between features and labels.
2. **Training** - the model is shown labeled examples, makes a prediction, compares it to the actual value, and the difference (called the **loss**) is used to nudge the model closer to the right answer. This repeats across the whole dataset, sometimes multiple passes.
3. **Evaluating** - after training, the model is tested on a labeled dataset it hasn't "learned from" in the same way. Only the features are given to it, and its predictions are compared against the real labels to see how well it generalizes.
4. **Inference** - once the model performs well during evaluation, it's used on real, unlabeled data to make actual predictions.

### The two big supervised learning tasks
- **Regression** - predicting a numeric value. Example: predicting house prices or how long a ride will take.
- **Classification** - predicting which category something belongs to.
  - **Binary classification** - only two possible outcomes (e.g. spam or not spam, rain or no rain).
  - **Multiclass classification** - more than two possible categories (e.g. rain, hail, snow, sleet).

### Common supervised learning algorithms (from the IBM explainer)
- **Linear regression** - fits a straight line relationship between input and output. Used for continuous predictions.
- **Logistic regression** - despite the name, this is for classification, especially binary outcomes (true/false type problems).
- **Polynomial / nonlinear regression** - for relationships that are not a straight line.
- **Naive Bayes** - a classification method based on probability (Bayes' theorem), assumes features are independent of each other. Common in spam detection and text classification.
- **Support Vector Machines (SVM)** - draws a boundary (hyperplane) that best separates classes of data.
- **K-nearest neighbor (KNN)** - classifies a data point based on how close it is to other known points. Simple but gets slower as the dataset grows.
- **Decision trees / Random forest** - random forest is a collection of many decision trees combined together to reduce variance and improve accuracy.
- **Ensemble learning** - training multiple models on the same problem and combining their results, so weaknesses in one model get balanced out by others.

### Where supervised learning falls short
It needs a lot of labeled data, which takes time and human effort to prepare. It can also overfit, meaning it becomes too tailored to the training data and performs worse on new, unseen data. It also struggles outside the range of data it was trained on.

### Supervised vs other approaches (quick comparison)
- **Semi-supervised learning** - only part of the data is labeled, rest is unlabeled. Cheaper than full supervised learning.
- **Self-supervised learning** - the model generates its own pseudo-labels from unstructured data, often used in NLP and computer vision where labeling everything by hand isn't realistic.
- **Reinforcement learning** - no labeled data at all, learns through trial and error using rewards instead of matching inputs to known outputs.

---

## 3. Unsupervised Learning

Unsupervised learning works with data that has no labels or predefined answers. The model's job is to find hidden structure in the data on its own, without anyone telling it what the "right answer" looks like.

The three main tasks in unsupervised learning are clustering, association, and dimensionality reduction.

### Clustering
Grouping similar data points together based on their similarities or differences.

- **Exclusive (hard) clustering** - a data point can only belong to one cluster. Example: **K-means clustering**, where data points are grouped into K clusters based on distance from each cluster's center point. A higher K value gives smaller, more granular groups.
- **Overlapping (soft) clustering** - a data point can belong to multiple clusters at once, with different degrees of membership. Fuzzy k-means is an example.
- **Hierarchical clustering** - builds a tree-like structure of clusters, visualized using a dendrogram.
  - **Agglomerative (bottom-up)** - starts with every point as its own cluster and merges them step by step based on similarity.
  - **Divisive (top-down)** - starts with one big cluster and splits it apart. Less commonly used.
- **Probabilistic clustering** - clusters data based on the probability that a point belongs to a certain distribution. Gaussian Mixture Models (GMM) are the common example here.

Important distinction: clustering is different from classification because in clustering, the categories aren't predefined by a human. The model finds the groupings itself, and it's up to you to interpret and name them afterward (for example, noticing that weather data naturally clusters into what we'd call "seasons").

### Association rules
This is about finding relationships between variables in a dataset, commonly used for market basket analysis. Basically figuring out "if someone buys/does X, they are also likely to buy/do Y." This is the logic behind "customers who bought this also bought" type recommendations. The Apriori algorithm is the most widely used method here.

### Dimensionality reduction
Used when a dataset has too many features/dimensions, which can slow things down, cause overfitting, and make visualization hard. The goal is to reduce the number of inputs while keeping as much of the meaningful information as possible.

- **Principal Component Analysis (PCA)** - transforms the data to find the directions (components) that capture the most variance, reducing redundant features.
- **Singular Value Decomposition (SVD)** - factorizes data into smaller matrices, often used for compressing data like images and reducing noise.
- **Autoencoders** - a neural network approach where data is compressed down (encoding) and then reconstructed (decoding), useful for learning efficient representations of data.

### Real world uses of unsupervised learning
News categorization (grouping articles about the same event from different sources), anomaly detection (spotting unusual data points, useful for fraud or equipment failure detection), customer segmentation, recommendation engines, and parts of computer vision and medical imaging.

### Where unsupervised learning struggles
Since there's no ground truth to check against, it's harder to know if the model actually did a good job. It can also be more computationally heavy, take longer to train, and the results (like why certain things got clustered together) aren't always easy to explain or interpret.

---

## 4. Supervised vs Unsupervised learning 

The core difference: supervised learning is given the correct answers up front and learns to match inputs to those answers. Unsupervised learning is given no answers and has to find structure/patterns on its own.

Supervised learning tends to be more accurate for well defined problems because it has ground truth to learn from, but it needs labeled data which takes time and effort to prepare. Unsupervised learning skips that labeling step and works well for exploratory analysis, when you don't fully know what you're looking for yet in the data.

---

## 5. Reinforcement Learning (RL)

Unlike supervised and unsupervised learning, RL doesn't learn from a fixed dataset of labeled examples or unlabeled examples sitting around. Instead, an agent learns by interacting with an environment, taking actions, and getting rewards or penalties based on the outcome of those actions.

Over time, the model builds up a **policy**, which is basically its strategy for choosing actions that lead to the most reward in the long run. It's a trial and error process rather than a "here's the correct answer, learn from it" process like supervised learning.

A simple way to think about it: it's less like a student studying old exam papers (supervised learning) and more like learning to ride a bike by actually trying, falling, adjusting, and slowly figuring out what works through repeated attempts.

Common use cases include training robots to perform physical tasks like walking or grasping objects, and training software agents to play games (AlphaGo learning to play Go is the classic example).

A few important things aboutabout RL:
- It doesn't need labeled data, which is an advantage over supervised learning.
- It can handle complex, sequential decision making problems where the "right" first move depends on what happens later.
- Training can be unstable or inconsistent early on since it's learning purely from trial and error.
- It usually needs a lot of interaction with an environment (real or simulated) to learn well, which can be resource intensive.
- Models can sometimes exploit loopholes in how rewards are set up rather than actually solving the intended task properly, something usually called reward hacking.

---

## 6. Data Cleaning Basics (from the Kaggle course)

Before any of the modeling stuff above can actually work well, the data usually needs cleaning first. Real world data is messy, and this course breaks the process into five parts.

### a) Handling missing values
- First step is figuring out how many missing values exist and why they're missing (was it not recorded, does it not exist, or was it lost).
- Options: drop rows/columns with missing values, or fill them in using some logical method (like using a placeholder, the column mean/median, or forward/backward filling for time based data).
- Dropping data is easier but risks losing useful information, so it's better to understand why something is missing before deciding.

### b) Scaling and normalization
- **Scaling** - changes the range of the data (e.g. transforming values so they fall between 0 and 1) without changing the shape of its distribution. Useful for models that are sensitive to the scale of input values, like SVM or KNN.
- **Normalization** - changes the shape of the distribution itself, usually to make it more like a normal (bell curve) distribution. Useful for methods that assume data is normally distributed.
- These two terms get mixed up a lot, but scaling is about range, normalization is about distribution shape.


---
