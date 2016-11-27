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
