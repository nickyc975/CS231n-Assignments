# 线性分类器

## 线性分类器的形式

假设共有$C$个类别，则对于第$i$个样本$x_i$，首先计算它的分类评分向量$s \in R^C$：

$$

s = f(x_i, W) + b

$$

其中$f$为评分函数（例如线性函数$f(x_i; W) = Wx_i$，人工神经网络等），$W$和$b$为该评分函数的参数，是需要学习的量。

有了评分向量$s$，就可以计算出样本$x_i$最有可能的分类：

$$

y_{pred} = \argmax_j\{s_j | 0 \le j \lt C\}

$$

## 线性分类器的损失函数

### 多分类SVM损失函数

* 损失函数形式

  $$

  L_i = \sum _{j \neq y_i} \max(0, s_j - s_{y_i} + 1)

  $$

* 损失函数梯度
  
  $$

  \frac{\partial L(W, b)}{\partial W} = \frac{1}{N}\sum_{1 \le i \le N 且 L_i \neq 0}\sum_{j \neq y_i}(M_j^i - M_{y_i}^i)

  $$

  其中$M_k^i$表示第$k$列为$x_i^T$，其余列均为$\boldsymbol{} 0$的与$W$同形的矩阵。

* 损失函数解释

  $s_j$表示将第$i$的样本判定为第$j$类的评分，$s_{y_i}$表示将第$i$个样本判定为正确分类的评分，其中$j \neq y_i$。

  整个损失函数的意思就是，如果将第$i$个样本判定为正确分类的评分远高于判定为其他分类的评分（例如高了阈值$1$），则取损失为$0$，否则取损失为差值加上阈值。

  这保证了：$L_i \ge 0$

### 多分类Softmax损失函数

* 损失函数形式

  $$

  L_i = -\log(\frac{e^{s_{y_i}}}{\sum_je^{s_j}})

  $$

* 损失函数梯度
  
  $$

  \frac{\partial L(W, b)}{\partial W} = \frac{1}{N}\sum_{i = 1}^N (\frac{\sum_je^{S_{ij}}M_j^i}{\sum_je^{S_{ij}}} - M_{y_i}^i)

  $$

  其中$S = x \cdot W$，$M_k^i$表示第$k$列为$x_i^T$，其余列均为$\boldsymbol{} 0$的与$W$同形的矩阵。

* 损失函数解释

  将分数归一化，使得对某一分类的分数可以看作属于该类的概率，然后对概率取对数再取相反数，保证最终的损失处于$[0, +\infin)$。

## 损失函数的正则化

正则化即在损失函数中添加一个正则项$\lambda R(W)$，其中$\lambda$是一个超参数，用于调整正则化的幅度，$R$为一个正则函数，根据$W$的值产生相应的惩罚。

* L1正则
  
  $$

  R(W) = \sum_i\sum_j|W_{ij}|

  $$

* L2正则
  
  $$

  R(W) = \sum_i\sum_jW_{ij}^2

  $$

* Elastic net (L1 + L2)
  
  $$

  R(W) = \sum_i\sum_j(\beta W_{ij}^2 + |W_{ij}|)

  $$

## 线性分类器的优化方法

一个训练集在某一组参数$W$和$b$下的损失为：

$$

L(W, b) = \frac{1}{N}\sum_{i=1}^NL_i(W, b; x_i, y_i) + \lambda R(W)

$$

则可以计算出其梯度：

$$

\frac{\partial L(W, b)}{\partial W} = \frac{1}{N}\sum_{i=1}^N\frac{\partial L_i(W, b; x_i, y_i)}{\partial W} + \lambda \frac{\partial R(W)}{\partial W}

$$

于是可将$W$更新为：

$$

W' = W - \alpha \frac{\partial L(W, b)}{\partial W}

$$

其中$\alpha$为超参数，称为步长或学习率。

### 随机梯度下降

在实际中由于样本数量巨大，往往不会直接计算整个训练集的损失来更新参数，而是随机选取一些样本计算损失，然后使用这个损失来估算整体损失，并使用估算值来更新参数。


