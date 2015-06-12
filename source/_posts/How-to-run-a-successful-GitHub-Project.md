title: 怎样做一个成功的GitHub项目?
date: 2015-06-12 11:35:41
tags: GitHub
---

##0. 前言

对于程序员来说，GiHub不仅仅是一个代码托管工具，同时也是一个展示自己编程能力的平台。简单地说，如果你的GitHub上有几个拿的出手的项目，将会对你找工作很有帮助。

最近，我做了一个将Hadoop打包到Docker镜像的项目，获得了一些小小的成功。 所以通过这篇博客分享一些经验，同时也是项目总结。下面是该项目的[博客](http://kiwenlau.com/2015/06/08/hadoop-cluster-docker/)以及[GitHub](https://github.com/kiwenlau/hadoop-cluster-docker)地址。

博客地址：[基于Docker快速搭建多节点Hadopp集群](http://kiwenlau.com/2015/06/08/hadoop-cluster-docker/)
GitHub地址: [kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)

下面是该项目获得的一些认可：
- 被[DockerOne](http://dockone.io/article/395)推荐
- 被[开发者头条](http://toutiao.io/top/2015-05-27)推荐
- 被Docker官方[weekly newsletter](https://go.docker.com/newsletter06032015)收录
- 获得[“Docker，巨好玩”镜像构建挑战赛](http://event.alauda.cn/)二等奖，赢了一块Apple Watch
- 被各种技术网站和技术微博转载...

最重要的是，这个项目在GitHub上目前一共获得了39个Star, 12个Fork。这样的指数不算太高，但是已经不错了。

那么，怎样做一个成功的GitHub项目呢？

##1. 做有价值的项目

我想，这应该是最重要的一点，你必须做一个对大家有用的项目才能获得认可。将Hadoop打包到Docker镜像里面，这样Hadoop用户就可以快速的在本机搭建一个多节点的Hadoop集群，这可以作为开发者的测试环境，也可以作为初学者的学习环境。所以，我的项目是有价值的，这是它被认可的最重要的原因。

但是，我并不是第一个有这个想法的人，也不是第一个做这样的项目的人，甚至有个[SequenceIQ](http://sequenceiq.com/)公司是专注于将Hadoop运行在Docker中。而我的项目是基于另一个GitHub项目[alvinhenrick/hadoop-mutinode](https://github.com/alvinhenrick/hadoop-mutinode), 只是我做了大量的重构和优化。有趣的是[alvinhenrick/hadoop-mutinode](https://github.com/alvinhenrick/hadoop-mutinode)项目的Star数只有7, 小于我的 [kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目，我想应该是因为项目本身存在很多问题，然后推广做得不够好吧，这些与我接下来要讨论的问题相关。

我想表达的是，谁是第一个做并不重要，重要的是你必须比他们做得更好。很多时候，我们都是站在巨人的肩膀上。只要你在别人的基础上做了足够多的改进和优化，那么你的项目就是有价值的。GiHub是一个开源平台，本来就可以用别人的代码，当然你必须遵守开源协议的规范，尊重每一行代码的作者。我从未否认自己用了[Alvin Henrick](https://twitter.com/alvinhenrick)的代码，做完项目也在推特上告诉了他，得到了他的认可。

##2. 做完美的项目

每一个GitHub项目都是你的产品，所以你必须追求完美。[alvinhenrick/hadoop-mutinode](https://github.com/alvinhenrick/hadoop-mutinode)项目是我的 [kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目的基础，但是仅仅只有7个star，为什么呢？因为它不够完美:

- 只提供了Dockerfile，却没有提供Docker镜像。而通过Dockerfile构建Docker镜像麻烦，耗时而且有bug。所以作为一个产品，它没有充分考虑用户体验。
- 镜像太大，4GB的Docker镜像使得它的上传下载速度都很慢。
- 不易于扩展节点，需要手动修改脚本，重新构建Docker镜像。

事实上，我所做的工作就是对[alvinhenrick/hadoop-mutinode](https://github.com/alvinhenrick/hadoop-mutinode)项目的不足之处进行优化，然后不断追求完美。下面是[kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目的特点：

- 提供了Docker镜像，同时提供了[Aladua](https://console.alauda.cn/cp/kiwenlau/#/repo/list)和[DockerHub](https://registry.hub.docker.com/u/kiwenlau/hadoop-master/)两个下载点。
- 优化了Docker镜像的大小，只有780MBM。
- 提供了自动化扩展任意节点的脚本。

当然，我的项目还存在很多问题，我会尽快进行优化。

##3. 提供详细的文档

很多时候，我们敲完代码，然后随便写点注释或者简单的文档方便自己以后使用就够了。但是对于一个GitHub项目，如果没有详细的文档，其他人是无法了解你的工作的，甚至不知道怎么使用。你能看懂简单文档，是因为代码是你自己写的，你熟悉整个项目的细节，而对其他人来讲并非如此。过了一段时间，可能你自己都需要阅读代码才能上手自己的项目，难道你要求使用者去看你的源代码？

关于文档，必须写清楚详细的使用教程，写清楚每一步的命令，输出以及其他必要信息。写文档的时候必须假设使用者都是零起点，只拥有尽量少的背景知识。这样做主要是针对项目的用户，方便他们快速使用。

对于一个期待获得成功GitHub项目，我们的目标应该是长期维护并且吸引开发者一起合作，所以你需要在文档中讲清楚技术细节，让感兴趣的开发者能够迅速上手。

如果你希望自己的项目更加国际化，那么你得同时写英文文档。

其实，这样做对于自己推广以及维护这个项目也是有很大好处的。

##4. 学会推广项目

事实上，我这篇博客也同时在推广[kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目。

如果你把代码放在GitHub上，然后写了个README，那么你的项目基本上没人会知道，唯一的希望是被其他人搜索到。

你得写一篇博客，最好能够发到不同的站点上，比如简书，图灵社区，SegmentFault, 博客园，Blogger, 微博，知乎...很多人希望成为“全栈工程师”，那你先做个“全站工程师“，在不同的站点上都有一个ID相同的账号。 关于这一点，你可以阅读[如何提高影响力](http://www.phodal.com/blog/how-to-improve-impact/)。这样做的确有些枯燥, 但是不同的用户获取信息的站点，这样可以帮助吸引不同站点的用户，在不同的站点积累声望，然后汇集到你的主站点。

![全站工程师](/image/150612/different_sites.png "全站工程师")

你的GitHub项目必定是针对特定技术的，所以博客应该发表的相应的站点。我的[kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目专注于Docker项目，因此我将它发表在[DockerOne](http://dockone.io/article/395)上，这篇文章已经获得了2000+的阅读量，成为我吸引用户的主要站点。

如果你的博客和项目足够好，那就自荐到[开发者头条](http://toutiao.io/top/2015-05-27)进行推广吧! 这会为你的项目推广带来很大帮助。同时我也将[kiwenlau/hadoop-cluster-docker](https://github.com/kiwenlau/hadoop-cluster-docker)项目的英文博客[Quickly build arbitrary size Hadoop Cluster based on Docker](http://kiwenlau.blogspot.jp/2015/05/quickly-build-arbitrary-size-hadoop.html)自荐到Docker官方的[weekly newsletter](https://go.docker.com/newsletter06032015), 这为我吸引到了国外开发者的关注。

如果你希望自己的项目更加国际化，那么你得同时写一篇英文博客。

##5. 持续维护
一个成功的GiHub项目应该是有生命力的，所以需要进行持续维护。这一点很难做到，我正在努力...

***
转载请注明作者[KiwenLau](http://kiwenlau.com/)和本文地址：[http://kiwenlau/2015/06/12/How-to-run-a-successful-GitHub-Project/](http://kiwenlau/2015/06/12/How-to-run-a-successful-GitHub-Project/)
***

