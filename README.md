# time_series_anomaly_detection
时序异常检测的基础知识

***

## 定义
异常是数据中不符合正常行为定义的模式。
与异常检测相关的另一个主题是新颖性检测[Markou and Singh 2003a；2003b；Saunders and Gero 2000]，其目的是检测数据中以前未观察到的（突发的、新颖的）模式，例如，新闻组中讨论的新主题。新模式和异常之间的区别在于，新模式通常在被检测到后被纳入正常模型。

***


## 挑战
1. 定义一个包含所有可能正常行为的正常区域是非常困难的。此外，正常行为和异常行为之间的界限通常并不精确。因此，靠近边界的异常观测实际上可能是正常的，反之亦然。
2. 当异常是恶意行为的结果时，恶意对手通常会调整自己，使异常观察看起来像正常的，从而使定义正常行为的任务更加困难。
3. 在许多领域中，正常行为不断演变，目前的正常行为概念在未来可能不具有足够的代表性。
4. 对于不同的应用领域，异常的确切概念是不同的。例如，在医学领域，与正常值的微小偏差（例如体温波动）可能是异常，而股市领域的类似偏差（例如股票价值波动）可能被视为正常。因此，将一个领域开发的技术应用到另一个领域并不简单。
5. 异常检测技术所用模型的培训/验证所需的标记数据的可用性通常是一个主要问题。
6. 通常，数据包含的噪声往往与实际异常相似，因此难以区分和消除。

***
## 常见异常检测方法
按照训练集是否包含异常值可以划分为**异常值检测（outlier detection）及新颖点检测（novelty detection）**
按照异常类别的不同，异常检测可划分为：**异常点检测（如异常消费用户）、上下文异常检测（如时间序列异常）、组异常检测（如异常团伙）**
按照学习方式的不同，异常检测可划分为：**有监督异常检测（Supervised anomaly Detection）、半监督检测（semi-Supervised Anomaly Detection）及无监督异常检测（Unsupervised Anomaly Detection）**
### 无监督异常检测
1. 基于聚类的方法
基于聚类的异常检测方法通常依赖下列假设
1） 正常数据实例属于数据中的一个簇，而异常数据实例不属于任何簇；
2） 正常数据实例靠近它们最近的簇质心，而异常数据离它们最近的簇质心很远；
3） 正常数据实例属于大而密集的簇，而异常数据实例要么属于小簇，要么属于稀疏簇；通过将数据归分到不同的簇中，异常数据则是那些属于小簇或者不属于任何一簇或者远离簇中心的数据。
- 将距离簇中心较远的数据作为异常点：这类方法有**SOM、K-means、最大期望（expectation maximization，EM）及基于语义异常因子（semantic anamaly factor）算法等**；
- 将聚类所得小簇数据作为异常点：代表方法有**K-means聚类**
- 将不属于任何一簇作为异常点：代表方法有**DBSCAN、ROCK、SNN聚类**

2. 基于统计的方法 
基于统计的方法依赖的假设是数据集服从某种分布( 如正态分布、泊松分布及二项式分布等) 或概率模型，通过判断某数据点是否符合该分布/模型( 即通过小概率事件的判别) 来实现异常检测
- **参数方法：**由已知分布的数据中估计模型参数（如高斯模型），其中最简单的参数异常检测模型就是假设样本服从一元正态分布，当数据点与均值差距大于两倍或三倍方差时，则认为该点为异常
- **非参数方法：**在数据分布未知是，可绘制直方图通过检测数据是否在训练集所产生的直方图中来进行异常检测，还可以利用数据的变异程度（如均值、标准差、变异系数、四分位数间距等）来发现数据中的异常点数据。

3. 基于深度的方法
该方法将数据映射到k维空间的分层结构中，并假设异常值分布在外围，而正常数据点靠近分层结构的中心（深度越高）
- **半空间深度法（ISODEPTH法）**，通过计算每个点的深度，并根据深度值判断异常数据点
- **最小椭球估计（minimum volume ellipsoid estimator，MVE）法**，根据大多数数据点（通常为 > 50%）的概率分布模型拟合出一个实线椭圆形所示的最小椭圆形球体的边界，不在此边界范围内的数据点将被判断为异常点
- **孤立森林**：是基于 Ensemble 的异常检测方法，因此具有线性的时间复杂度。基本思想是：通过树模型方法随机地切分样本空间，那些密度很高的簇要被切很多次才会停止切割（即每个点都单独存在于一个子空间内），但那些分布稀疏的点（即异常点），大都很早就停到一个子空间内了。

4. 基于分类模型
是寻找一个超平面将样本中的正例圈出来，预测就是用这个超平面做决策，在圈内的样本就认为是正样本。
注：由于核函数计算计较耗时，在海量数据的场景用的并不多
- **基于密度**，该方法通过计算数据集中各数据区域的密度，将密度较低区域作为离群区域。经典的方法为：局部离群因子（local outlier factor，LOF），LOF与传统异常点非彼即此定义不同，将异常点定义局域是异常点，为每个数据赋值一个代表相对于其领域的LOF值，LOF越大，说明其领域密度较低，越有可能是异常点，但是在LOF中难以确定最小近邻域，且随着数据维度的升高，计算复杂度和时间复杂度增加。
- **基于距离**，其基本思想是通过计算比较数据与近邻数据集合的距离来检测异常，正常数据点与其近邻数据相似，而异常数据则有别于近邻数据。

5. 基于偏差的方法
当给定一个数据集时，可通过**基于偏差法**找出与整个数据集特征不符的点，并且数据集方差会随着异常点的移除而减少。该方法可分为**逐个比较数据点的序列异常技术和OLAP数据立方体技术**。目前应用较少

6. 基于重构的方法
代表方法为**PCA**，PCA在异常检测方面的做法，大体有两种思路：一种是将数据映射到低纬特征空间，然后在特征空间不同维度上查看每个数据点跟其他数据的偏差；；另一种是将数据映射到低纬度特征空间，然后由低纬特征空间重新映射回原空间，尝试用低纬特征重构原始数据，看重构误差的大小

7. 基于神经网络的方法
代表方法有**自动编码器( autoencoder，AE) ，长短期记忆神经网络（LSTM）**等。

- **LSTM**可用于时间序列数据的异常检测：利用历史序列数据训练模型，检测与预测值差异较大的异常点。

- **Autoencoder异常检测** Autoencoder本质上使用了一个神经网络来产生一个高维输入的低维表示。Autoencoder与主成分分析PCA类似，但是Autoencoder在使用非线性激活函数时克服了PCA线性的限制。算法的基本上假设是异常点服从不同的分布。根据正常数据训练出来的Autoencoder，能够将正常样本重建还原，但是却无法将异于正常分布的数据点较好地还原，导致其基于重构误差较大。当重构误差大于某个阈值时，将其标记为异常值。
