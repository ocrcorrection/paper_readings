# Multi-Input Attention for Unsupervised OCR Correction

# 一、数据

>  on two corpora of historical newspaper and books 

分别来自一个报纸语料库和一个书籍语料库，也就是RDD和TCP。（作为训练集和测试集）

每一个训练集和测试集的样例，都是遵循扫描的图片文档布局的一行。

43%RDD在RDD中有witnesses, 64%TCP在TCP中有witness。

RDD每行平均长度42.4个字母，TCP平均53.2个



>  two million issues from the Chronicling America collection of historic U.S. newspapers, which is the largest public-domain full-text collection in the Library of Congress; and three million public-domain books in the Internet Archive. 

200w来自美国历史报纸的数据和300w来自internet archive的公共领域书籍。（作为witness）

在CA中，44%的行与至少一个其它witness对齐。



## 使用原因：

> severe recognition errors in corpora with low quality of printing and scanning or physical deterioration often hamper accessibility

语料库中，因为低质量的印刷和扫描以及物理上的损坏，经常影响OCR实际可行性，造成严重的识别错误。（需要后纠正的原因以及针对的材料，纠正的就是语料库）

监督学习需要较高的人力成本，集成方法中，基于选举的集成需要孩子少有一个输入是对的，分类方法则也需要人类标注。

> Our proposed method is based on the observation that significant number of duplicate
> and near-duplicate documents exist in many cor-pora (Xu and Smith, 2017), resulting in OCR output containing repeated texts with various quality.

OCR的输出中存在大量有着不同质量的重复文本。

> We propose to map each erroneous OCR’d text unit to either its high-quality duplication or a consensus correction among its duplications via bootstrapping from an uniform error model. 

模型目标：将出现错误的文本单元映射为高质量的重复部分或者一个在所有复制上的一致纠正，通过自一个统一错误模型基础上引导。

基线纠正系统：Bahdanau et al., 2015

多输入的注意力模型来产生一个统一结果仅仅用来解码。

average attention combination(是给横向不同输入以相同注意力，还是纵向顺序的输入以相同注意力？要看下面方法中的介绍)

one-best?（之前的问题。这玩意儿应该就来自上面那个基线纠正系统）把这个应用在了RDD和TCP上。

同时还把这个OCR应用在其它公众领域报纸（the Library of Congress）和书（from the Internet Archive）来获得各种复制作为见证，让每一行都有可行的见证。

不是人工标注，是通过一个已经被验证有效的OCR来作为one-best。

一个多输入的注意力机制，包含在多个序列上同时对齐，纠正和选举以完成一致性的解码，比已经存在的集成方法更为有效和高效。

一个能够使用或不适用重复版本来纠正文本的方法，大多数已知方法只能解决其中一种情况。

## 预处理

对于监督学习的训练和测试：
对于1384项来自RDD的手写文本和934本TCP的书，使用了其手工抄写本与其onebest训练的OCR转录本对齐。这些手写本独立来自现有主人（强调字体等方面的不同，而非相同），都是在公共领域和由英文完成。尽管作为witness的Chronicling America和Internet Archive包含很多非英文文本。

为了获得更多对于OCR行的准确读取的参照，

对于每一个RDD项都同RDD其它项以及CA的报纸进行了对齐。

对于每一个TCP项都同TCP其它项以及IA的书籍进行了对齐。

### 对齐机制

为了更有效进行对齐，使用了早先的文本重用分析的方法。

（文本重用分析这个应该就是之前疑惑的寻找相似文本的方法，见下方）

1. 制作了基于5gram的倒排索引（gram依旧不清楚单位是什么，暂时根据hashes of word 5-grams猜测为词），就得到了在同一个倒排表的不同页面对。

2. 拥有超过5个5字hash的页面，使用能同等代价进行插入删除替换的Smith-Waterman算法进行对齐，最后返回各对页面中最大对齐的子序列。

返回的最大对齐子序列最少有5行RDD或TCP的文本会被输出。

对于每一个训练和测试集中的行，除了实际的手写文本，还有0或更多witness（也就是上面的最大子序列）会被用于文本审查。





# 二、模型

- 该论文的核心模型主要就是结合RNN+attention
- 中间涉及到LMR(语言模型)， majority vote, 贪心算法，高斯分布，bootstraping

![ ](https://raw.githubusercontent.com/yaolinxia/img_resource/master/papers/commonsense/QQ图片20190128202241.png)



![ ](https://raw.githubusercontent.com/yaolinxia/img_resource/master/papers/commonsense/QQ图片20190128202253.png)

![ ](https://raw.githubusercontent.com/yaolinxia/img_resource/master/papers/commonsense/QQ图片20190128202257.png)

![ ](https://raw.githubusercontent.com/yaolinxia/img_resource/master/papers/commonsense/QQ图片20190128202303.png)







![ ](https://raw.githubusercontent.com/yaolinxia/img_resource/master/papers/commonsense/QQ图片20190128202307.png)







# 三、实验





# 四、结论





# 五、后续











