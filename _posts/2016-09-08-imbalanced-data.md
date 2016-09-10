---
layout: post
title: "Let's talk imbalanced data in machine learning（一）"
date: 2016-09-10
Stats & CS:true
tag:
- imbalanced data
comments: true

---

之前同事讲SVM分类的时候，就有同事注意到，挨？？你这个分类如果有一类特别多，一类特别少，会出现什么情况？That's a good question.这是大概10年前吧，机器学习领域蛮热门的不平衡数据问题，直到今天，这个问题算解决的差不多了，很难再在此领域发新论文了，所以这领域都出书了。既然该有的都有了，俺就来写个小小的Review，看看能不能帮到大家更好的理解这个问题。那么我习惯从以下几个方面来讲：1.基本问题是什么？2.为什么会出现这样的情况？3.遇到这类问题有啥手段？

## Definition

- What is Class-imbalanced Data?

	Having many more instances of certain classes than others.
	
	我就suppose大家都知道机器学习中一大类问题是classification，我们关注的outcome是categorical的，然后你会发现某些领域中的data在做分类的时候，某一类的instance巨多，然后某一类的instance巨少（暂时以简单的二分类为例），这就是个不平衡数据。
	
- What is Imbalanced Learning?

	Specifically, we define imbalanced learning as the learning process for data representation and information extraction **within severe data distribution skews** to develop **effective decision boundaries** to support the decision-making process.
	
	嗯，这段读起来蛮拗口的，啥叫不平衡学习？首先，遇到这样的数据，我们不管不顾，继续按原先的方式建立一个分类模型，然后去预测，可以吗？当然不可以。为啥？一会儿讲哈。这个时候我们需要有一套针对不平衡数据问题的学习方法，也就是，我们要利用这严重的Y分布不平衡的数据去找到一个有效的决策边界（原先的决策已经不适用啦），这就是不平衡学习。
	
- Why imbalanced learning has drawn a significant amount of interest?

	为啥我们要关注此类问题啊？尤其是过去10年，这领域发了不少paper。

	举个🌰：
	
	在生物医药领域，我们想用机器学习的方法根据一些predictors去预测一个人是否患癌症，现在不是也有好多医学影像数据吗！图像处理完也是一堆数值变量！anyway，我们要预测一个人是否患癌症。那么这个时候你会发现，我们的traing set里面，假设总共10000个人（这在生物医药圈儿样本量可真大！），可能只有20个人发病（你想想癌症的发病率也知道，当然是健康人远多于患病的人）。好了！样本量真大，我要建模了，肯定效果特别好！于是你建了个Decision Tree, Neural Networks, SVM, Logistic Regression，甚至你用了高大上的组合算法，random forest, Adaboost。然后拿来了testing set看看模型的performance吧，咦，我这一算，overall accuracy 99.8%！真是几乎完美的模型啊！但单纯的你可能不知道，有俩指标叫TPR和TNR，分别去算算看～
	
	- What's TPR and What's TNR?
	
		TPR: True Positive Rate
		
		TNR: True Negative Rate
		
		在不平衡问题中，minority class又叫positive class（正类），相应地，majority class又叫negative class（负类）。正类是我们着重要关注的。所以TPR就是正类样品中被正确地归到正类所占的比例。TNR就是负类样品中被正确地归到负类所占的比例。呦！我赶紧算算去！一算，TPR ＝ 10%，TNR ＝ 100%。啥玩意儿，我擦擦眼睛！嗯，没错，你没看错，正类里，你就分对了俩，其他的全被错分到负类了，然后你还被总精度99.8%给忽悠了。
		
	赶紧想想我们要解决啥问题来着？预测一个人是否患癌症。这样一来，你只识别出2个人，其他的都被误认为健康人，妈呀！这可是人命关天的事，我们要帮病人早发现早治疗啊，模型分错了，治病耽误了哪行。嗯，我们作为责任感爆棚的统计工作者，必须要想办法解决这个问题。
	
	还有很多领域，比如text mining里的垃圾邮件识别啦，一些和军事安全有关的数据，都存在此类问题。
	
	
## The nature of class imbalance problem

我们很直观地了解了问题是什么样子的，接下来就要去想为什么会出现这种情况呢？为什么模型在少数类的预测上表现的如此差？

- 首先，在有监督的分类问题下，我们先要知道我们训练的模型的表现是怎样被评估的。

	训练出来的模型，需要去评估其表现，这是常理。但这对于不平衡数据，显得更为重要了。我们分别算了TPR和TNR，发现这俩指标的值相差甚远，也就是错误率在两类样本上非常不对称。然而，这打破了普通机器学习算法的基本假定。普通机器学习算法一视同仁，不会因为你这类很少就会多关注你一毛钱。所以算法的基本假定是类别平衡～所以它优化的是总精度，而不是分开来优化。总精度是怎么算的？分对的样本数量除以样本量。它有一个潜在的weight，也就是各类的占比。所以你才会看到overall accuracy这么高的情况下，TPR这么低。
	
- 其次，样本点的绝对稀少也是个问题。刚刚举的例子还有20个呢！如果只有一个两个，那么问题就更严重了，这就是训练数据绝对稀少。

- 如果存在noise，那么在不平衡问题下，noise会更加被放大。

- 最后，从算法层面解释一下。很多算法无法去优化我们需要评估的指标，尤其在不平衡问题下，我们更多地想去优化TPR，然而大多数算法优化的是标准的overall accuracy。大部分算法是不具备识别少数类样本的内在模式的功能滴，它们只能识别出多数类样本的模式。尤其像决策树这样分而治之的模式，自顶向下，不断对数据集进行划分，随着划分，在每个分支节点上的数据量越来越少，少数类样本点会更加出现绝对稀少的情况，因此就更学习不到少数类的模式。所以，决策树这样的模型，更不适合imbalanced data啦。

FAQ：只讲了有监督，那么无监督学习会有不平衡问题吗？

A：答案是肯定的！无监督也存在不平衡问题。像clustering，虽然我们不能观测到Y，但是它还是有一个潜在的unobserved Y，所以这个时候，如果某类样本点很少的话，聚类也是会出问题的。但研究这个问题的人很少，因为我们无法给出一个比较好的criterion去衡量这个不平衡。以后会不会有人去探究这个问题呢？本媛媛先去搜搜论文！

讲到这里，只是抛砖引玉出了这个问题，有了一个基本概念了吧？下一篇会具体从data-level和algorithm-level继续讲述如何解决这个问题～如果有说错的，请评论告诉我吼～
