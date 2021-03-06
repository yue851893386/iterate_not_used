
Hadoop： The Definitive Guide大数据的存储与分析

本书结合理论和实践，由浅入深，全方位介绍了 Hadoop这一高性能的海量数据处理和分析平 台。全书5部分24章，第I部分介绍Hadoop基础知识，i题涉及Hadoop、MapReduce、Hadoop 分布式文件系统、YARN、Hadoop的I/O操作。第II部分介绍MapReduce，主题包括MapReduce应 用开发；MapReduce的工作机制、MapReduce的类型与格式、MapReduce的特性。第III部分介绍 Hadoop的运维，主题涉及构建Hadoop集群、管理Hadoop。第IV部分介绍Hadoop相关幵源项目， 主题涉及 Avro、Parquet、Flume、Sqoop、Pig、Hive、Crunch、Spark、HBase、ZooKeeper。第 V 部分提供了三个案例，分别来自医疗卫生信息技术服务商塞纳(Cerner)、微软的人工智能项目 ADAM(—种大规模分布式深度学习框架)和幵源项冃Cascading(—个新的针对MapReduce的数掘 处理 API)。

本书一本权威、全面的Hadoop参考与工具书，阐述了 Hadoop生态圈的最新发展和应用，程 序员可以从中探索海量数据集的存储和分析，管理员可以从中了解Hadoop集群的安装和运维。




Doug Cutting@加州院内小屋

Hadoop起源于Nutch项目。我们几个人有一段时间一直在尝试构建一个开 源的Web搜索引擎，但始终无法有效地将计算任务分配到多台计算机上， 即使就只是屈指可数的几台。直到谷歌发表GFS和MapReduce的相关论文 之后，我们的思路才清晰起来。他们设计的系统已经可以精准地解决我们 在Nutch项目中面临的困境。于是，我们（两个半天工作制的人）开始尝试重 建这些系统，并将其作为Nutch的一部分。

我们终于让Nutch可以在20台机器上平稳运行，但很快又意识一点：要想 应对大规模的Web数据计算，还必须得让Nutch能在几千台机器上运行， 不过这个工作远远不是两个半天工作制的开发人员能够搞定的。

差不多就在那个时候，雅虎也对这项技术产生了浓厚的兴趣并迅速组建了 一个开发团队。我有幸成为其中一员。我们剥离出Nutch的分布式计算模 块，将其称为“Hadoop”。在雅虎的帮助下，Hadoop很快就能够真正处理 海量的Web数据了。

从2006年起，Tom White就开始为Hadoop做贡献。很早以前，我便通过他 的一篇非常优秀的Nutch论文认识了他。在这篇论文中，他以一种优美的 文风清晰地阐述复杂的思路。很快，我还得知他开发的软件一如他的文 笔，优美易懂。

从一开始，Tom对Hadoop所做的贡献就体现出他对用户和项目的关注。与 大多数开源贡献者不同，Tom并没有兴致勃勃地调整系统使其更符合自己 个人的需要，而是尽可能地使其方便所有人使用。

最开始，Tom专攻如何使Hadoop在亚马逊的EC2和S3服务上高效运行。 随后，他转向解决更广泛的各种各样的难题，包括如何改进MapReduce API,如何增强网站特色，如何精心构思对象序列化框架，如此等等，不一而举。在所有这些工作中，Tom都非常清晰、准确地阐明了自己的想法。 在很短的时间里，Tom就赢得大家的认可，拥有Hadoop提交者(committer) 的权限并很快顺理成章地成为Hadoop项目管理委员会的成员。

现在的Tom，是Hadoop开发社区中受人尊敬的资深成员。他精通Hadoop 项目的若干个技术领域，但他更擅长于Hadoop的普及，使其更容易理解和 使用。

基于我对Tom的这些了解，所以当我得知Tom打算写一本Hadoop的书之 时，别提有多高兴了。是的，谁比他更有资格呢？！现在，你们有机会向 这位年青的大师学习Hadoop,不单单是技术，还有一些必知必会的常识， 以及他化繁为简、通俗易懂的写作风格。




Hadoop的内部工作机制非常复杂，是一个集分布式系统理论、实际工程和常识于一体的系统。而且，对门外汉而言， Hadoop更像是“天外来客”但Hadoop其实并没有那么让人费解，抽丝剥茧，我们来看看它的“庐山真 面目”。Hadoop提供的用于处理大数据的工具都非常简单。如果说这些工 具有一个共同的主题，那就是它们更抽象，为（有大量数据需要存储和分析 却没有足够的时间、技能或者不想成为分布式系统专家的）程序员提供一套 组件，使其能够利用Hadoop来构建一个处理数据的基础平台。

这样一个简单、通用的特性集，促使我在开始使用Hadoop时便明显感觉到 Hadoop真的值得推广。但最开始的时候（2006年初），安装、配置和Hadoop 应用编程是一门高深的艺术。之后，情况确实有所改善：文档增多了；示 例增多了；碰到问题时，可以向大量活跃的邮件列表发邮件求助。对新手 而言，最大的障碍是理解Hadoop有哪些能耐，它擅长什么，它如何使用。 这些问题使我萌发了写作本书的动机。


语。®当前，软件在可用性、性能、可靠性、可扩展性和可管理性方面都实 现了巨大的飞跃。在Hadoop平台上搭建和运行的应用增长迅猛。事实上， 对任何一个人来说，跟踪这些发展动向都很困难。但为了让更多的人采用 Hadoop,我认为我们要让Hadoop更好用。这需要创建更多新的工具，集成 更多的系统/创建新的增强型API。我希望自己能够参与，同时也希望本书 能够鼓励并吸引其他人也参与Hadoop项目。

说明
在文中讨论特定的Java类时，我常常会忽略包的名称以免啰嗦杂乱。如果 想知道一个类在哪个包内，可以查阅Hadoop或相关项目的Java API文档 （Apache Hadoop 主页http://hadoop.apache.org上有链接可以访问）。如果使 用IDE编程，其自动补全机制（也称“自动完成机制”）能够帮助你找到你需

本书中的示例代码可以从本书网站下载，网址为http://www.hadoopbook.com/。 可以根据网页上的指示获取本书示例所用的数据集以及运行本书示例的详 细说明、更新链接、额外的资源与我的博客。

本书的补充材料(代码、示例及练习等)：https://github.com/tofnwhite/hadoop-book/
