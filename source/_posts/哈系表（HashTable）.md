title: 哈系表（HashTable）
id: 1504793320763
author: 不识
date: 2017-09-07 22:08:48
tags:
  - 哈系表
categories:
  - 数据结构
---
***
#  HashTable
***
哈系表（Hash Table）是一种个将键映射到值的数据结构（也称为Table或Map抽象数据类型（ Abstract Data Type/ADT））。它使用一个hash函数将大型或者甚至非整数的键映射到一个小范围的整数索引（通常是 [0..hash_table_size-1]）上。

两个不同的键碰撞到相同的索引上的概率较高，并且每个这种潜在的碰撞都需要解决以维持数据完整。

这有几种冲突解决策略，将在此学习中突出显示：开放寻址（线性检测，二次探测和双重哈希）和闭合寻址（单独链接）。
<!-- more -->
***
#  动机
***
哈希是一种算法（通过一个hash函数），将可变长度的大的数据集（称为键，不需要是整数）映射到一个固定长度的整数数据集上。

哈希表是使用hash函数有效地将键映射到值（Table或Map ADT）的数据结构，用于高效的搜索/检索，插入和/或移除。

哈希表广泛应用于多种计算机软件，特别是关联数组，数据库索引，缓存和set。

本教程中我们将讨论Table ADT，哈希的基本思想，并在进入哈希表细节之前讨论哈希函数。

## Table ADT
***
一个Table ADT必须至少尽可能高效的支持以下三个操作：
1. **Search(v)** — 判断v是否在ADT中
2. **Insert(v)** — 向ADT中插入v
3. **Remove(v) **— 从ADT中移除v

哈希表是该Table ADT的一个可能的实现。
> PS：对于Table ADT的两个较弱的实现，可以单击相应的链接：[未排序的数组]()或[排序的数组]()来阅读详细的讨论。

## Direct Addressing Table（DAT）
***
当Integer键的范围很小时，例如[0..M-1]，我们可以使用大小为M的初始空（boolean）数组A，并直接实现以下Table ADT操作:
1. Search(v): 检查A[v]是否为true (已填充) 或为false (空的),
2. Insert(v): 设置A[v] 为true (填充),
3. Remove(v): 设置A[v] 为false (空的).

就是这样，我们使用小的整数键来确定数组A中的地址，因此命名为**直接寻址**（Direct Addressing）。很明显，所有三个主要的Table ADT操作时间复杂度都是O（1）。

## DAT限制
***
键必须是**非负整数值**。
键的范围必须比较小。如果比较大的话，内存的占用将疯狂增长。
键必须密集，也就是在键的值之间没有太多空隔。否则DAT将会包含很多空的单元格。
我们会通过使用哈希来克服这些限制。

***
# 哈希
***
使用哈希，我们可以：
1. 映射**非整数**键到整数值上
2. 映射大范围整数到更小的整数上
3. 哈系表的影响密度或负载因子**α = N/M**，这里N是键的数量，M是哈系表的大小

使用哈希，我们可以是使用整数数组来实现Table ADT的操作：
1. **Search(v)**: 检查是否A[h(v)] != -1 (假设v ≥ 0，我们使用-1表示一个空单元格),
2. **Insert(v)**: 设置A[h(v)] = v (我们哈希v 到h(v) 这样我们需要以某种方式记录键v)
3. **Remove(v)**: 设置A[h(v)] = -1 — 之后进一步阐释

如果我们要让键映射到卫星数据上，并且我们也要记录原始的键，那么我们可以使用（Integer，satellite-data-type）数组来实现哈希表，如下所示：
1. **Search(v)**: 返回A[h(v)]，它是一个pair (v, satellite-data)，也可能是空的
2. **Insert(v, satellite-data)**: 设置A[h(v)] =pair (v, satellite-data)
3. **Remove(v)**: 设置A[h(v)] = (empty pair) — 之后进一步阐释

但是你应该注意到有些是不完整的。哈希函数可能并且很可能将不同的键（整数或不整数）映射到相同的整数槽中，也就是，多对一映射而不是一对一映射。

这种情况称为冲突（collision），即两个（或更多个）键具有相同的哈希值。

>生日（冯米塞斯）悖论问：“不管年份和闰天（也就是所有的年份都有365天），一个房间（Hash Table）必须有多少人（key），才能让他们有相同生日（collision）的概率达到50%“？答案是：23人

这时产生了两个重要的问题：
1. 我们可以使用一个哈希函数将大范围的整数键映射到较小范围的整数键上，但非整数键如何呢？如何有效的进行这样的哈希？
2. 我们已经看到大范围键可以通过哈希或映射到小范围中，这时很有可能发生碰撞，该如何处理它们？

***
# 哈希函数
***
一个好的哈希函数应该满足以下要求
1. 快速计算，也就是在O（1）的时间复杂度内
2. 使用尽可能小的slot/Hash Table大小M
3. 尽可能均匀的将键分散到不同的地址∈ [0..M-1]
4. 实现尽可能小的冲突

常用的哈希函数设计有三种
## 除法哈希法（The Division Method）
***
```java
hash(key) = key mod m
```
其中key表示被哈希的关键字，m表示哈希表的大小，mod为取余操作。假定所选择的质数与关键字分布中的任何模式都是无关的，这种方法常常可以给出很好的结果。

## 乘法哈希法（The Multiplication Method）
***
```java
hash(key) = floor( m * ( A * key mod 1) )
```
其中 floor 表示对表达式进行下取整，常数 A 取值范围为（0<A<1），m 表示哈希表的大小，mod 为取余操作。[A * key mod 1] 表示将 key 乘上某个在 0~1 之间的数并取乘积的小数部分，该表达式等价于 [A*key - floor(A * key)]。
乘法哈希法的一个优点是对 m 的选择没有什么特别的要求，一般选择它为 2 的某个幂次，这是因为我们可以在大多数计算机上更方便的实现该哈希函数。

虽然这个方法对任何的 A 值都适用，但对某些值效果更好，最佳的选择与待哈希的数据的特征有关。Don Knuth 认为 A ≈ (√5-1)/2 = 0.618 033 988... 比较好，可称为黄金分割点。




假设我们有一个大小为M的哈希表，其中使用键来识别卫星数据，并且使用特定的哈希函数来计算哈希值。使用哈希函数从键v计算它的哈希值/哈希码，得到范围为0到M-1的整数。该哈希值用作卫星数据的哈希表的基本/父 索引/地址入口。

在讨论现实之前,让我们先讨论理想情况:完美哈希函数。一个完美的哈希函数是键和哈希值之间的一对一映射，即完全没有冲突。如果所有的键都是事先知道的话。例如，编译器/解释器搜索保留的关键字。但是，这种情况很少见。
当表的大小与提供的关键字数量相同时，可以实现最小的完美哈希函数。这种情况更是罕见。如果你有兴趣，您可以浏览[GNU gperf](https://www.gnu.org/software/gperf/)，这是一个免费提供的完美的哈希函数生成器，用C ++编写，可以从用户提供的关键字列表中自动构建完整的函数（一个C ++程序）。


***
# 开放寻址
***
通常采用的冲突解决策略为**开放寻址法**（Open Addressing），将所有的元素都存放在哈希表内的数组中，不使用额外的数据结构。开放寻址有三种方式：
- 线性探查（Linear Probing）—— **i=(base+step*1) % M**
- 二次探查（Quadratic Probing）—— **i=(base+step*step) % M**
- 双重哈希（Double Hashing）——  **i=(base+step*secondary) % M**

其中
M = HT.length = 当前哈系表大小
base = (key%HT.length)  =  键的基本地址
step = 当前探查步长
secondary = smaller_prime - key%smaller_prime = 次级哈希函数，smaller_prime是一个比M小的素数，这样写可以避免次级哈希函数为0

## 线性探查（Linear Probing）
***
### Insert(v)

开放寻址法的最简单的一种实现就是线性探查（Linear Probing），插入操作步骤如下：
1. 当插入新的元素时，使用哈希函数在哈希表中定位元素位置；
2. 检查哈希表中该位置是否已经存在元素。如果该位置内容为空，则插入并返回，否则转向步骤 3。
3. 如果该位置为i，则检查i+1是否为空，如果已被占用，则检查i+2，依此类推，直到找到一个内容为空的位置。

我们假设一个哈希表，**单元格区分空，占有和删除状态**，线性探查实现逻辑伪代码如下
```java
if N+1 == M, prevent insertion  //如果要插入元素数量N大于哈希表尺寸M，阻止插入

step = 0;   //当前探测的步数
i = base = key%HT.length;  // 基本地址，也就是通过哈希值获得的地址

while (HT[i] != EMPTY || HT[i] != DELETED)  //如果该地址不为空或不为删除状态，继续执行
  step++;    //要探测步长加1
  i = (base+step*1)%HT.length  //下一个地址
  
found insertion point, insert key at HT[i]
```
主要逻辑Java实现递归代码
```java
    public void insert(int key) {
        insertByIndex(hashing(key), key); // 传入基本地址，也就是该数据哈希值
    }

    public void insertByIndex(int index, int key) {
        if (hashTable[index] == Status.Empty || hashTable[index] == Status.Delete) { // 如果为空就直接插入
            hashTable[index] = key;
        } else {
            int nextIndex = (index + 1) % hashTable.length;  // 不为空设置下一个地址
            if(nextIndex != hashing(key))	//只查找一圈，防止哈希表全满时，递归无法收敛
            insertByIndex(nextIndex, key); //递归插入下一个地址
        }
    }
```
主要逻辑Java实现循环代码
```java
    public void insert(int key){
        int step = 0;
        int i = key  % hashTable.length;

        while(hashTable[i] != Status.Empty && hashTable[i] != Status.Delete){
            if(step > hashTable.length)
                throw new IndexOutOfBoundsException();
            step ++;
            i = (key + step * 1) % hashTable.length;
        }
        hashTable[i] = key;
    }
```
> 注意这里没有判断哈系表已满

### Search(v)

线性探查搜索操作类似于插入操作，步骤如下：
1. 当搜索指定元素时，使用哈希函数在哈希表中定位元素位置；
2. 检查哈希表中该位置是否已经存在元素。如果该位置内容为空，则返回-1表示元素不存在，否则转向步骤 3。
3. 如果该位置为i，则检查i+1是否为空，如果是返回-1，否则检查i+2，依此类推，直到找到该元素。

注意上面步骤中从基本地址开始搜索，如果遇到内容为空的单元格就停止搜索，因为根据插入原则，该元素存在的话不会为-1；伪代码实现为：
```java
step = 0;   //当前探测的步数
i = base = key%HT.length;  // 基本地址，也就是通过哈希值获得的地址
while (true)
  if (HT[i] == EMPTY)  // 如果当前单元格为空，返回未找到
	return "not found"
	
  else if (HT[i] == key) // 如果当前单元格为查找数据，返回索引
	return "found at index i"
	
  else step++; // 单元格不为空，也不为查找数据，探测步长加1
  i = (base+step*1)%HT.length //下一个地址
```
主要逻辑Java实现代码
```java
    public int search(int key) {
        return searchByIndex(hashing(key), key);  // 传入基本地址，也就是该数据哈希值
    }

    public int searchByIndex(int index, int key) {
        if (hashTable[index] == key) {   // 是搜索项的话，直接返回索引
            return index;
        }
        if(hashTable[index] == Status.Empty){ //判断当前索引处单元格是否为空，是的话返回为找到
            return -1;
        }		

        int nextIndex = (index + 1) % hashTable.length; //下一个地址
        if(nextIndex != hashing(key)){ // 判断是否已经查找了一圈
            return searchByIndex(nextIndex, key);
        }
        return -1;  // 返回未找到
    }
```

### Remove(v)

之前设置哈系表单元格状态分为空，占有，和删除，其中如果不设置删除状态，未插入数据的单元格和插入数据但已删除的单元格状态都为空，这时根据插入时原则，在判断时会出现问题：
```java
step = 0;   //当前探测的步数
i = base = key%HT.length;  // 基本地址，也就是通过哈希值获得的地址
while (true)
  if (HT[i] == EMPTY)  //如果为空，返回未找到，否则为占有或已删除则继续查找
  return "not found"
  
  else if (HT[i] == key)  //如果为要删除项，返回索引删除
  return "found at index i"
  
  else step++; // 单元格不为空，也不为查找数据，探测步长加1
  i = (base+step*1)%HT.length //新的地址
```
主要逻辑Java实现代码
```java
    public int remove(int key){
        return removeByIndex(hashing(key),key);
    }
    public int removeByIndex(int index,int key){
        if (hashTable[index] == key) {
            hashTable[index] = -2;
            return index;
        }
        if(hashTable[index] == -1){
            return -1;
        }
        int nextIndex = (index + 1) % hashTable.length;
        if(nextIndex != hashing(key)){
            return removeByIndex(nextIndex, key);
        }
        return -1;
    }
```

### 总结

线性探查的探查序列可以形式上如下描述：

> h（v） // 基本地址
（h（v） + 1\*<span style="color: red;">1</span>） % M // 如果有冲突，第1个探查步骤
（h（v） + 2\*<span style="color: red;">1</span>） % M // 如果还有冲突，第2个探查步骤
（h（v） + 3\*<span style="color: red;">1</span>） % M // 如果还有冲突，第3个探查步骤
...
（h（v） + k\*<span style="color: red;">1</span>） % M // 第K个探查步骤，等等...

虽然我们可以使用线性探查解决冲突，但这并不是最有效的方法。 我们将一个连续占用槽的集合定义为**群集**（cluster）。一个群集它覆盖了键的基本地址，这称为键的**一级群集**（Primary Clustering）。在insert(v)期间，如果存在冲突，但是在哈希表中存在空（或DEL）插槽，则我们确定在最多M个线性探测步长之后找到它。而当我们这样做的时候，冲突就会被解决，但键v的**一级群集**就会被扩展，未来的哈希表操作也会变慢。并且由于临近群集的合并，一级群集的尺寸将越来越大。这将增加Search(v)/Insert(v)/Remove(v)操作的运行时间，超出O（1）。对此一种改进的方式为二次探查（Quadratic Probing）


## 二次探查（Quadratic Probing）
***
为了减少一级群集，我可以可以这样修改探查序列

> h（v） // 基本地址
（h（v） + 1\*<span style="color: red;">1</span>） % M // 如果有冲突，第1个探查步骤
（h（v） + 2\*<span style="color: red;">2</span>） % M // 如果还有冲突，第2个探查步骤
（h（v） + 3\*<span style="color: red;">3</span>） % M // 如果还有冲突，第3个探查步骤
...
（h（v） + k\*<span style="color: red;">k</span>） % M // 第K个探查步骤，等等...

这样，二次探查跳过+1，+4，+9，+16，...的似乎能够解决我们早期使用线性探测的一级群集问题，但它依然会有些问题，如果二次探查的插槽恰好已经都有元素，这样即使哈系表依旧有空槽，但二次探查无法插入数据。

> **如果α<0.5和M是素数，那么我们总是可以使用二次探查找到一个空槽。回想一下：α是负载因子，M是哈希表大小（HT.length）**。

在二次探查中，沿着探查的路径形成群集，而不是像线性群集那样在基本地址周围形成群集，这样的被称为**二级群集**（Secondary Clusters）。所有的键使用相同的探查模式会形成二级群集，如果两个键拥有相同的基本地址，二次探查序列将会相同。二次探查中的二级群集并不像线性探查中的一级群集那样糟糕，因为一个好的哈希函数理论上应该将键分散到不同的基本地址∈[0..M-1]中。


## 双重哈希（Double Hashing）
***
为了减少一级和二级群集，我可以可以这样修改探查序列：

>h（v） // 基本地址
（h（v） + 1\*<span style="color: red;">h2(v)</span>） % M // 如果有冲突，第1个探查步骤
（h（v） + 2\*<span style="color: red;">h2(v)</span>） % M // 如果还有冲突，第2个探查步骤
（h（v） + 3\*<span style="color: red;">h2(v)</span>） % M // 如果还有冲突，第3个探查步骤
...
（h（v） + k\*<span style="color: red;">h2(v)</span>） % M // 第K个探查步骤，等等...

h2(v)被称为次级哈希函数，如果h2（v）= 1，则Double Hashing与Linear Probing完全相同。 所以我们通常希望h2（v）> 1来避免一级群集。如果h2（v）= 0，则由于任何探测步长乘以0，双重哈希不起作用，因为任何探测步长乘以0，即我们在冲突期间永远停留在基本地址 我们需要避免这种情况。通常（对于整数键），h2（v）= M' - v％M'，其中M'是比M小的素数。 这使得h2（v）∈[1..M']，其足够多样化以避免二级聚集。
> 次级哈希函数的使用从理论上避免了一级和二级群集的发生。

## 总结
***
总之，一个好的开放寻址冲突解决技术需要有：
1. 如果有空槽的话需要总能找到它
2. 最小化群集
3. 当两个不同的键冲突的时候，给出不同的探查序列
4. 足够快，O（1）

***
## 分离链接（Separate Chaining）
***
分离链接技术（Separate Chaining）是另一种冲突解决策略。它把哈希到同一个槽中的所有元素都放到一个链表中。分离链接技术将采用额外的数据结构来处理冲突，其将哈希表中每个位置（slot）都映射到了一个链表。当冲突发生时，冲突的元素将被添加到桶（bucket）列表中，而每个桶都包含了一个链表以存储相同哈希的元素。
![分离链接](\images\datastructure\seperatechain.gif)

如果我们使用分离链接技术，那么负载因子α= N / M描述列表的平均长度M，并且它确定Search（v）的性能，因为我们可能必须平均探索α元素。作为Remove（v） - 也需要Search（v），其性能将与Search（v）类似。insert(v)的时间复杂度显然是O（1）。如果我们可以将α绑定为小常数，则使用分离链接的所有Search（v），Insert（v）和Remove（v）操作将为O（1）。

***
## 哈希表扩增
***
当负载因子α越高时，哈希表的性能下降。对于（标准）二次探查冲突解决技术，当哈希表α> 0.5时，插入可能会失败。如果发生这种情况，我们可以重新哈希转换。我们使用新的哈希函数构建另一个哈希表，大小约是原来的两倍。我们重新计算原始哈希表中的所有键的新哈希值，并将卫星数据插入到新的更大的哈希表中，最后我们删除较旧的较小的哈希表。

> 根据经验，如果使用开放寻址，当α> 0.5时，重新哈希转换，如果使用分离链接，当α> 1.0时，重新进行哈希转换。