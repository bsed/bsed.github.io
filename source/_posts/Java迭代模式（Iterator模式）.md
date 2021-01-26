---
title: "Java迭代模式（Iterator模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","iterator" ]
draft: false
slug: "java-model-iterator"
date: "2014-10-06 13:25:00"
---

上了这么多年学，我发现一个问题，好象老师都很喜欢点名，甚至点名都成了某些老师的嗜好，一日不点名，就饭吃不香，觉睡不好似的，我就觉得很奇怪，你的课要是讲的好，同学又怎么会不来听课呢，殊不知：“误人子弟，乃是犯罪！”啊。


<!--more-->


好了，那么我们现在来看老师这个点名过程是如何实现吧：
1、老规矩，我们先定义老师（Teacher）接口类：

    public interface Teacher {
        public Iterator createIterator(); //点名
    }

2、具体的老师（ConcreteTeacher）类是对老师（Teacher）接口的实现：

    public class ConcreteTeacher implements Teacher{
        private Object[] present = {"张三来了","李四来了","王五没来"}; //同学出勤集合
        public Iterator createIterator(){
            return new ConcreteIterator(this); //新的点名
        }
        public Object getElement(int index){ //得到当前同学的出勤情况
            if(index<present.length){
                return present[index];
            }
            else{
                return null;
            }
        }
        public int getSize(){
            return present.length; //得到同学出勤集合的大小,也就是说要知道班上有多少人
        }
    }

3、定义点名（Iterator）接口类：

    public interface Iterator {
        void first(); //第一个
        void next(); //下一个
        boolean isDone(); //是否点名完毕
        Object currentItem(); //当前同学的出勤情况
    }

4、具体的点名（ConcreteIterator）类是对点名（Iterator）接口的实现：

    public class ConcreteIterator implements Iterator{
        private ConcreteTeacher teacher;
        private int index = 0;
        private int size = 0;
        public ConcreteIterator(ConcreteTeacher teacher){
            this.teacher = teacher;
            size = teacher.getSize(); //得到同学的数目
            index = 0;
        }
        public void first(){ //第一个
            index = 0;
        }
        public void next(){ //下一个
            if(index<size){
                index++;
            }
        }
        public boolean isDone(){ //是否点名完毕
            return (index>=size);
        }
        public Object currentItem(){ //当前同学的出勤情况
            return teacher.getElement(index);
        }
    }

5、编写测试类：

    public class Test {
        private Iterator it;
        private Teacher teacher = new ConcreteTeacher();
        public void operation(){
            it = teacher.createIterator(); //老师开始点名
            while(!it.isDone()){ //如果没点完
                System.out.println(it.currentItem().toString()); //获得被点到同学的情况
                it.next(); //点下一个
            }
        }
        public static void main(String agrs[]){
            Test test = new Test();
            test.operation();
        }
    }

6、说明：
A：定义：Iterator模式可以顺序的访问一个聚集中的元素而不必暴露聚集的内部情况。
B：在本例中，老师（Teacher）给出了创建点名（Iterator）对象的接口，点名（Iterator）定义了遍历同学出勤情况所需的接口。
C：Iterator模式的优点是当（ConcreteTeacher）对象中有变化是，比如说同学出勤集合中有加入了新的同学，或减少同学时，这种改动对客户端是没有影响的。
