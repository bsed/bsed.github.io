---
title: xss 处理
date: 2013-12-17 16:28:00
updated: 2015-01-08 15:17:14
tags: 
- php-ini
categories: 
- php

---
预防xss，页面中不同区域的字串应该使用不同的手段来处理，而不是一概的htmlspecialchars，简单+实用。

    <?php
    /**
     * Xss 处理
     *
     * 作者: 刘卫锋 (kevonliu@tencent.com)
     * 创建时间: 2013-07-04
     *
     * $Id: Xss.php 48518 2013-07-05 03:35:04Z kevonliu $
     */
    


<!--more-->


    class Xss {
    
        /**
         * 处理链接字串
         *
         * @param string $str
         * @return string
         */
        public static function link($str) {
    
            $pairs = array(
                '\'' => '%27',
                '"'  => '%22',
                '<'  => '%3C',
                '>'  => '%3E',
            );
    
            return strtr($str, $pairs);
        }
    
        /**
         * 处理js中的字串
         *
         * @param string $str
         * @return string
         */
        public static function js($str) {
    
            // See ECMA 262 section 7.8.4 for string literal format
            $pairs = array(
                // "\\" => "\\\\",
                // "\"" => "\\\"",
                // '\'' => '\\\'',
                '\"' => '\\x22',
                '\'' => '\\x39',
                '\\' => '\\x92',
    
                # To avoid closing the element or CDATA section
                '<' => '\\x3c',
                '>' => '\\x3e',
    
                # To avoid any complaints about bad entity refs
                '&' => '\\x26',
    
                # Work around https://bugzilla.mozilla.org/show_bug.cgi?id=274152
                # Encode certain Unicode formatting chars so affected
                # versions of Gecko don't misinterpret our strings;
                # this is a common problem with Farsi text.
                "\xe2\x80\x8c" => "\\u200c", // ZERO WIDTH NON-JOINER
                "\xe2\x80\x8d" => "\\u200d", // ZERO WIDTH JOINER
            );
    
            return strtr($str, $pairs);
        }
    
        /**
         * 处理html中的字串
         *
         * @param string $str
         * @return string
         */
        public static function html($str) {
    
            $pairs = array(
                '"'  => '&quot;',
                '\'' => '&#039;',
                '&'  => '&amp;',
                '>'  => '&gt;',
                '<'  => '&lt;',
            );
            return strtr($str, $pairs);
        }
    
    }
    
    ?>

