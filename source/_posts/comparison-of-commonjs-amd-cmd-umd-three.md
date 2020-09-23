---
title: CommonJS 、AMD、 CMD 、UMD 三者的比较
date: 2015-08-07 11:35:00
updated: 2015-08-07 11:37:33
tags: 
- css
- html
categories: 
- css

---
> CommonJS 
> AMD(Asynchromous Module Definition) 
> CMD(Common Module
> Definition) 
> UMD(Universal Module Definition)

### [CommonJS][0]

- CommonJs 是服务器端模块的规范，Node.js采用了这个规范 （Modues/1.x）
- 一个单独的文件就是一个模块
- 加载模块是同步的


<!--more-->


### [AMD][1](Asynchromous Module Definition)

- AMD 是 RequireJS 在推广过程中对模块定义的规范化产出
- AMD异步加载模块 

### [CMD][2](Common Module Definition) 延自CommonJS Modules/2.0规范

- CMD是SeaJS 在推广过程中对模块定义的规范化产出
- 对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）
- CMD推崇依赖就近，AMD推崇依赖前置

### [UMD][3](Universal Module Definition)

- UMD是AMD和CommonJS的糅合
- AMD 浏览器第一的原则发展 异步加载模块
- CommonJS 模块以服务器第一原则发展，选择同步加载，它的模块无需包装(unwrapped modules)
- UMD先判断是否支持Node.js的模块（exports）是否存在，存在则使用Node.js模块模式。 在判断是否支持AMD（define是否存在），存在则使用AMD方式加载模块

[0]:http://javascript.ruanyifeng.com/nodejs/commonjs.html
[1]:http://javascript.ruanyifeng.com/tool/requirejs.html
[2]:https://github.com/seajs/seajs/issues/242
[3]:https://github.com/umdjs/umd