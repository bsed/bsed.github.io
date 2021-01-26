---
title: "新公司 Java编码规范"
categories: [ "Java" ]
tags: [ "java","code" ]
draft: false
slug: "new-company-java-encoding-specification"
date: "2015-04-09 21:19:00"
---

代码规范：

1、代码 命名规范
  1）代码文件命名 应该是以包名结尾。 比如 activity包下应该叫 xxxActivity；utils包下 应该叫 xxxUtil；view包下叫xxxView；model包下叫 xxxModel;
  2）某个类的属性尽量不能用public开放给其他类调用，除非其必须共有。属性名称以 m（成员）开头(实体类除外)  
    比如 public context mContext; 
       public View mView; 
       public Button mBtnLogin;
       public TextView mTxvUserName;
    3）提交的代码不能有警告和大片的注释
    4）List定义的变量应该 List 作为后缀结尾。如： public List<User> mUserList;
  5）Map定义的变量应该 Map 作为后缀结尾。如：public Map<String,User> mUserMap;
  6）数组定义的变量应该 s 作为后缀结尾。如：public User[] mUsers;
  7）类成员变量是用 m 打头。
  8）变量名要使用有意义的名称，通过变量名能大概反映出其具体的用途, 除非是大大降低了可读性
  9）接口类名的名称以大写I开头，比如：IDisplayItem
  


<!--more-->


  
2、代码注释
  注意：换行用<br />，关联代码用<code></code>括起来等
  
  1）代码头注释如下：
    /**
     * @title 类的名称
     * @description 该类主要功能描述
     * @company 北京思维夫网络科技有限公司
     * @author 某某某  
     * @version 1.0
     * @created 2013-01-15 
     */
     
     这个设置下eclipse的code Templates 的Types为以下格式即可：
     Window->Prefencens->Java->code Style->code Templates->Comments->Types->编辑
      /**
       * @title 标题
       * @description 用一句话描述该文件做什么
       * @company 北京思维夫网络科技有限公司
       * @author 你的名字（如：杨福海）
       * @version 1.0
       * @created ${date} ${time}
       */
           
   2）方法注释：
     /**
     * 方法描述
     * @param model 参数描述
     * @return 返回值描述
     */
     
     
   3）方法内代码块注释 或者  变量、属性注释
      //该属性的主要功能有以下几点：
     //    1、xxxxx
     //    2、xxxx
     //    3、xxx
     

3、其他：
  1）代码提交到svn 必须写注释，代码尽量批量提交，而不是一个一个类的提交。
  2）少用eclipse 快捷健格式化代码（一般以快捷键+手动格式化 ，最终是以人性化的代码格式为前提）。
  3）用到第三方开源的框架，jar包直接放在libs目录下，如果是源码，建立一个代码文件夹，文件夹名称为该开源框架名字，然后把源代码放到该文件夹下。

4、安全方面
  1）用户中心的用户信息，必须先从cookie、或者session拿出用户id，再进行详细信息查询，可添加缓存
  2）涉及到用户删帖，一定要判断该帖子时候是该用户、或者删除的sql带上 userid