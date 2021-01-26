---
title: "几个PHP功能函数"
categories: [ "PHP" ]
tags: [ "php","functions" ]
draft: false
slug: "several-php-functions"
date: "2013-02-02 11:44:00"
---

从别人那儿qiang过来一些，在此分享出来，希望有朋友能用的到~

注意：部分函数可能不够完善，由此造成的漏洞风险自担~


<!--more-->


### 提交过滤 ##

    function filter($text)
    {
     //完全过滤注释
     $text = preg_replace('/<!--?.*-->/', '', $text);
     //完全过滤js
     $text = preg_replace('/<script?.*\/script>/', '', $text);
     //过滤危险的属性，如：过滤on事件lang js
     while (preg_match('/(<[^><]+)( lang|action|background|codebase|dynsrc|lowsrc)[^><]+/i', $text, $mat)) {
     $text = str_replace($mat[0], $mat[1], $text);
     }
     while (preg_match('/(<[^><]+)(window\.|javascript:|js:|about:|file:|document\.|vbs:|cookie)([^><]*)/i', $text, $mat)) {
     $text = str_replace($mat[0], $mat[1] . $mat[3], $text);
     }
     //过滤多余html
     $text = preg_replace('/<\/?(html|head|meta|link|base|basefont|body|bgsound|script|form|iframe|frame|frameset|applet|id|ilayer|layer|name|script|xml)[^><]*>/i', '', $text);
     //反转换
     $text = str_replace('[', '<', $text);
     $text = str_replace(']', '>', $text);
     $text = str_replace('|', '"', $text);
     return $text;
    }

## 提交过滤2 ##

    function stripslashes_array(&$array)
    {
     while (list($key, $var) = each($array)) {
     if ($key != 'argc' && $key != 'argv' && (strtoupper($key) != $key || '' . intval($key) == "$key")) {
     if (is_string($var)) {
     $array[$key] = stripslashes($var);
     }
     if (is_array($var)) {
     $array[$key] = stripslashes_array($var);
     }
     }
     }
     return $array;
    }

## 字符串截取(substr的增强版） ##

    function subString($strings, $start, $length)
    {
     if (function_exists('mb_substr') && function_exists('mb_strlen')) {
     $sub_str = mb_substr($strings, $start, $length, 'utf8');
     return mb_strlen($sub_str, 'utf8') < mb_strlen($strings, 'utf8') ? $sub_str . '...' : $sub_str;
     }
     $str = substr($strings, $start, $length);
     $char = 0;
     for ($i = 0; $i < strlen($str); $i++) {
     if (ord($str[$i]) >= 128)
     $char++;
     }
     $str2 = substr($strings, $start, $length + 1);
     $str3 = substr($strings, $start, $length + 2);
     if ($char % 3 == 1) {
     if ($length <= strlen($strings)) {
     $str3 = $str3 .= '...';
     }
     return $str3;
     }
     if ($char % 3 == 2) {
     if ($length <= strlen($strings)) {
     $str2 = $str2 .= '...';
     }
     return $str2;
     }
     if ($char % 3 == 0) {
     if ($length <= strlen($strings)) {
     $str = $str .= '...';
     }
     return $str;
     }
    }

## IP地址匿名化（最后一位替换为星号）##

    function AnonymousIP() 
    {
     $ip = GetIP();
     if ($ip == "Unknown") {
     $removed_ip = "未知用户";
     }
     if (strpos($ip, ":")) {
     $removed_ip = "IPv6用户";
     } else {
     $reg1 = '/((?:\d+\.){3})\d+/';
     $reg2 = '~(\d+)\.(\d+)\.(\d+)\.(\d+)~';
     $removed_ip = preg_replace($reg1, "\\1*", $ip);
     }
     return $removed_ip;
    }

## 获取客户端IP地址 ##

    function GetIP()
    {
     if (@$_SERVER["HTTP_X_FORWARDED_FOR"])
     $ip = $_SERVER["HTTP_X_FORWARDED_FOR"];
     else if (@$_SERVER["HTTP_CLIENT_IP"])
     $ip = $_SERVER["HTTP_CLIENT_IP"];
     else if (@$_SERVER["REMOTE_ADDR"])
     $ip = $_SERVER["REMOTE_ADDR"];
     else if (@getenv("HTTP_X_FORWARDED_FOR"))
     $ip = getenv("HTTP_X_FORWARDED_FOR");
     else if (@getenv("HTTP_CLIENT_IP"))
     $ip = getenv("HTTP_CLIENT_IP");
     else if (@getenv("REMOTE_ADDR"))
     $ip = getenv("REMOTE_ADDR");
     else
     $ip = "Unknown";
     return $ip;
    }

## 字符串加密（支持中文）##

    function dencrypt($string, $isEncrypt = true, $key = "youdian")
    {
     if (!isset($string{0}) || !isset($key{0})) {
     return false;
     }
     
     $dynKey = $isEncrypt ? hash('sha1', microtime(true)) : substr($string, 0, 40);
     $fixedKey = hash('sha1', $key);
     
     $dynKeyPart1 = substr($dynKey, 0, 20);
     $dynKeyPart2 = substr($dynKey, 20);
     $fixedKeyPart1 = substr($fixedKey, 0, 20);
     $fixedKeyPart2 = substr($fixedKey, 20);
     $key = hash('sha1', $dynKeyPart1 . $fixedKeyPart1 . $dynKeyPart2 . $fixedKeyPart2);
     
     $string = $isEncrypt ? $fixedKeyPart1 . $string . $dynKeyPart2 : (isset($string{339}) ? gzuncompress(base64_decode(substr($string, 40))) : base64_decode(substr($string, 40)));
     
     $n = 0;
     $result = '';
     $len = strlen($string);
     
     for ($n = 0; $n < $len; $n++) {
     $result .= chr(ord($string{$n}) ^ ord($key{$n % 40}));
     }
     return $isEncrypt ? $dynKey . str_replace('=', '', base64_encode($n > 299 ? gzcompress($result) : $result)) : substr($result, 20, -20);
    }

## 检查字符串是否包含中文 ##

    function CheckChinese($string)
    {
     if (preg_match("/[\x7f-\xff]/", $string)) {
     return true;
     } else {
     return false;
     }
    }

## 隐藏文件真实路径 ##

    function FileHeaderJump($file)
    {
     header('Content-Description: File Transfer');
     header('Content-Type: application/octet-stream');
     header('Content-Disposition: attachment; filename=' . basename($file));
     header('Content-Transfer-Encoding: binary');
     header('Expires: 0');
     header('Cache-Control: must-revalidate, post-check=0, pre-check=0');
     header('Pragma: public');
     header('Content-Length: ' . filesize($file));
     ob_clean();
     flush();
     readfile($file);
     exit;
    }

## 生成随机字符串，自定义长度 ##

    function CreateRandomString($length)
    {
     $chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
     $password = "";
     for ($i = 0; $i < $length; $i++) {
     $password .= $chars[mt_rand(0, strlen($chars) - 1)];
     }
     return $password;
    }