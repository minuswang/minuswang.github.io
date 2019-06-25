---
title: HashMap为啥要用位运算代替取模
date: 2019-06-25 15:54:03
tags:
---

> 2^n 表示 2 的 n 次方，也就是说， 一个数对 2^n 取模相当于一个数和 (2^n – 1) 做按位与运算

在hash中查找key的时候，经常会发现用&取代%，先看两段代码吧，

JDK6中的HashMap中的indexFor方法：

Java代码
/** 
 * Returns index for hash code h. 
 */  
static int indexFor(int h, int length) {  
    return h & (length-1);  
}  

Redis2.4中的代码段：

C代码
n.size = realsize;  
n.sizemask = realsize-1;  
//此处略去xxx行  
hile(de) {  
        unsigned int h;  
  
        nextde = de->next;  
        /* Get the index in the new hash table */  
        h = dictHashKey(d, de->key) & d->ht[1].sizemask;  
        de->next = d->ht[1].table[h];  
        d->ht[1].table[h] = de;  
        d->ht[0].used--;  
        d->ht[1].used++;  
        de = nextde;  
    }  

大家可以看到a%b取模的形式都被替换成了a&(b-1) ，当hashtable的长度是2的幂的情况下（疏忽，一开始没写），这两者是等价的，那为什么要用后者呢？

另一方面，为什么hashtable的长度最好要是2的n次方呢，这个不在本次讨论范围之列，原因简单说一下就是1、分布更均匀 2、碰撞几率更小  详情自己思考，JDK中的HashMap就会在初始化时，保证这一点：

Java代码
public HashMap(int initialCapacity, float loadFactor) {  
    if (initialCapacity < 0)  
        throw new IllegalArgumentException("Illegal initial capacity: " +  
                                           initialCapacity);  
    if (initialCapacity > MAXIMUM_CAPACITY)  
        initialCapacity = MAXIMUM_CAPACITY;  
    if (loadFactor <= 0 || Float.isNaN(loadFactor))  
        throw new IllegalArgumentException("Illegal load factor: " +  
                                           loadFactor);  
  
    // Find a power of 2 >= initialCapacity  
    int capacity = 1;  
    while (capacity < initialCapacity)  
        capacity <<= 1;  
  
    this.loadFactor = loadFactor;  
    threshold = (int)(capacity * loadFactor);  
    table = new Entry[capacity];  
    init();  
}  

redis中也有类似的保证：

Java代码
/* Our hash table capability is a power of two */  
static unsigned long _dictNextPower(unsigned long size)  
{  
    unsigned long i = DICT_HT_INITIAL_SIZE;  
  
    if (size >= LONG_MAX) return LONG_MAX;  
    while(1) {  
        if (i >= size)  
            return i;  
        i *= 2;  
    }  
}  

言归正传，大家都知道位运算的效率最高，这也是&取代%的原因，来看个程序：

C代码
int main(int argc, char* argv[])  
{  
    int a = 0x111;  
    int b = 0x222;  
    int c = 0;  
    int d = 0;  
  
    c = a & (b-1);  
    d = a % b;  
  
    return 0;  
}  
 
看反汇编的结果：

反汇编代码
13:       c = a & (b-1);  
00401044   mov         eax,dword ptr [ebp-8]  
00401047   sub         eax,1  
0040104A   mov         ecx,dword ptr [ebp-4]  
0040104D   and         ecx,eax  
0040104F   mov         dword ptr [ebp-0Ch],ecx  
14:       d = a % b;  
00401052   mov         eax,dword ptr [ebp-4]  
00401055   cdq  
00401056   idiv        eax,dword ptr [ebp-8]  
00401059   mov         dword ptr [ebp-10h],edx  
 
可以看到，&操作用了:3mov+1and+1sub  %操作用了：2mov+1cdp+1idiv
我们可以查阅Coding_ASM_-_Intel_Instruction_Set_Codes_and_Cycles资料，发现前者只需5个CPU周期，而后者至少需要26个CPU周期（注意，是最少！！！） 效率显而易见。所以以后自己在写的时候，也可以使用前者的写法

[Coding_ASM_-_Intel_Instruction_Set_Codes_and_Cycles](http://dl.iteye.com/topics/download/f54b6676-ee28-3223-890a-6e6bf1087309)


位运算(&)效率要比取模运算(%)高很多，主要原因是位运算直接对内存数据进行操作，不需要转成十进制，因此处理速度非常快。

a % b == a & (b - 1)
前提：b 为 2^n
来源自 HashMap
中的 indexFor
方法：

static int indexFor(int h, int length){
   return h & (length-1);
}
这个方法是使用哈希值对链表数组的长度取模，得到值所在的索引位置，里面使用位运算（&）代替普通的（%）运算。

原理
具体的效率对比这里不赘述，简单说一下为什么 &
可以代替 %
：

X % 2^n = X & (2^n - 1)
2^n 表示 2 的 n 次方，也就是说， 一个数对 2^n 取模相当于一个数和 (2^n – 1) 做按位与运算。

假设 n 为 3，则 2^3 = 8，表示成 2 进制就是 1000。2^3 – 1 = 7 ，即 0111。

此时 X & (2^3 – 1) 就相当于取 X 的 2 进制的最后三位数。

从 2 进制角度来看，X / 8 相当于 X >> 3，即把 X 右移 3 位，此时得到了 X / 8 的商，而被移掉的部分(后三位)，则是 X % 8，也就是余数。

推广到一般：

对于所有 2^n 的数，二进制表示为：

1000…000，1 后面跟 n 个 0

而 2^n – 1 的二进制为：

0111…111，0 后面跟 n 个 1

X / 2^n 是 X >> n，那么 X & (2^n – 1) 就是取被移掉的后 n 位，也就是 X % 2^n