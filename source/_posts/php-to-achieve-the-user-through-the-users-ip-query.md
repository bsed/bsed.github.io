---
title: php实现通过访问者的ip查询用户所在的城市
date: 2015-08-20 11:32:00
updated: 2015-08-20 15:14:42
tags: 
- phpmyadmin
- html5
- formdata
categories: 
- php

---
![IP-Geolocation-Search.jpg][1]
**ipinfobd.com** 国内访问有问题，gfw用户慎用

**php：**


<!--more-->


    function detect_city($ip) {
          
        $default = 'UNKNOWN';
   
        $curlopt_useragent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6 (.NET CLR 3.5.30729)';
          
        $url = 'http://ipinfodb.com/ip_locator.php?ip=' . urlencode($ip);
        $ch = curl_init();
          
        $curl_opt = array(
            CURLOPT_FOLLOWLOCATION  => 1,
            CURLOPT_HEADER      => 0,
            CURLOPT_RETURNTRANSFER  => 1,
            CURLOPT_USERAGENT   => $curlopt_useragent,
            CURLOPT_URL       => $url,
            CURLOPT_TIMEOUT         => 1,
            CURLOPT_REFERER         => 'http://' . $_SERVER['HTTP_HOST'],
        );
          
        curl_setopt_array($ch, $curl_opt);
          
        $content = curl_exec($ch);
          
        if (!is_null($curl_info)) {
            $curl_info = curl_getinfo($ch);
        }
          
        curl_close($ch);
          
        if ( preg_match('{<li>City : ([^<]*)</li>}i', $content, $regs) )  {
            $city = $regs[1];
        }
        if ( preg_match('{<li>State/Province : ([^<]*)</li>}i', $content, $regs) )  {
            $state = $regs[1];
        }
   
        if( $city!='' && $state!='' ){
          $location = $city . ', ' . $state;
          return $location;
        }else{
          return $default; 
        }
          
    }

**用法：**

    <?php
    $ip = $_SERVER['REMOTE_ADDR'];
    $city = detect_city($ip);
    echo $city;
    ?>


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/3031865150.jpg