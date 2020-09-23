---
title: Idea 下 启动一个工程下多个子模块项目
date: 2020-01-12 14:10:00
updated: 2020-01-16 16:09:59
tags: 
- http
- 幂等性
- 架构
categories: 
- java

---
用Idea 打开项目下的 .idea 下 的workspace.xml 文件

![idea_servicecomb_multi_child.png][1]


<!--more-->


查找`RunDashboard` :
![mbioq_spring_rundashboard_configurationTypes.png][2]

添加如下内容: 

```bash
<option name="configurationTypes">
      <set>
        <option value="SpringBootApplicationConfigurationType" />
      </set>
</option>
```
关掉IDEA重启，随便启动其中一个微服务就会看到service面板，关掉这个服务再点击debug按钮就可以了。


此时就可以一次启动多个微服务完成

 

  [1]: https://imgs.gnux.cn/usr/uploads/2020/01/449296550.png
  [2]: https://imgs.gnux.cn/usr/uploads/2020/01/3942478610.png