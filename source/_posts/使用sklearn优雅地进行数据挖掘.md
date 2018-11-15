---
title: 使用sklearn优雅地进行数据挖掘
date: 2018-11-15 19:25:11
categories: [数据挖掘]
tags: [数据挖掘, 机器学习]
---

之前的一篇博文的最后我们提到了一个问题，我们可以使用`sklearn`完成几乎所有特征处理的工作，而且不管是数据预处理，还是特征选择，抑或降维，它们都是通过某个类的方法`fit_transform`完成的，`fit_transform`要不只带一个参数：特征矩阵，要不带两个参数：特征矩阵加目标向量。这些难道都是巧合吗？还是故意设计成这样？方法`fit_transform`中有`fit`这一单词，它和训练模型的`fit`方法有关联吗？下面就让我们来一起揭晓答案！
<!--more-->

---

### 使用sklearn进行数据挖掘

#### 数据挖掘的步骤

数据挖掘通常包括**数据采集**，**数据分析**，**特征工程**，**训练模型**，**模型评估**等步骤。使用`sklearn`工具可以方便地进行特征工程和模型训练工作，在《使用sklearn做单机特征工程》中，我们最后留下了一些疑问：特征处理类都有三个方法`fit`, `transform`和`fit_transform`, `fit`方法居然和模型训练方法`fit`同名(不光同名，参数列表都一样)，这难道都是巧合？

显然，这不是巧合，这正是`sklearn`的设计风格。我们能够更加优雅地使用`sklearn`进行特征工程和模型训练工作。此时，不妨从一个基本的数据挖掘场景入手：

![数据挖掘流程](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/%E4%BD%BF%E7%94%A8sklearn%E4%BC%98%E9%9B%85%E5%9C%B0%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98%E6%B5%81%E7%A8%8B.jpg '数据挖掘流程')

我们使用`sklearn`进行虚线框内的工作(`sklearn`也可以进行文本特征提取)。通过分析`sklearn`源码，我们可以看到除**训练，预测和评估**以外，处理其他工作的类都实现了3个方法：`fit`、`transform`和`fit_transform`。从命名中可以看到，`fit_transform`方法是先调用`fit`然后调用`transform`，我们只需要关注`fit`方法和`transform`方法即可。

`transform`方法主要用来**对特征进行转换**。从可利用信息的角度来说，**转换分为无信息转换和有信息转换**。无信息转换是指不利用任何其他信息进行转换，比如指数、对数函数转换等。有信息转换从是否利用目标值向量又可分为无监督转换和有监督转换。无监督转换指只利用特征的统计信息的转换，统计信息包括均值、标准差、边界等等，比如标准化、PCA法降维等。有监督转换指既利用了特征信息又利用了目标值信息的转换，比如通过模型选择特征、LDA法降维等。通过总结常用的转换类，我们得到下表：

![常用的转换类](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/%E4%BD%BF%E7%94%A8sklearn%E4%BC%98%E9%9B%85%E5%9C%B0%E8%BF%9B%E8%A1%8C%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98/%E5%B8%B8%E7%94%A8%E7%9A%84%E8%BD%AC%E6%8D%A2%E7%B1%BB.png '常用的转换类')

不难看到，只有有信息的转换类的`fit`方法才实际有用，显然`fit`方法的主要工作是**获取特征信息和目标值信息**，在这点上，`fit`方法和模型训练时的`fit`方法就能够联系在一起了：都是通过分析特征和目标值，提取有价值的信息，对于转换类来说是某些统计量，对于模型来说可能是特征的权值系数等。另外，只有有监督的转换类的`fit`和`transform`方法才需要特征和目标值两个参数。`fit`方法无用不代表其没实现，而是除合法性校验以外，其并没有对特征和目标值进行任何处理，`Normalizer`的`fit`方法实现如下：

```Python
def fit(self, X, y=None):
        """Do nothing and return the estimator unchanged
        This method is just there to implement the usual API and hence
        work in pipelines.
        """
        X = check_array(X, accept_sparse='csr')
        return self
```

基于这些特征处理工作都有共同的方法，那么试想可不可以将他们组合在一起？在本文假设的场景中，我们可以看到这些工作的组合形式有两种：流水线式和并行式。基于流水线组合的工作需要依次进行，前一个工作的输出是后一个工作的输入；基于并行式的工作可以同时进行，其使用同样的输入，所有工作完成后将各自的输出合并之后输出。`sklearn`提供了包`pipeline`来完成**流水线式和并行式**的工作。

#### 数据初貌

在此，我们仍然使用`IRIS`数据集来进行说明。为了适应提出的场景，对原数据集需要稍微加工：

```Python
from numpy import hstack, vstack, array, median, nan
from numpy.random import choice
from sklearn.datasets import load_iris

#特征矩阵加工
#使用vstack增加一行含缺失值的样本(nan, nan, nan, nan)
#使用hstack增加一列表示花的颜色（0-白、1-黄、2-红），花的颜色是随机的，意味着颜色并不影响花的分类
iris.data = hstack((choice([0, 1, 2], size=iris.data.shape[0]+1).reshape(-1,1), vstack((iris.data, array([nan, nan, nan, nan]).reshape(1,-1)))))
#目标值向量加工
#增加一个目标值，对应含缺失值的样本，值为众数
iris.target = hstack((iris.target, array([median(iris.target)])))
```

#### 关键技术

**并行处理，流水线处理，自动化调参，持久化**是使用`sklearn`优雅地进行数据挖掘的核心。**并行处理和流水线处理**将多个特征处理工作，甚至包括模型训练工作组合成一个工作(从代码的角度来说，即将多个对象组合成了一个对象)。在组合的前提下，**自动化调参技术**帮我们省去了人工调参的繁琐。训练好的模型是贮存在内存中的数据，**持久化**能够将这些数据保存在文件系统中，之后使用时无需再进行训练，直接从文件系统中加载即可。

---

### 并行处理

**并行处理**使得多个特征处理工作能够并行地进行。根据对特征矩阵的读取方式不同，可分为**整体并行处理和部分并行处理**。整体并行处理，即并行处理的每个工作的输入都是特征矩阵的整体；部分并行处理，即可定义每个工作需要输入的特征矩阵的列。

#### 整体并行处理

`pipeline`包提供了`FeatureUnion`类来进行整体并行处理：

```Python
from numpy import log1p
from sklearn.preprocessing import FunctionTransformer
from sklearn.preprocessing import Binarizer
from sklearn.pipeline import FeatureUnion

#新建将整体特征矩阵进行对数函数转换的对象
step2_1 = ('ToLog', FunctionTransformer(log1p))
#新建将整体特征矩阵进行二值化类的对象
step2_2 = ('ToBinary', Binarizer())
#新建整体并行处理对象
#该对象也有fit和transform方法，fit和transform方法均是并行地调用需要并行处理的对象的fit和transform方法
#参数transformer_list为需要并行处理的对象列表，该列表为二元组列表，第一元为对象的名称，第二元为对象
step2 = ('FeatureUnion', FeatureUnion(transformer_list=[step2_1, step2_2, step2_3]))
```

#### 部分并行处理

整体并行处理有其缺陷，在一些场景下，我们只需要对特征矩阵的某些列进行转换，而不是所有列。`pipeline`并没有提供相应的类（仅`OneHotEncoder`类实现了该功能），需要我们在`FeatureUnion`的基础上进行优化：

```Python
from sklearn.pipeline import FeatureUnion, _fit_one_transformer, _fit_transform_one, _transform_one 
from sklearn.externals.joblib import Parallel, delayed
from scipy import sparse
import numpy as np

#部分并行处理，继承FeatureUnion
class FeatureUnionExt(FeatureUnion):
    #相比FeatureUnion，多了idx_list参数，其表示每个并行工作需要读取的特征矩阵的列
    def __init__(self, transformer_list, idx_list, n_jobs=1, transformer_weights=None):
        self.idx_list = idx_list
        FeatureUnion.__init__(self, transformer_list=map(lambda trans:(trans[0], trans[1]), transformer_list), n_jobs=n_jobs, transformer_weights=transformer_weights)

    #由于只部分读取特征矩阵，方法fit需要重构
    def fit(self, X, y=None):
        transformer_idx_list = map(lambda trans, idx:(trans[0], trans[1], idx), self.transformer_list, self.idx_list)
        transformers = Parallel(n_jobs=self.n_jobs)(
            #从特征矩阵中提取部分输入fit方法
            delayed(_fit_one_transformer)(trans, X[:,idx], y)
            for name, trans, idx in transformer_idx_list)
        self._update_transformer_list(transformers)
        return self

    #由于只部分读取特征矩阵，方法fit_transform需要重构
    def fit_transform(self, X, y=None, **fit_params):
        transformer_idx_list = map(lambda trans, idx:(trans[0], trans[1], idx), self.transformer_list, self.idx_list)
        result = Parallel(n_jobs=self.n_jobs)(
            #从特征矩阵中提取部分输入fit_transform方法
            delayed(_fit_transform_one)(trans, name, X[:,idx], y,
                                        self.transformer_weights, **fit_params)
            for name, trans, idx in transformer_idx_list)

        Xs, transformers = zip(*result)
        self._update_transformer_list(transformers)
        if any(sparse.issparse(f) for f in Xs):
            Xs = sparse.hstack(Xs).tocsr()
        else:
            Xs = np.hstack(Xs)
        return Xs

    #由于只部分读取特征矩阵，方法transform需要重构
    def transform(self, X):
        transformer_idx_list = map(lambda trans, idx:(trans[0], trans[1], idx), self.transformer_list, self.idx_list)
        Xs = Parallel(n_jobs=self.n_jobs)(
            #从特征矩阵中提取部分输入transform方法
            delayed(_transform_one)(trans, name, X[:,idx], self.transformer_weights)
            for name, trans, idx in transformer_idx_list)
        if any(sparse.issparse(f) for f in Xs):
            Xs = sparse.hstack(Xs).tocsr()
        else:
            Xs = np.hstack(Xs)
        return Xs
```

在本文提出的场景中，我们对特征矩阵的第1列(花的颜色)进行定性特征编码，对第2、3、4列进行对数函数转换，对第5列进行定量特征二值化处理。使用`FeatureUnionExt`类进行部分并行处理的代码如下：

```Python
from numpy import log1p
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import FunctionTransformer
from sklearn.preprocessing import Binarizer

#新建将部分特征矩阵进行定性特征编码的对象
step2_1 = ('OneHotEncoder', OneHotEncoder(sparse=False))
#新建将部分特征矩阵进行对数函数转换的对象
step2_2 = ('ToLog', FunctionTransformer(log1p))
#新建将部分特征矩阵进行二值化类的对象
step2_3 = ('ToBinary', Binarizer())
#新建部分并行处理对象
#参数transformer_list为需要并行处理的对象列表，该列表为二元组列表，第一元为对象的名称，第二元为对象
#参数idx_list为相应的需要读取的特征矩阵的列
step2 = ('FeatureUnionExt', FeatureUnionExt(transformer_list=[step2_1, step2_2, step2_3], idx_list=[[0], [1, 2, 3], [4]]))
```

---

### 流水线处理

`pipeline`包提供了`Pipeline`类来进行流水线处理。流水线上除最后一个工作以外，其他都要执行`fit_transform`方法，且上一个工作输出作为下一个工作的输入。最后一个工作必须实现`fit`方法，输入为上一个工作的输出；但是不限定一定有`transform`方法，因为**流水线的最后一个工作可能是训练！**

根据本文提出的场景，结合并行处理，构建完整的流水线的代码如下：

```Python
from numpy import log1p
from sklearn.preprocessing import Imputer
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import FunctionTransformer
from sklearn.preprocessing import Binarizer
from sklearn.preprocessing import MinMaxScaler
from sklearn.feature_selection import SelectKBest
from sklearn.feature_selection import chi2
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline

#新建计算缺失值的对象
step1 = ('Imputer', Imputer())
#新建将部分特征矩阵进行定性特征编码的对象
step2_1 = ('OneHotEncoder', OneHotEncoder(sparse=False))
#新建将部分特征矩阵进行对数函数转换的对象
step2_2 = ('ToLog', FunctionTransformer(log1p))
#新建将部分特征矩阵进行二值化类的对象
step2_3 = ('ToBinary', Binarizer())
#新建部分并行处理对象，返回值为每个并行工作的输出的合并
step2 = ('FeatureUnionExt', FeatureUnionExt(transformer_list=[step2_1, step2_2, step2_3], idx_list=[[0], [1, 2, 3], [4]]))
#新建无量纲化对象
step3 = ('MinMaxScaler', MinMaxScaler())
#新建卡方校验选择特征的对象
step4 = ('SelectKBest', SelectKBest(chi2, k=3))
#新建PCA降维的对象
step5 = ('PCA', PCA(n_components=2))
#新建逻辑回归的对象，其为待训练的模型作为流水线的最后一步
step6 = ('LogisticRegression', LogisticRegression(penalty='l2'))
#新建流水线处理对象
#参数steps为需要流水线处理的对象列表，该列表为二元组列表，第一元为对象的名称，第二元为对象
pipeline = Pipeline(steps=[step1, step2, step3, step4, step5, step6])
```

---

### 自动化调参

**网格搜索**为自动化调参的常见技术之一，`grid_search`包提供了自动化调参的工具，包括`GridSearchCV`类。对组合好的对象进行训练以及调参的代码如下：

```Python
from sklearn.grid_search import GridSearchCV

#新建网格搜索对象
#第一参数为待训练的模型
#param_grid为待调参数组成的网格，字典格式，键为参数名称（格式“对象名称__子对象名称__参数名称”），值为可取的参数值列表
grid_search = GridSearchCV(pipeline, param_grid={'FeatureUnionExt__ToBinary__threshold':[1.0, 2.0, 3.0, 4.0], 'LogisticRegression__C':[0.1, 0.2, 0.4, 0.8]})
#训练以及调参
grid_search.fit(iris.data, iris.target)
```

---

### 持久化

`externals.joblib`包提供了`dump`和`load`方法来持久化和加载内存数据：

```Python
#持久化数据
#第一个参数为内存中的对象
#第二个参数为保存在文件系统中的名称
#第三个参数为压缩级别，0为不压缩，3为合适的压缩级别
dump(grid_search, 'grid_search.dmp', compress=3)
#从文件系统中加载数据到内存中
grid_search = load('grid_search.dmp')
```

---

### 回顾

|包|类或方法|说明|
|:-:|:-:|:-|
|sklearn.pipeline|Pipeline|流水线处理|
|sklearn.pipeline|FeatureUnion|并行处理|
|sklearn.grid_search|GridSearchCV|网格搜索调参|
|externals.joblib|dump|数据持久化|
|externals.joblib|load|从文件系统中加载数据至内存|

**注意**：**组合和持久化**都会涉及`pickle`技术，在`sklearn`的技术文档中有说明，将`lambda`定义的函数作为`FunctionTransformer`的自定义转换函数将不能`pickle`化。

