---
title: 基于schema匹配为基础，计算gml文件结构匹配相似度
date: 2016-01-24 17:42:00
updated: 2016-01-24 18:20:59
tags: 
- emacs
categories: 
- vim

---
## 起步

这里的schema文档时指定义xml文件结构的`.xsd`文件，gml其实就是有schema约束的xml文件。

> 2000年5月，OGC推出了基于XML DTD (Document Type
> Definitions，文档类型定义)和RDF(Resource Description Frameworks，资源描述框架)的GML
> 1.0版。
> 
> 2001年2月，OGC又推出了完全基于XML Schema 的GML 2.0版。
> 
> 2001年2月，OGC又推出了完全基于XML Schema 的GML 2.0版。2003年2月，GML 3.0版正式发布。

所以，现在gml通用解释：地理标记语言(Geography Markup Language)，GML是XML在地理空间信息领域的应用。但在这里只是表示有schema约束的xml文件，注意，不要混了。

github地址：[https://github.com/sasukeax/schema_match](https://github.com/sasukeax/schema_match)

## 思考

schema文档树看成由根节点开始到叶节点的众多的一条条的路径所组成
<!--more-->
![schema_gml.png][1]

路径的相似度需要从以下两个角度来考虑：

 - 路径的长度
 - 路径上的相似节点的个数

节点的相似度则需要考虑：

 - 类型（标签名）是不是相等的
 - 类型是不是可转化的（如 int 型和double型）
 - 属性的值是不是相等的

*公式：*

`Sim = w1 * Lmatch(s, t) + w2 * Pmatch(s, t) + w3 * childMatch(s, t) - w4 * (pathLength1 - pathLength2);`

*公式解释：*

`w1`：名称相似度系数
`Lmatch(s, t)`:节点名称相似度
`w2`:属性相似度系数
`Pmatch(s, t)`:节点属性相似度
`w3`:儿子相似度系数
`childMatch(s, t)`:节点下面所有子孙节点的相似度
`w4`:路径相似度系数
`(pathLength1 - pathLength2)`:路径长度差

*步骤:*

1.先定义一下树节点的结构：

StructNode.class.php
```php
<?php
class StructNode {  
    public $tagName = "";   //标签名称
    public $depth = 0;      //树高
    public $xmlTagName = "";//定义的对应xml文件的标签名 如： <city></city> 则存'city'
    public $xmlTagType = "";//定义的对应xml文件的标签的属性
    public $children_list = array();//儿子节点
}
```

用类有如下原因，如果用基本类型，在数组情况下，$a = $b;数据会有一份拷贝，这对遍历树和节点赋值都是致命的。属性都设为public纯属作者偷懒。

顺便定义一下相似度系数:
```php
//设置schema匹配一致性系数
define('MATCH_W1',0.7);     //名称相似度系数
define('MATCH_W2',0.2);     //属性相似度系数
define('MATCH_W3',0.1);     //儿子相似度系数
define('MATCH_W4',0.001);   //路径相似度系数
```
2.读取xsd文件

xsd文件本身也是符合xml格式的，可以用DOMDocument类来转化成dom树

```php
$file_name = 'test.xsd';
$doc = new DOMDocument();
$doc->load($file_name);
```
3.转化dom树到已定义的结构

运行：`$tree = xml_to_tree($doc);`

```php
/**
 *从某节点开始向下遍历遍历XML文档所有节点的方法
 *@param DOMDocument $node
 *@param int $depth
 *@param StructNode $arr
 *@return StructNode
 */
function walk_xml($node, $depth, $arr) {
    for ($i = 0, $indent = ''; $i < $depth; $i++)
        $indent .= '   ';
    if(!empty($node->tagName)) {//非空格换行或非纯文本的有效标签
        $arr->tagName = $node->tagName;
        $arr->depth = $depth;
        $arr->xmlTagName = $node->getAttribute("name");//设置对应xml的标签名
        $arr->xmlTagType = $node->getAttribute("type");//设置对应xml的标签属性
    }
    $children = $node->childNodes;
    if(isset($children->length)){
        $children_count = $children->length;
        if($children_count > 0) {
            $depth++;
            for ($i=0; $i < $children_count; $i++) { 
                if(!empty($children->item($i)->tagName)){
                    $child = new StructNode();
                    walk_xml($children->item($i), $depth,$child);
                    $arr->children_list[] = $child;
                }
            }
        }
    }
    return $arr;
}

/**
 *把dom树转为自定义的树结构
 *@param DOMDocument $node
 *@return StructNode
 */
function xml_to_tree($node) {
    $root = new StructNode();//辅助根节点
    $root = walk_xml($node, 0, $root);
    $root = fidder_to_root_node($root);
    return $root;
}

/**
 *过滤xsd节点，获取对应xml的root节点
 *@param StructNode $node
 *@return StructNode
 */
function fidder_to_root_node($node) {
    if(!empty($node->xmlTagName)) {
        return $node;
    }else {
        if(count($node->children_list) != 0) {
            foreach ($node->children_list as $value) {
                return fidder_to_root_node($value);
            }
        }
    }
    return null;//找不到xml文件的root节点
}
```

匹配函数

节点名称相似度与节点属性相似度的判断比较简单，因为无需考虑语义问题，如：`name` 与 `author` 的相似度，所以在此直接返回`0`或 `1`
```php
/**
 *计算两个节点名称相似度,一个是xsd中标签名，一个是xml中的标签名
 *@param StructNode $node1
 *@param StructNode $node2
 *@return int
 */
function Lmatch($node1, $node2) {
    $name_sim = 0;
    if($node1->xmlTagName == $node2->xmlTagName) {
        $name_sim += 1;
    }
    if($node1->tagName != $node2->tagName) {//若xsd中标签名不一致，则匹配减半
        $name_sim /= 2;
    }
    return $name_sim;
}

/**
 *计算两个节点属性相似度
 *@param StructNode $node1
 *@param StructNode $node2
 *@return int
 */
function Pmatch($node1, $node2) {
    return $node1->xmlTagType == $node2->xmlTagType ? 1 : 0;
}
```

匹配节点相似度与子节点相似度

节点的相似度：
```php
/**
 *用路径匹配算法计算节点相似度
 *@param StructNode $tree1
 *@param StructNode $tree2
 *@return float
 */
function match($tree1, $tree2) {
    //获取两棵树儿子个数
    $tree1_children_count = count($tree1->children_list);
    $tree2_children_count = count($tree2->children_list);
    if($tree1_children_count == 0 && $tree2_children_count == 0) {//如果两个节点都是叶节点
        $sim = MATCH_W1 * Lmatch($tree1, $tree2) + MATCH_W2 * Pmatch($tree1, $tree2) + MATCH_W3;
    }else if(($tree1_children_count != 0 && $tree2_children_count == 0) || ($tree1_children_count == 0 && $tree2_children_count != 0)) {
        //如果两个节点一个是叶节点，一个不是
        $sim = MATCH_W1 * Lmatch($tree1, $tree2) + MATCH_W2 * Pmatch($tree1, $tree2) + 0;
    }else {
        //两个节点都不是叶节点
        $sim = MATCH_W1 * Lmatch($tree1, $tree2) + MATCH_W2 * Pmatch($tree1, $tree2) + MATCH_W3 * childMatch($tree1, $tree2);
    }
    return $sim;
}
```

计算子节点相似度：
```php
/**
 *计算节点下面所有孩子节点的相似度，其中用到match函数，两个函数相互调用，调用childMatch($tree1, $tree2)时要保证两个节点都有子节点
 *@param StructNode $tree1
 *@param StructNode $tree2
 *@return float
 */
function childMatch($tree1, $tree2) {
    $nMatch = 0;//匹配的路径数
    $max = 0;
    $weight = 0;
    foreach($tree1->children_list as $tree1_child) {
        foreach ($tree2->children_list as $tree2_child ) {
            $_weight = match($tree1_child, $tree2_child);//递归调用，将遍历所有儿子节点
            if($_weight > $max) {
                $max = $_weight;
            }
        }
        if($max > 0) {
            $pathLength1 = $tree1_child->depth;
            $pathLength2 = $tree2_child->depth;
            $weight = $_weight - MATCH_W4 * abs($pathLength1 - $pathLength2);
            $nMatch ++;
        }
    }
    //递归结束，下面计算匹配结果
    $w1 = $weight / count($tree1->children_list);
    $w2 = $nMatch / (count($tree2->children_list) + count($tree1->children_list));
    return ($w1 + $w2) / 2;
}
```
*测试：*

test.php

```php
    <?php
    $file_name1 = 'address.xsd';
    $file_name2 = 'order.xsd';
    
    // 首先要建一个DOMDocument对象 
    $doc1 = new DOMDocument();
    $doc2 = new DOMDocument();
    
    $doc1->load($file_name1);
    $doc2->load($file_name2);
    
    $arr1 = xml_to_tree($doc1);
    $arr2 = xml_to_tree($doc2);
    
    $result = match($arr1, $arr2);
    echo ("相似度为：".$result);//相似度为：0.37360208333333
```

原文: [https://www.hongweipeng.com/index.php/archives/307/](https://www.hongweipeng.com/index.php/archives/307/)
  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/4067018810.png