---
title: 树形组建TreePanel从servlet中读取JSON数据
date: 2011-12-30 22:07:00
updated: 2016-06-02 11:33:50
tags: 
- android
categories: 
- android

---
在Ext JS的Tree中的数据往往是从服务器端的动态程序中获取的。
为了获取数据，我们可以先编写一个返回JSON的Servlet共前台的树形组建访问：

*Java：*

    import java.io.PrintWriter;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    public class TreeNodeServlet extends HttpServlet {
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {


<!--more-->


    response.setContentType("text/html;charset=utf-8");
    //这里的node是前台的AsyncTreeNode组建中设置的ID，见下面的JS程序
    String treeNode = request.getParameter("node");
    String json = "";
    PrintWriter out = response.getWriter();
    if("0".equals(treeNode)) {
    json+="[{id:1,text:'0-1'},{id:2,text:'0-2'}]";
    }
    else if("1".equals(treeNode)) {
    json+="[{id:11,text:'0-1-1',leaf:true},{id:12,text:'0-1-2',leaf:true}]";
    }
    else if("2".equals(treeNode)) {
    json+="[{id:21,text:'0-2-1'},{id:22,text:'0-2-2',leaf:true}]";
    }
    else if("21".equals(treeNode)) {
    json+="[{id:211,text:'0-2-1-1',leaf:true},{id:212,text:'0-2-1-2',leaf:true}]";
    }
    out.write(json);
    }
    }

现在就可以在前台的TreePanel组建的loader方法中访问上面创建的servlet了，代码如下：
*客户端显示代码*

    Ext.onReady(function(){
    var tree = new Ext.tree.TreePanel({
    //这里的div-tree是在html中创建的一个对象的id值
    el: 'div-tree',
    //使用loader方法访问TreeNodeServlet
    loader: new Ext.tree.TreeLoader({dataUrl:'../TreeNodeServlet'})
    });
    var root = new Ext.tree.AsyncTreeNode({id:'0',text:'0'})
    tree.setRootNode(root);
    tree.render();
    root.expand();
    });