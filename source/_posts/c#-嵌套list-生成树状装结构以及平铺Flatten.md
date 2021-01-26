---
title: "c# 嵌套list 生成树状装结构以及平铺Flatten"
categories: [ "Linux" ]
tags: [ "c#" ]
draft: false
slug: "c"
date: "2018-11-02 15:20:00"
---

```c#
public static IEnumerable<T> Flatten<T, R>(this IEnumerable<T> source, Func<T, R> recursion) where R : IEnumerable<T>
        {
            var flattened = source.ToList();

            var children = source.Select(recursion);

            if (children != null)
            {
                foreach (var child in children)
                {
                    flattened.AddRange(child.Flatten(recursion));
                }
            }

            return flattened;
        }

var n = new List<FamilyMember>()
                {
                    new FamilyMember { Name = "Dominic", Children = new List<FamilyMember>()
                        {
                            new FamilyMember { Name = "Brittany", Children = new List<FamilyMember>() }
                        }
                    }
                }.Flatten(x => x.Children).ToList();
```
