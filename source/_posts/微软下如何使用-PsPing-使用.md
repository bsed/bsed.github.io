---
title: "微软下如何使用 PsPing 使用"
categories: [ "日常" ]
tags: [ "windows","psping" ]
draft: false
slug: "how-to-use-psping-under-microsoft"
date: "2019-12-17 11:21:00"
---

Windows下的支持ICMP Ping , TCP Ping， 网络延迟（TCP/UDP）, 带宽测试（TCP/UDP）!

## 下载
[https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip)

## 安装
下载zip文件，然后将压缩包中的 `psping.exe` 放到 `C:\Windows\System32`

打开CMD 控制台后，输入 `psping` 会弹出`License`框需要同意一下即可！
![windows_psping.png][1]


<!--more-->


## 使用
输入psping 查看帮助信息！
```cmd
C:\Users\kelvin>psping

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

PsPing implements Ping functionality, TCP ping, UDP/TCP latency, and UDP / TCP
bandwidth measurement.

Help usage: psping -? [i|t|l|b]
   -? i   Usage for ICMP ping.
   -? t   Usage for TCP ping.
   -? l   Usage for latency test.
   -? b   Usage for bandwidth test.
  -nobanner   Do not display the startup banner and copyright message.
```

## ICMP PING
直接输入 `psping -? i` 查看相关说明！

```cmd
C:\Users\kelvin>psping -? i

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

ICMP ping usage: psping [-t|-n count[s]] [-i interval] [-w count] [-q] [-h [buckets|val1,val2,...]] [-l requestsize[k]] [-6|-4] destination
  -t    Ping until stopped with Ctrl+C and type Ctrl+Break
        for statistics.
  -n    Number of pings or append 's' to specify seconds e.g. '10s'.
  -i    Interval in seconds. Specify 0 for fast ping.
  -w    Warmup with the specified number of iterations (default is 1).
  -q    Don't output during pings.
  -h    Print histogram (default bucket count is 20).
        If you specify a single argument, it's interpreted as a bucket
        count and the histogram will contain that number of
        buckets covering the entire time range of values.
        Specify a comma-separated list of times to create a custom
        histogram (e.g. "0.01,0.05,1,5,10").
  -l    Request size. Append 'k' for kilobytes.
  -4    Force using IPv4.
  -6    Force using IPv6.

For high-speed ping tests use -q and -i 0.
```
**解释**：
-t   一直PING不停
-n   连续PING多少次
-i   间隔秒数，快ping则设置为0
-w   热身ping包的个数（热身PING的包不统计）
-q   PING的过程中不输出
-h   延迟统计区间值
-l   PING包大小
-4   强制走IPv4
-6   强制走IPv6

例子:
```cmd
C:\Users\kelvin>psping -4 -n 10 -w 2 -h 10 kelvin.mbioq.com

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

Pinging 47.56.65.222 with 32 bytes of data:
12 iterations (warmup 2) ping test:
Reply from 47.56.65.222: 41.93ms
Reply from 47.56.65.222: 41.93ms
Reply from 47.56.65.222: 41.60ms
Reply from 47.56.65.222: 41.96ms
Reply from 47.56.65.222: 42.01ms
Reply from 47.56.65.222: 41.82ms
Reply from 47.56.65.222: 41.42ms
Reply from 47.56.65.222: 41.48ms
Reply from 47.56.65.222: 41.72ms
Reply from 47.56.65.222: 42.12ms
Reply from 47.56.65.222: 42.03ms
Reply from 47.56.65.222: 41.61ms

Ping statistics for 47.56.65.222:
  Sent = 10, Received = 10, Lost = 0 (0% loss),
  Minimum = 41.42ms, Maximum = 42.12ms, Average = 41.78ms

Latency Count
41.42   2
41.50   0
41.57   2
41.65   1
41.73   0
41.81   1
41.88   1
41.96   2
42.04   0
42.12   1
```

## TCP PING
在命令中输入 IP：port 就会启用 TCP PING了！

直接输入 `psping -? t` 查看相关说明！

```cmd
C:\Users\kelvin>psping -? t

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP ping usage: psping [-t|-n count[s]] [-i interval] [-w count] [-q] [-h [buckets|val1,val2,...]] [-6|-4] destination:port
  -t    Ping until stopped with Ctrl+C and type Ctrl+Break
        for statistics.
  -n    Number of pings or append 's' to specify seconds e.g. '10s'.
  -i    Interval in seconds. Specify 0 for fast ping.
  -w    Warmup with the specified number of iterations (default is 1).
  -q    Don't output during pings.
  -h    Print histogram (default bucket count is 20).
        If you specify a single argument, it's interpreted as a bucket
        count and the histogram will contain that number of
        buckets covering the entire time range of values.
        Specify a comma-separated list of times to create a custom
        histogram (e.g. "0.01,0.05,1,5,10").
  -4    Force using IPv4.
  -6    Force using IPv6.

For high-speed ping tests use -q and -i 0.
```

例子:
```cmd
C:\Users\kelvin>psping -4 -n 10 -w 2 -h 10 kelvin.mbioq.com:443

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 47.56.65.222:443:
12 iterations (warmup 2) ping test:
Connecting to 47.56.65.222:443 (warmup): from 172.29.33.222:50671: 41.47ms
Connecting to 47.56.65.222:443 (warmup): from 172.29.33.222:50672: 41.13ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50673: 43.91ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50675: 43.50ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50676: 39.25ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50677: 41.05ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50678: 39.27ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50679: 40.40ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50680: 38.30ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50681: 43.74ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50682: 43.54ms
Connecting to 47.56.65.222:443: from 172.29.33.222:50683: 41.26ms

TCP connect statistics for 47.56.65.222:443:
  Sent = 10, Received = 10, Lost = 0 (0% loss),
  Minimum = 38.30ms, Maximum = 43.91ms, Average = 41.42ms

Latency Count
38.30   1
38.92   2
39.55   0
40.17   1
40.79   2
41.41   0
42.04   0
42.66   0
43.28   3
43.91   1
```

## 响应延迟
测试发送TCP/UDP请求响应延迟
直接输入 `psping -? l` 查看相关说明！
```cmd
C:\Users\kelvin>psping -? l

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP and UDP latency usage:
server: psping [-6|-4] [-f] -s address:port
client: psping -l requestsize[k|m] -n count[s] [-r] [-u] [-w count] [-f] [-h [buckets|val1,val2,...]] [-6|-4] destination:port
  -l    Request size. Append 'k' for kilobytes and 'm' for megabytes.
  -n    Number of sends/receives. Append 's' to specify seconds e.g. '10s'.
  -r    Receive from the server instead of sending.
  -u    UDP (default is TCP).
  -w    Warmup with the specified number of iterations (default is 5).
  -f    Open source firewall port during the run.
  -h    Print histogram (default bucket count is 20).
        If you specify a single argument, it's interpreted as a bucket
        count and the histogram will contain that number of
        buckets covering the entire time range of values.
        Specify a comma-separated list of times to create a custom
        histogram (e.g. "0.01,0.05,1,5,10").
  -4    Force using IPv4.
  -6    Force using IPv6.
  -s    Server listening address and port.

The server can serve both latency and bandwidth tests and remains active until
you terminate it with Control-C.
```

例子：
```cmd
C:\Users\kelvin>psping -l 1500 -n 300 -h 10 bitbear.net:443

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP latency test connecting to 119.28.177.46:443: Connected
305 iterations (warmup 5) sending 1500 bytes TCP latency test: 100%

TCP roundtrip latency statistics (post warmup):
  Sent = 300, Size = 1500, Total Bytes: 450000,
  Minimum = 41.58ms, Maxiumum = 50.88ms, Average = 42.97ms

Latency Count
41.58   106
42.62   147
43.65   36
44.68   7
45.72   0
46.75   0
47.78   1
48.82   1
49.85   1
50.88   1
```

## 带宽测试
测试本地到被测试服务器之间的带宽
直接输入 `psping -? b` 查看相关说明

```cmd
C:\Users\kelvin>psping -? b

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP and UDP bandwidth usage:
server: psping [-6|-4] [-f] -s address:port
client: psping -b -l requestsize[k|m] -n count[s] [-r] [-u [target]] [-i outstanding] [-w count] [-f] [-h [buckets|val1,val2,...]] [-6|-4] destination:port
  -b    Bandwidth test.
  -l    Request size. Append 'k' for kilobytes and 'm' for megabytes.
  -n    Number of sends/receives. Append 's' to specify seconds e.g. '10s'.
  -r    Receive from the server instead of sending.
  -u    UDP (default is TCP). Specify target bandwidth in MB/s.
  -i    Number of outstanding I/Os (default is min of 16 and 2x CPU cores).
  -w    Warmup for the specified iterations (default is 2x CPU cores).
  -f    Open source firewall port during the run.
  -h    Print histogram (default bucket count is 20).
        If you specify a single argument, it's interpreted as a bucket
        count and the histogram will contain that number of
        buckets covering the entire time range of values.
        Specify a comma-separated list of times to create a custom
        histogram (e.g. "0.01,0.05,1,5,10").
  -4    Force using IPv4.
  -6    Force using IPv6.
  -s    Server listening address and port.
```

例子：
```cmd
C:\Users\kelvin>psping -b -l 1500 -n 300 -h 10 bitbear.net:443

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

Setting warmup count to match number of outstanding I/Os: 16
TCP bandwidth test connecting to 119.28.177.46:443: Connected
316 iterations (16 warmup) sending 1500 bytes TCP bandwidth test: 575705100%

TCP sender bandwidth statistics:
  Sent = 300, Size = 1500, Total Bytes: 472500,
  Minimum = 496.93 KB/s, Maximum = 586.44 KB/s, Average = 564.13 KB/s

Bandwidth       Count
508851.28       1
519036.25       0
529221.19       0
539406.13       0
549591.13       0
559776.06       0
569961.00       1
580146.00       0
590330.94       0
600515.88       1
```

*参考文献*：
[https://docs.microsoft.com/zh-cn/sysinternals/downloads/psping](https://docs.microsoft.com/zh-cn/sysinternals/downloads/psping)


  [1]: https://imgs.gnux.cn/usr/uploads/2019/12/3334248832.png