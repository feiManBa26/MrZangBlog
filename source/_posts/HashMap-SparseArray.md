---
title: HashMap/SparseArray
date: 2019-08-19 20:23:04
tags:
- Android
- Map
categories:
- Android
- Map
description: Android Map
---

存储 key / value 键值对值 在Java 中常用的是 HashMap 但是Android 对内存的要求是很高的 一切消耗内存的操作都应该避免 HashMap 存储模式是哈希表 {数组+链表} 实践证明HashMap 在存储 key/vlue 值 在Android上消耗 内存比较严重 特别是大量存储 删除的时候 为了避免这一问题 Android 在Utils包里给我们提供了 SparseArray 类用来实现key/vlue 存储。。。

年轻司机带路 走一波。。。


## # 为何HashMap 消耗内存比较大

为何SparseArray会比HashMap更节省内存，这要从它们各自的结构说起。HashMap底层数据结构是一个 数组+链表 的组合（关于数组和链表的概念，这里就不多阐述了），它采用一种所谓的“Hash 算法”来决定每个元素的存储位置。当程序执行 map.put(key,Obect) 方法 时，系统将调用key对象的 hashCode() 方法得到其 hashCode 值（每个Java对象都有 hashCode() 方法，都可通过该方法获得它的 hashCode 值）。得到这个对象的 hashCode 值之后，系统会根据该 hashCode 值再hash一遍来决定该元素在数组中的存储位置。


但是这就存在一个问题，如果两个key算出来的hash值刚好相等，也就是存放的数组位置一样时，就产生了Hash冲突（因为原本数组的那个位置已经有一个元素存放着，而一个位置只能存放一组数据），那HashMap是怎么解决这种冲突的呢？
HashMap采用链表法来解决Hash冲突，也就是说，如果发生这种情况，HashMap会在数组中冲突的那个位置，将后加入的元素指向原来占有数组位置的那个元素，从而追加形成一个链表

![](    HashMap-SparseArray\01.png)

## # 为何SparseArray 更为优化

先了解一个基本概念——什么是自动装箱？
自动装箱就是指自动将基本数据类型转换为包装器类型，比如下面这句代码：

```java
Interger = 99;
```

99是基本数据类型，将它直接赋值给Integer类型对象i时，就会自动将我们的基本类型int包装成Integer。装箱操作会创建对象，频繁的装箱操作会消耗许多内存，影响性能。

而SparseArray又称为稀疏数组，与HashMap不同，其内部是直接通过维护两个数组来实现存储

```java
public class SparseArray<E> implements Cloneable {

    private int[] mKeys;
    private Object[] mValues;
    ...
}
```

可以看到，一组存储键，一组存储值，key数组的类型是int型，也就是说，SparseArray只支持key为int类型的数据存储，关键就在这里，由于它是直接维护了一个int数组，那么key就避免了自动装箱的过程，举个例子，比如我们用HashMap存储下面这组数据：

```java
HashMap<Integer, String> hashMap = new HashMap<>();
hashMap.put(1, "test");
hashMap.put(2, "test");
hashMap.put(3, "test");
```

每次put进去的时候，由于传进去的是1，2，3，都是int基本类型，HashMap会自动帮我们包装成Integer类型的对象（也就是刚说的自动装箱），那么就肯定会消耗更多内存。但如果是SparseArray来存储的话，就直接将key存储在key数组了，省去了装箱这个过程，从而节省了内存开销。
另一方面，对SparseArray增删查改操作时，其内部会不断检查回收无用空间，从而压缩占用的内存大小，我们看下它的put方法：

```java
public void put(int key, E value) {
        //先调用二分法查询该key在数组中的位置
        int i = ContainerHelpers.binarySearch(mKeys, mSize, key);

        if (i >= 0) {
            //大于0说明已经存在数组中，可以直接赋值
            mValues[i] = value;
        } else {
            //小于0说明这是一个新的键值对，且它应该插在数组中的第i个位置
            i = ~i;
            //根据DELETED来查询当前位置的值是否已经被删除
            if (i < mSize && mValues[i] == DELETED) {
                mKeys[i] = key;
                mValues[i] = value;
                return;
            }
            //如果当前容量已满
            if (mGarbage && mSize >= mKeys.length) {
                //回收无效空间
                gc();

                // Search again because indices may have changed.
                i = ~ContainerHelpers.binarySearch(mKeys, mSize, key);
            }

            mKeys = GrowingArrayUtils.insert(mKeys, mSize, i, key);
            mValues = GrowingArrayUtils.insert(mValues, mSize, i, value);
            mSize++;
        }
}
```

可以看到，SparseArray会先调用二分法去查询key应该存放在数组中的位置，所以SparseArray的key数组一定是有序排列的，然后会用一个DELETED来作为当前位置的元素是否已经被删除，DELETED会在调用remove移除元素的时候赋给对应位置Value，如下：

```java
public void remove(int key) {
    delete(key);
}

public void delete(int key) {
    int i = ContainerHelpers.binarySearch(mKeys, mSize, key);

    if (i >= 0) {
        if (mValues[i] != DELETED) {
            mValues[i] = DELETED;
            mGarbage = true;
        }
    }
}
```

SparseArray通过这个来作为它压缩空间的一个标志（即该位置可不可以被回收），这样子也进一步节省了空间。

从刚才可以看出，无论是SparseArray的put还是delete（其实其他操作比如get也都是通过二分法寻找下标），都是通过二分法去查询这个key应该被存放的位置。而HashMap在插入的时候，不需要去遍历整个集合，而是直接通过hash计算出位置插入。所以在插入效率上，SparseArray会比HashMap稍慢一些，但在数据量不大的情况下，两者的差别不大。


## # 结语
SparseArray与HashMap相比，最大的优势在于内存方面，无论数据量级大小如何，SparseArray所占用的内存都会比HashMap小，在Android中内存是极为重要的，所以在需要保存<Integer,Object>键值对的场景中，推荐使用SparseArray替换HashMap。换句话说，SparseArray是Android中为<Integer,Object>这样的HashMap专门写的类，它避开了自动装箱并且压缩稀疏数组，目的就是为了节省内存。
另外，Android还提供了其他几种类似的集合类：SparseIntArray、SparseBooleanArray、SparseLongArray，可以支持存储<Integer,Integer>、<Integer,Boolean>、<Integer,Long>的数据类型，也就是同时让Value也避开了装箱过程，进一步优化。


