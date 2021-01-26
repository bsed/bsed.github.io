---
title: "在Go中使用自己签署的证书 [翻译]"
categories: [ "Golang" ]
tags: [ "golang","证书" ]
draft: false
slug: "selfissued-certificates-use-signed-certificates-in-go"
date: "2019-07-09 17:02:00"
---

如何创建[CA证书](https://en.wikipedia.org/wiki/Certificate_authority)，然后在[Golang](https://golang.org/)中使用该CA签名证书。我们将使用[httptest](https://godoc.org/net/http/httptest#Server)服务端来部署我们的证书，并使用[net/http](https://godoc.org/net/http#Client)客户端与服务器进行通信。

## 要求
[Golang v1.11.4 +](https://golang.org/dl/)
使用的包
在本演示中，我们将使用Golang 标准库中提供的以下[Go包](https://golang.org/pkg/#stdlib)：


<!--more-->


 - [bytes](https://godoc.org/bytes)
 - [crypto/rand](https://godoc.org/crypto/rand)
 - [crypto/rsa](https://godoc.org/crypto/rsa)
 - [crypto/tls](https://godoc.org/crypto/tls)
 - [crypto/x509](https://godoc.org/crypto/x509)
 - [crypto/x509/pkix](https://godoc.org/crypto/x509/pkix)
 - [encoding/pem](https://godoc.org/encoding/pem)
 - [fmt](https://godoc.org/fmt)
 - [io/ioutil](https://godoc.org/io/ioutil)
 - [math/big](https://godoc.org/math/big)
 - [net](https://godoc.org/net)
 - [net/http](https://godoc.org/net/http)
 - [net/http/httptest](https://godoc.org/net/http/httptest)
 - [strings](https://godoc.org/strings)
 - [time](https://godoc.org/time)
## 创建证书颁发机构
首先，我们将从创建CA证书开始。这是我们用来签署我们创建证书的方法：
```golang
ca := &x509.Certificate{
	SerialNumber: big.NewInt(2019),
	Subject: pkix.Name{
		Organization:  []string{"Company, INC."},
		Country:       []string{"US"},
		Province:      []string{""},
		Locality:      []string{"San Francisco"},
		StreetAddress: []string{"Golden Gate Bridge"},
		PostalCode:    []string{"94016"},
	},
	NotBefore:             time.Now(),
	NotAfter:              time.Now().AddDate(10, 0, 0),
	IsCA:                  true,
	ExtKeyUsage:           []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth, x509.ExtKeyUsageServerAuth},
	KeyUsage:              x509.KeyUsageDigitalSignature | x509.KeyUsageCertSign,
	BasicConstraintsValid: true,
}
```
`IsCA`设置为的字段true将指示这是我们的CA证书。从这里，我们需要为证书生成公钥和私钥：
```golang
caPrivKey, err := rsa.GenerateKey(rand.Reader, 4096)
if err != nil {
	return err
}
```
然后我们将生成证书：
```golang
caBytes, err := x509.CreateCertificate(rand.Reader, ca, ca, &caPrivKey.PublicKey, caPrivKey)
if err != nil {
	return err
}
```
现在**caBytes**我们已经生成了我们生成的证书，我们可以将其编码以供以后使用：

```golang
caPEM := new(bytes.Buffer)
pem.Encode(caPEM, &pem.Block{
	Type:  "CERTIFICATE",
	Bytes: caBytes,
})

caPrivKeyPEM := new(bytes.Buffer)
pem.Encode(caPrivKeyPEM, &pem.Block{
	Type:  "RSA PRIVATE KEY",
	Bytes: x509.MarshalPKCS1PrivateKey(caPrivKey),
})
```

现在我们已经创建了CA，我们已准备好签署证书。

## 创建证书
创建我们将用于HTTP服务器的证书与我们生成CA的方式类似，但对*x509.Certificate*字段进行了一些更改：
```golang
cert := &x509.Certificate{
	SerialNumber: big.NewInt(1658),
	Subject: pkix.Name{
		Organization:  []string{"Company, INC."},
		Country:       []string{"US"},
		Province:      []string{""},
		Locality:      []string{"San Francisco"},
		StreetAddress: []string{"Golden Gate Bridge"},
		PostalCode:    []string{"94016"},
	},
	IPAddresses:  []net.IP{net.IPv4(127, 0, 0, 1), net.IPv6loopback},
	NotBefore:    time.Now(),
	NotAfter:     time.Now().AddDate(10, 0, 0),
	SubjectKeyId: []byte{1, 2, 3, 4, 6},
	ExtKeyUsage:  []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth, x509.ExtKeyUsageServerAuth},
	KeyUsage:     x509.KeyUsageDigitalSignature,
}
```
请注意，在此证书中我们已经特别添加`IPAddresses: []net.IP{net.IPv4(127, 0, 0, 1), net.IPv6loopback}`,，因为我们希望此证书在以下位置有效localhost。

为证书创建私钥和公钥：
```golang
certPrivKey, err := rsa.GenerateKey(rand.Reader, 4096)
if err != nil {
	return err
}
```
## 使用CA签署证书
现在我们将创建证书并使用我们的CA进行签名：
```golang
certBytes, err := x509.CreateCertificate(rand.Reader, cert, ca, &certPrivKey.PublicKey, caPrivKey)
if err != nil {
	return err
}
```
PEM对证书和私钥进行编码：
```golang
certPEM := new(bytes.Buffer)
pem.Encode(certPEM, &pem.Block{
	Type:  "CERTIFICATE",
	Bytes: certBytes,
})

certPrivKeyPEM := new(bytes.Buffer)
pem.Encode(certPrivKeyPEM, &pem.Block{
	Type:  "RSA PRIVATE KEY",
	Bytes: x509.MarshalPKCS1PrivateKey(certPrivKey),
})
```
现在让我们创建`tls.Config`我们将用于测试的服务器和客户端：
```golang
serverCert, err := tls.X509KeyPair(certPEM.Bytes(), certPrivKeyPEM.Bytes())
if err != nil {
	return nil, nil, err
}

serverTLSConf = &tls.Config{
	Certificates: []tls.Certificate{serverCert},
}

certpool := x509.NewCertPool()
certpool.AppendCertsFromPEM(caPEM.Bytes())
clientTLSConf = &tls.Config{
	RootCAs: certpool,
}
```
在`httptest.Server`中使用我们的证书
现在，我们拥有使用CA签署的新证书启动服务器所需的一切，并让我们的客户信任该服务器。

首先，我们将配置并启动httptest.Server：
```golang
server := httptest.NewUnstartedServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "success!")
}))
server.TLS = serverTLSConf
server.StartTLS()
defer server.Close()
```
这将响应HTTP 200 OK和包含的主体success!。我们现在可以设置客户端以信任CA，并向服务器发送请求：
```golang
transport := &http.Transport{
	TLSClientConfig: clientTLSConf,
}
http := http.Client{
	Transport: transport,
}
resp, err := http.Get(server.URL)
if err != nil {
	panic(err)
}
```
如果没有发生错误，我们现在success!从服务器获得响应，并可以验证它：
```golang
respBodyBytes, err := ioutil.ReadAll(resp.Body)
if err != nil {
	panic(err)
}
body := strings.TrimSpace(string(respBodyBytes[:]))
if body == "success!" {
	fmt.Println(body)
} else {
	panic("not successful!")
}
```

完整程序示例：
```golang
package main

import (
	"bytes"
	"crypto/rand"
	"crypto/rsa"
	"crypto/tls"
	"crypto/x509"
	"crypto/x509/pkix"
	"encoding/pem"
	"fmt"
	"io/ioutil"
	"math/big"
	"net"
	"net/http"
	"net/http/httptest"
	"strings"
	"time"
)

func main() {
	// get our ca and server certificate
	serverTLSConf, clientTLSConf, err := certsetup()
	if err != nil {
		panic(err)
	}

	// set up the httptest.Server using our certificate signed by our CA
	server := httptest.NewUnstartedServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintln(w, "success!")
	}))
	server.TLS = serverTLSConf
	server.StartTLS()
	defer server.Close()

	// communicate with the server using an http.Client configured to trust our CA
	transport := &http.Transport{
		TLSClientConfig: clientTLSConf,
	}
	http := http.Client{
		Transport: transport,
	}
	resp, err := http.Get(server.URL)
	if err != nil {
		panic(err)
	}

	// verify the response
	respBodyBytes, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		panic(err)
	}
	body := strings.TrimSpace(string(respBodyBytes[:]))
	if body == "success!" {
		fmt.Println(body)
	} else {
		panic("not successful!")
	}
}

func certsetup() (serverTLSConf *tls.Config, clientTLSConf *tls.Config, err error) {
	// set up our CA certificate
	ca := &x509.Certificate{
		SerialNumber: big.NewInt(2019),
		Subject: pkix.Name{
			Organization:  []string{"Company, INC."},
			Country:       []string{"US"},
			Province:      []string{""},
			Locality:      []string{"San Francisco"},
			StreetAddress: []string{"Golden Gate Bridge"},
			PostalCode:    []string{"94016"},
		},
		NotBefore:             time.Now(),
		NotAfter:              time.Now().AddDate(10, 0, 0),
		IsCA:                  true,
		ExtKeyUsage:           []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth, x509.ExtKeyUsageServerAuth},
		KeyUsage:              x509.KeyUsageDigitalSignature | x509.KeyUsageCertSign,
		BasicConstraintsValid: true,
	}

	// create our private and public key
	caPrivKey, err := rsa.GenerateKey(rand.Reader, 4096)
	if err != nil {
		return nil, nil, err
	}

	// create the CA
	caBytes, err := x509.CreateCertificate(rand.Reader, ca, ca, &caPrivKey.PublicKey, caPrivKey)
	if err != nil {
		return nil, nil, err
	}

	// pem encode
	caPEM := new(bytes.Buffer)
	pem.Encode(caPEM, &pem.Block{
		Type:  "CERTIFICATE",
		Bytes: caBytes,
	})

	caPrivKeyPEM := new(bytes.Buffer)
	pem.Encode(caPrivKeyPEM, &pem.Block{
		Type:  "RSA PRIVATE KEY",
		Bytes: x509.MarshalPKCS1PrivateKey(caPrivKey),
	})

	// set up our server certificate
	cert := &x509.Certificate{
		SerialNumber: big.NewInt(2019),
		Subject: pkix.Name{
			Organization:  []string{"Company, INC."},
			Country:       []string{"US"},
			Province:      []string{""},
			Locality:      []string{"San Francisco"},
			StreetAddress: []string{"Golden Gate Bridge"},
			PostalCode:    []string{"94016"},
		},
		IPAddresses:  []net.IP{net.IPv4(127, 0, 0, 1), net.IPv6loopback},
		NotBefore:    time.Now(),
		NotAfter:     time.Now().AddDate(10, 0, 0),
		SubjectKeyId: []byte{1, 2, 3, 4, 6},
		ExtKeyUsage:  []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth, x509.ExtKeyUsageServerAuth},
		KeyUsage:     x509.KeyUsageDigitalSignature,
	}

	certPrivKey, err := rsa.GenerateKey(rand.Reader, 4096)
	if err != nil {
		return nil, nil, err
	}

	certBytes, err := x509.CreateCertificate(rand.Reader, cert, ca, &certPrivKey.PublicKey, caPrivKey)
	if err != nil {
		return nil, nil, err
	}

	certPEM := new(bytes.Buffer)
	pem.Encode(certPEM, &pem.Block{
		Type:  "CERTIFICATE",
		Bytes: certBytes,
	})

	certPrivKeyPEM := new(bytes.Buffer)
	pem.Encode(certPrivKeyPEM, &pem.Block{
		Type:  "RSA PRIVATE KEY",
		Bytes: x509.MarshalPKCS1PrivateKey(certPrivKey),
	})

	serverCert, err := tls.X509KeyPair(certPEM.Bytes(), certPrivKeyPEM.Bytes())
	if err != nil {
		return nil, nil, err
	}

	serverTLSConf = &tls.Config{
		Certificates: []tls.Certificate{serverCert},
	}

	certpool := x509.NewCertPool()
	certpool.AppendCertsFromPEM(caPEM.Bytes())
	clientTLSConf = &tls.Config{
		RootCAs: certpool,
	}

	return
}
```
这也可以在Github上[找到](https://gist.github.com/shaneutt/5e1995295cff6721c89a71d13a71c251)。

原文: [https://shaneutt.com/blog/golang-ca-and-signed-cert-go/](https://shaneutt.com/blog/golang-ca-and-signed-cert-go/)