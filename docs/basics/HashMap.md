本人学艺不精，Java的学习都是看大佬博客或者公众号的比较多。比如很喜欢的敖丙，Hollis等等。
内容知识大致一样，只是自己学习过后不想忘记得太快，讲自己理解的知识点记录下来，供自己之后的复习使用。
如果对大家有所帮助，希望可以来个star，谢谢大家。
# 正文
* **HashMap的底层实现原理**
 
 HashMap是Java中常用的数据结构，它在JDK1.8之前是由**数组**和**链表**组合构成的数据结构，也是就**链表散列**。
 我们知道数组和链表都有其各自的优点和缺点，数组连续存储，寻址容易，插入删除操作相对困难；而链表离散存储，寻址相对困难，而插入删除操作容易；而HashMap结合了这两种数据结构，保留了各自的优点，又弥补了各自的缺点。借用[敖丙](https://github.com/AobingJava/JavaFamily/blob/master/docs/basics/HashMap.md)的图大概就是以下这样的结构。数组中每个地方都存了Key-Value这样的实例，在JDK1.8之前叫Entry在JDK1.8中叫Node。

 ![](https://github.com/ckcxq123/Java-/blob/master/docs/image/hashmap0.png)
 
 因为HashMap的key和value都可以为null，在往里面put(添加)元素的时候会根据key的hashCode用hash方法去计算一个index(插入的位置)值。
 简单看一下HashMap中put和hash方法的源码。
 
![](https://github.com/ckcxq123/Java-/blob/master/docs/image/1584269025.png)

![](https://github.com/ckcxq123/Java-/blob/master/docs/image/1584270518(1).jpg)
 
 看过[Guide哥](https://juejin.im/post/5ab0568b5188255580020e56#heading-4)对HashMap的介绍，在JDK1.8之前数组是主体，链表则是主要为了解决哈希冲突而存在的。哈希冲突又是怎么样产生的呢？
 例如现在要向一个HashMap对象中put（”泉儿“，250），当插入了为”泉儿“的元素为key时，通过hash方法计算出插入的位置，计算出来index是1那结果如下。
 
![](https://github.com/ckcxq123/Java-/blob/master/docs/image/hashmap1.png)
hash（"泉儿"）= 1

但是我们都知道数组长度是有限的，在有限的长度里面我们使用哈希，哈希本身就存在概率性，就是”泉儿“和”儿泉“我们都去hash有一定的概率会一样，就像上图的情况我们再次hash("儿泉")极端情况也会hash到一个值上，通俗的讲就是不同的key有可以hash出来的值是一样的，该位置的数组就出现了两个，对于这种情况就形成了链表。这就是链表主要解决的哈希冲突。

![](https://github.com/ckcxq123/Java-/blob/master/docs/image/hashmap2.png)
 
 如果定位到的数组位置不含链表（当前Entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度依然为O(1)，因为最新的Entry会插入链表头部，急需要简单改变引用链即可，而对于查找操作来讲，此时就需要遍历链表，然后通过key对象的equals方法逐一比对查找。
 
 JDK1.8之后呢HashMap的结构就变为了**数组**加**链表**加[红黑树](https://github.com/ckcxq123/Java-/blob/master/docs/basics/RedBlackTree.md)的结构。当HashMap的链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间，红黑树的引用将原本O(n)的时间复杂度降低到了O(logn)。红黑树的介绍就不在此展开。
 
 * **HashMap的存取原理**
 
 HashMap的put方法，这里看JDK1.8之后的源码进行解释，JDK1.8相比较1.7就是多了一个红黑树的判断。思路如下
 
 1.table[]是否为空
 
2.判断table[i]处是否插入过值

3.判断链表长度是否大于8，如果大于就转换为红黑二叉树，并插入树中

4.判断key是否和原有key相同，如果相同就覆盖原有key的value，并返回原有value

5.如果key不相同，就插入一个key，记录结构变化一次

![](https://github.com/ckcxq123/Java-/blob/master/docs/image/put1.png)
![](https://github.com/ckcxq123/Java-/blob/master/docs/image/put2.png)
![](https://github.com/ckcxq123/Java-/blob/master/docs/image/put3.png)

HashMap的get方法，实现思路：

1.判断表或key是否是null，如果是直接返回null

2.判断索引处第一个key与传入key是否相等，如果相等直接返回

3.如果不相等，判断链表是否是红黑二叉树，如果是，直接从树中取值

4.如果不是树，就遍历链表查找

![](https://github.com/ckcxq123/Java-/blob/master/docs/image/get1.png)
 
  * **HashMap的JDK1.8之前和1.8之后的主要区别**
  
  1.HashMap在JDK1.8之前的结构是**数组**加**链表**,1.8之后是**数组**加**链表**加[红黑树](https://github.com/ckcxq123/Java-/blob/master/docs/basics/RedBlackTree.md)
  
  2.在1.8之前，新的Entry节点在插入链表的时候用的是头插法，1.8之后是尾插法
  
   * **HashMap头插法和尾插法**
   
1.8之前是头插法，就是说新来的值会取代原有的值，原有的值就顺推到链表中去，就像上面的"泉儿"例子一样，因为写这个代码的作者认为后来的值被查找的可能性更大一点，提升查找的效率。

1.8后换成了尾插法是有原因的，先看一下HashMap的扩容机制：

 之前提过了HashMap的数组容量是有限的，数据多次插入的，到达一定的数量就会进行扩容，也就是resize。
 
 什么时候进行扩容呢？
 
 主要有两个因素:
 
 有两个因素:
 
**Capacity：HashMap当前长度。**

**LoadFactor：负载因子，默认值0.75f。**

即使用空间达到总空间的0.75时，需要扩容。简单理解就比如当前的容量大小为100，当你存进第76个的时候，判断发现需要进行resize了，那就进行扩容。

至于LoadFactor为什么默认值是0.75f,主要是因为LoadFactor太大导致查找元素效率低，太小导致数组的利用率低，存放的数据会很分散。LoadFactor的默认值为0.75f是官方给出的一个比较好的临界值。  

扩容的步骤分为两步:

**扩容：创建一个新的Entry空数组，长度是原数组的2倍。**

**ReHash：遍历原Entry数组，把所有的Entry重新Hash到新数组。**

而这里ReHash重新Hash到新数组而不能直接复制到新数组中，主要是因为长度扩大以后，Hash的规则也随之改变。

Hash的公式---> index = HashCode（Key） & （Length - 1）

原来长度（Length）是8你位运算出来的值是2 ，新的长度是16你位运算出来的值明显不一样了。

1.8之后换成了尾插法，是因为在1.8之前的头插法在多线程的情况下put有可能发生环形链表。取值的时候回造成死循环。

举个例子，我们现在往一个容量大小为2的put两个值，负载因子是0.75是不是我们在put第二个的时候就会进行resize？

2*0.75 = 1 所以插入第二个就要resize了

现在我们要在容量为2的容器里面用不同线程插入A，B，C，假如我们在resize之前打个断点，那意味着数据都插入了但是还没resize那扩容前可能是这样的。

我们可以看到链表的指向A->B->C

**Tip：A的下一个指针是指向B的**

![](https://camo.githubusercontent.com/719e2b7309ad1da77a76ee4f0d8b4213c032038c/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303036744e6252776c79316739706b6178676735696a3330353030376f6466722e6a7067)

因为resize的赋值方式，也就是使用了**单链表的头插入方式，同一位置上新元素总会被放在链表的头部位置**，在旧数组中同一条Entry链上的元素，通过重新计算索引位置后，有可能被放到了新数组的不同位置上。

就可能出现下面的情况。

B的下一个指针指向了A

![](https://camo.githubusercontent.com/53ce9d11d6a120b561a618f8ced3114ca4ed16e8/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303036744e6252776c79316739706863706c3936386a3330396a3035343734372e6a7067)

一旦几个线程都调整完成，就可能出现环形链表

![](https://camo.githubusercontent.com/fe3e630f6f5609d3f34ddccff121ef91b2f01ad4/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303036744e6252776c79316739706b68386f6d6a796a3330616c3036706d78342e6a7067)

如果这个时候去取值，悲剧就出现了——Infinite Loop(死循环)。

使用**头插**”会改变链表的上的顺序，但是如果使用**尾插**，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。

就是说原本是A->B，在扩容后那个链表还是A->B

![](https://camo.githubusercontent.com/cbfe36fad3e7dc326cd8323adc12833d6d125b06/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303036744e6252776c79316739706c667471696d336a3330396630347930736e2e6a7067)

1.8之前在多线程操作HashMap时可能引起死循环，原因是扩容转移后前后链表顺序倒置，在转移过程中修改了原来链表中节点的引用关系。

1.8在同样的前提下并不会引起死循环，原因是扩容转移后前后链表顺序不变，保持之前节点的引用关系。

   * **HashMap为什么不是线程安全的**
   
即使在1.8中多线程取值不会出现死循环，但是通过源码看到put/get方法都没有加同步锁，多线程情况最容易出现的就是：无法保证上一秒put的值，下一秒get的时候还是原值，所以线程安全还是无法保证。

   * **HashMap默认初始化大小是多少？为啥是这么多？为啥大小都是2的幂？**
   
   HashMap初始化大小是16。








   
   
 
 
 
 
 


 
