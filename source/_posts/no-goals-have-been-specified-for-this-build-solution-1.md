---
title: mica-example 报No goals have been specified for this build”解决办法
date: 2019-10-27 23:06:00
updated: 2019-10-28 09:58:02
tags: 
- gradle
- maven
- jdk
categories: 
- java

---
解决办法：

打开pom.xml 在build节点中 增加
```
<defaultGoal>compile</defaultGoal>
```

<!--more-->

```
...
    <build>
<defaultGoal>compile</defaultGoal>
         
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.12.3</version>
                <configuration>
                    <groups>unit</groups>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.0</version>
                <configuration>
                  <verbose>true</verbose>
                  <fork>true</fork>
                  <compilerVersion>1.5</compilerVersion>
                </configuration>
            </plugin>
             
        </plugins>
    </build>
</project>
```