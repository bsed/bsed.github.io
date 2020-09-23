---
title: php中的include require 深入浅出
date: 2012-09-18 17:28:00
updated: 2015-01-08 15:27:19
tags: 
- libcurl
- mingw
categories: 
- default

---
include() 语句包括并运行指定文件。以下文档也适用于 require()。 

**以下内容引用自官方文档** 

1> 这两种结构除了在如何处理失败之外完全一样。include() 产生一个警告而 require() 则导致一个致命错误。换句话说，如果你想在遇到丢失文件时停止处理页面就用 require()。include() 就不是这样，脚本会继续运行。同时也要确认设置了合适的 include_path。注意在 PHP 4.3.5 之前，包含文件中的语法错误不会导致程序停止，但从此版本之后会。 


<!--more-->


2> 寻找包含文件的顺序先是在当前工作目录的相对的 include_path 下寻找，然后是当前运行脚本所在目录相对的 include_path 下寻找。例如 include_path 是 .，当前工作目录是 /www/，脚本中要 include 一个 include/a.php 并且在该文件中有一句 include "b.php"，则寻找 b.php 的顺序先是 /www/，然后是 /www/include/。如果文件名以 ./ 或者是 ../ 开始，则只在当前工作目录相对的 include_path 下寻找。 

注释：如果 访问地址是 http://localhost/test/a.php 那么当前工作目录是test/ ，一般程序中建议使用./ 包含文件，这样程序执行起来比较快捷 

3> 当一个文件被包括时，其中所包含的代码继承了 include 所在行的变量范围。从该处开始，调用文件在该行处可用的任何变量在被调用的文件中也都可用。不过所有在包含文件中定义的函数和类都具有全局作用域。 

如果 include 出现于调用文件中的一个函数里，则被调用的文件中所包含的所有代码将表现得如同它们是在该函数内部定义的一样。所以它将遵循该函数的变量范围。 

当一个文件被包括时，语法解析器在目标文件的开头脱离 PHP 模式并进入 HTML 模式，到文件结尾处恢复。由于此原因，目标文件中应被当作 PHP 代码执行的任何代码都必须被包括在有效的 PHP 起始和结束标记之中. 

4> 因为 include() 是一个特殊的语言结构，其参数不需要括号。在比较其返回值时要注意。

    <?php
    // won't work, evaluated as include(('vars.php') == 'OK'), i.e. include('')
    if (include('vars.php') == 'OK') {
        echo 'OK';
    }
    
    // works
    if ((include 'vars.php') == 'OK') {
        echo 'OK';
    }
    ?> 

个人理解： 1> 在不使用 ./ ../ 时会寻找程序设置的include_path, 如果使用了 ./ ../ 则程序中的include_path将失效。 

2> 在包含文件是，如果想要“找不到包含文件时给出合理的提示信息”，就建议使用 include. 如下代码:

    if(!@include_once('./config/config_global.php')) {
    	exit('config_global.php is missing');
    }

如果文件 config_global.php,找不到则会给出提示：config_global.php is missing .  如果上面使用 require_once，则在找不到文件时直接会有一个致命的错误，将不会继续执行下面的代码，友好提示也就没有了。
