---
title: node.js 使用 UglifyJS2 高效率压缩 javascript 文件
date: 2015-10-31 19:37:00
updated: 2016-01-24 08:23:47
tags: 
- mysql
- index
categories: 
- sql

---
UglifyJS2 这个工具使用很长时间了,但之前都是在  gulp 自动构建 时用到了 UglifyJS 算法进行压缩.
最近玩了一下 UglifyJS2 ,做了一个  在线压缩javascript工具 欢迎点击玩耍.

## 为什么要压缩 javascript

因为每个人开发者的书写习惯,定义参数习惯,已经使用习惯都不一样.
所以相同的功能出自不同开发者代码各异.这里牵扯到一个代码所占空间.

以前端为例来说明

大家通常都会认为,现在在网络时代,动不动就是10M光纤,拨号上网让人感觉是上个世纪的方式…
说法是没有问题,但是,我们今天讨论的并不是终端下载速度.
 而是前端压缩对服务器有何利好!

现在的前端越来越重,富客户端方式让人有更好的体验,所以前端各种框架,插件空前盛行

我们普普通通引用一个第三方包或者开源插件,轻则几十K,重则上M,十几M 都很常见.
如果一个客户端连接,服务器需要传输100K ,那么如果同时有几百个,几千个客户端并发时,服务器输出带宽是多大?
再想想公司租赁带宽一年费用多少? (这可不是我们普通家庭包年宽带的价格)

如果你可以把前端 100k 的静态资源压缩到 50k ,那么在相同带宽的情况下,是不是可以提高服务器并发量.这就是直接帮公司省钱了.

还有一个原因:

页面加载,我们通常的优化方案是把 css 放到 head 里面加载,把 javascript 放到 body 结束处加载.
就是因为 javascript 的运行机制是单线程,如果你把大量的 javascript 脚本放在 head 处加载,无形中影响了 页面渲染速度. 当然现在 html5 也在优化一些更优的方案.
但是不论这没有,小文件的加载一定比大文件加载用户体验更好.

## 开始学习下 UglifyJS2

UglifyJS 和 UglifyJS2 是什么关系
UglifyJS是UglifyJS2的前身，
它是一个Javascript开发的通用的语法分析、代码压缩、代码优化的工具.
基于Nodejs环境开发，支持CommonJS模块系统的任意的Javascript平台.


<!--more-->


**UglifyJS 功能**

生成JS代码的抽象语法树(AST)，通过parse-js.js完成.
 遍历AST语法树，做各种操作，比如自动缩进、缩短变量名、删除块括号{}、去空格、常量表达式、连续变量声明、语块合并、去掉无法访问的代码等，通过process.js完成.

UglifyJS2 是 UglifyJS 的改进版
作者对之前的 UglifyJS 做了很多的优化,包括下面手段:

    parser,用于实现抽象语言法树(AST)
    the code generator, 通过AST生成JS代码和source map
    compressor, 用于JS代码的压缩
    mangler, 用于减少局部变量的命名，用单字母替换
    scope analyzer, 用来判断 变量定义范围和变量引用范围的工具
    tree walker, AST树遍历工具
    tree transformer, AST树转换工具

## UglifyJS2安装

npm 全局安装

    npm install uglify-js -g

你也可以通过 github 来下载源码使用

    git clone git://github.com/mishoo/UglifyJS2.git

## UglifyJS2 参数说明

➜  /Users/zhangzhi/code  >uglifyjs --help
uglifyjs input1.js [input2.js …] [options]
Use a single dash to read input from the standard input.

NOTE: by default there is no mangling/compression.
Without [options] it will simply parse input files and dump the AST
with whitespace and comments discarded.  To achieve compression and
mangling you need to use -c and -m.

Options:
–source-map                  Specify an output file where to generate source
map.
–source-map-root             The path to the original source to be included
in the source map.
–source-map-url              The path to the source map to be added in //#
sourceMappingURL.  Defaults to the value passed
with --source-map.
–source-map-include-sources  Pass this flag if you want to include the
content of source files in the source map as
sourcesContent property.
–in-source-map               Input source map, useful if you’re compressing
JS that was generated from some other original
code.
–screw-ie8                   Pass this flag if you don’t care about full
compliance with Internet Explorer 6-8 quirks
(by default UglifyJS will try to be IE-proof).
–expr                        Parse a single expression, rather than a
program (for parsing JSON)
-p, --prefix                  Skip prefix for original filenames that appear
in source maps. For example -p 3 will drop 3
directories from file names and ensure they are
relative paths. You can also specify -p
relative, which will make UglifyJS figure out
itself the relative paths between original
sources, the source map and the output file.
-o, --output                  Output file (default STDOUT).
-b, --beautify                Beautify output/specify output options.
-m, --mangle                  Mangle names/pass mangler options.
-r, --reserved                Reserved names to exclude from mangling.
-c, --compress                Enable compressor/pass compressor options. Pass
options like -c
hoist_vars=false,if_return=false. Use -c with
no argument to use the default compression
options.
-d, --define                  Global definitions
-e, --enclose                 Embed everything in a big function, with a
configurable parameter/argument list.
–comments                    Preserve copyright comments in the output. By
default this works like Google Closure, keeping
JSDoc-style comments that contain “@license” or
"@preserve". You can optionally pass one of the
following arguments to this flag:
- “all” to keep all comments
- a valid JS regexp (needs to start with a
slash) to keep only comments that match.
Note that currently not all comments can be
kept when compression is on, because of dead
code removal or cascading statements into
sequences.
–preamble                    Preamble to prepend to the output.  You can use
this to insert a comment, for example for
licensing information.  This will not be
parsed, but the source map will adjust for its
presence.
–stats                       Display operations run time on STDERR.
–acorn                       Use Acorn for parsing.
–spidermonkey                Assume input files are SpiderMonkey AST format
(as JSON).
–self                        Build itself (UglifyJS2) as a library (implies
–wrap=UglifyJS --export-all)
–wrap                        Embed everything in a big function, making the
“exports” and “global” variables available. You
need to pass an argument to this option to
specify the name that your module will take
when included in, say, a browser.
–export-all                  Only used when --wrap, this tells UglifyJS to
add code to automatically export all globals.
–lint                        Display some scope warnings
-v, --verbose                 Verbose
-V, --version                 Print version number and exit.
–noerr                       Don’t throw an error for unknown options in -c,
-b or -m.
–bare-returns                Allow return outside of functions.  Useful when
minifying CommonJS modules.
–keep-fnames                 Do not mangle/drop function names.  Useful for
code relying on Function.prototype.name.
–reserved-file               File containing reserved names
–reserve-domprops            Make (most?) DOM properties reserved for
–mangle-props
–mangle-props                Mangle property names
–mangle-regex                Only mangle property names matching the regex
–name-cache                  File to hold mangled names mappings

> 上面列出了好多的选项,如果使用的时候不明白,可以对比看下下面的说明

    * source-map [string]，生成source map文件。
    * –source-map-root [string], 指定生成source map的源文件位置。
    * –source-map-url [string], 指定source map的网站访问地址。
    * –source-map-include-sources，设置源文件被包含到source map中。
    * –in-source-map，自定义source map，用于其他工具生成的source map。
    * –screw-ie8, 用于生成完全兼容IE6-8的代码。
    * –expr, 解析一个表达式或JSON。
    * -p, –prefix [string], 跳过原始文件名的前缀部分，用于指定源文件、source map和输出文件的相对路径。
    * -o, –output [string], 输出到文件。
    * -b, –beautify [string], 输出带格式化的文件。
    * -m, –mangle [string], 输出变量名替换后的文件。
    * -r, –reserved [string], 保留变量名，排除mangle过程。
    * -c, –compress [string], 输出压缩后的文件。
    * -d, –define [string], 全局定义。
    * -e, –enclose [string], 把所有代码合并到一个函数中，并提供一个可配置的参数列表。
    * –comments [string], 增加注释参数，如@license、@preserve。
    * –preamble [string], 增加注释描述。
    * –stats, 显示运行状态。
    * –acorn, 用Acorn做解析。
    * –spidermonkey, 解析SpiderMonkey格式的文件，如JSON。
    * –self, 把UglifyJS2做为依赖库一起打包。
    * –wrap, 把所有代码合并到一个函数中。
    * –export-all, 和–wrap一起使用，自动输出到全局环境。
    * –lint, 显示环境的异常信息。
    * -v, –verbose, 打印运行日志详细。
    * -V, –version, 打印版本号。
    * –noerr, 忽略错误命令行参数。

# UglifyJS2 使用方法
> UglifyJS2使用包括2种方式
1. shell 指令调用
2. api 调用

>> shell 指令
我们尝试用来压缩2个文件
➜  /Users/zhangzhi/code/test  >
我的 test 目录下有好多 js 文件,准备挑选2个 js 文件 ( start.js 和 test.js) 进行合并,合并后的文件名为 min.js

```shell
➜  /Users/zhangzhi/code/test  >uglifyjs start.js test.js -o min.js --source-map min.js.map

然后看下当前目录:


多出了2个文件 , min.js  和 min.js.map

压缩后的样子是这样的
![yigrherb_UglifyJS2_01.png][1]
![yigrherb_UglifyJS2_02.png][2]
api 调用方法

    var fs = require('fs');
    var uglifyjs = require("uglify-js");
    var result = uglifyjs.minify("../test.js",{
       mangle:false
    });
    上面的一个基本核心方法 minify ,下面我们单独看下这个方法

### minify 方法

这是一个非常智能的方法 ,共 2个参数

第一个参数 *

支持 字符串,路径,路径数组

1.字符串参数

就是我们书写的 javascript 代码可以直接当做一个字符串参数参数函数,但是需要有第二个参数告诉函数,这是 javascript 源码字符串

    var result = uglifyjs.minify("var fun=function(){ alert('一介布衣博客');};",{
        mangle:false,
         fromString:true,
    });

上面的第一个参数传入了 javascript 源码,第二个参数中 formString : true ,就是告诉 minify 函数,前面的参数是 需要压缩的 javascript 源码.

字符串路径

这是函数默认支持的一种方式,可以单独的一个参数,直接给定一个 需要压缩的 javascript 文件路径, 当然也可以2个参数.

    var result = uglifyjs.minify("../test.js");

上面的函数执行,会吧我的上级目录中的 test.js 进行压缩.默认一个参数时,表示文件路径

数组指定多个路径

可以一个参数,但是这个参数是一个数组 [ ‘路径1’,’路径2’,’路径3’] 类似这样
结果就是把上面路径的所有 javascript 压缩后返回到了 result 对象中,稍后我们单独说下 result 返回值.

    var result = uglifyjs.minify([ "../test.js", "../mian.js"]);

第二个参数*

参数说明

romString属性 (default false) 指定第一个参数中的 字符串是 javascript 源码

mangle属性 默认为true；指定为false时，表示不进行混淆压缩

width和max-line-len属性 按照说明，这里应该是指压缩后的文件的长度

outSourceMap属性 用来指定函数返回值result.map字符串转化为Object后file属性的值

sourceRoot属性 用来指定函数返回值result.map字符串转化为Object后sourceRoot属性的值

返回值 result *

返回值 result 是一个对象.
code 属性对应的是压缩后的脚本

    {"code":"这里是压缩后的 javascript 脚本","map":null}

来源：[http://yijiebuyi.com/blog/88e7fe09ac514c8b56ff9936cefdf142.html](http://yijiebuyi.com/blog/88e7fe09ac514c8b56ff9936cefdf142.html)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1549703386.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/01/4134539800.png