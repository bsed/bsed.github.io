---
title: "Java API String.java的主要源码的分析和学习"
categories: [ "Java" ]
tags: [ "java","api" ]
draft: false
slug: "analysis-and-learning-of-the-main-source-of-api-stringjava-java"
date: "2013-10-19 21:15:00"
---

## 1. 存储结构

    private final char value[];

> 备注：String的底层是通过字符数组实现的，而且是常量，所以我们不能修改String类型的变量的值。

## 2. 构造函数

> String的构造函数有很多：不带参数，参数可以为String, char[], byte[], StringBuffer, StringBuilder等。

### 2.1 第一类

	public String() {
		this.value = new char[0];
	}

	public String(String original) {
		this.value = original.value;
		this.hash = original.hash;
	}

> 备注：因为String是常量，不能被修改，因此这两个构造函数，如无必要一般不用。


<!--more-->


### 2.2 第二类

	public String(char value[]) {
		this.value = Arrays.copyOf(value, value.length);
	}

	public String(StringBuffer buffer) {
		synchronized(buffer) {
			this.value = Arrays.copyOf(buffer.getValue(), buffer.length());
		}
	}

	public String(StringBuilder builder) {
		this.value = Arrays.copyOf(builder.getValue(), builder.builder.length());
	}

> 备注：char[]，StringBuffer，StringBuilder的内在存储结构都是char[]，所以这几个构造函数都是通过Arrays的copyOf函数执行复制操作，Arrays.copyOf函数是通过System.arraycopy实现的，这个函数在看到Arrays的源码时再解释。

### 2.3 第三类

	public String(byte bytes[]) {
		this(bytes, 0, bytes.length);
	}

	public String(byte bytes[], int offset, int length) {
		checkBounds(bytes, offset, length);
		this.value = StringCoding.decode(bytes, offset, length);
	}

	public String(byte bytes[], int offset, int length, Charset charset) {
		if (charset == null)
			throw new NullPointerException("charset");
		checkBounds(bytes, offset, length);
		this.value = StringCoding.decode(charset, bytes, offset, length);
	}

> 备注：使用byte[]时，需要解码，如果没有指定字符编码，会使用系统默认的编码。

## 3. 常用函数

### 3.1 第一类

	public int length() {
		return value.length;
	}

	public boolean isEmpty() {
		return value.length == 0;
	}

	public char charAt(int index) {
		if ((index < 0) || (index >= value.length)) {
			throw new StringIndexOutOfBoundsException(index);
		}
		return value[index];
	}

> 备注：length在String，StringBuilder，StringBuffer里面都是函数length()，在char[]是成员变量。

### 3.2 第二类

	public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
		if (srcBegin < 0) {
			throw new StringIndexOutOfBoundsException(srcBegin);
		}
		if (srcEnd > value.length) {
			throw new StringIndexOutOfBoundsException(srcEnd);
		}
		if (srcBegin > srcEnd) {
			throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
		}
		System.arraycopy(value, 0, dst, dstBegin, value.length);
	}

	public byte[] getBytes(){
		return StringCoding.encode(value, 0, value.length);
	}


> 备注：getChars就是在字符级别进行复制，getBytes需要指定字符集解码。

### 3.3 第三类

	public boolean equals(Object anObject) {
		if (this == anObject) {
			return true;
		}
		if (anObject instanceOf String) {
			String anotherString = (String) anObject;
			int n = value.length;
			if (n == anotherString.value.length) {
				char v1[] = value;
				char v2[] =  anotherString.value;
				int i = 0;
				while (n-- > 0) {}
					if (v1[i] != v2[i])
						return false;
					i++;
				}
				return true;
			}
		}
		return false;
	}

	public boolean equalsIgnoreCase(String anotherString) {
		return (this == anotherString) ? true
			: (anotherString != null) && 
			(anotherString.value.length == value.length) && 
			regionMatches(true, 0, anotherString, 0, value.length);
	}

> 备注：字符串的比较，都是针对String类型的，一个是大小写敏感，一个忽略大小写，内部实现差不多的。

### 3.4 第四类

	public int compareToIgnoreCase(String str) {
		return CASE_INSENSITIVE_ORDER.compare(this, str);
	}

> 备注：CASE_INSENSITIVE_ORDER是内部静态类的一个实例，所以直接调用的效果是一样的：String.CASE_INSENSITIVE_ORDER.compare(str1, str2);
