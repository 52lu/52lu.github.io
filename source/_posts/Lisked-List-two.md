---
title: 数据结构-双向链表、循环链
date: 2019-03-27 17:34:29
tags:
 - 数据结构
categories:
 - 数据结构
---

### 1. 什么是双向链表?
>双向链表(双链表)又叫双面链表,双向链表中不仅有指向后一个节点的指针，还有指向前一个节点的指针。这样可以从任何一个节点访问前一个节点，当然也可以访问后一个节点，以至整个链表。一般是在需要大批量的另外储存数据在链表中的位置的时候用。双向链表也可以配合下面的其他链表的扩展使用。

`由于另外储存了指向链表内容的指针，并且可能会修改相邻的节点，有的时候第一个节点可能会被删除或者在之前添加一个新的节点。这时候就要修改指向首个节点的指针。有一种方便的可以消除这种特殊情况的方法是在最后一个节点之后、第一个节点之前储存一个永远不会被删除或者移动的虚拟节点，形成一个下面说的循环链表。这个虚拟节点之后的节点就是真正的第一个节点。这种情况通常可以用这个虚拟节点直接表示这个链表，对于把链表单独的存在数组里的情况，也可以直接用这个数组表示链表并用第0个或者第-1个（如果编译器支持）节点固定的表示这个虚拟节点。`

![双向链表图](https://52lu.github.io/directionsImg/dataStructure/Unidirectional_linked_list_two.png)

<!--more-->
### 2. 什么是循环链表?
>循环链表指的是首节点和末节点被连接在一起的链表，这种方式在单向和双向链表中皆可实现。循环链表的无边界使得在这样的链表上设计算法会比普通链表更加容易。对于新加入的节点应该是在第一个节点之前还是最后一个节点之后可以根据实际要求灵活处理。
![循环链表图](https://52lu.github.io/directionsImg/dataStructure/cycle_list.png)


### 3. 单向链表与双向链表优缺点
#### 3.1 单向链表
**优点：**
- 单向链表增加删除节点简单。遍历时候不会死循环。（双向也不会死循环，循环链表忘了进行控制的话很容易进入死循环）

**缺点：**
- 只能从头到尾遍历。只能找到后继，无法找到前驱，也就是只能前进。

#### 3.2 双向链表
**优点：**
- 可以找到前驱和后继，可进可退。

**缺点：**
- 增加删除节点复杂（其实就复杂一点点）

### 4. 代码（PHP）实现双向链表
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
    private $Prev;//上一个节点
    /**
     *
     * @var
     */
    private $Next;//下一个节点

    /**
     * Node constructor.
     * @param $next
     * @param $data
     * @param $prev
     */
    public function __construct($data, $next,$prev)
    {
        $this->setData($data);
        $this->setNext($next);
        $this->setPrev($prev);
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

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @param $prev
     */
    public function setPrev($prev)
    {
        $this->Prev = $prev;
    }

    /**
     *  Functional description :
     *  Programmer : Mr.Liu
     * @return mixed
     */
    public function getPrev()
    {
        return $this->Prev;
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
    private $header;

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
        //初始化根节点
        $this->setHeader(new Node(null, null,null));
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
     *  Functional description : 获取头部节点
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
     *  Functional description : 末尾追加
     *  Programmer : Mr.Liu
     * @param $data
     */
    public function append($data)
    {
        $node = $this->getHeader();
        //查找没有子节点的节点
        while ($node->getNext() != null) {
            $node = $node->getNext();
        }
        //找到后设置其下级节点
        $node->setNext(new Node($data, null,$node));
        //链表长度递增
        $this->len++;
    }


    /**
     *  Functional description : 头部添加
     *  Programmer : Mr.Liu
     * @param $data
     */
    public function addFirst($data){
        $rootNode = $this->getHeader();
        //查找没有子节点的节点(根节点)
        while ($rootNode->getPrev() != null) {
            $rootNode = $rootNode->getPrev();
        }
        //Root-A-B-C Root-D-A-B-C
        $oldFirst = $rootNode->getNext();
        $newFirst = new Node($data,$oldFirst,$rootNode);
        $oldFirst->setPrev($newFirst);
        $rootNode->setNext($newFirst);
        $this->len++;
    }

    /**
     *  Functional description : 根据指定位置添加
     *  Programmer : Mr.Liu
     * @param int $index
     * @param $data
     */
    public function insertByIndex(int $index,$data){
        $key = 1;
        //尾部添加
        if ($this->getLen() < $index){
            $this->append($data);
            return;
        }
        //头部添加
        if ($index == 1){
            $this->addFirst($data);
            return;
        }

        $node = $this->getHeader();
        while ($key < $index){
            $node = $node->getNext();
            $key++;
        }
        //中间添加 如：添加D到A和B之间 A-B-C  ---> A-D-B-C
        $node->setNext(new Node($data,$node->getNext(),$node));
        $this->len++;
    }


    /**
     *  Functional description : 删除第一个节点
     *  Programmer : Mr.Liu
     */
    public function delFirst(){
        $rootNode = $this->getHeader();
        while ($rootNode->getPrev() !=null){
            $rootNode = $rootNode->getPrev();
        }
        //删除：ROOT-A-B-C  -->  ROOT-B-C
        $firstNode = $rootNode->getNext();
        $secondNode = $firstNode->getNext();
        $rootNode->setNext($secondNode);
        $secondNode->setPrev($rootNode);
        $this->len--;
    }

    /**
     *  Functional description : 删除最后一个节点
     *  Programmer : Mr.Liu
     */
    public function delLast(){
        $node = $this->getHeader();
        while ($node->getNext() !=null){
            $node = $node->getNext();
        }
        //删除：ROOT-A-B-C  -->  ROOT-A-B
        $newLastNode = $node->getPrev();
        $newLastNode->setNext(null);
        $this->len--;
    }


    /**
     *  Functional description : 反转双向链表
     *  Programmer : Mr.Liu
     * @return void
     */
    public function reverse(){
        if ($this->getHeader() != null){
            return ;
        }

        //除去根节点后的节点
        $currentNode = $this->getHeader()->getNext();
        //后一个节点
        $next=null;
        $prev=null;//根节点,当前为null
        //rootNode->0->1->2->3 -->  3->2->1->0
        //查找最后一个
        while ($currentNode != null){
            //用一个变量暂时存储后一节点，因为一旦前面反转，就断链了
            $next = $currentNode->getNext();
            //将前一节点作为当前节点的后一节点，是为反转
            $currentNode->setNext($prev);
            $currentNode->setPrev($next);

            #指针后移
            $prev = $currentNode;
            $currentNode = $next;

        }
        return $prev;
    }

}
```