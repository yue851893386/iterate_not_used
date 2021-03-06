# LSTM


TODO

- 讲的还是很透彻的，但是我想知道，LSTM的cell在程序中是怎么实现的？还有那些求导的过程在程序中是怎么实现的？还是要学习一下有没有什么代码或者库的源码？




# 缘由：

对LSTM进行总结。




# LSTM简介


RNN解决了对之前的信息保存的问题。

但是！存在长期依赖的问题：比如说：看电影的时候，某些情节的推断需要依赖很久以前的一些细节。很多其他任务也一样。但是RNN会丢失很长一段时间之前的信息。也就是说，RNN的记忆容量是有限的。那么怎么办呢？LSTM。

LSTM 是 RNN的一种，大体结构几乎一样，但是：它的记忆细胞 cell 被改造过。使得该记的信息会一直传递，不该记的会被门截断。


#




# LSTM结构




## 之前提到的RNN结构如下：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/amEb5BK0cA.png?imageslim)

即可以写成如下的形式：**嗯 是的**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Hecfm41e7C.png?imageslim)




## 而LSTM的记忆细胞如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0c29Aj2ba7.png?imageslim)

解释一下：

  * 黄色的标记是一个非线性处理的模块。
  * 红点是一些逐点的运算，比如逐点的乘法


下面我们开始讲最关键的东西：


## LSTM关键：“细胞状态”


细胞状态（cell state 也就是之前RNN中提到的S，即细胞的记忆）类似于传送带，直接在整个链上运行，只有一些少量的线性交互，信息在上面流传保持不变会很容易。可见，从 \(C_{t-1}\) 到 \(C_{t}\) 是从上一个时刻的记忆到这个时刻的记忆。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/alh50LlGI8.png?imageslim)

## 说明一下这中间的 Sigmoid


可以通过 ”门“ 让信息选择性通过，来去除或者增加信息到细胞状态。那么这个”门“是什么呢？它其实就是一个 Sigmoid 层。Sigmoid 层输出0到1之间的概率值，描述每个部分有多少量可以通过，其中0代表不允许任何量通过，1代表允许任意量通过。所以，用这个层与之前过来的记忆做一个 pointwise  乘法就可以控制之前状态过来的记忆信息的通过程度。**为什么不用ReLU？因为要0~1才能起到信息筛选的作用。只有这样的话，C才能会被保持在一定的范围内，如果用ReLU的话，信息就会不断膨胀。**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/47iJKad8DB.png?imageslim)


# LSTM的几个关键”门“与操作




## 第1步：决定从”细胞状态“中丢弃什么信息？=> “忘记门”  forget gate


比如完形填空中填 “他” 或者 “她“ 的问题，细胞状态可能包含当前主语的类别，当我们看到新的代词，我们希望忘记旧的代词。

所以我用上一个时刻的输出和这个时刻的输入来一起决定我以多大的程度来忘却之前的细胞记忆 cell state，\(\sigma \)就是一个Sigmoid函数，\(f_t\)就是得到的留下的概率。

**注意：这里的\([h_{t-1},x_t]\)指的是向量的拼接操作。**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/k71DgHLJ7f.png?imageslim)



## 第2步：决定放什么新信息到”细胞状态“中


之前我已经忘掉一些信息了，那么我补什么新信息进来呢？




  1. \(i_t\)是一个概率，


  2. \(\widetilde{C_t}\) 是候选值向量。**没明白，为什么是与\(h_{t-1}\)和\(x_t\)有关的？不是只与\(x_t\)有关吗？**


  3. 相乘之后，就加到了我的cell state中。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Je9KhlHHl8.png?imageslim)



## 第3步：更新”细胞状态“






  1. 在完成上面两步之后，开始更新 \(C_{t-1}\)为\(C_t\)


  2. 把旧状态与\(f_t\)相乘，丢弃掉我们确定需要丢弃的信息


  3. 加上\(i_t*\widetilde{C_t}\)。这就是新的候选值，根据我们决定更新每个状态的程度进行变化



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Kdh6F67lIi.png?imageslim)



## 第4步：基于 ”细胞状态“ 得到输出






  1. 首先运行一个sigmoid层来确定细胞状态的哪个部分将输出。


  2. 接着用tanh处理细胞状态（得到一个在-1到1之间的值），再将它和sigmoid门的输出相乘，输出我们确定输出的那部分。


  3. 比如我们可能需要单复数信息来确定输出”他“还是”他们“


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ALHd954jea.png?imageslim)



# 那么为什么LSTM比RNN更能解决记忆长时依赖的问题？


之前的RNN中，\(S_t=tanh(Wx_t+VS_{t-1})\)是一个复合函数，那么它求偏导的时候由于里面的激活函数是tanh，因此在求偏导的时候，有可能是约等于0的，如果约等于0，那么就意味着RNN学不到什么东西，而什么时候会出现约等于0呢？链路越长越有可能存在一个是约等于0的，那么这种链路很长的情况下，就会带来梯度消失。

而LSTM呢？它实际上是两个函数求和： \(C_t=f_t*C_{t-1}+i_t*\widetilde{C_t}\) ，两个函数之和求偏导的话就是两个偏导求和，这时候，如果有一个是约等于0的，那么结果也不一定会是0，也就是说，会减少出现梯度消失的情况，也就是说，即使信息出现的时间比较远，也是可能学的到的。

也就是说LSTM最大的变化就是把RNN中的连乘的形式变成求和的形式。





# LSTM的变体


LSTM的变体实际上是非常多的。


## 变种1：


为什么只使用我上次的输出和我这次的输入作为忘掉或记住的依据？而不用我之前的状态和现在的状态作为依据？这种变体就是考虑这个的，它增加了 "peephole connection" ，让门层也会接受细胞状态的输入。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ILlcCccC7g.png?imageslim)




## 变种2：


为什么遗忘和记忆的概率是不一样的呢？能不能忘掉多少就补充多少？这个变体就是对应这个的。它通过使用 coupled 忘记和输入门。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lH8cLGBbbF.png?imageslim)



## 变种3：GRU （Gated Recurrent Unit）


这是一个比较著名的变种。2014年提出，将忘记门和输入门合成了一个单一的更新门，同样还混合了细胞状态和隐藏状态，和其他一些改动，比LSTM更简单。**看起来有些利害，C没有了，但是感觉整个流程又差不多。这个没有细讲，要仔细了解下。**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/KfF4fffDb6.png?imageslim)






# RNN拓展


2015的paper《LSTM：A Search Space Odyssey》中，对各种变体做了对比，发现其实本质上它们大同小异。

2015的paper《An Empirical Exploration of Recurrent Network Architectures》中，google和facebook的大神尝试了很多种RNN架构，发现并非所有任务上LSTM都表现最好。**为什么呢？难道记忆反而会有反作用？**

现在有更多的RNN研究方向和应用（attention model ，Grid LSTM等）参见：https://github.com/kjw0612/awesome-rnn     **awesome系列，还是要跟进学习的。**











# REF

1. 七月在线  深度学习
2. [Understanding LSTM Networks](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)
