# Tổng hợp Machine Learning Algorithms

## Linear Regression

$$
\begin{array}{l}
\mathbf{x} \in \mathbb{R}^m, \quad y \in \mathbb{R} \\
\hat{y} = \bar{\mathbf{x}}^\intercal \mathbf{w}, \quad \mathbf{w} \in \mathbb{R}^{m + 1}
\end{array}
$$

Loss function:

$$
\begin{array}{l}
J(\mathbf{w})   &=& \dfrac{1}{2N} \displaystyle \sum_{i=1}^N \left( y_i - \hat{y}_i \right)^2 \\
                &=& \dfrac{1}{2N} \left[ \left(y_1 -  \bar{\mathbf{x}}_1^\intercal \mathbf{w} \right)^2 + \dots + \left(y_N - \bar{\mathbf{x}}_N^\intercal \mathbf{w} \right)^2 \right] \\
                &=& \dfrac{1}{2N} \displaystyle \lVert \bar{\mathbf{X}}^\intercal \mathbf{w} - \mathbf{y} \rVert_2^2
\end{array}
$$

Đạo hàm:

$$
\dfrac{\partial J}{\partial \mathbf{w}} = \dfrac{1}{N} \bar{\mathbf{X}} \left( \bar{\mathbf{X}}^\intercal \mathbf{w} - \mathbf{y} \right)
$$

!!! code

    ```py
    class LinearRegression:
        def __init__(self, init_w: np.ndarray, lr: float, n_iters: int) -> None:
            self.w = init_w
            self.lr = lr
            self.n_iters = n_iters

        def loss(self, X: np.ndarray, y: np.ndarray):
            y_hat = X.T.dot(self.w)
            return (np.linalg.norm(y - y_hat, 2) ** 2) / (2 * N)

        def fit(self, X: np.ndarray, y: np.ndarray):
            y = np.expand_dims(y, axis=1)

            for i in range(self.n_iters):
                grad_w = 1 / N * (X.dot(X.T.dot(self.w) - y))
                self.w -= self.lr * grad_w

        def predict(self, x: np.ndarray):
            return x.transpose().dot(self.w)
    ```

## Logistic Regression

$$
\begin{array}{l}
\mathbf{x} \in \mathbb{R}^m, \quad y \in \left\{ 0, 1 \right\} \\
\hat{y} = \sigma(\bar{\mathbf{x}}^\intercal \mathbf{w}), \quad \mathbf{w} \in \mathbb{R}^{m + 1} \\
\sigma(z) = \dfrac{1}{1 + e^{-z}}
\end{array}
$$

Gradient descent:

$$
\mathbf{w} = \mathbf{w} - \eta \mathbf{X} \left( \hat{\mathbf{y}} - \mathbf{y} \right)
$$

!!! code

    ```py
    class LogisticRegression:
        def __init__(self, pretrained: np.ndarray, lr: float, n_iter: int) -> None:
            self.w = pretrained
            self.lr = lr
            self.n_iter = n_iter

        def fit(self, X, y):
            for _ in range(self.n_iter):
                y_hat = sigmoid(X.T @ self.w)
                J = X @ (y_hat - y)
                J /= X.shape[1]
                self.w -= self.lr * J

        def predict_proba(self, X):
            return sigmoid(X.T @ self.w)

        def predict(self, X):
            return self.predict_proba(X) >= 0.5
    ```

## SVM

$$
\begin{array}{l}
\mathbf{x} \in \mathbb{R}^m, \quad y \in \left\{ -1, 1 \right\} \\
\hat{y} = \mathbf{w}^\intercal\mathbf{x} + b, \quad \mathbf{w} \in \mathbb{R}^m, \quad b \in \mathbb{R}
\end{array}
$$

Loss function:

$$
J(\mathbf{\bar{w}}) = \underbrace{\sum_{i=1}^N \max(0, 1 - y_i\bar{\mathbf{w}}^T\bar{\mathbf{x}}_i)}_{\text{hinge loss}} + \underbrace{\frac{\lambda}{2} \lVert \mathbf{w} \rVert_2^2}_{\text{regularization}}
$$

Gradient descent:

$$
\bar{\mathbf{w}} = \bar{\mathbf{w}} - \eta \left(\sum_{i \in \mathcal{H}} - y_i\bar{\mathbf{x}}_n + \lambda \left[\begin{matrix}
\mathbf{w}\newline
0
\end{matrix}\right]\right), \quad \mathcal{H} = \left\{ i: y_i\bar{\mathbf{w}}^T\bar{\mathbf{x}}_i < 1 \right\}
$$

!!! code

    ```py
    import numpy as np
    import matplotlib.pyplot as plt
    from sklearn.datasets import make_circles, make_classification
    from sklearn import preprocessing

    np.random.seed(42)

    means = [[2, 2], [2.75, 1.25]]
    cov = [[.3, .15], [.15, .3]]
    N = 50
    X0 = np.random.multivariate_normal(means[0], cov, N)
    X1 = np.random.multivariate_normal(means[1], cov, N)

    X = np.concatenate((X0.T, X1.T), axis = 1)
    y = np.concatenate((np.ones((1, N)), -1*np.ones((1, N))), axis = 1)


    class SVM:
        def __init__(self, w: np.ndarray, lr: float, regularization: float) -> None:
            self.w = w
            self.lr = lr
            self.lamb = regularization

        def y_hat(self, X: np.ndarray, y: np.ndarray):
            return self.w.T @ X

        def loss(self, X: np.ndarray, y: np.ndarray):
            z = self.y_hat(X, y)  # Shape (1, N)
            return (np.sum(np.maximum(0, 1 - y * z)) + self.lamb * 0.5 * np.linalg.norm(self.w, 2) ** 2) / X.shape[1]

        def grad(self, X: np.ndarray, y: np.ndarray):
            z = self.y_hat(X, y)  # Shape (1, N)
            yz = y * z  # Shape (1, N)
            active_set = np.where(yz < 1)[1]

            yX = -y * X  # Shape (m, N)
            
            w_0 = self.w.copy()
            w_0[-1] = 0

            grad_w = (np.sum(yX[:, active_set], axis=1, keepdims=True) + self.lamb * w_0) / X.shape[1]  # Shape (m, 1)

            return grad_w

        def fit(self, X: np.ndarray, y: np.ndarray, n_iter: int = 1e6):
            it = 0
            best_loss = 1e9
            best_iter = -1
            while it < n_iter:
                it += 1
                gw = self.grad(X, y)
                self.w -= self.lr * gw

                loss = self.loss(X, y)
                if loss < best_loss:
                    best_loss = loss
                    best_iter = it
                elif it - best_iter > 100:
                    print("No improve in the last 20 iter")
                    break

                if (it % 100) == 0:
                    print(f"iter {it}; loss: {loss}")
    ```

## K-NN

!!! code
    ```py
    class KNN:
        def __init__(self, k: int) -> None:
            print("k = ", k)
            self.k = k
            self.X = None
            self.y = None

        def fit(self, X, y):
            self.X = X
            self.y = y

        def predict(self, z: np.ndarray):
            indices = list(range(X.shape[1]))
            indices = sorted(
                indices,
                key=lambda i: np.linalg.norm(self.X[:, i].reshape(2, 1), 2) ** 2 - 2 * z.transpose().dot(self.X[:, i].reshape(2, 1))
            )
            knn_indices = indices[: self.k]
            knn_labels = [self.y[i] for i in knn_indices]
            return knn_indices, Counter(knn_labels).most_common(1)[0][0]
    ```

## K-means

!!! code
    ```py
    class KMeansCluster:
        def __init__(self, n_cluster: int):
            self.n_cluster = n_cluster

            self.centroids = []
            self.cluster_of_item = []

        def fit(self, X):
            self.centroids = random.choices(X, k=self.n_cluster)
            self.cluster_of_item = [-1] * len(X)

            while True:
                stop = True

                for ix, x in enumerate(X):
                    closet_center_idx = min(list(range(len(self.centroids))), key=lambda i: np.linalg.norm(x - self.centroids[i], 2))
                    if self.cluster_of_item[ix] != closet_center_idx:
                        stop = False

                    self.cluster_of_item[ix] = closet_center_idx

                for ic, _ in enumerate(self.centroids):
                    x_in_cluster = [X[i] for i in range(len(X)) if self.cluster_of_item[i] == ic]
                    new_center = np.array(x_in_cluster).mean(axis=0)
                    self.centroids[ic] = new_center

                if stop:
                    break
    ```

## Navie Bayes Classifier

[Navie Bayes Classifier](nbc.md)

## Decision Tree

[Decision Tree](decision-tree.md)

## Random Forest

```py
class RandomForest:
    def __init__(self, num_trees: int) -> None:
        self.num_trees = num_trees
        self.trees = []

    def bootstrap_sample(self, data):
        n_samples = data.shape[0]
        indices = np.random.choice(n_samples, size=n_samples, replace=True)
        bootstrap_sample = data.iloc[indices].reset_index(drop=True)
        return bootstrap_sample

    def fit(self, data, target_column):
        for _ in range(self.num_trees):
            tree = DecisionTree()
            sample_data = self.bootstrap_sample(data)
            tree.fit(sample_data, target_column=target_column)
            self.trees.append(tree)

    def predict(self, new_data):
        predictions = [tree.predict(new_data) for tree in self.trees]
        labels = [list(pred) for pred in zip(*predictions)]
        return [Counter(lb).most_common(1)[0][0] for lb in labels]
```