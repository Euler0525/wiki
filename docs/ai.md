# [AI Learning](https://github.com/Euler0525/AI-Learning)

## Dataset


### [Iris](https://archive.ics.uci.edu/dataset/53/iris)

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split

iris = load_iris()
df = pd.DataFrame(iris.data, columns=iris.feature_names)
df["label"] = iris.target
df.columns = [
    "sepal length", "sepal width", "petal length", "petal width", "label"
]

plt.scatter(df[:50]["sepal length"], df[:50]["sepal width"], label="0")
plt.scatter(df[50:100]["sepal length"], df[50:100]["sepal width"], label="1")
plt.xlabel("sepal length")
plt.ylabel("sepal width")
plt.legend()

data = np.array(df.iloc[:100, [0, 1, -1]])
X, y = data[:,:-1], data[:,-1]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)
```

## [Machine Learning](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning)

> 吴恩达机器学习

### [exp1-Linear Regression](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp1-Linear_Regression)

### [exp2-Logistic Regression](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp2-Logistic_Regression/)

### [exp3-Neural Network(fp)](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp3-Neural_Network%28fp%29/)

### [exp4-Neural Network(bp)](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp4-Neural_NetWork%28bp%29/)

### [exp5-Bias VS Variance](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp5-Bias_VS_Variance)

### [exp6-SVM](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp6-SVM/)

### [exp7-K-means And PCA](https://github.com/Euler0525/AI-Learning/tree/master/Machine_Learning/exp7-K-means_And_PCA/)

## Statistical Learning Methods

> 统计学习方法（第 2 版）

### [01-Introduction](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/01-Introduction.ipynb)

### [02-Perceptron](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/02-Perceptron.ipynb)

### [03-KNN](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/03-KNearestNeighbors.ipynb)

### [04-Naive Bayes](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/04-NaiveBayes.ipynb)

### [05-Decision Tree](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/05-DecisionTree.ipynb)

### [06-Logistic Regression](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/06-LogisticRegression.ipynb)

### [07-SVM](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/07-SVM.ipynb)

### [08-Boost](https://github.com/Euler0525/AI-learning/blob/master/Statistical_Learning_Methods/08-Boost.ipynb)

## Reference

### Machine Learning

[**ladykaka007** 的投稿视频](https://space.bilibili.com/49109393/video)

### Statistical Learning Methods

[统计学习方法（第 2 版）](https://book.douban.com/subject/33437381/)

[Learn-Statistical-Learning-Method](https://github.com/hktxt/Learn-Statistical-Learning-Method)

[lihang-code](https://github.com/fengdu78/lihang-code)

[lihang_book_algorithm](https://github.com/WenDesi/lihang_book_algorithm)
