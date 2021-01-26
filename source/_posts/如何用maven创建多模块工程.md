---
title: "如何用maven创建多模块工程"
categories: [ "Maven" ]
tags: [ "maven" ]
draft: false
slug: "how-to-use-maven-to-create-a-multi-module-project"
date: "2014-07-03 21:07:00"
---

三年前,我已经开始使用maven这样伟大的工具,Maven支持仓库管理以及多模块创建。 本教程中,我将向您展示如何创建一个多模块maven项目。 我使用 Eclipse IDE 和  m2eclipse eclipse 的插件。 

下面的图片显示了Maven 在eclipse中的项目结构。 

我的项目是 shims 的,它有两个模块 shims-core  和 shims-web 。 一个模块包含所有业务逻辑类和数据访问对象 jar 文件。 和其他模块的web模块包含控制器相关类(Struts2 action 或 Servlets或 Spring controller 类)和视图相关的东西。


<!--more-->


本教程总结如下。 

 - 安装maven
 - 配置m2eclipse maven和eclipse
 - 创建父项目
 - 创造孩子jar模块
 - 创造孩子war模块
 - 构建多模块项目

## 安装maven

下载Apache maven 发行版本并解压到你的电脑中。 你可以从 apache官网下载 [maven](http://maven.apache.org/download.html)。

`D:\tools\apache-maven-3.3.3`

接下来,您必须设置” MAVEN_HOME 的环境变量和Path系统环境。 在Ubuntu中,您可以设置变量,如下所示。 你必须添加以下两行 bashrc 文件。 首先,运行以下命令来打开的 `bashrc` 的文件。 

    $ vi ~/.bashrc

并添加以下两行。 

    export MAVEN_HOME=/home/vinesh/apache-maven-3.0.4 
    export PATH=$MAVEN_HOME/bin:$PATH 

在windows中,设置一个环境变量没有太多的困难。 所以我不打算解释,在这个教程。可以参考[此教程]()

经过编辑的 `bashrc` 的文件,打开一个命令窗口并运行以下命令。

    $ mvn -version 

如果你的bash控制台出现如下内容,说明你成功地配置了*apache maven*。

    Apache Maven 3.0.4 (r1232337; 2012-01-17 14:14:56+0530) 
    Maven home: /home/semika/apache-maven-3.0.4 
    Java version: 1.6.0_30, vendor: Sun Microsystems Inc. 
    Java home: /home/semika/java/jdk1.6.0_30/jre 
    Default locale: en_US, platform encoding: UTF-8 
    OS name: "linux", version: "2.6.38-16-generic", arch: "amd64", family: "unix"

你完成了第一步,让我们搬到下一个步骤。   

## 配置m2eclipse Maven和eclipse 

你必须先安装m2eclipse插件。 你可以通过eclipse安装m2eclipse插件。 我使用`eclipse Indigo` 的版本,可能会有轻微的差异与其他版本的eclipse。当然你可以用Eclipse插件仓库直接安装

这个是 **m2eclipse** 插件的 更新URL。 

`http://download.eclipse.org/technology/m2e/releases`

去 `Help --> Install New Software`。 你会得到下面的窗口。 


您可以添加上述更新URL并继续。 通常情况下,这需要一些时间和完成安装后,您必须重新启动eclipse。 去 `Window -->Preferences` 。 您应该看到 Maven 已经被 添加到Perferences 下面。


然后对maven插件做一些必要的配置， 见上图中的红色高亮标记。

点击“ 添加 ”按钮,找到您的maven安装目录。 为进一步澄清看到图像波纹管。



你现在可以看到,与我们的maven插件已经配置安装。 现在,您已经准备好使用m2eclipse创建项目。 让我们开始创建我们的多模块项目。 

Eclipse提供了很好的设施创建多模块项目和模块之间的关系。 在本教程中,我将创建一个maven的多模块项目。 
 
创建maven父项目 

Step01 :去 文件- - >新建- - >项目 。 

你会得到的 新项目 的窗口。 下的 Maven 的类别,选择 Maven项目 ”。 点击“ 下一个 ”按钮。 你会得到的 新的Maven项目 的窗口。 看到波形图像。




一步02 :我们要创建简单的maven项目。 我指出从右边图像,确保检查” 创建一个简单的项目 ”复选框。 点击“ 下一个 ”按钮。 然后你会得到下面的窗口。 

我觉得,最好是给小解释四个字段用红色突出显示颜色框右边的图像。 

组Id :这是根包装您的项目结构。 确保使用相同的根包结构在项目的每个模块。

工件Id 这是类似于你的项目模块名称。 在右边图片,我给的 垫片 随着工件id。这是我的父项目所有项目模块。

包装: 这是非常重要的在创建多与maven模块项目。 有几个选项,你可以选择包装。 如果你想创建一个父项目,我们所做的就像现在,你必须选择“ 砰的一声 随着包装。 创建Java模块,选择“ jar 对于web模块,选择的 战争” 。 我将解释本教程以后,如何创建一个“ jar ”和“ 战争 的模块。 你知道的 shims-core ”是一个包含所有的jar模块业务逻辑和数据访问对象和“ shims-web ”是一个web模块包含所有控制器和视图相关的东西。

点击“ 完成 ”按钮,看看,maven是创建父项目多模块项目。 让我们检查这个。

打开“ pom.xml 的文件。 它应该类似于下面的。


<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.semika</groupId>
  <artifactId>shims</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
</project>


让我们创建我们的第一个孩子模块。   

创建maven孩子jar模块 

我们要保持我们的所有业务逻辑 刀 类作为一个单独的模块,可以捆绑在一起作为一个“ jar 的文件。 我们可以创建一个单独的项目模块。 在我的示例应用程序中,它的 shims-core 的模块。 这是一个子模块的父项目我们已经创建。

创建子jar模块。 

步骤1 :右键点击父项目,去 新- - >项目 。 选择“ Maven模块 “从” Maven 的类别。 你还记得,我们选择的 Maven项目 当我们创建我们的父项目。 因为我们现在创建一个项目模块,我们应该选择“ Maven模块 ”。

点击“ 下一个 ”按钮。 你会得到的 新的Maven模块 ”窗口,如下所示。




确保检查” 创建一个简单的项目 复选框和模块名称。 你可以看到的 垫片 这是我们的父项目自动选为“ 父项目 ”。 我们是在一个正确的方法。 点击“ 下一个 ”按钮。 你会得到熟悉的窗口如下所示。



你可以看到在右边图片中,“ 组Id ”、“ 工件Id ”和“ 版本 “已经默认设置。 我们不需要改变任何的三个字段值。 保留这些值。 因为我们正在创造的 jar 的模块,我们应该选择 jar 随着包装。 点击“ 完成 ”按钮,看看maven模块创建我们的第一个孩子。

现在刷新父项目,注意到maven创造了一个新项目的 shims-core “内部的” 垫片 ”。 “ shims-core ”代表了另一个maven项目模块独立 pom.xml 文件。 还的 shims-core 代表的新项目在eclipse中。

请看下面图片。 


现在,如果你看到父项目的 pom.xml 文件中,您可以看到, shims-core 已经列在模块说, shims-core ”子模块的 垫片 父项目。

<modules>
   <module>shims-core</module>
  </modules>

另外,在这个孩子的项目 pom.xml 父项目文件,它有一个引用。 此外,在子模块的 pom.xml 文件”, groupId ”和“ 版本 “还没有被定义。 这些都是遗传自父母的项目。 这是好的做法,当你创建多与maven模块项目。
<parent>
    <artifactId>shims</artifactId>
    <groupId>com.semika</groupId>
    <version>1.0-SNAPSHOT</version>
    <relativePath>..</relativePath>
  </parent>


创造孩子war模块 

接下来,我们将创建web模块”, shims-web 它包含我们的控制器类和其他视图相关的东西。 创建 战争 模块与m2eclipse有些不同,而创建 jar 模块。 我们必须使用maven 预定义的 原型 用于创建web模块。 别担心,我会给你一步一步的解释关于创建maven 战争 模块。

步骤1 :右键单击父项目,去 新- - >项目 ,选择“ Maven模块 ”,点击“ 下一个 ”按钮。 


在本例中,我们将创建一个简单的项目。 所以不要检查” 创建简单的项目 ”复选框。 你可以看到从右边图像,父项目自动选择为“ 垫片 这是我们的父项目。 你必须指定模块名称为“ shims-web ”。 然后单击“ 下一个 ”按钮。 

一步02 :点击“ 下一个 从上面的图片”按钮。 我们将使用的 maven-archetype-webapp 创建war模块的工件。 看到波形图像。 

 

一步03 :点击“ 下一个 从上面窗口”按钮,你会得到以下窗口。 


你只需要改变的 包 “从右边的窗口。 点击“ 完成 ”按钮,让maven创建我们的web模块。 创建新的web模块后,刷新父项目,注意到它有一个新模块的 shims-web ”。 现在我们有一个多模块项目两个模块。 开放 pom.xml 文件从web模块,确保删除” groupId ”, 的版本 ”。 也看到父项目的 pom.xml 文件和注意的 shims-web 已添加到它作为一个新的模块。

我们希望我们的应用程序作为一个战争包部署到服务器。 如果你想改变你的war文件的名字,你可以改变的 filaName 的属性 pom.xml 文件的 shims-web 的模块。


<build>
    <finalName>shims-web</finalName>
  </build>

这将创建的 shims-web.war 文件下的 目标 “目录” shims-web 的模块。 接下来,我们需要定义之间的依赖关系的 shims-core ”和“ shims-web 的模块。 这将迫使maven反应堆之前构建的shims-core模块构建“shims-web”模块,当我们从父母运行maven构建项目。 ,因为我们想要包业务逻辑和刀相关类到一个单独的jar模块和给web模块作为一个单独的库以及其他库,核心模块应该构建在web模块。

内的下列内容的 依赖关系 的部分 pom.xml 文件的 shims-web 的模块。

<dependency>
        <groupId>${project.groupId}</groupId>
        <artifactId>shims-core</artifactId>
        <version>${project.version}</version>
    </dependency>

$ { project.groupId } 和 $ { project.version } 是两个全局引用的maven提供“groupId”和“版本”在整个模块的多模块项目。 我们可以直接使用它。

构建多模块项目 

有很多的优势创建多模块项目。 我们可以建立整个项目,我们也可以建立每个模块分别。 开发人员可以同时分别工作在不同的模块不依赖他人。 另一件事是,我们有明确的分离,提高可维护性。
我们必须创建maven构建我们的项目目标。 右键单击父项目。 去 作为- - >运行Maven构建 。 将会出现以下窗口只有第一次。 在那里你可以创建一个新的目标来构建您的项目。

右边窗口允许您创建maven目标构建您的项目。 你必须给一个名称为您的构建目标和浏览所需的项目模块目录。 因为我要创建这个maven目标构建整个项目,我已经找到我的父目录。 把“ 全新安装 到目标字段。
以后,我们要建立我们的项目,第一次我们应该允许maven从全球库下载所需的依赖项。 确保“网下”复选框“不”。 第一个成功构建之后,您可以更新这个目标在脱机模式下运行它通过选择“离线”复选框。
确保的 Maven运行时 已经将我们的maven安装目录。 否则你会得到构建错误。

好吧!。 就是这样。 您已经准备好构建第一个maven多模块项目。 好运！

点击“ 应用 “然后” 运行 ”按钮。 Maven将启动建设项目。 仔细看看eclipse控制台。 它说完整的故事。 您应该获得“成功”的信息的构建。 类似的输出如下所示。

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
[信息]安装/home/vinesh/workspace-maaven /垫片/ shims-web / pom。 xml /home/vinesh/.m2/repository/com/semika/shims-web/1.0-SNAPSHOT /垫片- web - 1.0 snapshot.pom 
[信息]- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
[信息]反应堆简介: 
[信息][信息]垫片............................................. 成功(1.093秒) 
[信息]shims-core ........................................ 成功(2.005秒) 
[信息]shims-web Maven Webapp ............................ 成功(1.027秒) 
[信息]- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
[信息]构建成功 
[信息]- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
[信息]总时间:4.354秒 
[信息]了:坐11月03 22:37:27是2012 
[INFO] Final Memory: 6M/103M 
[INFO] ------------------------------------------------------------------------

Let's deploy our application. I am using Apache tomcat7 to deploy my application. If you see inside the target directory of ' shims-web ' module after the successful build, you can see,maven has create ' shims-web.war ' file. Copy that file into tomcat's webapps folder and start the tomcat.

Point your browser the following URL.

http://localhost:8080/shims-web/

You should get hello world page. 
