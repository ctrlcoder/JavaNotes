### 递归的三大要素
第一要素：明确函数的功能
第二要素：寻找递归结束条件
第三要素：找出函数的等价关系式
第三要素就是，我们要不断缩小参数的范围，缩小之后，我们可以通过一些辅助的变量或者操作，使原函数的结果不变。
例如，f(n) 这个范围比较大，我们可以让 f(n) = n * f(n-1)。这样，范围就由 n 变成了 n-1 了，范围变小了，并且为了原函数f(n) 不变，我们需要让 f(n-1) 乘以 n。
说白了，就是要找到原函数的一个等价关系式，f(n) 的等价关系式为 n * f(n-1)，即f(n) = n * f(n-1)。

例题1 斐波那契数列
例题2 跳台阶

### 例题3 反转单链表

> 反转单链表。例如链表为：1->2->3->4。反转后为 4->3->2->1

链表的节点定义如下：

```
class Node{
    int date;
    Node next;
}
复制代码
```

虽然是 Java语言，但就算你没学过 Java，我觉得也是影响不大，能看懂。

还是老套路，三要素一步一步来。

**1、定义递归函数功能**

假设函数 reverseList(head) 的功能是反转但链表，其中 head 表示链表的头节点。代码如下：

```
Node reverseList(Node head){
    
}
复制代码
```

**2. 寻找结束条件**

当链表只有一个节点，或者如果是空表的话，你应该知道结果吧？直接啥也不用干，直接把 head 返回呗。代码如下：

```
Node reverseList(Node head){
    if(head == null || head.next == null){
        return head;
    }
}
复制代码
```

**3. 寻找等价关系**

这个的等价关系不像 n 是个数值那样，比较容易寻找。但是我告诉你，它的等价条件中，一定是范围不断在缩小，对于链表来说，就是链表的节点个数不断在变小，所以，如果你实在找不出，你就先对 reverseList(head.next) 递归走一遍，看看结果是咋样的。例如链表节点如下



![img](../img/1697218c0d3c1f06)



我们就缩小范围，先对 2->3->4递归下试试，即代码如下

```
Node reverseList(Node head){
    if(head == null || head.next == null){
        return head;
    }
    // 我们先把递归的结果保存起来，先不返回，因为我们还不清楚这样递归是对还是错。，
    Node newList = reverseList(head.next);
}
复制代码
```

我们在第一步的时候，就已经定义了 reverseLis t函数的功能可以把一个单链表反转，所以，我们对 2->3->4反转之后的结果应该是这样：



![img](../img/169721b333dc403e)



我们把 2->3->4 递归成 4->3->2。不过，1 这个节点我们并没有去碰它，所以 1 的 next 节点仍然是连接这 2。

接下来呢？该怎么办？

其实，接下来就简单了，我们接下来只需要**把节点 2 的 next 指向 1，然后把 1 的 next 指向 null,不就行了？**，即通过改变 newList 链表之后的结果如下：



![img](../img/16972220dbbceb38)



也就是说，reverseList(head) 等价于 **reverseList(head.next)** + **改变一下1，2两个节点的指向**。好了，等价关系找出来了，代码如下(有详细的解释)：

```java
//用递归的方法反转链表
public static Node reverseList2(Node head){
    // 1.递归结束条件
    if (head == null || head.next == null) {
             return head;
         }
         // 递归反转 子链表
         Node newList = reverseList2(head.next);
         // 改变 1，2节点的指向。
         // 通过 head.next获取节点2
         Node t1  = head.next;
         // 让 2 的 next 指向 2
         t1.next = head;
         // 1 的 next 指向 null.
        head.next = null;
        // 把调整之后的链表返回。
        return newList;
    }
```





### 有关递归的一些优化思路
1.**考虑是否重复计算**
f(n) = f(n-1) + f(n-2)
递归计算的时候，重复计算了两次 f(5)，五次 f(4)。。。。这是非常恐怖的，n 越大，重复计算的就越多，所以我们必须进行优化。
如何优化？一般我们可以把我们计算的结果保证起来，例如把 f(4) 的计算结果保证起来，当再次要计算 f(4) 的时候，我们先判断一下，之前是否计算过，如果计算过，直接把 f(4) 的结果取出来就可以了，没有计算过的话，再递归计算。

```java
// 我们实现假定 arr 数组已经初始化好的了。
int climbStairs(int n){
    if(n <= 1){
        return n;
    }
    //先判断有没计算过
    if(arr[n] != -1){
        //计算过，直接返回
        return arr[n];
    }else{
        // 没有计算过，递归计算,并且把结果保存到 arr数组里
        arr[n] = f(n-1) + f(n-1);
        reutrn arr[n];
    }
}

```



2.**考虑是否可以自底向上**
那么我们就可以推出 f(3) = f(2) + f(1) = 3。从而可以推出f(4),f(5)等直到f(n)。因此，我们可以考虑使用自底向上的方法来取代递归。这种方法，其实也被称之为递推。



```java
    public int climbStairs(int n) {
        if(n <= 2)
           return n;
       int f1 = 1;
       int f2 = 2;
       int sum = 0;
       for (int i = 3; i <= n; i++) {
           sum = f1 + f2;
           f1 = f2;
           f2 = sum;
       }
       return sum;
    }
```

