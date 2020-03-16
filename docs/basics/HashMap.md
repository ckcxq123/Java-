本人学艺不精，Java的学习都是看大佬博客或者公众号的比较多。比如很喜欢的敖丙，Hollis等等。
内容知识大致一样，只是自己学习过后不想忘记得太快，讲自己理解的知识点记录下来，供自己之后的复习使用。
如果对大家有所帮助，希望可以来个star，谢谢大家。
# 正文
* **HashMap的底层实现原理**
 
 HashMap是Java中常用的数据结构，它在JDK1.8之前是由**数组**和**链表**组合构成的数据结构，也是就**链表散列**。
 借用敖丙的图大概就是以下这样的结构。数组中每个地方都存了Key-Value这样的实例，在JDK1.8之前叫Entry在JDK1.8中叫Node。

 ![](https://camo.githubusercontent.com/371582d1941290e70c693f968be1ca56fd1e1138/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f303036744e6252776c7931673970636868627270336a3330657a30326e676c692e6a7067)
 
 因为HashMap的key和value都可以为null，在往里面put(添加)元素的时候会根据key的hashCode用hash方法去计算一个index(插入的位置)值。
 看过Guide哥对HashMap的介绍，在JDK1.8之前数组是主体，链表则是主要为了解决哈希冲突而存在的。
 如果定位到的数组位置不含链表（当前Entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度依然为O(1)，因为最新的Entry会插入链表头部，急需要简单改变引用链即可，而对于查找操作来讲，此时就需要遍历链表，然后通过key对象的equals方法逐一比对查找。

 简单看一下HashMap中put和hash方法的源码。
 
![](https://github.com/ckcxq123/Java-/blob/master/docs/image/1584269025.png)

![](https://github.com/ckcxq123/Java-/blob/master/docs/image/1584270518(1).jpg)
 
