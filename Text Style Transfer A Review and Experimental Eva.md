Style define:在保留文本内容的基础上，语言表现形式上的变化。例如正式，非正式 。    

早期的TST研究主要基于平行语料库

TST目标：For example, most of these tasks aim to generate fluent texts with minimal grammatical errors, and the generated texts should contain specific intended content. TST inherits these common goals and adds
another objective: generating text in specific styles.



# 2相关领域对比

与图像中的神经风格迁移相似，可以使用cnn将图像的内容与风格分离出来。将普通图片的内容特征与艺术品的风格特征结合  

与图像的风格迁移区别：

- 图像的风格容易识别，文本的风格不容识别
- 图像的风格与内容特征容易通过不同cnn层分离出来，而文本的风格与特征不容分离
- 图像中任意改变一个像素很难被察觉到，但文本中任意修改一个词则容易影响文本的意思



# 3风格定义

- 语义学上的观点：
 - 风格是一个直观的概念，涉及某些语义的传递方式。 
 - 内容是客观的，他是作者想要传递的观点。

- TST的观点：
  - TST以数据驱动的方式来定义风格，TST通常将风格视为特定语料库的属性

# 4TST分类

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/taxonomyOfTST.png)

- 按数据集分类
  - 有监督平行语料
  - 有监督非平行语料
  - 纯无监督：语料库中的文本没有风格标签

- 解纠缠方式
  - 直接风格内容解纠缠（解耦）：短语替换的方式
  - 间接风格内容解纠缠（解耦）：将原语句的内容隐表示与目标风格的隐表示结合。相关技术：后向翻译，对抗学习，可控文本生成
  - 不解耦：

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/DataSet.png)

## Seq2Seq 平行语料

S2S是基于编解码结构的，将原风格的语句喂给编码器，解码器输出对应的目标风格语句。缺点是需要平行语料库，解决方法数据增强，构建伪平行语料库。数据增强方式，翻译到另一种语言再翻译回来，挑选符合要求的。

## 直接风格关键词替换

相关模型：Delete-Retrieve-Generate，简单来说就是把体现风格的短语删除，再替换成目标风格的短语。优点是model可解释性强，比较简单。缺点：局限性高，使用像情感这种风格，但不适用于正式非正式风格迁移。

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/DeleteRetreiveGenerate.png)

## 对抗学习

目标：1）生成与真实数据无差别的句子 2）移除句子的隐表示中的风格属性

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/paperImageadversarialModel.png)

上图中左边是多解码器模型，右边是风格嵌入模型。context z 是通过对抗网络输出的内容表示，不含风格特征。风格分类器，用来分离出风格特征。



为提高内容的保留又提出了以下模型，通过添加cycle-consistency loss 来保留句子的语义

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/ReconstructionLoss.png)

**缺点：** 1）对抗训练在解耦风格与内容并不实用； 2）依赖风格分类器

## 后向翻译

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/Back-translation.png)

缺点同对抗学习方法一样：无法保证风格与内容的充分解耦

## 属性控制生成 Attribute Control Generation

两种策略：1）间接的解耦，使得隐表示只包含内容信息，同时学习风格属性编码； 2）不解耦或限制隐表示z只包含内容信息，同时学习风格属性编码。

这种方法十分依赖风格分类器（style classifier)

1)解耦

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImageAttribute.png)

2）不解耦

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/attributes.png)

## 编辑隐表示 Entangled Latent Representation Editing

不解耦风格与内容，二十直接编辑自编码机学习到的隐表示。

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/laten.png)

## 	强化学习

使用奖励函数代替损失函数。policy gradient 算法使得TST更加容易，因为不用担心由自动解码过程导致的离散训练难度The policy gradient algorithm makes training easier without worrying about the difficulty of discrete
training caused by the automatic regression decoding process.

缺点：由于梯度采样的差距高，导致训练结果不稳定

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/dual.png)

## 纯粹的无监督方法

不需要风格特定的语料库，可以使用不同风格混合的语料库。但目前该方法主要用于情感转化任务

## 其他方法

。。。



# 资源和评估方法

## 数据集

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/dataset%20statis.png)



## tasks

写作风格迁移； 情感迁移；  正式非正式转换；

 标题风格迁移； 正式，浪漫，幽默风格转换 ；性别风格迁移；

offensive语言转换；多属性风格迁移

## 自动评价

1. 风格迁移的能力
2. TST之后的内容保留量
3. 风格迁移的流畅度

**风格强度**使用风格分类器来判断转移的风格正确性,Style transfer accuracy

**内容保留** BLEU；source-BLEU; Cosine Similarity(CS); Word Overlap;WO(content preservation);

**流畅度**使用这个模型来判断流畅度Kneser-Ney language mode,PPL(perplexity score)评价流畅度

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/yelp.png)

![](https://raw.githubusercontent.com/berserk-112/paperNote/main/paperImage/gyafc.png)



