---
title: c# list copy 用法
date: 2019-01-22 10:54:00
updated: 2019-01-22 10:54:41
tags: 
- kafka
- conf
categories: 
- linux

---
方法一：
```csharp
List<string> t = new List<string>(); //original
List<string> t2 = t.ToList();
```
方法二：
```csharp
List<string> t = new List<string>(); //original
List<string> t2 = new List<string>(t.ToArray()); // copy of t
```


<!--more-->


方法三：

It is a one liner using LINQ.

```csharp
      List<string> list1 = new List<string>();
      List<string> list2 = new List<string>();

      // This will copy all the items from list 1 to list 2
      list1.ForEach(i => list2.Add(i));
```

方法四：

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using System.Diagnostics;

namespace Delegates
{
  class X
  {
    public int Id { get; set; }
    public string Name { get; set; }
  }

  class Y
  {
    public int Id { get; set; }
    public string Name { get; set; }
  }

  class Program
  {
    static void Main(string[] args)
    {
      List<X> x = new List<X>();
      for (int i = 0; i < 100; i++)
        x.Add(new X { Id = i, Name = string.Format("x_{0}", i.ToString()) });
      // copy x to y
      List<Y> y = new List<Y>(x.ConvertAll<Y>(e => { return new Y { Id = e.Id, Name = e.Name }; }));
      Debug.Assert(x.Count == y.Count);
    }

  }
}
```

方法五：

```csharp
static class Extensions {
        public static IList<T> Clone<T>(this IList<T> listToClone) where T : ICloneable
        { return listToClone.Select(
            item => (T)item.Clone())
            .ToList();
        }
    }
```

方法六：
 If your elements are value types, then you can just do:

```
List<YourType> newList = new List<YourType>(oldList);  
```

However, if they are reference types and you want a deep copy (assuming your elements properly implement `ICloneable`), you could do something like this:

```
List<ICloneable> oldList = new List<ICloneable>();
    List<ICloneable> newList = new List<ICloneable>(oldList.Count);
    oldList.ForEach((item) => { 
        newList.Add((ICloneable)item.Clone());
    }); 
```

方法七：
 You could try this:

```csharp
List<Int32> copy = new List<Int32>(original);  
```

or if you’re using C# 3 and .NET 3.5, with Linq, you can do this:

```csharp
List<Int32> copy = original.ToList(); 
```

方法八：

```csharp
targetList.AddRange(sourceList); 
```

方法九：

```csharp
List<int> i=original.Take(original.count).ToList;  
```

方法十：

```csharp
List<string> myNewList = Clone(myOldList); 
List<T> Clone<T>(IEnumerable<T> oldList) {      
	return newList = new List<T>(oldList); 
}
```