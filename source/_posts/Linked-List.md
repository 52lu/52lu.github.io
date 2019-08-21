---
title: 单向链表
date: 2019-03-25 19:15:35
tags:
 - 数据结构
categories:
 - 数据结构
---

### 1. 什么是链表?
>链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的指针(Pointer)。由于不必须按顺序存储，链表在插入的时候可以达到O(1)的复杂度，比另一种线性表[顺序表](https://zh.wikipedia.org/wiki/%E9%A1%BA%E5%BA%8F%E8%A1%A8)快得多，但是查找一个节点或者访问特定编号的节点则需要O(n)的时间，而顺序表相应的时间复杂度分别是O(logn)和O(1)。


### 2. 链表的组成部分
> 链表通常由一连串节点组成，每个节点包含任意的实例数据（信息域 ）和一或两个用来指向上一个/或下一个节点的位置的链接（链域）

<!--more-->

### 3. 数组和链
- 链表结构可以克服数组链表需要预先知道数据大小的缺点，链表结构可以充分利用计算机内存空间，实现灵活的内存动态管理


### 4. 数组与链表的优缺点
1. 存取方式上，数组可以顺序存取或者随机存取，而链表只能顺序存取；
2. 存储位置上，数组逻辑上相邻的元素在物理存储位置上也相邻，而链表不一定；　
3. 存储空间上，链表由于带有指针域，存储密度不如数组大；　
4. 按序号查找时，数组可以随机访问，时间复杂度为O(1)，而链表不支持随机访问，平均需要O(n)；　
5. 按值查找时，若数组无序，数组和链表时间复杂度均为O(n)，但是当数组有序时，可以采用折半查找将时间复杂度降为O(logn)；
6. 插入和删除时，数组平均需要移动n/2个元素，而链表只需修改指针即可；
7. 空间分配方面：
> 数组在静态存储分配情形下，存储元素数量受限制，动态存储分配情形下，虽然存储空间可以扩充，但需要移动大量元素，导致操作效率降低，而且如果内存中没有更大块连续存储空间将导致分配失败； 
  　　链表存储的节点空间只在需要的时候申请分配，只要内存中有空间就可以分配，操作比较灵活高效； 
8. 经典数据结构涵盖了多种抽象数据类型（ADT），其中包括栈、队列、有序列表、排序表、哈希表及分散表、树、优先队列、集合和图等。对于每种情况，都可以选用数组或某一种链表数据结构来实现其抽象数据类型（ADT）。由于数组和链表几乎是建立所有ADT的基础，所以称数组与链表为基本数据结构

### 5. 链表类型
- 单向链表
- [双向链表]()
- [循环链表]()



### 6. 单向链表
> 链表中最简单的一种是单向链表，它包含两个域，一个信息域和一个指针域。这个链接指向列表中的下一个节点，而最后一个节点则指向一个空值。

`一个单向链表的节点被分成两个部分。第一个部分保存或者显示关于节点的信息，第二个部分存储下一个节点的地址。单向链表只可向一个方向遍历。`

![单向链表图](https://mrliuqh.github.io/directionsImg/dataStructure/Unidirectional_linked_list.png)


### 7. 代码（PHP）实现单向链表

```php
<?php

/**
 * Class Node
 */
class Node
{
    /**
     *
     * @var
     */
    private $Data;//数据集
    /**
     *
     * @var
     */
    private $Next;//下一个节点

    /**
     * Node constructor.
     * @param $next
     * @param $data
     */
    public function __construct($data, $next)
    {
        $this->setData($data);
        $this->setNext($next);

    }

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param $data
     */
    public function setData($data)
    {
        $this->Data = $data;
    }

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @return mixed
     */
    public function getData()
    {
        return $this->Data;
    }

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param $next
     */
    public function setNext($next)
    {
        $this->Next = $next;
    }

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @return mixed
     */
    public function getNext()
    {
        return $this->Next;
    }

}

/**
 * Class LinkList
 */
class LinkList
{
    /**
     *
     * @var
     */
    private $header;//链表头部信息
    /**
     *
     * @var
     */
    private $len;//链表长度


    /**
     * LinkList constructor.
     */
    public function __construct()
    {
        $this->setHeader(new Node(null, null));
        $this->len = 0;
    }


    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param Node $node
     */
    public function setHeader(Node $node)
    {
        $this->header = $node;
    }

    /**
     *  Functional description : 获取当前节点
     *  Programmer : Mr.Liu
     * @return Node
     */
    public function getHeader()
    : Node
    {
        return $this->header;
    }


    /**
     *  Functional description : 获取链表长度
     *  Programmer : Mr.Liu
     * @return mixed
     */
    public function getLen()
    : int
    {
        return $this->len;
    }


    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param $data
     */
    public function add(string $data)
    {
        $node = $this->getHeader();
        //查找没有子节点的节点
        while ($node->getNext() != null) {
            $node = $node->getNext();
        }
        //找到后设置其下级节点
        $node->setNext(new Node($data, null));

        //链表长度递增
        $this->len++;
    }


    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param Node $currentNode
     * @param Node $prevNode
     *
     * 示例：A-B-C ==> A-C
     * @return bool
     */
    private function delLink(Node $currentNode, Node $prevNode)
    {
        $prevNode->setNext($currentNode->getNext());
        $this->len--;
        return true;
    }

    /**
     *  Functional description : 根据节点的数据进行删除
     *  Programmer : Mr.Liu
     * @param $data
     */
    public function delByData($data)
    {
        $node     = $this->getHeader();
        $prevNode = $node;
        //查找没有数据一致的节点
        while ($node->getData() != $data) {
            $prevNode = $node;
            $node     = $node->getNext();
        }
        //删除(把要删的节点A下的子节点位置移到A节点，从而删除)
        $this->delLink($node, $prevNode);
    }


    /**
     *  Functional description : 删除第一个节点0~N
     *  Programmer : Mr.Liu
     * @param int $index
     * @return bool
     */
    public function delByIndex(int $index)
    {
        $node = $prevNode = $this->getHeader();
        $i    = 0;
        if ($i > $this->getLen() -1 ){
            //节点超
            return false;
        }
        while ($i != $index) {
            if ($node->getNext() == null) {
                return false;
            }
            $prevNode = $node;
            $node     = $node->getNext();
            $i++;
        }
        return $this->delLink($node, $prevNode);
    }

    /**
     *  Functional description : 获取链表上所有数据
     *  Programmer : Mr.Liu
     * @return array
     */
    public function getLinkData()
    {
        $node = $this->getHeader();
        $list = [];

        //根节点处理
        if ($node->getData() != null) {
            $list[] = $node->getData();
        }

        //从下一个节点开始获取数据
        $node = $node->getNext();
        if ($node->getData() == null) {
            return $list;
        }
        while ($node->getData() != null && $node->getNext() != null) {
            $list[] = $node->getData();
            $node   = $node->getNext();
        }
        return $list;
    }


    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * 添加D到1示例：A-B-C ==> A-D-B-C
     * @param int $index
     * @param string $data
     * @return bool
     */
    public function addByIndex(int $index,string $data){
        $node  = $this->getHeader();
        $i=0;
        while ($i != $index){
            if ($node->getNext() == null) {
                return false;
            }
            $node = $node->getNext();
            $i++;

        }
        //进行添加
        $node->setNext(new Node($data,$node->getNext()));
        $this->len++;
        return true;
    }

}
```

