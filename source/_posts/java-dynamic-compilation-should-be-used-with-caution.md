---
title: Java动态编译要慎用
date: 2013-09-17 10:31:00
updated: 2015-01-13 10:36:42
tags: 
- lua
categories: 
- linux

---
动态编译一直是Java 的梦想，从Java 6 版本它开始支持动态编译了，可以在运行期直接编译.java 文件，执行.class，并且能够获得相关的输入输出，甚至还能监听相关的事件。


<!--more-->


不过，我们最期望的还是给定一段代码，直接编译，然后运行，也就是空中编译执行（onthe-fly），来看如下代码：

    public class Client {
        public static void main(String[] args) throws Exception {
            //Java 源代码
            Stri ng sourceStr = "public class Hello{public String sayHello (String name)
                {return \"Hello,\" + name + \"!\";}}";
            // 类名及文件名
            String clsName = "Hello";
            // 方法名
            String methodName = "sayHello";
            // 当前编译器
            JavaCompiler cmp = ToolProvider.getSystemJavaCompiler();
            //Java 标准文件管理器
            StandardJavaFileManager fm = cmp.getStandardFileManager(null,null,null);
            //Java 文件对象
            JavaFileObject jfo = new StringJavaObject(clsName,sourceStr);
            // 编译参数，类似于javac <options> 中的options
            List<String> optionsList = new ArrayList<String>();
            // 编译文件的存放地方，注意：此处是为Eclipse 工具特设的
            optionsList.addAll(Arrays.asList("-d","./bin"));
            // 要编译的单元
            List<JavaFileObject> jfos = Arrays.asList(jfo);
            // 设置编译环境
            Java Compiler.CompilationTask task = cmp.getTask(null, fm, null,optionsList,null,jfos);
            // 编译成功
            if(task.call()){
                // 生成对象
                Object obj = Class.forName(clsName).newInstance();
                Class<? extends Object> cls = obj.getClass();
                // 调用sayHello 方法
                Method m = cls.getMethod(methodName, String.class);
                String str = (String) m.invoke(obj, "Dynamic Compilation");
                System.out.println(str);
            }
        }
    }
    // 文本中的Java 对象
    class StringJavaObject extends SimpleJavaFileObject{
        // 源代码
        private String content = "";
        // 遵循Java 规范的类名及文件
        public StringJavaObject(String _javaFileName,String _content){
            super(_createStringJavaObjectUri(_javaFileName),Kind.SOURCE);
            content = _content;
        }
        // 产生一个URL 资源路径
        private static URI _createStringJavaObjectUri(String name){
            // 注意此处没有设置包名
            return URI.create("String:///" + name + Kind.SOURCE.extension);
        }
        // 文本文件代码
        @Override
        public CharSequence getCharContent(boolean ignoreEncodingErrors)
            throws IOException {
                return content;
            }
    }

上面的代码较多，这是一个动态编译的模板程序，读者可以拷贝到项目中使用，代码中的中文注释也较多，相信读者看得懂，不多解释，读者只要明白一件事：只要是在本地静态编译能够实现的任务，比如编译参数、输入输出、错误监控等，动态编译就都能实现。

Java 的动态编译对源提供了多个渠道。比如， 可以是字符串（ 例子中就是字符串），可以是文本文件，也可以是编译过的字节码文件（.class 文件），甚至可以是存放在数据库中的明文代码或是字节码。汇总成一句话， 只要是符合Java 规范的就都可以在运行期动态加载， 其实现方式就是实现JavaFileObject 接口， 重写getCharContent、openInputStream、openOutputStream，或者实现JDK 已经提供的两个SimpleJavaFileObject、ForwardingJavaFileObject，具体代码可以参考上个例子。

动态编译虽然是很好的工具，让我们可以更加自如地控制编译过程，但是在我目前所接触的项目中还是使用得较少。原因很简单，静态编译已经能够帮我们处理大部分的工作，甚至是全部的工作，即使真的需要动态编译，也有很好的替代方案，比如JRuby、Groovy 等无缝的脚本语言。

另外，我们在使用动态编译时，需要注意以下几点：
1. 在框架中谨慎使用
比如要在Struts 中使用动态编译，动态实现一个类，它若继承自ActionSupport 就希望它成为一个Action。能做到，但是debug 很困难；再比如在Spring 中，写一个动态类，要让它动态注入到Spring 容器中，这是需要花费老大功夫的。
2. 不要在要求高性能的项目使用
动态编译毕竟需要一个编译过程，与静态编译相比多了一个执行环节，因此在高性能项目中不要使用动态编译。不过，如果是在工具类项目中它则可以很好地发挥其优越性，比如在Eclipse 工具中写一个插件，就可以很好地使用动态编译，不用重启即可实现运行、调试功能，非常方便。
3. 动态编译要考虑安全问题
如果你在Web 界面上提供了一个功能，允许上传一个Java 文件然后运行，那就等于说：“我的机器没有密码，大家都来看我的隐私吧”，这是非常典型的注入漏洞，只要上传一个恶意Java 程序就可以让你所有的安全工作毁于一旦。
4. 记录动态编译过程
建议记录源文件、目标文件、编译过程、执行过程等日志，不仅仅是为了诊断，还是为了安全和审计，对Java 项目来说，空中编译和运行是很不让人放心的，留下这些依据可以更好地优化程序。