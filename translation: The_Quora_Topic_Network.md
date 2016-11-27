## The Quora Topic Network

[原文](https://data.quora.com/The-Quora-Topic-Network-1)

Authors: Don van der Drift and Shankar Iyer

### Introduction

Topics play several crucial roles in Quora’s ecosystem of knowledge. For example, people follow topics to indicate their interests, which helps us show them content that they'll find valuable and engaging. Meanwhile, when people add questions, they tag them with relevant topics so that the question can be channeled to answerers who have relevant expertise, as well as to those who want to learn more about the matter. To complement this, people identify topics in which they have specialist knowledge and about which they can provide compelling answers, either on their profile pages or through their topic bios. This is definitely not an exhaustive list: in these and many other ways, topics form an important organizational backbone for Quora's corpus of knowledge.

This backbone will only become more essential as that corpus continues to grow. Early on, Quora's Silicon Valley roots allowed it to quickly become a great platform for questions on Startups and Entrepreneurship. Over the past few years though, people from all over the world have joined, bringing with them their diverse passions and interests. Our goal is to become the Internet's best source for knowledge on as many of these topics as possible. Achieving this requires both diversification into new domains of knowledge and a scalable scheme for organizing content as it's added. We recently looked into how we're doing in both of these areas, finding that:

1. People are creating quality content on a rapidly growing number of topics, and
2. By tagging content with relevant topics and curating this metadata, people are creating a sensible hierarchy of domains of knowledge.

Thus, the Quora community is organically creating a corpus of knowledge that will scale healthily, making us optimistic about reaching our goal of sharing, organizing, and growing the world's knowledge.

In this blog post, we describe the work that underpins this optimism. Our analysis relies on data describing various actions that people take on Quora: users follow one another, but they also follow questions and topics. Meanwhile, as already alluded to above, users tag questions with a list of pertinent topics. Schematically, these relationships between users, questions, and topics can be described by a graph of the type shown here:

![](/raw/fig2.png/?raw=true)

* A schematic representation of "following" relationships on Quora. Users (U) follow one another, and they also follow questions (Q) and topics (T). Additionally, questions are tagged with pertinent topics. Networks or graphs of this type provide the setting for the analyses that we describe in this blog post.*

Below, we detail our investigations into this graph, briefly tackling the issue of diversification before delving deeply into the issue of a topic hierarchy.

### Quora's Diversification

As the first step in our investigations, we wanted to get a quantitative sense of Quora's expansion into new domains of knowledge. In other words, on how many topics is there currently a substantial collection of content, and how is this number trending with time?

Conceptually, we began by constructing snapshots of the network shown above for dates from mid-2011 to the present. In each graph, we decided to identify a question as "good" if at least two users pointed to it. Since the author of the question automatically follows it, this criterion ensures that at least one other person found the question valuable and was curious about the answer. Finally, we simply counted how many good questions pointed to each topic within the graph. The results are summarized in the following plot, where we show the number of topics that had more than 100 good questions by a given date.

![](/raw/fig3.png?raw=true)

* The plot shows the number of topics that had at least 100 "good" questions by the date on the x-axis. We've recently passed 5000 distinct topics that meet this threshold.*

The plot reveals that there are now more than 5000 topics with at least 100 good questions. This is nearly four times the number of topics that met this threshold in mid-2011. This means that there are at least 5000 topical "points of entry," where someone who is interested in a particular topic can find a large amount of compelling content. These well-developed topics are very diverse, ranging from Dating and Relationships to Medicine and Healthcare and Visiting and Travel to Mathematics. Meanwhile, the discussion around Technology, Startups, and Entrepreneurship continues to flourish. Most excitingly, the set of well-developed topics is growing very rapidly, so we expect that people with an increasingly broad set of interests will find Quora an engaging platform to share and discover knowledge.

### Defining the Probabilistic Topic Network

This rapid growth is great, but to succeed in our mission, it's not sufficient for the absolute knowledge represented in Quora to diversify rapidly into new domains. That knowledge also needs to be organized such that it can be explored and shared efficiently. Conveniently, topic data can also be used to explore how Quora is doing on this front.

The key observation here is that the tagging of questions with multiple topics provides potentially rich metadata on inter-topic relationships. As a simple example, consider the following question: What is it like being the voice of a Disney character?. It's tagged with the topics The Walt Disney Company (company), Disney Movies, Voice, and Animation. Disney Movies is, conceptually, a subtopic of The Walt Disney Company (company): all questions about Disney Movies are about The Walt Disney Company (company) as well. Meanwhile, neither The Walt Disney Company (company) nor Animation is a subtopic of the other, but these are nevertheless closely related topics due to The Walt Disney Company (company)'s prominence as a maker of animated films. Finally, the topic Voice, though clearly relevant to this particular question, has no strong conceptual tie to the other three topics. These relationships between the topics can't be deduced just by looking at this one question, but we were motivated to ask whether they can be inferred from the entire corpus of questions on Quora. In other words, by tagging questions with topics, is the Quora community organically creating a topic hierarchy? 

To answer this, we started again with the graph of users, topics, and nodes from the introduction. We wanted to reduce this graph to one involving only topics and directed links between them encoding inter-topic relationships. We started by drawing a link from topic A to topic B if both topics are tagged together, or cocited, on at least one question. Note that this cocitation would also generate a link in the opposite direction, from A to B. For instance, a question like How did Neil Armstrong and Buzz Aldrin decide who stepped outside first? is tagged with the topics Neil Armstrong, Buzz Aldrin, NASA, and Moon Landings. In our network, this would generate directed links between each of these topics and the three others. We modified this simple cocitation graph to capture the inherent asymmetry in relationships between topics: while many questions on the Moon Landing will also be tagged with NASA, the reverse is not necessarily true, since NASA has been involved in many other scientific missions. To account for this, we weighted the link from Moon Landing to NASA by the probability that a question tagged with the topic Moon Landing is also tagged with the topic NASA. The picture below schematizes the creation of links between these two topics:

![](raw/fig4.png?raw=true)

* Schematization of the link-creation process in the probabilistic topic network. There are directed links between any two topics that are cocited on at least one question. The weight of the link is the probability that a question tagged with the pointing topic is also tagged with the topic to which it points. In this example, the link from Moon Landings to NASA is very strong because most questions about the Moon Landings also pertain to NASA. The reverse link is weaker because only a small fraction of questions about NASA pertain to the Moon Landings. The mathematical expression for the link weight is given below. *

To this cocitation-probability graph, we wanted to add one more bit of information: specifically, we wanted to emphasize questions that have multiple followers. When people follow a question, we take this as a very strong indication of the question's quality (and of the extent to which the question's topics have been curated). Therefore, we weighted a question $$N_f$$ times if it had $$N_f$$ followers. Mathematically, the resulting link strength, or edge weight, from topic $$A$$ to topic $$B$$ looks as follows:

$$ W_{A\rightarrow B}=\frac{\sum_{Q\in A^{N_{f}}\cap B }}{\sum_{Q \in A^{N_{f}}}} $$

This link-weight formula defines our probabilistic topic network. Before diving into our analysis, we want to emphasize that this is not the only network that we could define to explore these issues of hierarchy; it does, however, turn out to be a convenient and informative choice.

### Hints of the Topic Hierarchy

One of the simplest metrics to determine for our network is the indegree of each node. This is just the sum of the weights of the links that point to the node. In our case, this corresponds to the sum of the incoming link weights that we defined in the previous section. While our weighting procedure makes this a tricky quantity to interpret directly, the weighted indegree does have the virtue that it captures all the effects that we need. If question-topic association and curation is indeed creating a topic hierarchy that conforms to our intuitive expectations, a topic has the opportunity to acquire a large indegree through at least two distinct mechanisms. A topic like Career Advice can have a large indegree due to small conceptual overlap (and rare cocitation) with a huge number of other topics; meanwhile, a topic like Physics can also have large indegree due to very strong overlap (and consequently, frequent cocitation) with its subdisciplines. Conversely, very specific subtopics like Antelopes will have small indegree. In the future, when Quora is larger, the Antelopes topic will acquire higher indegree due to the presence of currently absent topics like Anatomy of Antelopes, but in a properly curated topic hierarchy, its indegree will always remain lower than that of, say, Animals.

The reasoning above motivates a very simple initial test of whether a topic hierarchy is developing. If it is, then the mean and median indegrees of nodes in the probabilistic topic network could behave very differently. The median would probably be dominated by the typical topic, which would have small indegree, and would be relatively low. As more refined topics get added to the system, this metric might decrease with time. Meanwhile, the mean would probably exhibit the influence of the rare "hubs" of large indegree and be considerably larger. Many common network-theoretic metrics can be very easily calculated using a Python package called NetworkX, so we used this package to measure the mean and median indegree for snapshots of the probabilistic topic network from mid-2011 to the present. The results were consistent with our expectations:

![](raw/fig5.png?raw=true)

* Mean and median indegree for nodes in the probabilistic topic network. The mean is dominated by topic "hubs" of large indegree, while the median is controlled by refined subtopics. This is the first hint of the topic hierarchy that has been built by the Quora community. *

The plot above suggests that we can deduce a hierarchy from our topic network. We will now closely examine the final snapshot (i.e., the current "state of the world"). In doing so, we'll uncover more evidence for the hierarchy, culminating in a visualization of the top-level "hubs" of activity on Quora.

### Diving Deeper into the Topic Hierarchy

To more thoroughly explore the current "state of the world," we can begin by looking beyond summary statistics like the mean and the median and examining the full degree distribution. Our degree distribution describes the probability that a randomly chosen topic points to k other topics. In this section, we'll ignore the weighting of the links, so k can be thought of as the unweighted outdegree. 

Different types of networks can have differently shaped degree distributions. For example, in a network where each node has an equal probability of being tied together, you would expect to see approximately a Poisson distribution. On the other hand, networks that are growing through a process known as preferential attachment, which can be thought of as popular people making more friends more easily than unpopular ones, will show a distribution that follows a power law $ (P(k)\propto k^{-\gamma }) $. Networks with a power-law distributed degree distribution are called scale-free networks. The term scale-free refers to the property that a node with $ 2k $ number of links is always $ 2^{\gamma} times less likely to occur than a node with kk links, independent of the value of $ k $.

The degree distribution of our topic network shows the characteristics of a power-law distribution (see below) and is proportional to $k^{−1.6}$. The scale-free nature of the topic network can be easily explained: when dealing with large topics with many questions, e.g., Physics, it is beneficial for both askers and answerers to specify the subfield within physics to which the question applies, thus increasing the probability that these topics will have many subtopics or cross-over topics. The more specific the tags on every question, the steeper the slope of the degree distribution of our topic network.

![](raw/fig6.png?raw=true)

* The probability that a randomly selected topic has outdegree kk. A larger kkimplies an increasing number of links to other topics. *

A semi-related fun fact is that 99.8% of all topics are connected together in one big "component." It may take several hops, but if you randomly pick any of the topics in this big component as a starting point, you can "walk" through the network and reach almost all other topics.

Diving in a little deeper, let's take a look at another commonly used metric: the joint degree distribution (JDD). The JDD is, effectively, a 2-D plot that lets us inspect how often, relatively, nodes of degree $k_1$ and nodes of degree $k_2$ link together. For example, for a particular social network, you can see if people with many friends ("popular people") hang out together, or are more likely to befriend someone with not very many friends ("the not-so-popular ones"). A network in which popular people hang out with each other, and unpopular people hang out with each other, is assortative. The opposite case, where popular people hang out with mostly unpopular people, is disassortative.

Looking at the JDD of our topic network (shown below), we can see that the network is mildly disassortative: large, well-connected, general topics tend to be linked to smaller, more specific topics. In the graph, this is reflected by the "hot spots" at the top-left and bottom-right. In other words, a large topic like Cars and Automobiles is more likely to link to smaller topics, such as Car Engines and Auto Repair, than to another big one such as Books. The "hot-spot" at the bottom-left of the graph tells us that smaller topics tend to cluster together as well. Though these features make sense, they can't be assumed a priori when building a topic graph based only on question co-occurrence. Instead, they are reflections of the developing hierarchy organically reproducing the relationships that we intuitively expect.

![](raw/fig7.png?raw=true)

* The probability that a topic with k1k1 links and a topic with k2k2 links are connected. Analogous to: "Do popular people with lots of friends mainly befriend other popular people, or mostly unpopular people, and vice versa?" *

So far, so good: we have a scale-free network with big topics linking to lots of smaller, more detailed topics, but to bring the point home completely, we take a look at one more commonly used metric: the clustering coefficient (CC). Using the social network analogy again, the CC measures the probability that any two of my friends are also friends with each other, given that they are my friends. For example, I'm friends with Amy, Brian, and Chris, but among those three, only Amy and Brian are friends with each other. As a result, my clustering coefficient is 1/3: out of the 3 possible pairs of friends that could have occurred between them, only one other pair actually exists.

The distribution of clustering coefficients for our topic network (see graph below) decreases steeply with the number of links a topic has. This decreasing curve indicates that smaller, more specialized topics, such as Freddie Mercury and Brian May, tend to cluster closely together, while larger topics do not tend to do so. This further supports the case that the network graph is hierarchical in nature.

![](raw/fig8.png?raw=ture)

* The fraction of all k topics that a certain topic links to, that are also linking to each other. Analogous to: "What fraction of my friends are also pairwise friends with each other?" *

### Topic Clustering

The hierarchy we found in our topic network allows us to perform a type of hierarchical topic clustering on network, to reduce the network to a handful of representative topics. To cluster topics together, we follow these steps (see graph below for graphical representation):

1. Create a list of empty trees with each topic as the root
2. Find the topic with the largest total outdegree in the topic network
3. Add the topic, and its subtree, to the subtree of each topic it links to with weight $ W_{A\rightarrow B}/\sum_{x}{W_{A\rightarrow x}} $
4. Remove the topic from the topic network
5. Goto 2 until only N topics are left

![](raw/fig9.png?raw=true)

The end result was a list of topics, each the root node of a hierarchical tree structure, allowing us to measure how closely related topics are.

With these hierarchical trees we can pick any topic from the topic network and "climb" up and down the tree to find the relative relevance of a topic to its parent and children topics. We purposefully chose this fuzzy way of clustering to allow for a topic to have multiple parents. This is especially useful for topics that are not strict subsets of one parent topic.

If we stop clustering topics together when we have only 2000 nodes left in the network, we are left with 2000 topics that cover a broad areas of interest, such as Parenting, Biology and Literature. 

We visualized the largest 33 topics out of the 2000 topics mentioned (see graph below). The size of each topic cluster is determined by the number of questions that can (partially) be tied to the topic. The color of each link represents how strongly those topics are linked to each other (darker is more strongly linked).

![](raw/fig10.png?raw=true)

* A visualization of the 33 largest (by number of questions) topics and their link strength. *

It is interesting to see that the interrelations that we intuitively expect to occur, are actually visible. For example, topics such as Physics, Mathematics and Evolutionary Biology show a relatively strong links with Science, with Physics and Math connected as well. Note that the topic names Mobile Apps and Software Development have been used in this graphic in order to better capture the content of the subtopics within those clusters.

### Conclusion

Early in this blog post, we noted the importance of maintaining a rigid topic backbone as we scale our knowledge database. The need for this backbone is especially urgent given how rapidly Quora is diversifying in to new domains of knowledge. As we have seen, however, a large, scalable topic hierarchy is emerging naturally from the Quora community's tagging of questions with relevant and detailed topic information. As we continue to grow, diversify, and scale, this emergent topic structure will become more and more detailed and fill increasingly more niches of interests, allowing us to serve everyone the best and most relevant content, catered to individual interests.
