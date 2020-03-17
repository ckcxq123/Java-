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
  
  2.
 


 
