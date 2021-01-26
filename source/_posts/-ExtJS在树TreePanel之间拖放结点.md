---
title: " ExtJS在树TreePanel之间拖放结点"
categories: [ "JS" ]
tags: [ "extjs","treepanel" ]
draft: false
slug: "extjs-drag-and-drop-nodes-in-the-tree-treepanel"
date: "2015-09-30 22:12:00"
---

有时候我们在程序中，需要把一棵TreePanel的元素拖放到另外一棵TreePanel中，如果是在同一棵树中拖动时设置组件的enableDD参数为true，而现在需要在不同给的书之间拖动元素，这个时候就可以设置组件的enableDrag和enableDrop参数，详细示例如下：

## 一、编写JS代码：

    Ext.onReady(function(){
    
        var tree1 = new Ext.tree.TreePanel({
            el: 'tree1',
    		//这里设置enableDrag为true表示可以从这里拖动元素到别处
            enableDrag:true,
            loader: new Ext.tree.TreeLoader({dataUrl: 'treeData1.txt'})
        });


<!--more-->


        var tree2 = new Ext.tree.TreePanel({
            el: 'tree2',
    		//这里设置enableDrop为true表示可以在这棵树中放置拖动过来的元素
            enableDrop:true,
            loader: new Ext.tree.TreeLoader({dataUrl: 'treeData2.txt'})
        });
        var root1 = new Ext.tree.AsyncTreeNode({text:'跟节点'});
        var root2 = new Ext.tree.AsyncTreeNode({text:'图书'});
        tree1.setRootNode(root1);
        tree2.setRootNode(root2);
        tree1.render();
        tree2.render();
    
    });

## 二、HTML代码如下：
*HTML代码*

    <div id="tree1"></div>
    <div id="tree2"></div>

## 三、编写两个TreeLoader需要装载的txt文件，里面的数据为JSON格式：
*treeData1.txt:*

    [
            {text:'非叶子结点'},
            {text:'叶子结点',leaf:true}
    ]

*treeData2.txt:*

    [
        {text:'计算机',children:[
            {text:'Java',children:[
                {text:'Java核心技术',leaf:true},
                {text:'Thinking in Java',leaf:true}
            ]},
            {text:'算法导论',leaf:true}
        ]},
        {text:'音乐',children:[
            {text:'乐理基础',leaf:true},
            {text:'卡尔卡西古典吉他教程',leaf:true}
            ]}
    ]

## 四、程序效果如下图所示：
图片丢失:>