---
title: 二叉排序树 BST（Binary Sort Tree）
author: 晓
date: 2020-7-2 14:16:00 +0800
categories: [数据结构与算法]
tags: [tree, java]
---

## 二叉排序树

二叉排序树是一颗空树或者具有以下性质二叉树

- 若它的左子树不为空，则左子树上所有结点的值均小于它根结点的值
- 若它的右子树不为空，则右子树上所欲结点的值均大于它根结点的值
- 它的左右子树分别为二叉排序树

## 实现

```java
package com.learn.bst;

public class BinarySortTreeDemo {

    public static void main(String[] args) {
        int[] arr = {7, 3, 10, 12, 5, 1, 9, 2};
        BinarySortTree binarySortTree = new BinarySortTree();
        //循环的添加结点到二叉排序树
        for(int i = 0; i< arr.length; i++) {
            binarySortTree.add(new Node(arr[i]));
        }

        //中序遍历二叉排序树
        System.out.println("中序遍历二叉排序树~");
        binarySortTree.infixOrder(); // 1, 3, 5, 7, 9, 10, 12

        //测试一下删除叶子结点


        binarySortTree.delNode(12);


        binarySortTree.delNode(5);
        binarySortTree.delNode(10);
        binarySortTree.delNode(2);
        binarySortTree.delNode(3);

        binarySortTree.delNode(9);
        binarySortTree.delNode(1);
        binarySortTree.delNode(7);


        System.out.println("root=" + binarySortTree.getRoot());


        System.out.println("删除结点后");
        binarySortTree.infixOrder();
    }

}

//创建二叉排序树
class BinarySortTree {
    private Node root;




    public Node getRoot() {
        return root;
    }

    //查找要删除的结点
    public Node search(int value) {
        if(root == null) {
            return null;
        } else {
            return root.search(value);
        }
    }

    //查找父结点
    public Node searchParent(int value) {
        if(root == null) {
            return null;
        } else {
            return root.searchParent(value);
        }
    }

    //编写方法:
    //1. 返回的 以node 为根结点的二叉排序树的最小结点的值
    //2. 删除node 为根结点的二叉排序树的最小结点
    /**
     *
     * @param node 传入的结点(当做二叉排序树的根结点)
     * @return 返回的 以node 为根结点的二叉排序树的最小结点的值
     */
    public int delRightTreeMin(Node node) {
        Node target = node;
        //循环的查找左子节点，就会找到最小值
        while(target.left != null) {
            target = target.left;
        }
        //这时 target就指向了最小结点
        //删除最小结点
        delNode(target.value);
        return target.value;
    }


    //删除结点
    public void delNode(int value) {
        if(root == null) {
            return;
        }else {
            //1.需求先去找到要删除的结点  targetNode
            Node targetNode = search(value);
            //如果没有找到要删除的结点
            if(targetNode == null) {
                return;
            }
            //如果我们发现当前这颗二叉排序树只有一个结点
            if(root.left == null && root.right == null) {
                root = null;
                return;
            }

            //去找到targetNode的父结点
            Node parent = searchParent(value);
            //如果要删除的结点是叶子结点
            if(targetNode.left == null && targetNode.right == null) {
                //判断targetNode 是父结点的左子结点，还是右子结点
                if(parent.left != null && parent.left.value == value) { //是左子结点
                    parent.left = null;
                } else if (parent.right != null && parent.right.value == value) {//是由子结点
                    parent.right = null;
                }
            } else if (targetNode.left != null && targetNode.right != null) { //删除有两颗子树的节点
                int minVal = delRightTreeMin(targetNode.right);
                targetNode.value = minVal;


            } else { // 删除只有一颗子树的结点
                //如果要删除的结点有左子结点
                if(targetNode.left != null) {
                    if(parent != null) {
                        //如果 targetNode 是 parent 的左子结点
                        if(parent.left.value == value) {
                            parent.left = targetNode.left;
                        } else { //  targetNode 是 parent 的右子结点
                            parent.right = targetNode.left;
                        }
                    } else {
                        root = targetNode.left;
                    }
                } else { //如果要删除的结点有右子结点
                    if(parent != null) {
                        //如果 targetNode 是 parent 的左子结点
                        if(parent.left.value == value) {
                            parent.left = targetNode.right;
                        } else { //如果 targetNode 是 parent 的右子结点
                            parent.right = targetNode.right;
                        }
                    } else {
                        root = targetNode.right;
                    }
                }

            }

        }
    }

    //添加结点的方法
    public void add(Node node) {
        if(root == null) {
            root = node;//如果root为空则直接让root指向node
        } else {
            root.add(node);
        }
    }
    //中序遍历
    public void infixOrder() {
        if(root != null) {
            root.infixOrder();
        } else {
            System.out.println("二叉排序树为空，不能遍历");
        }
    }
}

//创建Node结点
class Node {
    int value;
    Node left;
    Node right;
    public Node(int value) {

        this.value = value;
    }


    //查找要删除的结点
    /**
     *
     * @param value 希望删除的结点的值
     * @return 如果找到返回该结点，否则返回null
     */
    public Node search(int value) {
        if(value == this.value) { //找到就是该结点
            return this;
        } else if(value < this.value) {//如果查找的值小于当前结点，向左子树递归查找
            //如果左子结点为空
            if(this.left  == null) {
                return null;
            }
            return this.left.search(value);
        } else { //如果查找的值不小于当前结点，向右子树递归查找
            if(this.right == null) {
                return null;
            }
            return this.right.search(value);
        }

    }
    //查找要删除结点的父结点
    /**
     *
     * @param value 要找到的结点的值
     * @return 返回的是要删除的结点的父结点，如果没有就返回null
     */
    public Node searchParent(int value) {
        //如果当前结点就是要删除的结点的父结点，就返回
        if((this.left != null && this.left.value == value) ||
                (this.right != null && this.right.value == value)) {
            return this;
        } else {
            //如果查找的值小于当前结点的值, 并且当前结点的左子结点不为空
            if(value < this.value && this.left != null) {
                return this.left.searchParent(value); //向左子树递归查找
            } else if (value >= this.value && this.right != null) {
                return this.right.searchParent(value); //向右子树递归查找
            } else {
                return null; // 没有找到父结点
            }
        }

    }

    @Override
    public String toString() {
        return "Node [value=" + value + "]";
    }


    //添加结点的方法
    //递归的形式添加结点，注意需要满足二叉排序树的要求
    public void add(Node node) {
        if(node == null) {
            return;
        }

        //判断传入的结点的值，和当前子树的根结点的值关系
        if(node.value < this.value) {
            //如果当前结点左子结点为null
            if(this.left == null) {
                this.left = node;
            } else {
                //递归的向左子树添加
                this.left.add(node);
            }
        } else { //添加的结点的值大于 当前结点的值
            if(this.right == null) {
                this.right = node;
            } else {
                //递归的向右子树添加
                this.right.add(node);
            }

        }
    }

    //中序遍历
    public void infixOrder() {
        if(this.left != null) {
            this.left.infixOrder();
        }
        System.out.println(this);
        if(this.right != null) {
            this.right.infixOrder();
        }
    }

}

```

## 平衡二叉树（AVL 树）

平衡二叉树时一种二叉排序树，其中每一个结点的左子树和右子树的高度差至多等于 1

定义：它时一颗空树或它的左右两个子树的高度差绝对值不超过 1，并且
左右两个子树都是一颗平衡二叉树

一个 BST 左子树全部为空 从形式上看 更像一个单链表
插入速度没有影响，但是查询速度比链表更慢，因为需要判断左子树

平衡二叉树是在二叉搜索树的基础之上成立的

平衡二叉树常用实现方法有
红黑树
Avl
替罪羊树
Treap
伸展树

右子树高的时候进行左旋，左子树搞得时候进行右旋

左旋思路： 1.创建一个新节点，值等于当前根节点的值 2.把新节点的左子树设置成当前节点的左子树 3.把新节点的右子树设置为当前节点的右子节点的左子树 4.把当前节点的值换位右子节点的值 5.把当前节点的左子树设置为新节点 6.把当前节点的右子树设置成当前节点右子树的右子树

```java
package com.learn.avl;

public class AVLTreeDemo {
    public static void main(String[] args) {
       // int[] arr = {4, 3, 6, 5, 7, 8};
        int[] arr ={10,12,8,9,7,6};
        AVLTree tree = new AVLTree();
        for (int i = 0; i < arr.length; i++)
        {
            tree.add(new Node(arr[i]));
        }
        System.out.println(tree.height());
    }
}

class AVLTree {
    private Node root;

    public Node getRoot() {
        return root;
    }

    public int height()
    {
        return root.height();
    }

    //查找要删除的结点
    public Node search(int value) {
        if (root == null) {
            return null;
        } else {
            return root.search(value);
        }
    }

    //查找父结点
    public Node searchParent(int value) {
        if (root == null) {
            return null;
        } else {
            return root.searchParent(value);
        }
    }

    //编写方法:
    //1. 返回的 以node 为根结点的二叉排序树的最小结点的值
    //2. 删除node 为根结点的二叉排序树的最小结点

    /**
     * @param node 传入的结点(当做二叉排序树的根结点)
     * @return 返回的 以node 为根结点的二叉排序树的最小结点的值
     */
    public int delRightTreeMin(Node node) {
        Node target = node;
        //循环的查找左子节点，就会找到最小值
        while (target.left != null) {
            target = target.left;
        }
        //这时 target就指向了最小结点
        //删除最小结点
        delNode(target.value);
        return target.value;
    }


    //删除结点
    public void delNode(int value) {
        if (root == null) {
            return;
        } else {
            //1.需求先去找到要删除的结点  targetNode
            Node targetNode = search(value);
            //如果没有找到要删除的结点
            if (targetNode == null) {
                return;
            }
            //如果我们发现当前这颗二叉排序树只有一个结点
            if (root.left == null && root.right == null) {
                root = null;
                return;
            }

            //去找到targetNode的父结点
            Node parent = searchParent(value);
            //如果要删除的结点是叶子结点
            if (targetNode.left == null && targetNode.right == null) {
                //判断targetNode 是父结点的左子结点，还是右子结点
                if (parent.left != null && parent.left.value == value) { //是左子结点
                    parent.left = null;
                } else if (parent.right != null && parent.right.value == value) {//是由子结点
                    parent.right = null;
                }
            } else if (targetNode.left != null && targetNode.right != null) { //删除有两颗子树的节点
                int minVal = delRightTreeMin(targetNode.right);
                targetNode.value = minVal;


            } else { // 删除只有一颗子树的结点
                //如果要删除的结点有左子结点
                if (targetNode.left != null) {
                    if (parent != null) {
                        //如果 targetNode 是 parent 的左子结点
                        if (parent.left.value == value) {
                            parent.left = targetNode.left;
                        } else { //  targetNode 是 parent 的右子结点
                            parent.right = targetNode.left;
                        }
                    } else {
                        root = targetNode.left;
                    }
                } else { //如果要删除的结点有右子结点
                    if (parent != null) {
                        //如果 targetNode 是 parent 的左子结点
                        if (parent.left.value == value) {
                            parent.left = targetNode.right;
                        } else { //如果 targetNode 是 parent 的右子结点
                            parent.right = targetNode.right;
                        }
                    } else {
                        root = targetNode.right;
                    }
                }

            }

        }
    }

    //添加结点的方法
    public void add(Node node) {
        if (root == null) {
            root = node;//如果root为空则直接让root指向node
        } else {
            root.add(node);
        }
    }

    //中序遍历
    public void infixOrder() {
        if (root != null) {
            root.infixOrder();
        } else {
            System.out.println("二叉排序树为空，不能遍历");
        }
    }

}
    //创建Node结点
    class Node {
        int value;
        Node left;
        Node right;

        public Node(int value) {

            this.value = value;
        }


        /**
         * 左旋思路：
         * 1.创建一个新节点，值等于当前根节点的值
         * 2.把新节点的左子树设置成当前节点的左子树
         * 3.把新节点的右子树设置为当前节点的右子节点的左子树
         * 4.把当前节点的值换位右子节点的值
         * 5.把当前节点的左子树设置为新节点
         * 6.把当前节点的右子树设置成当前节点右子树的右子树
         */
        public void leftRotate()
        {
            Node temp = new Node(this.value);
            temp.left = left;
            temp.right = right.left;
            value=right.value;
            this.left=temp;
            right=right.right;
        }

        public void rightRotate()
        {
            Node temp = new Node(this.value);
            temp.right = right;
            temp.left = left.right;
            value=left.value;
            left = left.left;
            right=temp;
        }

        /**
         * 以本节点为根节点的高度,如果只有根节点，高度则是 1
         */
        public int height()
        {
            int left_height=1;
            int right_height=1;
            if(left!=null)
            {
                left_height=left.height()+1;
            }
            if(right!=null)
            {
                right_height=right.height()+1;
            }

            return Math.max(left_height,right_height);
        }

        /**
         * 右子树高度
         * @return
         */
        public int rightHeight()
        {
            int mHeight= 0;
            if(right!=null)
            {
                return right.height();
            }
            return mHeight;
        }

        /**
         * 左子树高度
         * @return
         */
        public int leftHeight()
        {
            int mHeight =0;
            if (left!=null)
            {
                return left.height();
            }
            return mHeight;
        }

        //查找要删除的结点
        /**
         * @param value 希望删除的结点的值
         * @return 如果找到返回该结点，否则返回null
         */
        public Node search(int value) {
            if (value == this.value) { //找到就是该结点
                return this;
            } else if (value < this.value) {//如果查找的值小于当前结点，向左子树递归查找
                //如果左子结点为空
                if (this.left == null) {
                    return null;
                }
                return this.left.search(value);
            } else { //如果查找的值不小于当前结点，向右子树递归查找
                if (this.right == null) {
                    return null;
                }
                return this.right.search(value);
            }

        }
        //查找要删除结点的父结点

        /**
         * @param value 要找到的结点的值
         * @return 返回的是要删除的结点的父结点，如果没有就返回null
         */
        public Node searchParent(int value) {
            //如果当前结点就是要删除的结点的父结点，就返回
            if ((this.left != null && this.left.value == value) ||
                    (this.right != null && this.right.value == value)) {
                return this;
            } else {
                //如果查找的值小于当前结点的值, 并且当前结点的左子结点不为空
                if (value < this.value && this.left != null) {
                    return this.left.searchParent(value); //向左子树递归查找
                } else if (value >= this.value && this.right != null) {
                    return this.right.searchParent(value); //向右子树递归查找
                } else {
                    return null; // 没有找到父结点
                }
            }

        }

        @Override
        public String toString() {
            return "Node [value=" + value + "]";
        }


        //添加结点的方法
        //递归的形式添加结点，注意需要满足二叉排序树的要求
        public void add(Node node) {
            if (node == null) {
                return;
            }

            //判断传入的结点的值，和当前子树的根结点的值关系
            if (node.value < this.value) {
                //如果当前结点左子结点为null
                if (this.left == null) {
                    this.left = node;
                } else {
                    //递归的向左子树添加
                    this.left.add(node);
                }
            } else { //添加的结点的值大于 当前结点的值
                if (this.right == null) {
                    this.right = node;
                } else {
                    //递归的向右子树添加
                    this.right.add(node);
                }

            }

            if(rightHeight()-leftHeight()>1)
            {
                if(right!=null&&right.leftHeight()>right.rightHeight())
                {
                    right.rightRotate();
                }
                leftRotate();
                return;
            }
            if(leftHeight()-rightHeight()>1)
            {
                if(left!=null&&left.rightHeight()>left.leftHeight())
                {
                    left.leftRotate();
                }
                rightRotate();
            }
        }

        //中序遍历
        public void infixOrder() {
            if (this.left != null) {
                this.left.infixOrder();
            }
            System.out.println(this);
            if (this.right != null) {
                this.right.infixOrder();
            }
        }
    }
```

## 多路查找树

多路查找树（muitl-way search tree），其每一个结点的孩子数可以多于两个，且每一个结点可以存储多个元素。由于它时查找树，所有元素之间存在某种特定的排序关系

多路查找树 4 中特殊形式 2-3 树，2-3-4 树，b 树，b+树

在二叉树中，每个节点有数据项，最多两个子节点，如果
允许每个节点有更多的数据项和更多的子节点，就是多叉树

### 2-3 树

其中每一个结点都具有两个孩子或者三个孩子
一个 2 节点包含一个元素和两个孩子或者没有孩子
一个 3 结点包含一小一大两个元素和三个孩子或者没有孩子
2=3 树所有叶子节点都在同一层（只要是 B 树都满足这个条件）
2-3 树是由 2，3 结点构成的

### B 树

B 树是一种平衡的多路查找树，2-3 树和 2-3-4 树都是 B 树的特例。
节点最大的孩子数目称为 B 树的阶 因此 2-3 树是 3 阶的 B 树，2-3-4 树
是 4 阶的 B 树。

一个 m 阶的 b 树具有如下属性
如果根结点不是叶子结点，则至少有两颗子树
每一个非根的分支节点都有 k-1 个元素和 k 个孩子，其中 m/2<=k<=m
每一个叶子节点 n 都有 k-1 个元素，其中 m/2<=k<=m.
所有叶子节点都位于同一层次

B 树上查找的过程式一个顺指针查找结点和再结点中查找关键字的交叉过程

B 树是怎么做到减少内存与外存交换数据的

外存，比如硬盘，将所有信息分割成相等大小的页面，每次硬盘读写都是一个
或多个完整的页面，对于一个硬盘来说，一页的长度可能是 211 到 214 个字节。

在一个典型的 B 树应用中，要处理的硬盘数据量很大，因此无法一次全部装入
到内存。因此我们会对 B 树进行调整，使得 B 树的阶数（结点的元素）与硬盘存储
的页面大小相匹配。通过这种方式，在有限内存的情况下，每一次磁盘的访问我们
都可以获得最大数据量的数据。由于 b 树每结点可以具有比二叉树多得多的元素
，所以与二叉树的操作不同，它们减少了必须访问结点和数据块的数量，从而提高了性能
。可以说，b 树的数据结构就是为内外存的数据交互准备的。

### B+树

对于树结构来说，我们可以通过中序遍历来顺序查找树中的元素，这一切都是在
内存中进行。
可是在 b 树结构中，我们往返于每个节点之间意味着，我们必须得在硬盘的页面
之间进行多次访问。为了能够解决所有元素遍历等基本问题，我们在
原有的 B 树结构基础上，加上了新的元素组织方式，这就是 B+树。
B+树是应文件系统所需而出的一种 B 树的变形树，严格意义上讲，不是数据
结构定义的树了。
