---
title: "Golang xml字符串转struct-xml-json"
categories: [ "Golang" ]
tags: [ "golang","xml" ]
draft: false
slug: "golang-xmxl-string-to-structxmxljson"
date: "2017-07-16 08:50:00"
---

Golang xml字符串转struct/xml/json

<!--more-->

```go
package main

import (
    "encoding/json"
    "encoding/xml"
    "fmt"
    "os"
)

var xmlDataRq = `<?xml version="1.0" encoding="UTF-8"?>
<request>
<head>
     <h_exch_code>880061</h_exch_code>
     <h_bank_no>0000</h_bank_no>
     <h_branch_id>B00000</h_branch_id>
     <h_fact_date>20110224</h_fact_date>
     <h_fact_time>14:49:20</h_fact_time>
     <h_exch_date>20110224</h_exch_date>
     <h_serial_no>12345678</h_serial_no>
     <h_rsp_code>1</h_rsp_code>
     <h_rsp_msg>ok</h_rsp_msg>
</head>
<body>
     <record>
            <user_id>1204300001</user_id>
            <user_pwd>e10adc3949ba59abbe56e057f20f883e</user_pwd>
            <login_ip>127.0.0.1</login_ip>
     </record>
     <record>
            <user_id>1204300002</user_id>
            <user_pwd>e10adc3949ba59abbe56e057f20f883e2</user_pwd>
            <login_ip>127.0.0.2</login_ip>
     </record>
</body>
</request>

`

type head struct {
    H_exch_code string `xml:"h_exch_code" json:"h_exch_code"`
    H_bank_no   string `xml:"h_bank_no" json:"h_bank_no"`
    H_branch_id string `xml:"h_branch_id" json:"h_branch_id"`
    H_fact_date string `xml:"h_fact_date" json:"h_fact_date"`
    H_fact_time string `xml:"h_fact_time" json:"h_fact_time"`
    H_exch_date string `xml:"h_exch_date" json:"h_exch_date"`
    H_serial_no string `xml:"h_serial_no" json:"h_serial_no"`
    H_rsp_code  string `xml:"h_rsp_code" json:"h_rsp_code"`
    H_rsp_msg   string `xml:"h_rsp_msg" json:"h_rsp_msg"`
}

type rqRecord struct {
    User_id  string `xml:"user_id" json:"user_id"`
    User_pwd string `xml:"user_pwd" json:"user_pwd"`
    Login_ip string `xml:"login_ip" json:"login_ip"`
}

type rqBody struct {
    Record []rqRecord `xml:"record" json:"record"`
}

type rspRecord struct {
    Server_code         string `xml:"server_code" json:"server_code"`
    Server_name         string `xml:"server_name" json:"server_name"`
    Trans_ip            string `xml:"trans_ip" json:"trans_ip"`
    Trans_port          string `xml:"trans_port" json:"trans_port"`
    Query_ip            string `xml:"query_ip" json:"query_ip"`
    Query_port          string `xml:"query_port" json:"query_port"`
    Broadcast_ip        string `xml:"broadcast_ip" json:"broadcast_ip"`
    Broadcast_port      string `xml:"broadcast_port" json:"broadcast_port"`
    Risk_trans_ip       string `xml:"risk_trans_ip" json:"risk_trans_ip"`
    Risk_trans_port     string `xml:"risk_trans_port" json:"risk_trans_port"`
    Risk_broadcast_ip   string `xml:"risk_broadcast_ip" json:"risk_broadcast_ip"`
    Risk_broadcast_port string `xml:"risk_broadcast_port" json:"risk_broadcast_port"`
}

type rspBody struct {
    Record []rspRecord `xml:"record" json:"record"`
}

type Request struct {
    XMLName xml.Name `xml:"request" json:"request"`
    Head    head     `xml:"head" json:"head"`
    Body    rqBody   `xml:"body" json:"body"`
}

type Response struct {
    XMLName xml.Name `xml:"response" json:"response"`
    Head    head     `xml:"head" json:"head"`
    Body    rspBody  `xml:"body" json:"body"`
}

var xmlDataRsp = `<?xml version="1.0" encoding="utf-8"?>
<response>
<head>
     <h_exch_code>880061</h_exch_code>
     <h_bank_no>0000</h_bank_no>
     <h_branch_id>B00000</h_branch_id>
     <h_fact_date>20110224</h_fact_date>
     <h_fact_time>14:49:20</h_fact_time>
     <h_exch_date>20110224</h_exch_date>
     <h_serial_no>12345678</h_serial_no>
     <h_rsp_code>HJ0000</h_rsp_code>
     <h_rsp_msg>处理成功</h_rsp_msg>
</head>
<body>
     <record>
            <server_code>M01</server_code>
            <server_name>内网 </server_name>
            <trans_ip>168.33.114.248</trans_ip>
            <trans_port>15000</trans_port>
            <query_ip>168.33.114.248</query_ip>
            <query_port>16000</query_port>
            <broadcast_ip>168.33.114.248</broadcast_ip>
            <broadcast_port>17000</broadcast_port>
            <risk_trans_ip>168.33.114.248</risk_trans_ip>
            <risk_trans_port>20000</risk_trans_port>
            <risk_broadcast_ip>168.33.114.248</risk_broadcast_ip>
            <risk_broadcast_port>21000</risk_broadcast_port>
     </record>
     <record>
            <server_code>M02</server_code>
            <server_name>外网</server_name>
            <trans_ip>210.21.197.124</trans_ip>
            <trans_port>15000</trans_port>
            <query_ip>210.21.197.124</query_ip>
            <query_port>16000</query_port>
            <broadcast_ip>210.21.197.124</broadcast_ip>
            <broadcast_port>17000</broadcast_port>
            <risk_trans_ip>210.21.197.124</risk_trans_ip>
            <risk_trans_port>20000</risk_trans_port>
            <risk_broadcast_ip>210.21.197.124</risk_broadcast_ip>
            <risk_broadcast_port>21000</risk_broadcast_port>
     </record>
</body>
</response>`

func main() {
    v := Request{}
    xml.Unmarshal([]byte(xmlDataRq), &v)

    // sanity check - XML level
    fmt.Printf("XMLName: %#v\n", v.XMLName)
    fmt.Printf("XMLName: %#v\n", v.Head)
    fmt.Printf("Body: %#v\n", v.Body)

    fmt.Println("---json")
    b, _ := json.Marshal(v)
    os.Stdout.Write(b)

    fmt.Println("------")

    v2 := Response{}
    xml.Unmarshal([]byte(xmlDataRsp), &v2)

    // sanity check - XML level
    fmt.Printf("XMLName: %#v\n", v2.XMLName)
    fmt.Printf("XMLName: %#v\n", v2.Head)
    fmt.Printf("Body: %#v\n", v2.Body)

    fmt.Println("---json")
    b2, _ := json.Marshal(v2)
    os.Stdout.Write(b2)
}
```