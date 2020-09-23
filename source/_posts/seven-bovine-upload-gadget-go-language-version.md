---
title: 七牛上传小工具-Go语言版本
date: 2015-02-04 15:52:00
updated: 2015-02-04 15:55:03
tags: 
- UserMedia
- AudioAPI
categories: 
- java

---
运行环境： Mac os X and Linux 

现在当前目录下创建qiniu.json文件：

    {
        "AK":"XXX" ,
        "SK":"XXX", "BN":"xx"
    }


<!--more-->


    package main
     
    import (
        "encoding/json"
        "fmt"
        "io/ioutil"
        syslog "log"
        "os"
        "path"
        "strings"
    )
    import (
        . "github.com/qiniu/api/conf"
        "github.com/qiniu/api/io"
        "github.com/qiniu/api/rs"
        "github.com/qiniu/log"
    )
     


    import (
        "etag"
    )
     
    var uptoken string
    var buckName string
    var currentDir string
     
    func init() {
        currentDir, _ = os.Getwd()
     
        file, err := os.Open(currentDir + "/" + "qiniu.json") // For read access.
        if err != nil {
            log.Fatal(err)
        }
        configFromFile, _ := ioutil.ReadAll(file)
        var conf config
        err = json.Unmarshal([]byte(configFromFile), &conf)
        if err != nil {
            fmt.Println("error:", err)
        }
     
        ACCESS_KEY = conf.AK
        SECRET_KEY = conf.SK
        buckName = conf.BN
        uptoken = getUpToken(buckName)
     
    }
     
    type config struct {
        AK string
        SK string
        BN string
    }
     
    func main() {
     
        if len(os.Args) < 2 {
            //fmt.Println("please input the file's path")
            file, err := os.OpenFile(currentDir+"/"+"token.txt", os.O_WRONLY|os.O_CREATE, 0777) // For read access.
            _, err = file.WriteString(uptoken)
            if err != nil {
                syslog.Fatal(err)
            }
            fmt.Println("token is saved at ", currentDir+"/"+"token.txt")
            file.Close()
            return
     
        }
        filePath := os.Args[1]
        if !strings.ContainsRune(filePath, '/') {
            filePath = currentDir + "/" + filePath
        }
        etag, err := etag.GetEtag(filePath)
        if err != nil {
            fmt.Fprintln(os.Stderr, err)
            return
        }
        fileSuffix := getSuffix(filePath)
        //  fmt.Println("etag:" + etag)
     
        key := etag + fileSuffix
        doUpload(filePath, key)
    }
     
    //genereate  uptoken  for uploading to qiniu
    func getUpToken(bucketName string) string {
        putPolicy := rs.PutPolicy{
            Scope: bucketName,
            //CallbackUrl: callbackUrl,
            //CallbackBody:callbackBody,
            //ReturnUrl:   returnUrl,
            //ReturnBody:  returnBody,
            //AsyncOps:    asyncOps,
            //EndUser:     endUser,
            Expires: 86400, //one day
        }
        return putPolicy.Token(nil)
    }
     
    func doUpload(localFile string, key string) {
     
        var err error
        var ret io.PutRet
        var extra = &io.PutExtra{
            //Params:    params,
            //MimeType:  mieType,
            //Crc32:     crc32,
            CheckCrc: 0,
        }
     
        // ret       变量用于存取返回的信息，详情见 io.PutRet
        // uptoken   为业务服务器生成的上传口令
        // key       为文件存储的标识
        // localFile 为本地文件名
        // extra     为上传文件的额外信息，详情见 io.PutExtra，可选
     
        err = io.PutFile(nil, &ret, uptoken, key, localFile, extra)
     
        if err != nil {
            //上传产生错误
            log.Print("io.PutFile failed:", err)
            return
        }
     
        //上传成功，处理返回值
        //fmt.Println("hash:", ret.Hash, "key:", ret.Key)
        fileUrl := "http://" + buckName + ".qiniudn.com/" + ret.Key
        fmt.Println("token: ", uptoken)
        fmt.Println()
        fmt.Println("file's url: ", fileUrl)
     
    }
     
    func getSuffix(filePath string) string {
        fileNameWithSuffix := path.Base(filePath)
        fileSuffix := path.Ext(fileNameWithSuffix)
        return fileSuffix
     
    }

----

    package etag
     
    import (
        "bytes"
        "crypto/sha1"
        "encoding/base64"
        //"fmt"
        "io"
        "os"
    )
     
    const (
        BLOCK_BITS = 22 // Indicate that the blocksize is 4M
        BLOCK_SIZE = 1 << BLOCK_BITS
    )
     
    func BlockCount(fsize int64) int {
     
        return int((fsize + (BLOCK_SIZE - 1)) >> BLOCK_BITS)
    }
     
    func CalSha1(b []byte, r io.Reader) ([]byte, error) {
     
        h := sha1.New()
        _, err := io.Copy(h, r)
        if err != nil {
            return nil, err
        }
        return h.Sum(b), nil
    }
     
    func GetEtag(filename string) (etag string, err error) {
     
        f, err := os.Open(filename)
        if err != nil {
            return
        }
        defer f.Close()
     
        fi, err := f.Stat()
        if err != nil {
            return
        }
     
        fsize := fi.Size()
        blockCnt := BlockCount(fsize)
        sha1Buf := make([]byte, 0, 21)
     
        if blockCnt <= 1 { // file size <= 4M
            sha1Buf = append(sha1Buf, 0x16)
            sha1Buf, err = CalSha1(sha1Buf, f)
            if err != nil {
                return
            }
        } else { // file size > 4M
            sha1Buf = append(sha1Buf, 0x96)
            sha1BlockBuf := make([]byte, 0, blockCnt*20)
            for i := 0; i < blockCnt; i++ {
                body := io.LimitReader(f, BLOCK_SIZE)
                sha1BlockBuf, err = CalSha1(sha1BlockBuf, body)
                if err != nil {
                    return
                }
            }
            sha1Buf, _ = CalSha1(sha1Buf, bytes.NewReader(sha1BlockBuf))
        }
        etag = base64.URLEncoding.EncodeToString(sha1Buf)
        return
    }
     
    // func main() {
    //
    //     if len(os.Args) < 2 {
    //         fmt.Fprintln(os.Stderr, `Usage: qetag <filename>`)
    //         return
    //     }
    //     etag, err := GetEtag(os.Args[1])
    //     if err != nil {
    //         fmt.Fprintln(os.Stderr, err)
    //         return
    //     }
    //     fmt.Println(etag)
    // }