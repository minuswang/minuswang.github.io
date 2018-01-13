---
title: 一个Quicksort究竟可以写到多么短
date: 2017-11-10 10:03:29
tags:
---
[原文转载自-博客园](http://www.cnblogs.com/figure9/archive/2010/12/10/1902711.html)

说实话，我从来没有能一次写对一个快速排序，总是有各种各样的错误。快排麻烦就麻烦在，没办法去调试它，因为它是生成递归的，只能去静态调试，或者是不断的打印数组的状态以推测错误的可能性。 然而快排的基本思想却是极其简单的：接收一个数组，挑一个数，然后把比它小的那一摊数放在它的左边，把比它大的那一摊数放在它的右边，然后再对这个数左右两摊数递归的执行快排过程，直到子数组只剩一个数为止。下面我先用最常用的C语言来写一个快速排序：

首先可以先写出一些伪代码：
```c
void quicksort(int array[], int left, int right)
{
    //Do nothing if left <= right
    //p <- Get a number from array
    //Put elements <= p to the left side
    //Put elements >= p to the right side
    //Put p in the middle slot which index is pivot
    //Recursive quicksort the left parts and right parts
}
```
然后慢慢的把这些伪代码转化成`C code`：

>Step 1:

```c
void quicksort(int array[], int left, int right)
{
    if(left<right)
    {
        //p <- Get a number from array
        //Put elements <= p to the left side
        //Put elements >= p to the right side
        //Put p in the middle slot which index is pivot
        //Recursive quicksort the left parts and right parts
    }
}
```

>Step 2:获得数组中的某个元素，在这里总是获取最左边的元素


```c
void quicksort(int array[], int left, int right)
{
    if(left<right)
    {
        int p=array[left];
        //Put elements <= p to the left side
        //Put elements >= p to the right side
        //Put p in the middle slot which index is pivot
        //Recursive quicksort the left parts and right parts
    }
}
```

>Step 3:这是比较麻烦的一步，可以遍历数组，如果碰到比标记值小的数，就把它与前面的数字交换，这样遍历一遍之后，小的数字就被移到了前面

```c
void quicksort(int array[], int left, int right)
{
    //Do nothing if left <= right
    if(left<right)
    {
        //p <- Get a number from array
        int p=array[left];
        //Put elements < p to the left side
        //Put elements >= p to the right side
        int i=left,j;
        for(j=left+1;j<=right;j++)
        {
            if(array[j]<p)
            {
                i++;
                swap(array,i,j);
            }
        }
        //Put p in the middle slot which index is pivot
        swap(array,i,left);
        //Recursive quicksort the left parts and right parts
    }
}
```

>Step 4:容易发现之前所做的步骤是一个划分数组的过程，为了便于理解，可以把它提升成一个函数。与此同时递归的快排左右两部分就可以了。

```c
void partition(int array[],int left, int right)
{
    //p <- Get a number from array
    int p=array[left];
    //Put elements < p to the left side
    //Put elements >= p to the right side
    int i=left,j;
    for(j=left+1;j<=right;j++)
    {
        if(array[j]<p)
        {
            i++;
            swap(array,i,j);
        }
    }
    //Put p in the middle slot which index is pivot
    swap(array,i,left);
    return i;
}
```

```c
void quicksort(int array[], int left, int right)
{
    //Do nothing if left <= right
    if(left<right)
    {
        int pivot=partition(array,left,right);
        //Recursive quicksort the left parts and right parts
        quicksort(array,left,pivot-1);
        quicksort(array,pivot+1,right);
    }
}
```

>Step 5: 最后套一个wrapper，就完成了一个基本的qsort

```c
void q_sort(int array[], int size)
{
    quicksort(array, 0, size-1);
}
```
当然上面的步骤省去了很多的测试及调试过程，之前我也提到过了，我做不到一下子写一个正确的快排，但一步一步来的话，还是可以写出来的。 接下来回到标题：quicksort能够写多短？ 拿之前的C程序来说，把它弄短的途径无非是展开函数，去除大括号等山寨方法。

>Step 1：展开partition函数并去除注释

```
void quicksort(int array[], int left, int right)
{
    if(left<right)
    {
        int p=array[left];
        int pivot=left,j;
        for(j=left+1;j<=right;j++)
        {
            if(array[j]<p)
            {
                pivot++;
                swap(array,pivot,j);
            }
        }
        swap(array,pivot,left);
        quicksort(array,left,pivot-1);
        quicksort(array,pivot+1,right);
    }
}
```

>Step 2：去除临时变量，大括号并把自增自减操作放在一个语句里

```
void quicksort(int array[], int left, int right)
{
    if(left<right){
        int pivot=left,j;
        for(j=left+1;j<=right;j++)
            if(array[j]<array[left])
                swap(array,++pivot,j);
        swap(array,pivot,left);
        quicksort(array,left,pivot-1);
        quicksort(array,pivot+1,right);
    }
}
```

>Step 3：利用C的指针算术，去掉多余的参数

```
void quicksort(int *array, int n)
{
    if(n>1){
        int pivot=0,j;
        for(j=1;j<n;j++)
            if(array[j]<array[0])
                swap(array,++pivot,j);
        swap(array,0,pivot);
        quicksort(array,pivot);
        quicksort(array+pivot+1,n-pivot-1);
    }
}
```

这样的话可以把原本20多行的快排缩减到10行，但是这样有什么意义呢，程序的可读性大为下降，而且运行效率也没有丝毫的提升。此外，指针算术很可能会导致各种越界错误。 况且C语言的话，再短也短不了多少了，接下来可以看看快排在其它的语言中的实现，鉴于`Java`,`C#`之类的语言实际上和`C`是一个系列的（都是基于`Von-Neuman`体系的`Imperative Language`）。我来展示如何用`Declarative Language`中来编写`quicksort`，在这里我使用`Scheme`（函数式语言）和`Python`（脚本语言）来演示。

接下来展示如何用Scheme编写的quicksort

`Scheme是MIT的Guy Steele和Jay Sussman开发的一种函数式编程语言`，大名鼎鼎的Sicp（Structure and Intepretation of Computer Programs）和Htdp（How to design programs）采用的就是Scheme语言，它的语法非常简单，但功能很强大，mit的6.001课程就选用scheme作为计算机专业学生的入门语言。
```
;; define x as value 1
(define x 1)
;; define l as a list of 1 2 3
(define x (list 1 2 3))
;; define f as a square function
(define (f x) (* x x))
;; define a function use lambda
(define f (lambda (x) (* x x)))
;; use a high-order function filter, will return (list 1 2 3)
(filter (lambda (x) (<= x 3)) (list 1 2 3 4 5))
```

关于Scheme语言的更多内容可以参考The little schemer或是网上的specification，在这里就不赘述了。
让我们回到快速排序这个话题，按照之前的思路，首先写一下伪码：

```
;; Sort a number list via quicksort algorithm
;; list of numbers -> list of numbers
(define (q-sort l)
  ;;get a number p from l
  ;;get numbers<=p from l-{p} as small part
  ;;get number>p from l-{p} as bigger part
  ;;recursively quicksort on small part and bigger part
  ;;combine small part, p, bigger part together as the sorted list
  )
```

>Step 1: 首先获得序列中的某个数，在这里取第一个数

```
;; Sort a number list via quicksort algorithm
;; list of numbers -> list of numbers
(define (q-sort l)
  ;;get a number p from l
  (let ((p (first l)))
      ;;get numbers<=p from l-{p} as small part
      ;;get number>p from l-{p} as bigger part
      ;;recursively quicksort on small part and bigger part
      ;;combine small part, p, bigger part together as the sorted list
     )
  )

```

>Step 2: 从序列中提取出比标记值小的一摊数和大的一摊数，然后对其进行递归的快排调用

```
;; Sort a number list via quicksort algorithm
;; list of numbers -> list of numbers
(define (q-sort l)
  (cond
    [(empty? l) empty]
    [(empty? (rest l)) (list (first l))]
    [else
    ;;get a number p from l
    ;;get numbers<=p from l-{p} as small part
    ;;get number>p from l-{p} as bigger part
     (let ((small-part (filter (lambda (x) (<= x (first l))) (rest l)))
           (big-part (filter (lambda (x) (> x (first l))) (rest l))))
        ;;recursively quicksort on small part and bigger part
       )]
    )
  )

```

>Step 3:为这个递归函数加上终止条件

```
;; Sort a number list via quicksort algorithm
;; list of numbers -> list of numbers
(define (q-sort l)
  (cond
    [(empty? l) empty]
    [(empty? (rest l)) (list (first l))]
    [else
    ;;get a number p from l
    ;;get numbers<=p from l-{p} as small part
    ;;get number>p from l-{p} as bigger part
     (let ((small-part (filter (lambda (x) (<= x (first l))) (rest l)))
           (big-part (filter (lambda (x) (> x (first l))) (rest l))))
        ;;recursively quicksort on small part and bigger part
       (append (q-sort small-part)
               (list (first l))
               (q-sort big-part)))]
    )
  )

```

可以发现`scheme`程序的一大特点就是声明性，你只需告诉它`what to do`，而C程序的话则强调`How to do`，实际上，上面的程序根本不需要注释，它自己的代码已经足够说明自己的用途了。
最终的Scheme程序：

```
;; Sort a number list via quicksort algorithm
;; list of numbers -> list of numbers
(define (q-sort l)
  (cond
    [(empty? l) empty]
    [(empty? (rest l)) (list (first l))]
    [else
     (let ((small-part (filter (lambda (x) (<= x (first l))) (rest l)))
           (big-part (filter (lambda (x) (> x (first l))) (rest l))))
       (append (q-sort small-part)(list (first l))(q-sort big-part)))]
    )
  )
```


### 最后我们看看如何用`Python`来构建一个快速排序：

需要注意的是，`Python`和前面的`C、Scheme`语言不一样，它是一个`multi-paradigm programming language`，换句话说，用`python`既可以procedural programming ,也可以oop甚至是fp。 首先用C的思想来写一个python版的快排：

### Step 1: 列出伪代码，要实现的步骤

```python
def q_sort(l):
    #get first number p from l
    #move elements<p to the left side
    #move elements>=p to the right side
    #recursively quicksort left and right part
    #combine them together
```
### Step 2: 进一步细化，这里利用了Python支持在函数内部定义函数的性质

```python
def q_sort(l):
    def quicksort(l,left,right):
        #get first number p from left end
        #move elements<p to the left side
        #move elements>=p to the right side
        #recursively quicksort left and right part
        #combine them together
    quicksort(l,0,len(l)-1)
```

### Step 3: 再一步细化移动list元素的过程，就完成了这个函数，Python支持多重赋值，所以交换元素非常方便

```python
def q_sort(l):
    def quicksort(l,left,right):
        if right>left:
            #get first number p from left end
            pivot,j,tmp=left,left+1,l[left]
            #move elements<p to the left side
            #move elements>=p to the right side
            while j<=right:
                if l[j]<tmp:
                    pivot=pivot+1
                    l[pivot],l[j]=l[j],l[pivot]
                j=j+1
            l[left],l[pivot]=l[pivot],l[left]
            #recursively quicksort left and right part
            quicksort(l,left,pivot-1)
            quicksort(l,pivot+1,right)
    quicksort(l,0,len(l)-1)
```
`python`有一个很好用的特性就是`list comprehension`，利用这个特性可以写出声明性很强的代码，比如说：

```python
# Get all even numbers between 1 and 100
[x for x in range(1,101) if x%2==1]
# Get all line which start with 'From'
[line for line in lines if line.startwith('From')]
```

### 我们可以直接使用这个特性来构建一个更易懂的quicksort，回到之前的第一步：

```python
def q_sort(l):
    #get first number p from l
    #move elements<p to the left side
    #move elements>=p to the right side
    #recursively quicksort left and right part
    #combine them together
```

### Step 2: 利用`python`的`list slice`和`list comprehension`，很容易得到比标记值大和小的两部分，然后把它们拼接起来即可

```python
def q_sort(l):
    #get first number p from l
    p=l[0]
    #move elements<p in l-{p} to the left side
    small_part=[x for x in l[1:] if x<p]
    #move elements>=p in l-{p} to the right side
    big_part=[x for x in l[1:] if x>=p]
    #recursively quicksort left and right part and combine them together
    return q_sort(small_part)+[p]+q_sort(big_part)
```

执行程序，`oops`，进入死循环了。

### Step 3: 增加终止条件

```python
def q_sort(l):
    if len(l)<=1:
        return l
    else:
        #get first number p from l
        p=l[0]
        #move elements<p in l-{p} to the left side
        small_part=[x for x in l[1:] if x<p]
        #move elements>=p in l-{p} to the right side
        big_part=[x for x in l[1:] if x>=p]
        #recursively quicksort left and right part and combine them together
        return q_sort(small_part)+[p]+q_sort(big_part)
```

这次运行结果正常了，需要注意的是，这个q_sort并不是进行原地排序，而是不断的生成新的list，当list的元素很多时会对性能造成很大的负面影响，这里只是为了演示python的特性才这么写。 如何把它变得更短呢？首先去掉注释试试

### Step 4: 去除注释

```python
def q_sort(l):
    if len(l)<=1:
        return l
    else:
        p=l[0]
        small_part=[x for x in l[1:] if x<p]
        big_part=[x for x in l[1:] if x>=p]
        return q_sort(small_part)+[p]+q_sort(big_part)
```

去除注释之后的`q_sort`函数只剩下8行了，还可以更短些吗？答案是肯定的。 注意到q_sort里面用了不少的临时变量，可以把它们去除掉

### Step 5: 去除临时变量

```python
def q_sort(l):
    if len(l)<=1:
        return l
    else:
        return q_sort([x for x in l[1:] if x<l[0]])+[l[0]]+q_sort([x for x in l[1:] if x>=l[0]])
```

只剩下5行了！

### Step 6: 利用Python的3元表达式 if else 来改写上面的函数，注意此处的逻辑是相同的

```python
def q_sort(l):
    return l if len(l)<=1 else q_sort([x for x in l[1:] if x<l[0]])+[l[0]]+q_sort([x for x in l[1:] if x>=l[0]])
```

现在只剩下2行了，需要注意的是python也提供了lambda表达式，因此q_sort可以被进一步简化：

### Final Step: 利用lambda表达式，再次简化

```python
q_sort= lambda l: l if len(l)<=1 else q_sort([x for x in l[1:] if x<l[0]])+[l[0]]+q_sort([x for x in l[1:] if x>=l[0]])
```
一个Quicksort究竟可以写到多么短呢？上面的代码给出了答案，1行就足够了。

>总结：

我们可以看到不同的语言在处理同样的问题时所采用的方案也是截然不同的，C语言强调的是how to do，而Scheme、Python则强调的是what to do。使用C的话要注重细节，因为每一个小失误都可能会使程序down掉，这是imperative language的通性，而诸如scheme、python这样的语言，所注重的则不是实现的细节，而是解决问题的逻辑，我们只需要告诉电脑what to do，而compiler将会为你生成对应的代码，这样我们就不需要把大把的精力花费在调试程序的细节方面了。 

一些人认为程序语言只是表述思想的一种方式，换句话说，他们认为语言无关紧要，重要的是所谓的思想。甚至有一些人认为掌握一门主流语言（诸如C/C++/Java/C#等）就足够了，因为语言都是相通的。 的确，C语言的熟手学Java语法，可能也就是两三天的功夫，而C++的熟手学C#的使用的话顶多就是一上午不到。但需要注意的是，知道一门语言的语法和熟练使用一门语言是很不一样的，每一种语言都有它独到的一面，而这些成分需要使用此语言相当长的时间才能够体会到。

Steve McConnel曾说过，要`Program into a language`而不是`Program on a language`[4]，打个比方，`假设我熟悉C，然后花了两天学会了Java的语法，但这不代表我就能写出像样的Java程序了，因为我不知道GC，不知道Spring, Hibernate等一系列框架，也不熟悉OOP。` 换个说法，我们从初中开始学英语学到现在也有十年多了，但我们说的English还是被外国人称为Chinglish，why？我认为这不是我们发音不够好或是不够流利，而是我们一直都是thinking in the Chinese way，在这种情况下是不可能说出纯正的English来的。 

所以我认为诸如语言无关论的观点都是错误的，一方面持有这些观点的人的眼光一般很狭窄，只是限定在命令式程序设计语言语言的圈子里（因为根本不知道其它范式的程序设计语言的存在，所以他们自然认为所有语言都是相通的了）；另外一方面，这些观点的持有者认为语言并不会影响思想，但实际上并不是如此，一个Lisp程序员和一个C程序员去处理同一个问题的思路肯定相差甚远，抛开程序设计语言，`即使是不同的语言，比如说汉语和英语，也会影响人的思路`（著名的Sapir-Whorf假设），这也就是我们常说的东方人思维和西方人思维的产生原因之一[5]。

`Eric Raymond`在他的`How to become a hacker`一文中就曾提到过作为一个`Hacker`应该掌握`Python, C/C++, Java, Perl`这5种语言[6]，因为它们各自代表了一种截然不同的编程方式：

>It's best, actually, to learn all five of Python, C/C++, Java, Perl, and LISP. Besides being the most important hacking languages, they represent very different approaches to programming, and each will educate you in valuable ways.

`Peter Norvig`则更加直接[7]：

>Learn at least a half dozen programming languages. Include one language that supports class abstractions (like Java or C++), one that supports functional abstraction (like Lisp or ML), one that supports syntactic abstraction (like Lisp), one that supports declarative specifications (like Prolog or C++ templates), one that supports coroutines (like Icon or Scheme), and one that supports parallelism (like Sisal)

所以他说才会说`Teach Yourself Programming in Ten Years`

>References

```
[1] The practice of Programming. Brian W Kernighan and Rob Pike. Addison Wisley 

[2] How to design programs. MIT Press. 

[3] Learning Python 3rd edition. O'Reilly Press 

[4] Code complete 2nd edition. Microsoft Press 

[5] Linguistic Relativity. Wikipedia 

[6] How to become a hacker. Eric S Raymond 

[7] Learning programming in Ten Years. Peter Norvig
```