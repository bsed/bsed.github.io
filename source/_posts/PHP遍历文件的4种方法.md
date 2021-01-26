---
title: "PHP遍历文件的4种方法"
categories: [ "PHP" ]
tags: [ "php","foreach" ]
draft: false
slug: "php-foreach-four-method"
date: "2012-09-25 12:30:00"
---

一次实习中用到了PHP遍历文件夹中的文件，无奈对于php是一窍不通，所以转载下面的文章。
【常规遍历方法】
常规遍历方法是指从PHP4开始就可以使用的方法，它是结合使用opendir()函数和readdir()函数，通过打开目录，读取目录和关


<!--more-->


闭目录，逐一判断列出所有的文件。简单实现代码如下：

    /**
     * 获取当前目录及子目录下的所有文件
     * @param string $dir 路径名
     * @return array 所有文件的路径数组
     */
    function get_files1($dir) {
        $files = array();
     
        if(!is_dir($dir)) {
            return $files;
        }
     
        $handle = opendir($dir);
        if($handle) {
            while(false !== ($file = readdir($handle))) {
                if ($file != '.' && $file != '..') {
                    $filename = $dir . "/"  . $file;
                    if(is_file($filename)) {
                        $files[] = $filename;
                    }else {
                        $files = array_merge($files, get_files($filename));
                    }
                }
            }   //  end while
            closedir($handle);
        }
        return $files;
    }   //  end function

【使用glob】
glob() 函数依照 libc glob() 函数使用的规则寻找所有与 pattern 匹配的文件路径，类似于一般 shells 所用的规则一样。不进行缩写扩展或参数替代。返回一个包含有匹配文件／目录的数组。如果出错返回 FALSE。
此函数不能作用于远程文件，被检查的文件必须通过服务器的文件系统访问。

    <?php
    /**
     * 获取当前目录下的所有文件
     * @param string $dir 路径名
     * @return array 所有文件的路径数组
     */
    function get_files($dir) {
        $dir = realpath($dir) . "/";
        $files  = array();
     
        if (!is_dir($dir)) {
            return $files;
        }
     
        $pattern =  $dir . "*";
        $file_arr = glob($pattern);
     
        foreach ($file_arr as $file) {
            if (is_dir($file)) {
                $temp = get_files($file);
     
                if (is_array($temp)) {
                    $files = array_merge($files, $temp);
                }
            }else {
                $files[] = $file;
            }   //  end if
        }
        return $files;
    }   //  end function
    ?>

【使用directory 类】
这是个仿冒面向对象的机制来读取一个目录。
dir() 函数打开一个目录句柄，并返回一个对象。这个对象包含三个方法：read() , rewind() 以及 close()。并且有两个属性可用。handle 属性可以用在其它目录函数例如 readdir()，rewinddir() 和 closedir() 中。path 属性被设为被打开的目录路径。
若成功，则该函数返回一个目录流，否则返回 false 以及一个 error。可以通过在函数名前加上 “@” 来隐藏 error 的输出。
注意: read 方法返回的目录项的顺序依赖于系统。
注意: 本函数定义了内部类 Directory，意味着不能再用同样的名字定义用户自己的类。

    /**
     * 递归显示当前指定目录下所有文件
     * 使用dir函数
     * @param string $dir 目录地址
     * @return array $files 文件列表
     */
    function get_files($dir) {
        $files = array();
     
        if (!is_dir($dir)) {
            return $files;
        }
     
        $d = dir($dir);
        while (false !== ($file = $d->read())) {
            if ($file != '.' && $file != '..') {
                $filename = $dir . "/"  . $file;
     
                if(is_file($filename)) {
                    $files[] = $filename;
                }else {
                    $files = array_merge($files, get_files($filename));
                }
            }
        }
        $d->close();
        return $files;
    }

【RecursiveDirectoryIterator类】
此方法自PHP 5.0有效

    /**
     * 使用RecursiveDirectoryIterator遍历文件，列出所有文件路径
     * @param RecursiveDirectoryIterator $dir 指定了目录的RecursiveDirectoryIterator实例
     * @return array $files 文件列表
     */
    function get_files($dir) {
        $files = array();
     
        for (; $dir->valid(); $dir->next()) {
            if ($dir->isDir() && !$dir->isDot()) {
                if ($dir->haschildren()) {
                    $files = array_merge($files, get_files($dir->getChildren()));
                };
            }else if($dir->isFile()){
                $files[] = $dir->getPathName();
            }
        }
        return $files;
    }
     
    $path = "/var/www";
    $dir = new RecursiveDirectoryIterator($path);
    print_r(get_files($dir));

文章转载自：[http://www.phppan.com/2009/10/use-php-traversal-file/](http://www.phppan.com/2009/10/use-php-traversal-file/)