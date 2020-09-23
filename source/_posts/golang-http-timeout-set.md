---
title: Golang http 超时设置方法  
date: 2012-12-08 14:06:00
updated: 2014-11-13 14:11:24
tags: 
- linux
- send-email
categories: 
- linux

---
golang提供的http get、post请求是没有设置超时的，但实现生产环境一般都需要设置超时时间。

网上搜索，可以查询到通过设置http.Client中Transport的Dail函数，在自定义的Dail函数里面设置建立连接超时时长和发送接收数据超时：  
  
<!--more-->

    c := http.Client{
            Transport: &http.Transport{
                Dial: func(netw, addr string) (net.Conn, error) {
                    c, err := net.DialTimeout(netw, addr, time.Second*3) //设置建立连接超时
                    if err != nil {
                        return nil, err
                    }
                    c.SetDeadline(time.Now().Add(5 * time.Second)) //设置发送接收数据超时
                    return c, nil
                },
            },
        }

因为上面的代码中调用SetDeadline设置了绝对过期时间，这会导致在该连接上发生的第二次及后来的请求的超时与预期的不一致。第一次请求的发送接收数据超时是5s，但第二次请求的发送接收数据超时是5s减去第一次请求发送接收数据耗时(忽略其他轻微耗时)，后面的请求以此类推。所以，这种方法只适用于所有的请求都有各自的http.Client，也就是说每次请求都需要创建各自的http.Client，建立各自的连接。

但golang官方文档里面有说“Clients should be reused instead of created as needed. Clients are safe for concurrent use by multiple goroutines.”（详见http包中Client的定义说明）。重复利用http.Client，可以考虑如下方法：

    var c *http.Client = &http.Client{
    
        Transport: &http.Transport{
            Dial: func(netw, addr string) (net.Conn, error) {
                c, err := net.DialTimeout(netw, addr, time.Second*3)
                if err != nil {
                    fmt.Println("dail timeout", err)
                    return nil, err
                }
                return c, nil
    
            },
            MaxIdleConnsPerHost:   10,
            ResponseHeaderTimeout: time.Second * 2,
        },
    }

这里添加了ResponseHeaderTimeout配置，默认是0，无限等待。该设置不包括发送数据超时和接收返回包正文体超时，只能大致的实现发送接收数据超时设置。

*附录：*

    c := http.Client{
                Transport: &http.Transport{
                    Dial: func(netw, addr string) (net.Conn, error) {
                        deadline := time.Now().Add(25 * time.Second)
                        c, err := net.DialTimeout(netw, addr, time.Second*20)
                        if err != nil {
                            return nil, err
                        }
                        c.SetDeadline(deadline)
                        return c, nil
                    },
                },
            }
         
        c.Get("http://www.qq.com")
    // 上载请求
    func NetUploadJson(addr string, buf interface{}) (*[]byte, *int, error) {
        // 将需要上传的JSON转为Byte
        v, _ := json.Marshal(buf)
        // 上传JSON数据
        req, e := http.NewRequest("POST", addr, bytes.NewReader(v))
        if e != nil {
            // 提交异常,返回错误
            return nil, nil, e
        }
        // Body Type
        req.Header.Set("Content-Type", "application/json")
        // 完成后断开连接
        req.Header.Set("Connection", "close")
        // -------------------------------------------
        // 设置 TimeOut
        DefaultClient := http.Client{
            Transport: &http.Transport{
                Dial: func(netw, addr string) (net.Conn, error) {
                    deadline := time.Now().Add(30 * time.Second)
                    c, err := net.DialTimeout(netw, addr, time.Second*30)
                    if err != nil {
                        return nil, err
                    }
                    c.SetDeadline(deadline)
                    return c, nil
                },
            },
        }
        // -------------------------------------------
        // 执行
        resp, ee := DefaultClient.Do(req)
        if ee != nil {
            // 提交异常,返回错误
            return nil, nil, ee
        }
        // 保证I/O正常关闭
        defer resp.Body.Close()
        // 判断返回状态
        if resp.StatusCode == http.StatusOK {
            // 读取返回的数据
            data, err := ioutil.ReadAll(resp.Body)
            if err != nil {
                // 读取异常,返回错误
                return nil, nil, err
            }
            // 将收到的数据与状态返回
            return &data, &resp.StatusCode, nil
        } else if resp.StatusCode != http.StatusOK {
            // 返回异常状态
            return nil, &resp.StatusCode, nil
        }
        // 不会到这里
        return nil, nil, nil
    }
     
    // 下载文件
    func NetDownloadFile(addr string) (*[]byte, *int, *http.Header, error) {
        // 上传JSON数据
        req, e := http.NewRequest("GET", addr, nil)
        if e != nil {
            // 返回异常
            return nil, nil, nil, e
        }
        // 完成后断开连接
        req.Header.Set("Connection", "close")
        // -------------------------------------------
        // 设置 TimeOut
        DefaultClient := http.Client{
            Transport: &http.Transport{
                Dial: func(netw, addr string) (net.Conn, error) {
                    deadline := time.Now().Add(30 * time.Second)
                    c, err := net.DialTimeout(netw, addr, time.Second*30)
                    if err != nil {
                        return nil, err
                    }
                    c.SetDeadline(deadline)
                    return c, nil
                },
            },
        }
        // -------------------------------------------
        // 执行
        resp, ee := DefaultClient.Do(req)
        if ee != nil {
            // 返回异常
            return nil, nil, nil, ee
        }
        // 保证I/O正常关闭
        defer resp.Body.Close()
        // 判断请求状态
        if resp.StatusCode == 200 {
            data, err := ioutil.ReadAll(resp.Body)
            if err != nil {
                // 读取错误,返回异常
                return nil, nil, nil, err
            }
            // 成功，返回数据及状态
            return &data, &resp.StatusCode, &resp.Header, nil
        } else {
            // 失败，返回状态
            return nil, &resp.StatusCode, nil, nil
        }
        // 不会到这里
        return nil, nil, nil, nil
    }