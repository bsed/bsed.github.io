---
title: "php中的无限极分类中最经典的代码（函数）"
categories: [ "PHP" ]
tags: [ "php","分类","经典函数" ]
draft: false
slug: "php-function-fenlei"
date: "2010-10-13 17:49:00"
---

第一种形式（最新版本）推荐使用：

    <?php
    function d($arr,$pid,$s) {
      static $r_arr=array();
      static $n = 0;
            $s=$s."&nbsp;&nbsp;&nbsp;";
            for($i=0;$i<count($arr);$i++){
                if($arr[$i]['parentid']==$pid){
                    $r_arr[$n]['catname'] = $s.'|-'.$arr[$i]['catname'];
                    $r_arr[$n]['catid'] = $arr[$i]['catid'];
        $n++;
                    for($j=0;$j<count($arr);$j++){
                        if($arr[$i]['catid']==$arr[$j]['parentid']){
                            $r_arr[$n]['catname'] = $s."&nbsp;&nbsp;&nbsp;|-".$arr[$j]['catname'];
          $r_arr[$n]['catid'] = $arr[$j]['catid'];
          $n++;
                            d($arr,$arr[$j]['catid'],$s."&nbsp;&nbsp;&nbsp;");
                        }
                    }
                }
    
    
            }
      return $r_arr;
        }
    ?>

第二种形式（原始版本）：

    <?php
    header("Content-Type:text/html;charset=utf-8");
    function d($arr,$pid,$s) {
            $s=$s."&nbsp;&nbsp;&nbsp;";
            for($i=0;$i<count($arr);$i++){
                if($arr[$i][2]==$pid){
                    echo $s.$arr[$i][1]."<br>";
    
    
                    for($j=0;$j<count($arr);$j++){
                        if($arr[$i][0]==$arr[$j][2]){
                            echo $s."&nbsp;&nbsp;&nbsp;".$arr[$j][1]."<br>";
                            d($arr,$arr[$j][0],$s."&nbsp;&nbsp;&nbsp;");
                        }
                    }
                }
            }
        }
        $arr1=array('a1','根目录1','0','1');
        $arr2=array('b1','根目录2','0','1');
        $arr3=array('a2','一级目录','a1','2');
        $arr4=array('b2','一级目录','b1','2');
        $arr5=array('a3','二级目录','a2','3');
        $arr6=array('b3','二级目录','b2','3');
        $arr7=array('a4','三级目录','a3','4');
        $arr=array($arr1,$arr2,$arr3,$arr4,$arr5,$arr6,$arr7);
        d($arr,'0'," ");
    ?>