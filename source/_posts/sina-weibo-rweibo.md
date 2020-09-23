---
title: R模拟登陆新浪微博[新浪微博与Rweibo]
date: 2014-11-13 21:27:00
updated: 2014-12-31 12:22:57
tags: 
- brackets
categories: 
- css

---
近期研究R爬取新浪围脖。
发现现有对Rweibo的微博搜索已经无法使用了。
写下此文记录解析过程。希望对继续研究者有帮助
新浪一直在更新，R微博已经很久没变了。。。。。
目前新浪网有如下改变：


<!--more-->


1、若非微博登入用户，无法在使用高级搜索，且只能访问搜索第一页
解决方案：cookie模拟登入

2、一定周期的，会在url间加入跳转url，使用固定的url会有一定频率爬去失败。
解决方案：若获取为跳转信息，解析跳转链接地址。

3、部分页面使用fromJSON无法解析（提示有异常字符，俺查了半天json没找出错在哪）
解决方案：改用gsub代替fromJSON

以下方法参考了RCurl不务正业，以及R微博的官方教程，对Rweibo建议应有以下改进。
1、使用cookie模拟登入：
将头信息换成如下形式：

    myHttpheader<- c(
      "Connection"="keep-alive",
      "Host"="s.weibo.com",
      "User-Agent"="Mozilla/5.0 (Windows NT 6.2; WOW64; rv:32.0) Gecko/20100101 Firefox/32.0",
      "Accept"="text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
      "Accept-Language"="zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3",
      #"Accept-Encoding"="gzip,deflate",#（建议根据需要添加，若是不懂尽量别加这条，若是加错甚至会导致R崩溃）
      #登入的相关cookie信息都贴过来，以;分割。
      "Cookie"="SUB=0033WrSXqPxfM725Ws9jq(略);UOR=ww(略);_ndefined"
      )
    #设置背包
    d = debugGatherer()
    cHandle2 <- getCurlHandle(httpheader=myHttpheader,followlocation=1,
                              debugfunction=d$update,verbose=TRUE)
    #若无意外此时手工拼：
    temp<-getURL("http://s.weibo.com/weibo/%E5%9B%BD%E5%AE%B6%E5%BC%80%E5%8F%91%E9%93%B6%E8%A1%8C&xsort=time&Refer=g&nodup=1&page=2",curl=cHandle2,encoding='UTF-8')
    #会返回对应搜索页html。

2、也可能返回，跳转链接
如下：  

     “\n\t\t<html>\n\t\t<head>\n\t\t<title>\xd0\xc2\xc0\xcbͨ\xd0\xd0֤</title>\n\t\t<meta http-equiv=\”refresh\” content=\”0; url='<a href="http://s.weibo.com/weibo/%E5%9B%BD%E5%AE%B6%E5%BC%80%E5%8F%91%E9%93%B6%E8%A1%8C&xsort=time&Refer=g&nodup=1&page=1?retcode=6102&#39" rel="nofollow">http://s.weibo.com/weibo/%E5%9B%BD%E5%AE%B6%E5%BC%80%E5%8F%91%E9%93%B6%E8%A1%8C&xsort=time&Refer=g&nodup=1&page=1?retcode=6102&#39</a>;\”/>\n\t\t<meta http-equiv=\”Content-Type\” content=\”text/html; charset=GBK\” />\n\t\t</head>\n\t\t<body bgcolor=\”#ffffff\” text=\”#000000\” link=\”#0000cc\” vlink=\”#551a8b\” alink=\”#ff0000\”>\n\t\t<script type=\”text/javascript\” language=\”javascript\”>\n\t\tlocation.replace(\”http://s.weibo.com/weibo/%E5%9B%BD%E5%AE%B6%E5%BC%80%E5%8F%91%E9%93%B6%E8%A1%8C&xsort=time&Refer=g&nodup=1&page=1?retcode=6102\”);\n\t\t</script>\n\t\t</body>\n\t\t</html>”

其中location.replace()内的信息就是新地址，利用其即可访问搜索页。

##  如果抓到的是跳转页则读取跳转地址  ##

     if(grep("location.replace\\(\"",temp)==1){
        strurl<-gsub(".*location.replace\\(\"(.*)\"\\);.*","\\1",temp)
        temp <- getURL(strurl, curl = curl, .encoding = 'UTF-8')
      }

3、在Rweibo函数中有如下一句解析json的代码

    weibolist <- .fromJSON(weibojson)
    weibopage <- htmlParse(weibojson2, asText=TRUE, encoding = "UTF-8")

经测试这段代码解析新浪围脖html大概有5%左右的概率会解析失败，利用gsub修改如下：

    weibojson1<-gsub(".*\"html\":\"(.*)\"}","\\1",weibojson)
      weibojson2<-gsub("\\\\","",weibojson1)
      #weibolist <- .fromJSON(weibojson)
      weibopage <- htmlParse(weibojson2, asText=TRUE, encoding = "UTF-8")

最后给出略微修改的Rweibo搜索解析函数web.search.combinewith(稍有改动)：
使用样例：

    res12 <- wb("统计之都", page = 20,curl=cHandle2, sleepmean = 30)

需要准备
使用前需加载包：

    library(Rcpp)
    library(bitops)
    library(RCurl)
    library(rjson)
    library(tools)
    library(digest)
    library(XML)
    library(Rweibo)

需单读加载Rweibo包中的utils.R内的函数

    wb<-function(sword="国际开发银行",page=1,curl = NULL,sleepmean=20,combinewith=NULL,since=NULL){
      #判断页数输入无问题
      if (length(page) == 1) page = 1:page
      page <- page[page > 0 & page <= 50]
      page <- sort(page)
      if (length(page) > 50) page <- page[1:50]
      #如果判断扩展前格式,排重后读取
      if (!is.null(combinewith)) {
        if (all(c("MID", "UID" ,"Author", "Weibo", "Forward", "Time_Weibo", "Time_Search", "Count_Forward", "Count_Reply") %in% names(combinewith))) {
          OUT <- combinewith[, c("MID", "UID" ,"Author", "Weibo", "Forward", "Time_Weibo", "Time_Search", "Count_Forward", "Count_Reply")]
          maxid <- max(as.numeric(OUT$MID))
        } else {
          OUT <- data.frame(stringsAsFactors = FALSE)
          maxid <- 0
          warning("Ignored 'combinewith' because of wrong format!")
        }
      } else {
        OUT <- data.frame(stringsAsFactors = FALSE)
        maxid <- 0
      }
      #判断启适日期格式是否正确
      if (is.null(since)) {
        maxdate <- -Inf
      } else {
        if (inherits(since, "character")) {
          since <- strptime(since, format = "%Y-%m-%d")
          if (is.na(since)) {
            warning("Ignore 'since' because of the wrong format!")
            maxdate <- -Inf
          }
        }
        if (inherits(since, "POSIXlt")) maxdate <- since
      }
      #开始循环读取指定页
      for(ipage in page){
        #同一页面连续获取5次若是失败5次破坏
        print(paste("当前页面是：",ipage))
        for(i in 1:5){
          #系统待机时间
          Sys.sleep(abs(rnorm(1, mean = sleepmean, sd = 2)))
          tmp.search <- try(lgsearch.content(sword,page=ipage,curl=curl), silent = TRUE)
          if (is.null(tmp.search)) {
            #睡眠时间随失败次数翻倍
            Sys.sleep(abs(rnorm(1, mean = sleepmean*(1.5^i), sd = 2)))
            cat(paste(ipage - 1, " pages was stored!\n", sep = ""))
            Search <- FALSE
          } else if (inherits(tmp.search, "try-error")){
            warning(paste("Error in page ", ipage, sep = ""))
          } else {
            if (min(as.numeric(tmp.search$MID)) <= maxid || min(tmp.search$Time_Weibo) < maxdate) {
              Search <- FALSE
              tmp.search <- tmp.search[as.numeric(tmp.search$MID) > maxid & tmp.search$Time_Weibo >= maxdate, ]
            }
            OUT <- rbind(tmp.search, OUT)
            break;
          }
          print(paste("当前错误次数是：",i,"（到达5次就崩溃了）"))
        }
      }
      return(OUT)
    }
    lgsearch.content<-function(sword="国家开发银行",page=1,curl = NULL){
      #拼接URL地址
      requestURL <- "http://s.weibo.com/weibo/"
      sword <- curlEscape(.cntoUTF8(sword))
      strurl <- paste(requestURL, sword, "&xsort=time&Refer=g&nodup=1&page=", page,sep = "")# time sorting
      temp <- getURL(strurl, curl = curl, .encoding = 'UTF-8')
      #如果抓到的是跳转页则读取跳转地址
      if(grep("location.replace\\(\"",temp)==1){
        strurl<-gsub(".*location.replace\\(\"(.*)\"\\);.*","\\1",temp)
        temp <- getURL(strurl, curl = curl, .encoding = 'UTF-8')
      }
      resHTMLs <- .strextract(temp, "<script>.+?</script>")[[1]]
      resHTML <- resHTMLs[grep("\"pid\":\"pl_weibo_direct\"", resHTMLs)][1]
      if (is.na(resHTML)) {
        warning("Can not crawl any page now. May be forbidden by Sina temporarily.", call. = FALSE)
        return(NULL)
      }
      weibojson <- gsub("\\)</script>$", "", gsub("^.*STK.pageletM.view\\(", "", resHTML))
      #使用正则代替解析
      weibojson1<-gsub(".*\"html\":\"(.*)\"}","\\1",weibojson)
      weibojson2<-gsub("\\\\","",weibojson1)
      #weibolist <- .fromJSON(weibojson)
      weibopage <- htmlParse(weibojson2, asText=TRUE, encoding = "UTF-8")
      weiboitem.attr <- getNodeSet(weibopage, "//dl[@action-type='feed_list_item']")
      weiboitem.con <- getNodeSet(weibopage, "//dd[@class='content']")
      weiboitem.nores <- getNodeSet(weibopage, "//div[@class='pl_noresult']")
      if (length(weiboitem.nores) == 0) {
        res.mid <- sapply(weiboitem.attr, function(X) xmlGetAttr(X, "mid"))
        res.con <- sapply(weiboitem.con, FUN = function(X) xmlValue(getNodeSet(X, "p[@node-type='feed_list_content']")[[1]]))
        res.uid <- sapply(weiboitem.con, FUN = function(X) xmlGetAttr(getNodeSet(X, "p[@node-type='feed_list_content']/a")[[1]], "usercard"))
        res.uid <- gsub("id=(.*)&.*","\\1",res.uid)
        res.name <- sapply(weiboitem.con, FUN = function(X) xmlGetAttr(getNodeSet(X, "p[@node-type='feed_list_content']/a")[[1]], "nick-name"))
        res.date <- sapply(weiboitem.con, FUN = function(X) xmlGetAttr(getNodeSet(X, "p/a[@node-type='feed_list_item_date']")[[1]], "title"))
        res.stat <- lapply(weiboitem.con, FUN = function(X) sapply(getNodeSet(X, "p/span/a"), xmlValue))
        res.forward <- sapply(weiboitem.con, FUN = function(X) {
          tmp.node <- getNodeSet(X, "dl/dt[@node-type='feed_list_forwardContent']")
          if (length(tmp.node) == 0) {
            NA
          } else {
            xmlValue(tmp.node[[1]])
          }
        }
        )
        Encoding(res.name) <- "UTF-8"
        res.con <- .strtrim(res.con)
        res.forward <- .strtrim(res.forward)
        res.date <- strptime(res.date, format = "%Y-%m-%d %H:%M")
        res.stat.f <- as.numeric(gsub("[^0-9]", "", sapply(res.stat, FUN = function(X) X[grep("\u8F6C\u53D1", X)])))
        res.stat.r <- as.numeric(gsub("[^0-9]", "", sapply(res.stat, FUN = function(X) X[grep("\u8BC4\u8BBA", X)])))
        res.stat.f[is.na(res.stat.f)] <- 0
        res.stat.r[is.na(res.stat.r)] <- 0
        OUT <- data.frame(MID = res.mid, UID=res.uid,Author = res.name, Weibo = res.con, Forward = res.forward, Time_Weibo = res.date,
                          Time_Search = Sys.time(), Count_Forward = res.stat.f, Count_Reply = res.stat.r, stringsAsFactors = FALSE)
        OUT$Weibo <- sapply(seq_along(OUT$Weibo), FUN = function(X)
          gsub(paste("^ *", OUT$Author[X], "\uFF1A *", sep = ""), "", OUT$Weibo[X]))
      } else {
        OUT <- NULL
      }
      return(OUT)
    }
**参考链接：**
[http://cos.name/cn/topic/158520/](http://cos.name/cn/topic/158520/)
[http://cos.name/cn/topic/407938/](http://cos.name/cn/topic/407938/)