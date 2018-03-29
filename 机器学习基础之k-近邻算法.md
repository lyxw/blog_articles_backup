# 机器学习基础之k-近邻算法

### 0x01 k-近邻算法概述

**K-近邻算法**(k-Nearest Neighbor，KNN)，是一个理论上比较成熟的方法，也是最简单的机器学习算法之一。该方法的基本思路是：如果一个样本在特征空间中的k个最相似(即特征空间中最邻近)的样本中的大多数属于某一个类别，则该样本也属于这个类别。

简单点说，k-近邻算法采用测量不同特征值之间的距离方法进行分类。其优点是`精度高`、`对异常值不敏感`、`无数据输入假定`；缺点是`计算复杂度高`、`空间复杂度高`；适用数据范围为`数值型`和`标称型`。

> **工作原理**：存在一个样本数据集合（训练样本集），并且样本集中每个数据都存在标签，即我们知道样本集中每一数据与所属分类的对比关系。输入没有标签的行数据后，将新数据的每个特征与样本集中数据对应的特征进行比较，然后算法提取样本集中特征最相似数据（最近邻）的分类标签。一般来说，我们只选择样本数据集中前k个最相似的数据，通常k是不大于20的整数，最后，选择k个最相似数据中出现次数最多的分类，作为新数据的分类。

### 0x02 k-近邻算法一般流程

此处用手写识别系统来说明。用到的数据可以从此处获取 ——> [数据集](https://lyxw.github.io/download/kNN.zip)

#### 1、收集数据

通过多种渠道收集数据，一般为文本文件、统一的数据格式文件等。

此处直接采用上面的数据集。

#### 2、准备数据

将数据文件转换为分类器使用的向量格式。

> 需要注意的是，同等权重下，数字差值最大的属性对计算结果的影响最大，因此，需要对这些值进行**数值归一化**，即将取值范围处理为在0到1或者-1到1之间。

基本公式：`newValue=(oidValue-min)/(max-min)`

```python
def autoNorm(dataSet):
    minVals = dataSet.min(0)	# 将每列的最小值放在minVals中  
    maxVals = dataSet.max(0)
    ranges = maxVals -minVals	# 计算可能的取值范围
    normDataSet = zeros(shape(dataSet))		# 创建新的返回矩阵
    m = dataSet.shape[0]	 # 得到数据集的行数  shape方法用来得到矩阵或数组的维数  
    normDataSet = dataSet - tile(minVals, (m, 1))	# 将原来的数组minVals扩充成m行1列的数组，矩阵中所有的值减去最小值 
    normDataSet = normDataSet/tile(ranges, (m, 1))	# 矩阵中所有的值除以最大取值范围进行归一化 
    return normDataSet, ranges, minVals
```

由于文件中的值已经在0和1之间，故不需要进行数值归一化。

首先将图像格式化处理为一个向量，即把一个32×32的二进制图像矩阵转换为1×1024的向量。

```python
def img2vector(filename):
    returnVect = zeros((1, 1024))   # 首先创建一个1x1024的numpy数组，用0填充
    fr = open(filename)
    for i in range(32):
        lineStr = fr.readline()		# 循环都出文件前32行
        for j in range(32):
            returnVect[0, 32 * i + j] = int(lineStr[j])		# 将每行的头32个字符值存储在numpy数组中
    return returnVect
```

#### 3、分析数据

检查数据，确保符合要求。

在Python命令行中输入下列命令测试`img2vector`函数，然后与文本编辑器打开的文件进行比较。

```python
>>> testVector = kNN.img2vector('testDigits/0_13.txt')
>>> testVector[0, 0:31]
array([ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  
		0.  1.  1.  1.  1.  0.  0.  0.  0.  0.  0.  0.  0.  
		0.  0.  0.  0.  0.])
>>> testVector[0, 32:63]
array([ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  1.  
		1.  1.  1.  1.  1.  1.  0.  0.  0.  0.  0.  0.  0.  
		0.  0.  0.  0.  0.])
```

文本编辑器查看到的内容如下：

```python
00000000000000111100000000000000
00000000000011111110000000000000
00000000000111111111000000000000
00000000001111111111100000000000
00000000001111111111100000000000
00000000111111111111110000000000
00000000011111111111111000000000
00000000111111110111111000000000
00000001111110000000111100000000
00000001111110000000011100000000
00000011111110000000011100000000
00000011111100000000011100000000
00000011111100000000011100000000
00000001111100000000001111000000
00000001111000000000000111000000
00000011110000000000000111000000
00000011110000000000000111000000
00000011110000000000000111000000
00000011110000000000000111000000
00000011110000000000000111000000
00000000111000000000000011100000
00000000111000000000000011100000
00000000111100000000000111100000
00000000111100000000000111100000
00000000111110000000011111100000
00000000011111000000111111000000
00000000011111111111111110000000
00000000001111111111111111000000
00000000000111111111111111000000
00000000000111111111111110000000
00000000000011111111111000000000
00000000000000111111110000000000
```

#### 4、实施kNN算法

##### kNN算法基本设计过程

对未知类别属性的数据集中的每个点依次执行以下操作：

* 计算已知类别数据集中的点与当前点之间的距离，用的是`两点间距离公式`；

* 按照距离递增次序排序；

* 选取与当前点距离最小的k个点；

* 确定前k个点所在类别的出现概率；

* 返回前k个点出现频率最高的类别作为当前点的预测分类。

##### kNN算法具体设计

在这里用到两个模块：科学计算包`numpy`，运算符模块`operator`，详细设计过程及代码如下：

```python
def classify0(inX, dataSet, labels, k):
    dataSetSize = dataSet.shape[0]		# 得到数据集的行数，shape方法用来得到矩阵或数组的维数
    diffMat = tile(inX, (dataSetSize, 1)) - dataSet		# 将原来的数组扩充成dataSetSize行1列的数组，计算目标与训练数值之间的差值
    sqDiffMat = diffMat ** 2		# 计算差值的平方
    sqDistances = sqDiffMat.sum(axis = 1)	 # 计算差值平方和 
    distances = sqDistances ** 0.5		# 计算距离 
    sortedDistIndicies = distances.argsort()		# 得到排序后坐标的序号，argsort方法得到矩阵中每个元素的排序序号  
    classCount = {}
    for i in range(k):
        voteIlabel = labels[sortedDistIndicies[i]]		# 找到前k个距离最近的坐标的标签
        classCount[voteIlabel] = classCount.get(voteIlabel, 0) + 1		# 在字典中设置键值对（标签：出现的次数）。如果voteIlable标签在classcount中就用它的值加1否则就是0+1 
    sortedClassCount = sorted(classCount.iteritems(), key = operator.itemgetter(1), reverse = True)		# 对字典中的类别出现次数进行排序，classCount中存储的是label出现的次数，key=operator.itemgetter(1)选中的是value，也就是对次数进行排序，reverse = True表示降序排列
    return sortedClassCount[0][0]
```

#### 5、测试算法

错误率是常用的评估方法，主要用于评估分类器在某个数据集上的执行效果。

使用提供的部分数据集作为测试样本，测试样本与非测试样本的区别在于测试样本是已经完成分类的数据，如果预测分类与实际类别不同，则标记为一个错误。

通过大量的测试数据，获取分类器给出的错误结果次数，用错误结果次数除以测试执行的总数可以得到分类器的错误率。完美的分类器错误率为0，做差的分类器错误率为1.0。

手写数字识别系统测试代码：

```python
def handwritingClassTest():
    hwLabels = []
    trainingFileList = listdir('trainingDigits')	# 获取目录文件
    m = len(trainingFileList)
    trainingMat = zeros((m, 1024))		# 创建m行1024列训练矩阵，每行存储一个图像
    for i in range(m):
        fileNameStr = trainingFileList[i]
        fileStr = fileNameStr.split('.')[0]
        classNumStr = int(fileStr.split('_')[0])	# 从文件名中解析出分类数字
        hwLabels.append(classNumStr)
        trainingMat[i, : ] = img2vector('trainingDigits/%s' % fileNameStr)
    testFileList = listdir('testDigits')
    errorCount = 0.0
    mTest = len(testFileList)
    for i in range(mTest):
        fileNameStr = testFileList[i]
        fileStr = fileNameStr.split('.')[0]
        classNumStr = int(fileStr.split('_')[0])
        vectorUnderTest = img2vector('testDigits/%s' % fileNameStr)
        classifierResult = classify0(vectorUnderTest, trainingMat, hwLabels, 3)
        print "the classifier came back with: %d, the real answer is: %d" % (classifierResult, classNumStr)
        if (classifierResult != classNumStr):
            errorCount += 1.0
    print "\nthe total number of errors is: %d" % errorCount
    print "\nthe total error rate is: %f" % (errorCount / float(mTest))
```

在Python命令提示符中输入`kNN.handwritingClassTest()`，测试该函数的输出结果。依赖于机器速度，加载数据集可能需要花费很长时间，然后函数开始依次测试每个文件，输出结果如下所示：

```python
>>> kNN.handwritingClassTest()
the classifier came back with: 0, the real answer is: 0
the classifier came back with: 0, the real answer is: 0
.
.
the classifier came back with: 7, the real answer is: 7
.
.
the classifier came back with: 8, the real answer is: 8
the classifier came back with: 6, the real answer is: 8
.
.
the classifier came back with: 9, the real answer is: 9

the total number of errors is: 11

the total error rate is: 0.011628
```

可以看到k-近邻算法识别手写数字数据集的错误率为**1.2%**。改变变量`k`的值、修改函数`handwritingClassTest`随机选取训练样本的数目都会对k-近邻算法的错误率产生影响。

### 0x03 kNN算法实际使用

首先需要输入样本数据和结构化的输出结果，然后运行k-近邻算法判定输入数据分别属于哪个分类，最后应用对计算出的分类执行后续的处理。

k-近邻算法是基于实例的学习，使用时必须有接近实际数据的训练样本数据。

必须保存全部数据集，消耗大量存储空间，由于必须对数据集中的每一个数据计算距离值，实际中会耗费大量时间。

k-近邻算法另一个缺陷是无法给出任何数据的基础结构信息，无法知晓实例样本和典型实例样本具有的特征。

