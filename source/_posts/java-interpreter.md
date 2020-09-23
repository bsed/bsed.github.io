---
title: Java解释器模式（Interpreter模式）
date: 2014-10-11 13:33:00
updated: 2014-11-03 13:34:41
tags: 
- angularjs
- scriptsweb-server-js
categories: 
- js

---
Interpreter定义：定义语言的文法，并且建立一个解释器来解释该语言中的句子。

Interpreter似乎使用面不是很广，它描述了一个语言解释器是如何构成的，在实际应用中，我们可能很少去构造一个语言的文法。我们还是来简单的了解一下。


<!--more-->


首先要建立一个接口，用来描述共同的操作。

    public interface AbstractExpression {
    　　 void interpret( Context context );
    }

再看看包含解释器之外的一些全局信息
 
  `public interface Context { }`

AbstractExpression的具体实现分两种：终结符表达式和非终结符表达式。
  
      public class TerminalExpression implements AbstractExpression {
    　　 public void interpret( Context context ) { }
        }

对于文法中没一条规则，非终结符表达式都必须的：

    public class NonterminalExpression implements AbstractExpression {
    　　 private AbstractExpression successor;
    　　
    　　 public void setSuccessor( AbstractExpression successor ) {
    　　　　 this.successor = successor;
    　　 }
    
    　　 public AbstractExpression getSuccessor() {
    　　　　 return successor;
    　　 }
    
    　　 public void interpret( Context context ) { }
    }
