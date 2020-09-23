---
title: 服务器自动部署 publish.sh
date: 2015-05-11 21:27:00
updated: 2015-10-31 10:34:30
tags: 
- golang
- ping
categories: 
- go

---
#!/bin/sh

##定义变量

    tom_bin="/root/tomcat-7.0.61/bin"
    tom_name="tomcat-7.0.61"
    pro_name="guizhou001.com"

##待更新的zip、war包

    zip_file=$1

##当前路径

    _path_=`pwd`

##项目目录

    pro_path="$_path_/$pro_name"

<!--more-->


##待更新的全路径

    fill_path="$_path_/$zip_file"

##临时目录

    temp_path="$_path_/_temp"
    mkdir $temp_path

##不能更新的目录、文件

    _tempDir="upload,uploadFile,luceneContent"
    _config="jdbcConfig.properties"

##备份临时文件

    OLD_IFS="$IFS" 
    IFS=","
    _dirArr=($_tempDir)
    IFS="$OLD_IFS" 
    for dir in ${_dirArr[@]} 
    do
        cp -rf "$pro_path/$dir" $temp_path
    done
    
    cp -rf "$pro_path/WEB-INF/classes/$_config" $temp_path

##重写解压替换

    cd $pro_path
    rm -rf *
    
    echo "$zip_file解压中..."
    unzip -o $fill_path -d $pro_path > "$temp_path/_zip_temp.log"

##从临时文件恢复

    for dir in ${_dirArr[@]} 
    do
        cp -rf "$temp_path/$dir" $pro_path
    done
    cp -rf "$temp_path/$_config" "$pro_path/WEB-INF/classes/"

##删除临时文件

    rm -rf $temp_path

echo "$pro_name重启中..."
##重启服务

    sh "$tom_bin/shutdown.sh"
    sleep 5
    kill -9 `ps aux|grep $tom_name |grep -v grep |grep java |awk '{print $2}'`
    sleep 3
    sh "$tom_bin/startup.sh"

##发布使用`./publish.sh ToutiaoCMS.war`


