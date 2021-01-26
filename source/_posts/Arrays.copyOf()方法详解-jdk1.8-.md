---
title: "Arrays.copyOf()方法详解-jdk1.8 "
categories: [ "Java" ]
tags: [ "array","jdk1.8","copyof" ]
draft: false
slug: "details-of-arrays-copyof-method-jdk18"
date: "2020-02-08 18:21:00"
---

```java
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

可以看到，最终调用的是System.arraycopy()方法，关于这个方法使用，可查看[System.arraycopy](https://blog.csdn.net/balsamspear/article/details/85069207)详解


<!--more-->


**1. 方法的含义**
返回一个类型为T的数组，数组的容量为newLength和original.length中的小值，元素为original数组中的元素（取0到newLength和original.length中的小值）

> 说白了，这个方法就是为了数组元素的向上转型，还有就是截断数组

**2. (Object)newType == (Object)Object[].class**
判断newType是否是Object数组

**3. 为什么要加(Object)呢？**
因为要用使用==去比较它们的内存地址，从而判断它们是不是同一类型，而使用==，就要向上强转为Object，不然编辑器不让通过，不能比较

```java
public class SystemArrayCopy {

    public static void main(String[] args) {
        System.out.println(Object.class == Integer.class);
    }
}
/*
编辑时报错：
Error:(37, 41) java: 不可比较的类型: java.lang.Class<java.lang.Object>和java.lang.Class<java.lang.Integer>
*/
```

加上(Object)后

```java
public class SystemArrayCopy {

    public static void main(String[] args) {
        System.out.println(Object.class == (Object)Integer.class);
    }
}
/*
false
*/
```

**4. newType.getComponentType()**

```java
public native Class<?> getComponentType();
```

本地方法，返回数组内的元素类型，不是数组时，返回null

```java
public class SystemArrayCopy {

    public static void main(String[] args) {
        String[] o = {"aa", "bb", "cc"};
        System.out.println(o.getClass().getComponentType());
        System.out.println(Object.class.getComponentType());
    }
}
/*
class java.lang.String
null
*/
```

**5. Array.newInstance(newType.getComponentType(), newLength)**
创建一个类型与newType一样，长度为newLength的数组

```java
public static Object newInstance(Class<?> componentType, int length)
    throws NegativeArraySizeException {
    return newArray(componentType, length);
}
private static native Object newArray(Class<?> componentType, int length)
        throws NegativeArraySizeException;
```

> Array.newInstance内部直接调用Array.newArray，newArray为本地方法，由虚拟机实现

newInstance返回为Object，实质为数组

```
public class SystemArrayCopy {

    public static void main(String[] args) {
        Object o = Array.newInstance(String.class, 10);
        System.out.println(o.getClass());
        System.out.println(String.class); // 用于对比
    }
}
/*
class [Ljava.lang.String;
class java.lang.String
*/
```

> 可以看到，Array.newInstance的返回虽然是Object类型，但是它实质上是String数组，可以强制转换成String[]，如：String[] arr = (String[]) Array.newInstance(String.class, 10);

**6. (T[]) new Object[newLength]，为什么Object[]可以强制转换成T[]呢？**
判断(Object)newType == (Object)Object[].class
为true时，执行的是(T[]) new Object[newLength]，那T就是Object，newType也是Object[].class，所以可以强转成T[]
为false时，执行的是(T[]) Array.newInstance(newType.getComponentType(), newLength)，Array.newInstance返回的本质就是T[]，所以可以强转成T[]
**7. 为什么强转为T[]，而不是Object[]？**
因为我这个方法就是要返回T[]，这是方法编写的本意


<!--more-->


**8. 作用**
把源数组中元素的类型向上转型
截断数组，当给定长度小于给定数组时，就可以实现截断的效果

> 如：ArrayList底层数组的类型转换使用的这个方法（ArrayList参数为集合的构造函数中）

本文转载自：https://blog.csdn.net/balsamspear/article/details/85078599