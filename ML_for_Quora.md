##  Machine Learning for Q&A Site: The Quora Example

ppt[在这里](http://slideshare.neatcn.com/xamat/machine-learning-for-qa-sites-the-quora-example)

Quora的Mission：**To share and grow the world's knowledge.**

> * Millions of questions & answers
> * Millions of users
> * Thousands of topics
> * ...

### Feed Ranking

Quora主要考虑的三个因素：Relevance、Quality和Demand。

Quora做推荐的一个最核心的问题就是Personalized Feed Ranking。

Quora是以问题、答案与主体为核心把“知识”串联起来，然后给予用户的顶和踩等动作来划分内容质量，最后再通过人和问题的Follow关系让知识在社区内流动起来

Quora Feed Ranking的首要目标是确保推送进用户Feed的内容应该是和用户兴趣高度相关的，其次还要考虑的包括拥护之间的Follow关系以及互动（Social relevance），另外还有时间因素，比如一些和热点事件相关的问答，也应该及时地推送进用户Feed。

1. 目标：Present most insteresing stories for a user at a given time

  > - Interesting = topical relevance + social relevance + timeliness
  > - Stories = questions + answers

2. 主要使用的是个性化的learning-to-rank方法

3. 相比于时间排序（time-ordered），相关度排序大大提升了用户参与度。

4. 面临的挑战：
  > - potentially many candidate stories
  > - real-time ranking
  > - optimize for relevance

### Feed dataset: impression logs

![](/raw/fig1.png?raw=true)

围绕这些基础行为数据，使用一个“行为加权函数”来预测用户对一个story的感兴趣程度。
有两种可选方法：

- 一种方法是把所有行为数据放在一个回归模型里面直接预测最终值；
- 另外一种是先分别预测每个动作的可能性（比如顶、阅读、分享等），然后再综合起来加权求和。

第一种方法简单，但可解释行稍差，第二种方法可赢更好的利用每个动作信号，但需要给每个动作了一个分类器，计算消耗大。

### Quora主要使用的三类模型

- Linear
  > - simple, fast to train
  > - manual, non-linear transforms for richer representation

- Decision trees
  > - learn non_linear representations

- Tree ensembles
  > - Random forests
  > - Gradient boosted decision trees
  
### 特征工程的重要性
Quora最主要的特征包括：

- user(e.g. age, country, recent activity)
- story(e.g. popularity, trendiness, quality)
- interactions between the two (e.g. topic or author affinity)

Quora比较特别的是它的数据模型相对其他网站更复杂，之间的关系也更多样化。比如从用户角度看，既可以follow其它用户User，又可以follow 问题Question，还可以follow主题。

- Follow用户接收到的信息范围更广也更多样化，惊喜内容很可能就是来自于自己关注的有趣的用户，但也可能最容易制造不相关的内容噪音，这块的最重要工作是用户专业度的评估。
- Question/Answer是Quora最核心的内容元素，也是驱动Quora体系里知识流动的原力，这块的主要工作是引导更多的高专业度用户来贡献优质答案，另外就是如何激发生产出更多的好问题（甚至是自动生成问题），要计算answer ranking，还有要做反spam的工作。

- Topic是对一个主题内容的聚合，Topic在Quora的信息架构里面承载着极其重要的角色，是知识架构的骨架，Quora管这个叫做Topic Network。如何构建Topic Network本身就是一个非常大的挑战。另外还需要解决的问题包括，如何把Topic下潜在的优质问题发掘出来，以及如何把水问题降权和过滤／合并重复问题等。

### Answer Ranking

Goal: Given a question and n answers, come up with the ideal ranking of those n answers.

Quora主要考虑了下面三大维度来进行Ranking计算：
 
 - 答案内容本身的质量。应该是有事实根据的，有可服用价值的，提供了解释说明的，进行了好的格式排版等等。
 - 互动。包括顶／踩、评论、分享、收藏、点击等等。
 - 回答者本身的一些特征，比如回答者在问题领域的专业度。
 
另外这块包括非个性化与个性化的两部分。某些类问题的排序是非个性化的，最好的答案对所有用户而言都是一致的，而另外一些问题则是个性化的。对于每个人而言最好的答案会有自己个性化的判断。
 
### Ask2Answers

A2A是Quora产品里面非常重要的一个功能，本来Quora是可以直接把相关问题推荐给系统认为的合适的回答者的，Quora最开始也是这么做的，但系统自动做这事儿显然不如发动群众人肉邀请回答来得感觉好。

A2A操作增强了仪式感，让被邀请者有种被人需要的感觉，心理上很满足。另外这也是一种社交动作，社交的精髓之一就是为用户制造“装逼”的便利，回答问题前很随意的“谢邀／泻药”，一切尽在不言中。

Quora把A2A也model成了一个机器学习问题：

Given a question and a viewer rank all other users based on how "well-suited" they.

well-suited = likelihood of viewer sending a request + likelihood of the candidate adding a good answer。既要考虑浏览用户发送邀请的可能性，又要考虑被邀请者受邀回答的可能性。

### Topic Network
Quora花了很大力气来正确引导用户给内容打标签，持续不断坚持这项工作的好处开始逐渐显露出来：
- 随着用户群体的扩大，Topic正在呈现出多样化的势头
- 很多领域都自组织出了相当不错的层级知识结构

Quora需要找出某个领域的专家，然后通过产品引导这些专家在这个领域里贡献更多的优质答案。

Quora会考虑用户在某个领域里回答问题的多少，接收到的顶、踩、感谢、分享、收藏及浏览等数据。另外还有一很重要的是专业度的传播效应，比如某个知名专家在该领域对某个答案顶了一下，那么这个答案作者在该领域很可能具备较高的专业度。


从这片文章里发现了Quora专门用来发布对自己平台数据分析的博客：https://data.quora.com。 看了一下，只有几篇文章，我要试着翻译一下。。。
