---
title: 赫夫曼树
author: 晓
date: 2020-6-22 14:16:00 +0800
categories: [DataStructure]
tags: [tree,java]
--- 

## 赫夫曼树
树的带权路径长度：树的带权路径长度规定为所有的叶子节点的带权路径长度之和（wpl的和），wpl最小的树就是赫夫曼树
给定n个权值作为n个叶子节点，构造一颗二叉树，若该树的带权路径达到最小（wpl），这样的二叉树称为最优二叉树。
赫夫曼树是带权路径最短的树，权越大离根结点距离越近

## 构建赫夫曼树

构建赫夫曼树的思路
- 从小到大进行排序，每个数据看成一个节点，每个节点看成最简单的二叉树
- 取出根节点权重值最小的两颗二叉树
- 组成一颗新的二叉树，新的二叉树的根节点权值是前面两颗二叉树的根节点权值的和
- 再将这颗新的二叉树，以根节点的权值大小再次排序
- 不断重复 1-4的步骤，直到数列中，所有的数据都被处理。就得到一颗赫夫曼树。

``` java
package com.learn.huffman;

import java.util.ArrayList;
import java.util.Collections;

public class HuffmanTree {

    public static void main(String[] args)
    {
        int[] arr=new int[]{13,7,8,3,29,6,1};
        createHuffManTree(arr);
    }

    //创建赫夫曼树
    public static void createHuffManTree(int[] arr)
    {
        ArrayList<Node> nodes=new ArrayList<Node>(arr.length);
        for(int i=0;i<arr.length;i++)
        {
            nodes.add(new Node(arr[i]));
        }
        while (nodes.size()>1)
        {
            //排序
            Collections.sort(nodes);
            Node leftNode =nodes.get(0);
            Node rightNode = nodes.get(1);
            Node root= new Node(leftNode.value+rightNode.value);
            root.left=leftNode;
            root.right = rightNode;
            nodes.remove(leftNode);
            nodes.remove(rightNode);
            nodes.add(root);
        }

        //前序遍历 nodes
        nodes.get(0).preOrder();
    }

}

class Node implements Comparable<Node>{

    int value;

    Node left;

    Node right;

    public Node(int value)
    {
        this.value = value;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public void setRight(Node right) {
        this.right = right;
    }

    public int getValue() {
        return value;
    }

    public Node getLeft() {
        return left;
    }

    public Node getRight() {
        return right;
    }

    /**
     * 前序遍历
     */
    public void preOrder()
    {
        System.out.println(this.toString());
        if(left!=null)
        {
            left.preOrder();
        }
        if(right!=null)
        {
            right.preOrder();
        }
    }

    @Override
    //从小到大排序
    public int compareTo(Node o) {
        return this.value-o.value;
    }

    @Override
    public String toString()
    {

        return this.value+"";
    }
}

```

## 赫夫曼编码

- 赫夫曼编码是赫夫曼树在电讯通信中的经典应用之一
- 赫夫曼编码广泛地用于数据文件压缩，其压缩率通常在20%~90%之间
- 赫夫曼码是可变字长编码（vlc）的一种

## 赫夫曼编码实现
- 前缀编码：字符的编码都不能是其他字符编码的前缀，符合此要求的编码叫做前缀编码，即不能匹配到重复的编码
- 根据赫夫曼树，给各个字符，规定编码，向左的路径为0，向右为1 ，前缀编码，匹配的时候不会出现多意性（因为每一个字符都有唯一路径）

``` java 
package com.learn.huffmancode;

import java.util.*;

//赫夫曼编码实现
public class huffManCodeDemo {
    public static void main(String[] args) {
        String str = "i like like like java do you like a java";
        huffManCode tree = new huffManCode(str.getBytes());
        HashMap<Byte, String> huffmanCodes = tree.getHuffManCode();
        var bytes = huffManZip(str.getBytes(),huffmanCodes);

        System.out.println( new String(decode(bytes,huffmanCodes)) );
    }

    /**
     * 解压缩赫夫曼编码后的byte数组
     * 1.将byte还原成二进制字符串
     * @param byteArr  byteArr是将赫夫曼表，按字符顺序对应的二进制字符串按8位组成一个byte存储后的数组，最后一位不够8位则高位补0
     * @param huffManCodes 对应的哈夫曼编码表
     * @return
     */
    public static byte[] decode(byte[] byteArr,HashMap<Byte,String> huffManCodes)
    {

        //转置哈夫曼编码
        HashMap<String,Byte> reverseHuffManCodes = new HashMap<>();
        for(Map.Entry<Byte,String> entry:huffManCodes.entrySet())
        {
            reverseHuffManCodes.put(entry.getValue(),entry.getKey());
        }

        StringBuilder bitString =new StringBuilder();
        for(int i=0;i<byteArr.length;i++)
        {
            bitString.append(byteToBitString(i!=byteArr.length-1,byteArr[i]));
        }
        int mark =0;
        List<Byte> lst= new ArrayList<>()  ;
        for(int i=0;i<bitString.length();i++)
        {
            var temp=reverseHuffManCodes.get(bitString.substring(mark,i));
            if(temp!=null)
            {
                lst.add(temp);
                mark=i;
            }
        }
        lst.add(reverseHuffManCodes.get(bitString.substring(mark)));
        byte[] original = new byte[lst.size()];
        for(int i=0;i<lst.size();i++)
        {
            original[i]=lst.get(i);
        }
        return original;
    }

    /**
     * 将一个byte转成二进制的字符串
     * @param flag 是否需要进行补位，如果是最后一个字节不需要补高位
     * @return
     */
    public static String byteToBitString(boolean flag,byte b)
    {
        int temp =b;
        if(flag)
        {
            temp |=256;
        }
        String bitString = Integer.toBinaryString(temp);
        if(flag)
        {
            bitString = bitString.substring(bitString.length()-8);
        }

        return bitString;
    }
    /**
     * @param bytes 需要压缩byte【】
     * @return 返回赫夫曼编码处理后的 byte[]
     * 举例： String content = "i like like like java do you like a java"; =》 byte[] contentBytes = content.getBytes();
     * 返回的是 字符串 "1010100010111111110010001011111111001000101111111100100101001101110001110000011011101000111100101000101111111100110001001010011011100"
     * => 对应的 byte[] huffmanCodeBytes  ，即 8位对应一个 byte,放入到 huffmanCodeBytes
     * huffmanCodeBytes[0] =  10101000(补码) => byte  [推导  10101000=> 10101000 - 1 => 10100111(反码)=> 11011000= -88 ]
     * huffmanCodeBytes[1] = -88
     */
    public static byte[] huffManZip( byte[] bytes,HashMap<Byte, String> huffmanCodes) {

        StringBuilder builder = new StringBuilder();
        //获取每个字符对应的赫夫曼路径编码
        for (var b : bytes) {
            builder.append(huffmanCodes.get(b));
        }
        String huffStr = builder.toString();

        int len = (huffStr.length() + 7) / 8;
        byte[] zipBytes = new byte[len];
        //每8位二进制转成一个byte类型数据
        //获取长度
        int index = 0;
        for (int i = 0; i < huffStr.length(); i += 8) {
            String byteStr;
            if ((i + 8) < huffStr.length()) {
                byteStr =  huffStr.substring(i, i + 8);
            } else {
                byteStr =  huffStr.substring(i);
            }
            zipBytes[index] = (byte)Integer.parseInt(byteStr,2);
            index++;
        }
        return zipBytes;
    }

}

/**
 * 将字符串转成哈夫曼编码
 */
class huffManCode {
    //根节点
    Node root;
    HashMap<Byte, String> huffCodeMap = new HashMap<Byte, String>();

    /**
     * 把所有字符转换成哈夫曼树节点形式
     *
     * @param bytes 目标字符串
     * @return 所欲节点列表
     */
    ArrayList<Node> createNodeLst(byte[] bytes) {
        ArrayList<Node> arrayList = new ArrayList<>();
        HashMap<Byte, Node> nodeDictionary = new HashMap<Byte, Node>();
        for (int i = 0; i < bytes.length; i++) {
            Byte data = bytes[i];
            Node node = nodeDictionary.get(data);
            if (node == null) {
                node = new Node(bytes[i], 0);
                node.data = data;
                node.weight++;
                nodeDictionary.put(data, node);
            } else {
                node.weight++;
            }
        }
        for (var nodeSet : nodeDictionary.entrySet()) {
            arrayList.add(nodeSet.getValue());
        }
        return arrayList;
    }


    public huffManCode(byte[] bytes) {
        ArrayList<Node> nodes = createNodeLst(bytes);
        while (nodes.size() > 1) {
            Collections.sort(nodes);
            Node left = nodes.get(0);
            Node right = nodes.get(1);
            Node root = new Node(null, left.weight + right.weight);
            root.lNode = left;
            root.rNode = right;
            nodes.remove(left);
            nodes.remove(right);
            nodes.add(root);
        }
        root = nodes.get(0);
    }

    public void print() {
        System.out.println(getHuffManCode());
    }


    public HashMap<Byte, String> getHuffManCode() {
        getHuffManCode(root, "", new StringBuilder());
        return huffCodeMap;
    }

    void getHuffManCode(Node node, String code, StringBuilder builder) {
        StringBuilder tempBulider = new StringBuilder(builder);
        tempBulider.append(code);
        if (node != null) {
            //找到叶子节点
            if (node.data == null) {
                //向左0 向右 1
                getHuffManCode(node.lNode, "0", tempBulider);
                getHuffManCode(node.rNode, "1", tempBulider);
            } else {
                huffCodeMap.put(node.data, tempBulider.toString());
            }
        }
    }

}

class Node implements Comparable<Node> {
    //字符
    Byte data;
    //权值
    public int weight = 0;
    //左子结点
    Node lNode;
    //右子结点
    Node rNode;

    public Node(Byte data, int weight) {
        this.data = data;
        this.weight = weight;
    }

    public Node getlNode() {
        return lNode;
    }

    public void setlNode(Node lNode) {
        this.lNode = lNode;
    }

    public Node getrNode() {
        return rNode;
    }

    public void setrNode(Node rNode) {
        this.rNode = rNode;
    }

    @Override
    public int compareTo(Node o) {
        return this.weight - o.weight;
    }

}

```