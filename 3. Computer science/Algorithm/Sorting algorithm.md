作为一个计算机专业相关的人员（程序猿），无论你从事什么方向（前端、后端、机器学习等），最最基础的就是对排序和查找的算法原理理解与实现。如果连这个还没有烂熟于心，随手就来的话，只能说明你的发展比较堪忧，因为这个是最最初级但也是显示该专业的最最扎实基础的部分，所以本人专门详细整理了十大排序算法及七大查找算法。本人打算用两篇博客分别进行探讨，本篇主要和大家分享一下十大排序算法。[下一篇](http://www.cnblogs.com/wujingqiao/articles/8975875.html)将和大家分享七大查找算法～接下来我会用自以为条理最清楚、语言最简洁的方式与大家进行分享～欢迎大家批评指正～

ps：笔者一直在努力修正更新中！～

## 阅读目录

> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#0)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#1)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#2)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#3)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#4)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#5)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#6)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#7)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#8)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#9)
> -   [](https://www.cnblogs.com/wujingqiao/articles/8961890.html#10)

## 0. 干货总结

### 0.1 整体分类

首先让我们宏观的分类一下几种排序算法：我们平常所说的排序算法大部分是指**内部排序算法**，其实还有三种常见的外部排序算法～

那么什么是内部排序和外部排序呢？

所谓的内排序是指所有的数据已经读入内存，在内存中进行排序的算法。排序过程中不需要对磁盘进行读写。同时，内排序也一般假定所有用到的辅助空间也可以直接存在于内存中。与之对应地，另一类排序称作外排序，即内存中无法保存全部数据，需要进行磁盘访问，每次读入部分数据到内存进行排序。

下面给出它们的具体分类：

-   内部排序算法：冒泡排序、快速排序、直接择排序、直接插入排序、希尔排序、归并排序、堆排序
    
    -   其中冒泡排序和快速排序属于交换排序，直接插入排序和希尔排序属于插入排序
-   外部排序算法：计数排序、基数排序、桶排序等
    

是不是有点乱，没关系，看一看我下面画的图表清醒一下！

![](https://images2018.cnblogs.com/blog/1386490/201805/1386490-20180501165444775-1799001999.png)

为什么里面的数据不填写？其实是我故意不填写的(用来检测对各种算法的理解程度)，以防止我以后回想和大家参考时总是不思考直接看结果，这样一来，我们就可以根据自己的理解自动脑补各个算法的时间空间复杂度、稳定性等了～大家别打我。。。当然相关结果也可以在我接下来的详细讲解中找得到嘞！反正千辛万苦只为一句话：透彻理解各种算法，彻底据为己有！

### 0.2 概念讲解

-   稳定性：如果i=j，排序前i在j的前面，排序后i仍然在j的前面，即相等的两个数字的相对位置在排序前后不变，则该算法是稳定的，否则不稳定。为了方便大家理解，举个形象点的例子: 用某一算法对[1,3,**2**,4,2]进行排序后的结果为[1,**2**,2,3,4]，我们可以看到排序前粗体**2**在细体2之前，排序之后仍然是，则该算法为稳定的。  
    大家一定会有疑问，稳定性有什么用呢，是这样的，排序算法如果是稳定的，那么**从一个键上排序，然后再从另一个键上排序，前一个键排序的结果可以为后一个键排序所用**。可能比较难理解，这里再举个例子方便理解，比如在基数排序中，先将低位排序，再逐次按高位排序，稳定的话就可以保证排序后低位元素的顺序在高位相同时是不会改变的，这里大家可以参考我下一篇的基数排序的详细讲解，这样会理解的更透彻一些～
    
-   时间复杂度：指执行算法所需要的工作量，即对待排序数据的总操作次数，我们用它来描述算法的运行时间。这里不能再多说了，不然会把笔者累死。。。大家想深入理解的话自行搜索学习吧～
    
-   空间复杂度：指执行算法所需的内存空间。
    

在这里我想特别嘱咐大家两点：

-   本人用的是python来实现代码，其实无论哪种语言，只要你懂得了算法其中的原理，稍微学习一点相应的不同语言基础语法，你就应该能够用相应的语言实现出来的～所以笔者认为，编程语言的不同并不会阻碍我们交流～如何你还在为实现代码所用的语言不同而苦恼，那你可能需要加一把劲儿喽～毕竟语言只是一门工具，你的大脑思想（算法）才是王道！这是本喳喳个人的理解，可能有误，忘大家见谅～
    
-   对于算法的时间复杂度、空间复杂度、稳定性等千万不要死记硬背（这也是我为什么不填写上面表格数据的原因，大家别骂我。。如果你是一个稍微不那么慵懒的程序猿，你就可以在我下面的详细讲解中找到全部答案，并深刻理解），深入理解了算法的原理，自然而然就可以得出来。大家千万不要死记硬背、照本宣科，举个例子，每个算法的稳定性并不是绝对的，比如冒泡算法若把判断条件由原来的**if L(j) > L(j+1)**改成**if L(j) >= L(j+1)**，那么冒泡算法的稳定性就由原来的稳定变为不稳定（后面会详细讲解，这里只是举个例子），所以死记硬背完全没有意义，理解算法融于脑中才是王道～
    

废话不多说，下面一一进行介绍这十大排序算法～

## 1. 冒泡排序（Bubble Sort）

### 1.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(n^2)
    -   最好情况：O(n)
    -   最坏情况：O(n^2)
-   空间复杂度：O(1)
    
-   稳定性：稳定 (仅限于本博客代码，前面说过若把判断条件由原来的**if L(j) > L(j+1)**改成**if L(j) >= L(j+1)**，那么稳定性就由原来的稳定变为不稳定！)，之后的算法稳定性都是这个道理，之后也不会在赘述了，大家一定要注意啊～！
    

### 1.1 基本思想

冒泡排序可以算是最简单、最基础的排序算法了，它的基本思想是：重复的遍历（走过）待排序的一组数字（通常是列表形式），依次比较两个相邻的元素（数字），若它们的顺序错误则将它们调换一下位置，直至没有元素再需要交换为止。因为每遍历一次列表，最大（或最小）的元素会经过交换一点点”浮“到列表的一端（顶端），所以形象的称这个算法为冒泡算法

### 1.2 具体步骤

1.  比较两个相邻元素，如果前一个比后一个大，则交换这两个相邻元素
2.  从头至尾对每一对相邻元素进行步骤1的操作，完成1次对整个待排序数字列表的遍历后，最大的元素就放在了该列表的最后一个位置上了
3.  对除最后一个元素的所有元素重复上述步骤，这第二次遍历后第二大的元素就也放在了正确的位置（整个列表的倒数第二位置上）
4.  不断重复上述步骤，每次遍历都会将一个元素放在正确的位置上，从而下次遍历的元素也会随之减少一个，直至没有任何一对数字需要比较

### 1.3 代码实现

```python3
#冒泡排序（python3实现）
def Bubble_Sort(L):
    for i in range(len(L)):
        for j in range(len(L)-i-1):
            if L[j] > L[j+1]:
                temp = L[j]
                L[j] = L[j+1]
                L[j+1] = temp
    return L
```

那么问题来了，上面代码的时间复杂度无论如何都是O（n^2）,那么冒泡算法的最好状况时间复杂度O(n)是从何而来的呢，这个其实是针对下面的改进的冒泡算法的！

### 1.4 改进的冒泡排序算法

细心的同学会发现上面的冒泡排序有些笨拙，如果一组数字一开始就完全有序的话，上述算法还是会萌萌的一遍一遍的遍历，其实完全不必要，所以我们就稍微改进一下嘞～

我们每次遍历一遍列表，同时记住交换元素的位置，若此次遍历无元素交换，则直接停止。

下面给出这种改进的冒泡算法的代码实现

```python3
# 改进的冒泡排序（python3实现）
def Imroved_Bubble_Sort(L):
    lenght = len(L)
    lastswap = lenght - 1
    for i in range(lenght):
        sign = lastswap
        for j in range(lastswap):
            if L[j] > L[j+1]:
                temp = L[j]
                L[j] = L[j+1]
                L[j+1] = temp
                lastswap = j
        if sign == lastswap:
            break
    return L
```

## 2. 快速排序(Quick Sort)

### 2.0 性质总结

-   时间复杂度： （关于快排复杂度的详细分析大家可以参考这篇[博客](https://blog.csdn.net/hn_gsf/article/details/52249621)，讲的很清楚，这里我就不再耗费精力赘述嘞～）
    
    -   平均情况：O(nlogn)
    -   最好情况：O(nlong)
    -   最坏情况：O(n^2)  
        其实不难理解，快排的最坏情况就已经退化为冒泡排序了！所以大家深入理解就会发现各个排序算法是相通的，学习时间久了你就会发现它们的内在联系！是不是很神奇哈～
-   空间复杂度：
    
    -   平均情况：O(logn)
    -   最好情况：O(logn)
    -   最坏情况：O(n)
-   稳定性：不稳定 （由于关键字的比较和交换是跳跃进行的，所以快速排序是一种不稳定的排序方法～）
    

### 2.1 基本思想

快速排序顾名思义，就是算法比较快嘞～当然，这个“快”是根据其平均情况O(nlogn)而言的，毕竟我们的世界里平均情况最普遍嘛，如果抬杠人士非要用各个算法的最坏情况来比教，那我也真没啥办法哈～快排的基本思想是：通过一趟排序将待排序列表分割成独立的两部分，其中一部分的所有元素都比另一部分小，然后再按此方法将独立的两部分分别继续重复进行此操作，这个过程我们可以通过递归实现，从而达到最终将整个列表排序的目的。

### 2.2 具体步骤

1.  从待排序列表（数组）中选择一个元素作为基准（pivot），这里我们选择最后一个元素元素
2.  遍历列表，将所有小于基准的元素放在其前面，这样就可以将待排序列表分成两部分了
3.  递归地对每个部分进行1、2操作，这里递归结束的条件是序列的大小为0或1，此时递归结束，排序就已经完成了

### 2.3 代码实现

```python3
# 快速排序（python3实现）
def Swap(L,i,j):       
    temp = L[i]
    L[i] = L[j]
    L[j] = temp

def Partition(L,left,right):
    pivot = L[right]
    tail = left - 1
    # 将所有小于基准的数依次堆到前面
    for i in range(left,right):
        if L[i] <=  pivot:
            tail += 1
            Swap(L,i,tail)
    Swap(L,tail+1,right)
    return tail + 1

def Quick_Sort(L,left,right):
    if left >= right:
        return
    pivot = Partition(L,left,right)
    QuickSort_wujingqiao(L,left,pivot-1)
    QuickSort_wujingqiao(L,pivot+1,right)
    return L
```

另附一个专属于python的代码（python的初衷就是用最少的代码完成功能！），且看且珍惜：

```cmake
def quick_sort(L):
    if len(L) < 2:   # 基线条件（停止递归的条件）
        return L
    else:
        base_value = L[0]     # 选择基准值
        less = [m for m in L[1:] if m < base_value]     # 由所有小于基准值的元素组成的子数组(python独有的切片，生成器等特性)
        greater = [m for m in L[1:] if m >= base_value]   # 由所有大于基准值的元素组成的子数组
    return quick_sort(less) + [base_value] + quick_sort(greater)
```

非递归实现快排

```haskell
# 快排非递归实现，即将需要排序的首尾下标存入栈中，依次出栈 
class Solution:
 
    def quickSortNoRecur(self, L, left, right):
        stack = []
        stack.append(left)
        stack.append(right)
        while stack:
            r = stack.pop()
            l = stack.pop()
            index = self.partSort(L, l, r)
            if l <= index - 1:
                stack.append(l)
                stack.append(index - 1)
            if r >= index + 1:
                stack.append(index + 1)
                stack.append(r)
    
    def partSort(self, L, left, right):
        tail = left
        for i in range(left, right):
            if L[i] < L[right]:
                self.swap(L, i, tail)
                tail += 1
        self.swap(L, tail, right)
        return tail

    def swap(self, L, i, j): 
        temp = L[i]
        L[i] = L[j]
        L[j] = temp
```

## 3. 直接选择排序(Straight Select Sort)

### 3.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(n^2)
    -   最好情况：O(n^2)
    -   最坏情况：O(n^2)  
        由此可见这排序可以号称最没效率的排序了。。。
-   空间复杂度：O(1)
    
-   稳定性：不稳定
    

### 3.1 基本思想

选择排序也很简单直观（其实这几种排序算法都很简单直观～。～），它的基本思想是：先在待排序列表中找到最小（大）的元素，把它放在起始位置作为已排序序列；然后，再从剩余待排序序列中找到最小（大）的元素放在已排序序列的末尾，以此类推，直至完毕。  
这个有点像暴力解决的意思，所以它的效率不高（最坏、最好、平均都一样很差。。。）

### 3.2 具体步骤

1.  初始状态整个待排序序列为无序序列，有序序列为空
2.  每次遍历无序序列将最小元素交换到有序序列之后
3.  n-1趟遍历后排序完成

### 3.3 代码实现

```python3
# 直接选择排序（python3实现）
def Straight_Select_Sort(L):
    for i in range(len(L)):
        min = i
        for j in range(i,len(L)):
            if L[j] < L[min]:
                min = j
        if min != i:
            temp = L[i]
            L[i] = L[min]
            L[min] = temp
    print(L)
    return  L
```

## 4. 堆排序(Heap Sort)

### 4.0 性质总结

-   时间复杂度：初始化堆O(n)因为循环内部需要比较的次数是O(n)，交换元素后调整堆O(nlogn)因为做n-1次交换，每次交换后的调整为3(n-1)h, h=logn
    
    -   平均情况：O(nlogn)
    -   最好情况：O(nlogn)
    -   最坏情况：O(nlogn)  
        可以看出，堆排序效率很好，所以大家一定要会熟练将堆排序应用到各种场景中～
-   空间复杂度：O(1)
    
-   稳定性：不稳定
    

### 4.1 基本思想

堆排序比之前几种排序稍微难理解一些，理解此算法，大家需要具备一些关于堆这种数据结构的知识储备。堆的结构相当于一个完全二叉树，最大堆满足下面的性质：父结点的值总大于它的孩子结点的值。

### 4.2 具体步骤

1.  将待排序列表构造成一个最大堆，作为初始无序堆（即初始无序列表）
2.  将堆顶元素（最大值）与堆尾元素互换
3.  将该堆（无序区）尺寸缩小1，并对缩小后的堆重新调整为最大堆形式
4.  重复上述步骤，直至堆（无序区）的尺寸变为1，此时排序完成

### 4.3 代码实现

```python3
# 堆排序（python3实现）
def Swap(L,i,j):
    temp = L[i]
    L[i] = L[j]
    L[j] = temp

def Heap_adj(L, i, size):      # 从L[i]向下进行堆调整
    left_child = 2 * i + 1     # 左孩子索引
    right_child = 2 * i + 2    # 右孩子索引
    max = i                    # 选出当前结点与其左右孩子结点中的最大值
    if left_child < size and L[left_child] > L[max]:
        max = left_child
    if right_child < size and L[right_child] > L[max]:
        max =  right_child
    if max != i:
        Swap(L, i, max)        # 将当前结点和最大子结点交换
        Heap_adj(L, max, size) # 递归向下进行堆调整（每次子结点与父结点交换后，需将以子结点为根的完全二叉树调整为大顶堆）

def Heap_Build(L):           # 建堆、初始堆时，从下往上调整；交换堆顶与堆尾后，从上往下调整
    heap_size = len(L)
    for i in range(heap_size // 2 - 1, -1, -1):   # 从每一个非叶结点开始向上进行堆调整
        Heap_adj(L, i, heap_size)

def Heap_Sort(L):
    heap_size = len(L)    # 先建立最大堆
    Heap_Build(L)
    while heap_size > 1:            # 直至堆尺寸缩减为1,排序结束
        heap_size -= 1
        Swap(L, 0, heap_size)
        Heap_adj(L, 0, heap_size)
    return L
```

## 5.0 直接插入排序(Straight Insertion Sort)

### 5.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(n^2)
    -   最好情况：O(n)
    -   最坏情况：O(n^2)
-   空间复杂度：O(1)
    
-   稳定性：稳定
    

### 5.1 基本思想

顾名思义，直接插入排序就是将未排序元素一个个的插入到已排序列表中，它的基本思想是：对于未排序元素，在已排序序列中从后向前扫描，找到相应位置把它插入进去；在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为新元素提供插入空间。

### 5.2 具体步骤

1.  从第一个元素开始，默认该元素已被排好序
2.  取出下一个元素，在已经排序的元素序列中从后向前扫描
3.  如果该元素（已排序）大于新元素，将该元素移到下一位置
4.  重复步骤3，直到找到已排序的元素小于或者等于新元素的位置
5.  将新元素插入到该位置后
6.  重复步骤2~5

### 5.3 代码实现

```python3
# 直接插入排序（python3实现）
def Insert_Sort(L):
    for i in range(1,len(L)):
        temp = L[i]
        j =  i-1
        while L[j] > temp and j >= 0:
            L[j+1] = L[j]
            j -= 1
        L[j+1] = temp
    return L
```

## 6.0 希尔排序(Shell Sort)

### 6.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(nlogn~n^2)
    -   最好情况：O(n^1.3)
    -   最坏情况：O(n^2)
-   空间复杂度：O(1)
    
-   稳定性：不稳定
    

### 6.1 基本思想

我们还可以将希尔排序叫做缩小增量排序，它是插入排序的一种更高效的改进版本。它与简单插入排序不同的是，它优先比较距离较远的元素。希尔排序通过将比较的全部元素分为几个区域来提升插入排序的性能。

它的基本思想是：将待排序列表按下标的一定增量分组（比如增量为2时，下标1，3，5，7为一组，下标2，4，6，8为另一组），各组内进行直接插入排序；随着增量的越来越小，每组所包含的数字序列越来越多，当增量减至1时，整个序列被分成一个组，排序就完成了了。

### 6.2 具体步骤

1.  选择一个增量序列（定义增量的递减状况，直至最后为1）
2.  按增量序列的个数k，对序列进行k趟排序
3.  每趟排序，对各分组进行直接插入排序

### 6.3 代码实现

```python3
# 希尔排序（python3实现）
def Shell_Sort(L):
    n = len(L)
    h = 0
    while h <= n:                         # 生成初始增量
        h = 3 * h + 1
    while h >= 1:                        # 增量缩小到1时完成排序
        for i in range(h, n):            # 对每组进行直接插入排序
            j = i - h
            temp = L[i]
            while j >= 0 and L[j] > temp:
                L[j + h] = L[j]
                j -= h
            L[j + h] = temp
        h = (h - 1) // 3                   # 递减增量
    return L
```

## 7. 归并排序(Merge Sort)

### 7.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(nlogn)
    -   最好情况：O(nlogn)
    -   最坏情况：O(nlogn)
-   空间复杂度：O(n)
    
-   稳定性：稳定  
    可以看出，归并排序的效率很好（与堆排序一样），只是它的空间复杂度较高（需要占用一定的内存空间）
    

### 7.1 基本思想

归并排序的递归实现是算法设计中分治策略的典型应用，它的基本思想是：递归的将两个已排序的序列合并成一个序列。

### 7.2 具体步骤

1.  申请空间，其大小为两个已经排序序列之和，该空间用来存放合并后的序列
2.  设定两个指针，最初位置分别为两个已经排序序列的起始位置
3.  比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置

### 7.3 代码实现

```python3
# 归并排序（python3实现）
def Merge(left, right):
    l, r = 0, 0
    result = []
    while l < len(left) and r < len(right):   # 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
        if left[l] < right[r]:
            result.append(left[l])
            l += 1
        else:
            result.append(right[r])
            r += 1
    result += left[l:]                       # 若最后left列表剩余，则将其剩余部分加入到result后面
    result += right[r:]                      # 若最后right列表剩余，则将其剩余部分加入到result后面
    return result

def Merge_Sort(L):
    if len(L) <= 1:
        return L
    mid = len(L) // 2         # 这里的//是python3中除以后取整的用法，大家不要以为我打错了～
    left = Merge_Sort(L[:mid])
    right = Merge_Sort(L[mid:])
    return Merge(left, right)
```

上面和大家分享的7种排序算法是属于内部排序算法的，下面和大家分享3种外部排序算法～

## 8. 计数排序（Counting Sort）

### 8.0 性质总结

当输入的元素是 n 个 0到 k 之间的整数时，时间复杂度是O(n+k)，空间复杂度也是O(n+k)，且计数排序是稳定的。

### 8.1 基本思想

计数排序的优势在于在对一定范围内的整数排序时，它的复杂度为Ο(n+k)（其中k是整数的范围），快于任何排序算法（平均情况）！当然这是一种牺牲空间换取时间的做法，而且当O(k)>O(n*log(n))的时候其效率反而不如某些排序算法（堆排序、归并排序）

它的基本思想是：对于给定的输入序列中的每一个元素x，确定该序列中值小于x的元素的个数（此处并非比较各元素的大小，而是通过对元素值的计数和计数值的累加来确定）。一旦有了这个信息，就可以将x直接存放到最终的输出序列的正确位置上。

例如，如果输入序列中只有17个元素的值小于x的值，则x可以直接存放在输出序列的第18个位置上。当然，如果有多个元素具有相同的值时，我们不能将这些元素放在输出序列的同一个位置上，因此，上述方案还要作适当的修改。

这里需要注意两点：

-   计数排序算法没有用到元素间的比较，它利用元素的实际值来确定它们在输出数组中的位置。因此，计数排序算法不是一个基于比较的排序算法，从而它的计算时间下界不再是Ω(nlogn)。
    
-   计数排序算法之所以能取得线性计算时间的上界是因为对元素的取值范围作了一定限制，即k=O(n)。如果k=n2,n3,..，就得不到线性时间的上界。
    

### 8.2 具体步骤

1.  统计列表L中每个值L[i]出现的次数，存入count[L[i]]
2.  从前向后，使列表count中的每个值等于其与前一项相加，这样列表count[L[i]]就变成了代表列表L中小于等于L[i]的元素个数
3.  反向填充目标列表target：将列表元素L[i]放在列表targrt的第count[L[i]]个位置（下标为count[L[i]] - 1），每放一个元素就将count[L[i]]递减

### 8.3 代码实现

```python3
# 计数排序（python3实现）
def Counting_Sort(L):
    n = len(L)
    k = 100    # 这里暂时定基数为100，即排序[0,99]内的整数
    count = [0 for i in range(k)]
    target = [0 for i in range(n)]
    for i in range(n):       # 让C[i]保存等于L[i]的元素个数
        count[L[i]] += 1
    for i in range(1, k):       # 让C[i]保存小于等于L[i]的元素个数，排序后元素L[i]就放在第C[i]个输出位置上
        count[i] = count[i] + count[i - 1]
    for i in range(n-1, -1, -1):     # 反向填充目标列表
        count[L[i]] -= 1
        target[count[L[i]]] = L[i]
    for i in range(n):       # 将目标列表拷贝回原列表
        L[i] = target[i]
    return L
```

## 9. 桶排序（Bucket Sort）

### 9.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(n)
    -   最好情况：O(n)
    -   最坏情况：O(nlogn)或O(n^2)  
        最坏情况是只有一个桶的情况，所以当然是取决于这个桶内你所采用的排序算法嘞～，在本片博客中，我在桶内用的是直接插入算法，所以最坏情况为O(n^2)。
-   空间复杂度：O(n + bucket_num)
    
-   稳定性：稳定
    

### 9.1 基本思想

桶排序又叫箱排序，它是计数排序的升级版，利用了函数的映射关系将数据分到有限数量的桶里，每个桶再分别进行排序（使用别的排序方法或者递归的继续使用桶方法排序）。

### 9.2 具体步骤

1.  设置定量列表（数组）当作空桶
2.  遍历待排序序列，将数据一个一个放到对应的桶里面
3.  对每个非空桶进行排序（本片博客桶内采用直接插入排序，当然大家也可以用其他排序试一试）
4.  把排好序的非空桶里的序列拼接在一起

### 9.3 代码实现

```python3
# 桶排序（python3实现）
bucket_num = 5     # 这里暂定将待排序序列分为5个桶，大家也可以根据输入动态的定义此变量
bound = [0 for i in range(bucket_num)]         # 计数列表，存放桶的边界信息

def Insertsort(L, left, right):           # 直接插入排序（应用于桶内排序中）
    for i in range(left + 1,right + 1):
        get = L[i]
        j = i - 1
        while j >= left and L[j] > get:
            L[j + 1] = L[j]
            j -= 1
        L[j + 1] = get
    return L

def Map(x):           # 映射函数,将整个待排序序列分割成不同的桶
    return x//10

def Countingsort(L):       # 计数排序（应用于桶边界的确认）
    n = len(L)
    for i in range(n):
        bound[Map(L[i])] += 1
    for i in range(1, bucket_num):
        bound[i] = bound[i] + bound[i - 1]
    temp = [0 for i in range(n)]
    for i in range(n-1, -1, -1):
        bound[Map(L[i])] -= 1
        temp[bound[Map(L[i])]] = L[i]
    for i in range(n):
        L[i] = temp[i]
    return L

def Bucket_Sort(L):
    n = len(L)
    Countingsort(L)                   # 利用计数排序确定各个桶的边界
    for i in range(bucket_num):       # 每个桶内采用直接插入排序
        left = bound[i]               # bound[i]为桶的左边界
        if i == bucket_num - 1:
            right = n - 1             # bound[i + 1] - 1为桶的右边界
        else:
            right = bound[i + 1] - 1
        if left < right:                 # 对元素个数大于1个桶进行直接插入排序
            Insertsort(L, left, right)
    return L
```

## 10. 基数排序（Radix Sort）

### 10.0 性质总结

-   时间复杂度：
    
    -   平均情况：O(n*bucket_num)
    -   最好情况：O(n*bucket_num)
    -   最坏情况：O(n*bucket_num)
-   空间复杂度：O(n*bucket_num)
    
-   稳定性：稳定
    

### 10.1 基本思想

基数排序的基本思想是：将所有待比较正整数统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始进行基数为10的计数排序，一直到最高位计数排序完后，数列就变成一个有序序列（利用了计数排序的稳定性）。由此可知，基数排序、桶排序、计数排序三者关系很紧密，尤其计数排序！

### 10.2 具体步骤

1.  确定位数基数
2.  从低位到高位，每位看作一个桶，每个桶内进行计数排序
3.  最高位桶排序完成后，整个排序便完成了

### 10.3 代码实现

```python3
# 基数排序（python3实现）
bucket_num = 3    # 这里暂定待排序元素为三位数及以下，大家也可以动态的定义此变量
k = 10            # 基数为10,即每一位数字范围为[0,9]

def Get_Digit(x, d):           # 获取元素x的第d位数字(由低到高)
    redix = [1, 1, 10, 100]    # 最大为三位数，所以这里只要到百位就够了
    return (x // redix[d]) % 10

def Counting_Sort(L, d):       # 计数排序，应用于每位对应的桶内排序
    n = len(L)
    count = [0 for i in range(k)]
    temp = [0 for i in range(n)]
    for i in range(n):
        count[Get_Digit(L[i], d)] += 1
    for i in range(k):
        count[i] = count[i] + count[i - 1]
    for i in range(n-1, -1, -1):
        count[Get_Digit(L[i], d)] -= 1
        temp[count[Get_Digit(L[i], d)]] = L[i]
    for i in range(n):
        L[i] = temp[i]
    return L

def Radix_Sort(L):
    for d in range(bucket_num + 1):    # 从低位到高位，依次依据第d位数字对L进行计数排序
        Counting_Sort(L, d)
    return L
```

#### 至此，本人整理的十大排序算法已全部完成，希望大家给予批评指正，共同进步～

### 如果觉得这文章还算用心，请劳驾点击右下角的推荐，这是对我们这些做开源分享的最大的肯定，谢谢。

------口天丶木乔

分类: [data structure](https://www.cnblogs.com/wujingqiao/category/1381865.html)

好文要顶 关注我 收藏该文 ![](https://common.cnblogs.com/images/icon_weibo_24.png) ![](https://common.cnblogs.com/images/wechat.png)

[![](https://pic.cnblogs.com/face/1386490/20180425204931.png)](https://home.cnblogs.com/u/wujingqiao/)

[口天丶木乔](https://home.cnblogs.com/u/wujingqiao/)  
[粉丝 - 14](https://home.cnblogs.com/u/wujingqiao/followers/) [关注 - 7](https://home.cnblogs.com/u/wujingqiao/followees/)  

+加关注

7

0

[«](https://www.cnblogs.com/wujingqiao/p/8955405.html) 上一篇： [Markdown总结整理](https://www.cnblogs.com/wujingqiao/p/8955405.html "发布于 2018-04-26 22:35")  
[»](https://www.cnblogs.com/wujingqiao/p/8978652.html) 下一篇： [word2vec详解与实战](https://www.cnblogs.com/wujingqiao/p/8978652.html "发布于 2018-05-02 09:06")

posted @ 2018-04-27 12:47  [口天丶木乔](https://www.cnblogs.com/wujingqiao/)  阅读(15001)  评论(10)  [编辑](https://i.cnblogs.com/EditPosts.aspx?postid=8961890)  收藏  举报