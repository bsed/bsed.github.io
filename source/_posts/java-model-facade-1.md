---
title: Java外观模式（Facade模式）
date: 2014-10-18 13:38:00
updated: 2014-11-03 13:39:28
tags: 
- css
- box-shadow
categories: 
- css

---
外观模式（Facade）的定义：为子系统中的一组接口提供一个一致的界面。

Facade一个典型应用就是数据库JDBC的应用，如下例对数据库的操作：


<!--more-->


    public class DBCompare {
    　Connection conn = null;
    　PreparedStatement prep = null;
    　ResultSet rset = null;
    　try {
    　　Class.forName( "<driver>" ).newInstance();
    　　conn = DriverManager.getConnection( "<database>" );
    　　　　
    　　String sql = "SELECT * FROM <table> WHERE <column name> = ?";
    　　prep = conn.prepareStatement( sql );
    　    prep.setString( 1, "<column value>" );
    　　rset = prep.executeQuery();
    　　if( rset.next() ) {
    　　　　System.out.println( rset.getString( "<column name" ) );
    　　}
    　} catch( SException e ) {
    　　e.printStackTrace();
    　} finally {
    　　rset.close();
    　　prep.close();
    　　conn.close();
    　}
    }

上例是Jsp中最通常的对数据库操作办法。

在应用中，经常需要对数据库操作，每次都写上述一段代码肯定比较麻烦，需要将其中不变的部分提炼出来，做成一个接口，这就引入了facade外观对象。如果以后我们更换Class.forName中的<driver>也非常方便，比如从Mysql数据库换到Oracle数据库，只要更换facade接口中的driver就可以。

我们做成了一个Facade接口，使用该接口，上例中的程序就可以更改如下：

    public class DBCompare {
    　String sql = "SELECT * FROM <table> WHERE <column name> = ?";　　
    　try {
    　　Mysql msql=new mysql(sql);
    　　prep.setString( 1, "<column value>" );
    　　rset = prep.executeQuery();
    　　if( rset.next() ) {
    　　　System.out.println( rset.getString( "<column name" ) );
    　　}
    　} catch( SException e ) {
    　　e.printStackTrace();
    　} finally {
    　　mysql.close();
    　　mysql=null;
    　}
    }

可见非常简单，所有程序对数据库访问都是使用改接口，降低系统的复杂性，增加了灵活性。

如果我们要使用连接池，也只要针对facade接口修改就可以。
Java 外观模式
由上图可以看出，facade实际上是个理顺系统间关系，降低系统间耦合度的一个常用的办法，也许你已经不知不觉在使用，尽管不知道它就是facade。