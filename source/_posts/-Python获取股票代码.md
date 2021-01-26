---
title: " Python获取股票代码"
categories: [ "Python" ]
tags: [ "股票代码" ]
draft: false
slug: "python-access-to-stock-code"
date: "2015-01-21 17:10:00"
---

## 目标 ##
获取目前国内的主要股票代码，包括A股、B股、创业板等股票名称和代码

## 方法 ##
1. 网上寻找股票、基金等分类，收集分类信息

2. 网上寻找包含所有股票代码的网页

3. 爬取网页信息、提取股票名称和代码

4. 将信息存储到Mysql中


<!--more-->


## 编码实现 ##

    import mysql.connector
    from mysql.connector import errorcode
    import urllib.parse
    import urllib.request
    from bs4 import BeautifulSoup
    #coding:gb2312
    stockTypes = {
               '001': '国债现货',
               '110': '企业债券',  
               '120': '企业债券',
               '129': '可转换债券',
               '100': '可转换债券',
               '201': '国债回购',
               '310': '基金',
               '500': '基金',  
               '550': '企业债券',
               '710': '转配股',
               '701': '转配股再配股',
               '711': '转配股再配股',
               '720': '红利',  
               '735': '新基金申购',  
               '737': '新股配售',
               '300': '创业板',  
               '600': '沪市A股',  
               '601': '沪市A股',
               '603': '沪市A股',
               '900': '沪市B股',
               '000': '深市A股',
               '002': '中小板',
               '200': '深市B股',
               '730': '新股申购',
               '700': '沪市配股',
               '080': '深市配股',
               '580': '沪市权证',
               '031': '深市权证',
               '18':'深交所基金',
               '50':'上交所基金',
               '16':'深交所基金',
               '51':'上交所基金',
               '15':'深交所基金',
     }
    #获取网页请求信息
    url = 'http://quote.eastmoney.com/stocklist.html'
    response = urllib.request.urlopen(url)
    responseContent = response.read()
    soup = BeautifulSoup(responseContent,from_encoding="gb18030")
    index = [5,9]
    j = 4
    #数据库配置
    config = {
      'user': 'root',
      'password': 'admin',
      'host': '127.0.0.1',
      'database': 'StocksDB',
      'raise_on_warnings': True,
    }
    cnx = mysql.connector.connect(**config)
    cur = cnx.cursor()
    add_stock = ("INSERT INTO stocknametable (StockName, StockCode, StockType) VALUES (%s, %s, %s)")
    
    #根据网页布局提取信息并入库
    for j in index:
        content = soup.find(id="quotesearch").contents[j]
        for string in content.strings:
            if len(string) > 10:
                str1 = string.split('(')
                stockType = '其他类型'
                stockName = str1[0]
                stockCode = str1[1][0:6]
                startCode = stockCode[0:3]
                if startCode in stockTypes:
                    stockType = stockTypes.get(startCode)
                else:
                    startCode1 = stockCode[0:2]
                    if startCode1 in stockTypes:
                        stockType = stockTypes.get(startCode1)
                print(stockName+':'+stockCode+':'+stockType)
                data_stock = (stockName,stockCode,stockType)
                cur.execute(add_stock,data_stock)
    cnx.commit()
    cur.close()
    cnx.close()