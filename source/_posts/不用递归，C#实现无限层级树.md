---
title: "不用递归，C#实现无限层级树"
categories: [ "Linux" ]
tags: [ "c#","nested" ]
draft: false
slug: "no-recursive-c"
date: "2015-10-19 23:22:00"
---

首先是对象的属性：
```c#
public class School
{
 public int Id { get; set; }
 public string Name { get; set; }                 
 public int Pid { get; set; }      
 public List<School>  Children { get; set; }
}
```


<!--more-->


有父级Id、子级Id 的那种

下面是实现层级树的方法
```bash
public class ToTree
{
	var schoolList= 所有数据集合;
	var dic = new Dictionary<int, School>(schoolList.Count);
	foreach (var school in schoolList)
	{
		dic.Add(school .Id, chapter);
	}
	foreach (var school in dic.Values)
	{
		if (dic.ContainsKey(chapter.Pid))
		{
			if (dic[school .Pid].Children == null){
			   dic[school .Pid].Children = new List<School>();
			}
			dic[school .Pid].Children.Add(chapter);
		 }
	}      
	return dic.Values.Where(t => t.Pid == 0).ToList();   
}
```
参考：

 - [https://blog.csdn.net/qq_39547747/article/details/79655622](https://blog.csdn.net/qq_39547747/article/details/79655622)
 - [https://blog.csdn.net/sadwxds/article/details/50957991](https://blog.csdn.net/sadwxds/article/details/50957991)
 - [https://www.cnblogs.com/yxwkf/p/5154959.html](https://www.cnblogs.com/yxwkf/p/5154959.html)
 - [https://hanssens.com/code/iterate-through-a-hierarchical-list-of-infinite-child-elements/](https://hanssens.com/code/iterate-through-a-hierarchical-list-of-infinite-child-elements/)
 - [https://blog.csdn.net/u014419512/article/details/29562191](https://blog.csdn.net/u014419512/article/details/29562191)

