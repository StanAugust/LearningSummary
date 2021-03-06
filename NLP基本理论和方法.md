## 一. 绪论

### 1.1 NLP的主要过程

​	NLP大体包括了 自然语言理解 和 自然语言生成 两个部分

#### 1.1.1 自然语言理解

​	主要包括两方面： 

**（1） 语言信息的录入，包括语音和文字**

**（2）文本理解**

​	从某种意义上来说，NLP的最终目的是在语义理解的基础上实现相应的操作

​	文本理解的过程可以划分为三个步骤：

​		1）词法分析：确定各个单词的词性（词类）

​		2）句法分析：识别出句子中的各类短语（根据词法分析结果）

​		3）语义分析：确定句中==名词性成分（实体，$Entity$）==与==核心谓语动词==之间的==关系（格，$Case$）==

#### 1.1.2 自然语言生成

​	从抽象的概念层次开始，通过选择并执行一定的语义和语法规则生成文本（语音系统还需要将文本转换成对应的语音表征）

---

### 1.2 NLP面临的困难

​	一是歧义现象的处理

​	二是未知语言现象的处理

---

### 1.3 NLP的基本方法

​	一种理性主义方法，又称基于规则的方法

​	一种经验主义方法，又称基于统计的方法



## 二. 语料库与词汇知识库

### 2.1 语料库

​	语料库是指存放实际使用中**真实出现过**的语言材料的数据库

#### 2.1.1 语料库类型

1. 通用与专用

 	2. 单语与双语

   3. 共时与历时（动态追踪）
   4. 生（原始）与熟（经过加工处理、标注了特定信息）

#### 2.1.2 语料处理的基本问题

​	原始文本需要做一些**预处理**工作

 1. 汉语方面

 2. 英语方面

    **前后有空格的连续字母组成的字符串并不一定就是一个词**

    1）词的前后也不总是存在空格

    ​	① “词+标点”：句点(.)的删除存在问题，比如 $etc.$ 和 $Prof.$。用排除法解决

    ​	② “词+单撇号”：

    ​	③ 由连字符连接多个单词

    2）空格有时不是分界标志

    ​	比如电话号码（$9162\, 8245$）或者多词地名（$New\,York$），即使有空格也想当做一个整体

    **英语中还有一个基本问题就是大小写的问题**

---

### 2.2 词汇知识库

#### 2.2.1 WordNet

​	其最具特色之处就是==根据词义==来组织词汇信息

​	以同义词集合（$Synset$）作为基本的构建单位，每个$Synset$都代表一个基本的语义概念，并且这些集合之间也由各种关系连接。这些关系包括：同义关系、反义关系、上下位关系、整体与部分关系、继承关系、致使语义关系等。

​	如果一个$Synset$没有上位$Synset$，则称之为**独立起始概念**

#### 2.2.2 知网(HowNet)

​	知网是一个以汉语和英语的词语所代表的概念为描述对象，以揭示概念与概念之间以及概念所具有的属性之间的关系为基本内容的常识知识库。



## 三. 语言模型

​	语言模型（$Language\,Model$）试图反映的是字符串$w$作为一个句子出现的概率$p(w)$，其与句子是否合乎语法是没有关系的

​	语言模型的用途主要有两个：① 已知若干个词，预测下一个词；② 决定哪一个词序列的可能性更大

​	语言模型定义：
$$
\begin{aligned}
p(w) &= p(w_1w_2...w_k)\\
	&= p(w_1|w_0)\times p(w_2|w_0w_1)\times\dots\times p(w_{k+1}|w_0w_1w_2...w_k)\\
	&=\prod_{t=1}^{k+1} p(w_t|w_0w_1w_2...w_{t-1})
\end{aligned}
$$

### 3.1 马尔科夫链与二元语法

​	随着句子长度增大，语言模型会遇到两个瓶颈

1. 数据稀疏

 	2. 计算代价大

​	为了解决这两个问题，需要马尔科夫假设来简化语言模型：给定时间线上有一串事件顺序发生，假设每个事件的发生概率**只**取决于前一个事件，那么这串事件构成的因果链被称作==马尔科夫链$(Markov\; Chain)$==

​	在NLP中，事件可以具象为单词，于是基于此假设，语言模型的式子可以简化成：
$$
\begin{aligned}
p(w) &= p(w_1w_2...w_k)\\
	&= p(w_1|w_0)\times p(w_2|w_1)\times\dots\times p(w_{k+1}|w_k)\\
	&=\prod_{t=1}^{k+1} p(w_t|w_{t-1})
\end{aligned}
$$
​	由于每次计算只涉及连续两个单词的二元接续，此时的语言模型称为==二元语法模型$(bigram)$==

---

### 3.2 $n$元语法模型

​	利用类似的思路，可以得到==n元语法（n-gram）==的定义：认为一个词出现的概率只依赖于它前面的$n-1$个词

​	采用最大似然估计$MLE$

---

### 3.3 数据平滑技术

​	如果仅靠语料库来预测，因为实际遇到的句子大部分都在语料库之外（它们的概率都会被当作0），所以会存在**数据稀疏** 的问题，故需要借助数据平滑技术

​	平滑处理的基本思想就是提高低概率，降低高概率，尽量使概率分布趋于均匀

#### 3.3.1 Laplace法则

​	将所有实例（n元组）的出现次数都加1（或者其他数值），从而所有实例出现次数都不会为0

​	为了保证所有实例的概率总和为1，将分母也增加实例的种类数

#### 3.3.2 Good-Turing估计

​	对于任何一个发生r次的n-gram，都假设它发生r*次，这里：
$$
r^* = (r+1)\frac{n_{r+1}}{n_r}
$$

​	$n_r$为训练样本中发生r次的事件的数目

![image-20201021205143553](https://gitee.com/StanAugust/picbed/raw/master/img/20201021205414.png)

#### 3.3.3 绝对折扣和线性折扣

​	在绝对折扣中，所有的非零$MLE$频率用一个常数$\delta$折扣，由此得到的剩余概率被**均匀**分配到未知事件上

​	在线性折扣中，所有的非零$MLE$频率的折扣量与其自身的频率值成线性比例关系

#### 3.3.4 Witten-Bell平滑算法

​	将在训练语料中看到新实例（即第一次出现的实例）的概率值分摊给所有出现次数为零的词

#### 3.3.5 扣留估计

​	把训练数据分成两部分，一部分建立最初的模型，然后另一部分来精炼这个模型

#### 3.3.6 交叉校验

​	 扣留估计算法有个缺点，就是最初的训练数据比较少的时候，得到的概率估计是不可靠的 

​	 交叉校验的思想在于，训练数据的每一部分既作为最初训练数据，又作为留存数据，对两部分数据（以0和1表示）分别训练和平滑，然后根据$n^0_r$相对于$n^1_r$的比率进行加权

#### 3.3.7 删除插值法和Katz回退算法

​	 如果两对词同时出现0次：C(send the) = C(send thou) = 0，按照之前任何一个平滑方法都得到两个概率相等，但是明显前者要大于后者的。

 	删除插值法是这样做的:

![](https://gitee.com/StanAugust/picbed/raw/master/img/20201021211151.png)

​	可以说，删除插值和Katz回退法都使用低阶分布的信息来确定计数为0的n元语法的概率；

​	不同的是：在Katz回退中，只有高阶计数为0时才启用低阶计数，而删除插值中，高阶计数和低阶计数同时起作用。



## 四. 隐马尔科夫模型