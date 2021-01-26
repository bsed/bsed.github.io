---
title: "适合SWOOLE_HTTP_SERVER用的服务脚本"
categories: [ "PHP" ]
tags: [ "SWOOLE" ]
draft: false
slug: "for-swoolehttpserver-service-script"
date: "2015-01-01 18:33:00"
---

api接口使用的`ZPHP`框架开发,整合了swoole的`swoole_http_server`,由于测试机上cli了很多php脚本,重启php脚本变的比较麻烦,写了个脚本来进行启动/重启/状态/重载。
脚本使用了`netstat`来监控端口工作状态。


<!--more-->


脚本内容如下:

    #!/usr/local/php/bin/php
    <?php
    /**
     * @Description 
     * @filename test.php
     * @encoding UTF-8
     * @package 
     * @subpackage 
     * @author$
     * @date$
     * @Revision$
     * @Id$
     */
    
    namespace bin;
    
    use ZPHP\ZPHP;
    
    //取得命令
    if (empty($cmd = $argv[1])) {
        exit("Usage: HttpServer {start|stop|restart|reload|status} " . PHP_EOL);
    }
    //运行版本
    if (version_compare(PHP_VERSION, '5.3.0', '<')) {
        exit("HttpServer PHP >= 5.3.0 required " . PHP_EOL);
    }
    //必须安装swoole扩展
    if (!get_extension_funcs('swoole')) {
        exit("HttpServer must install the swoole extension " . PHP_EOL);
    }
    //swoole版本检查
    if (version_compare(SWOOLE_VERSION, '1.7.8', '<')) {
        exit("HttpServer Swoole >= 1.7.8 required " . PHP_EOL);
    }
    //需要exec执行命令
    if (!function_exists('exec')) {
        exit("HttpServer must enable exec " . PHP_EOL);
    }
    //读取配置文件
    if (FALSE === $config = parse_ini_file("/home/server/Project/api.51zhima.com/bin/http.ini", TRUE)) {
        echo 'the config file no such or no parse' . PHP_EOL;
        return;
    }
    //关闭警告输出
    if ($config['app']['disable_notice']) {
        error_reporting(E_ALL & ~E_NOTICE);
    }
    
    switch ($cmd) {
        case 'start'://启动
            if ("" === get_bind_port_status($config)) {
                HttpServer::getInstance($config);
            }
            echo "HttpServer is running..." . PHP_EOL;
            break;
        case 'stop'://停止
            if ("" !== $pid = get_bind_port_status($config)) {
                exec("kill -15 {$pid}");
            }
            exit("HttpServer is stop..." . PHP_EOL);
            break;
        case 'restart'://冷重启
            if ("" !== $pid = get_bind_port_status($config)) {
                exec("kill -15 {$pid}");
            }
            HttpServer::getInstance($config);
            break;
        case 'reload'://热重启
            if ("" === $pid = get_bind_port_status($config)) {
                exit("HttpServer not running..." . PHP_EOL);
            }
            exec("kill -USR1 {$pid}");
            echo "HttpServer is reload..." . PHP_EOL;
            break;
        case 'status'://状态
            if ("" === $pid = get_bind_port_status($config)) {
                exit("HttpServer not running..." . PHP_EOL);
            }
            echo "HttpServer is running..." . PHP_EOL;
            break;
        default:
            exit("Usage: HttpServer {start|stop|restart|reload|status} " . PHP_EOL);
            break;
    }
要给`httpserver`增加无人值守也很方便,在`crontab`里增加一条`httpserver start`,定期检测是否启动，未启动重启就可以了。