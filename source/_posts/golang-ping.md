---
title: Golang Ping
date: 2012-12-09 14:13:00
updated: 2014-11-13 14:30:09
tags: 
- android
categories: 
- android

---
前段时间在用go写个工具，偶然在github上发现了一个用go写的icmp协议ping的实现代码，在此分享
编辑的时候用pre标签格式化代码好像有点问题，附上原链接 


<!--more-->


[https://github.com/paulstuart/ping/blob/master/ping.go](https://github.com/paulstuart/ping/blob/master/ping.go)

    // Copyright 2009 The Go Authors.  All rights reserved.
    // Use of this source code is governed by a BSD-style
    // license that can be found in the LICENSE file.
    
    // taken from http://golang.org/src/pkg/net/ipraw_test.go
    
    package ping
    
    import (
    	"bytes"
    	"errors"
    	"net"
    	"os"
    	"time"
    )
    
    const (
    	icmpv4EchoRequest = 8
    	icmpv4EchoReply   = 0
    	icmpv6EchoRequest = 128
    	icmpv6EchoReply   = 129
    )
    
    type icmpMessage struct {
    	Type     int             // type
    	Code     int             // code
    	Checksum int             // checksum
    	Body     icmpMessageBody // body
    }
    
    type icmpMessageBody interface {
    	Len() int
    	Marshal() ([]byte, error)
    }
    
    // Marshal returns the binary enconding of the ICMP echo request or
    // reply message m.
    func (m *icmpMessage) Marshal() ([]byte, error) {
    	b := []byte{byte(m.Type), byte(m.Code), 0, 0}
    	if m.Body != nil && m.Body.Len() != 0 {
    		mb, err := m.Body.Marshal()
    		if err != nil {
    			return nil, err
    		}
    		b = append(b, mb...)
    	}
    	switch m.Type {
    	case icmpv6EchoRequest, icmpv6EchoReply:
    		return b, nil
    	}
    	csumcv := len(b) - 1 // checksum coverage
    	s := uint32(0)
    	for i := 0; i < csumcv; i += 2 {
    		s += uint32(b[i+1])<<8 | uint32(b[i])
    	}
    	if csumcv&1 == 0 {
    		s += uint32(b[csumcv])
    	}
    	s = s>>16 + s&0xffff
    	s = s + s>>16
    	// Place checksum back in header; using ^= avoids the
    	// assumption the checksum bytes are zero.
    	b[2] ^= byte(^s & 0xff)
    	b[3] ^= byte(^s >> 8)
    	return b, nil
    }
    
    // parseICMPMessage parses b as an ICMP message.
    func parseICMPMessage(b []byte) (*icmpMessage, error) {
    	msglen := len(b)
    	if msglen < 4 {
    		return nil, errors.New("message too short")
    	}
    	m := &icmpMessage{Type: int(b[0]), Code: int(b[1]), Checksum: int(b[2])<<8 | int(b[3])}
    	if msglen > 4 {
    		var err error
    		switch m.Type {
    		case icmpv4EchoRequest, icmpv4EchoReply, icmpv6EchoRequest, icmpv6EchoReply:
    			m.Body, err = parseICMPEcho(b[4:])
    			if err != nil {
    				return nil, err
    			}
    		}
    	}
    	return m, nil
    }
    
    // imcpEcho represenets an ICMP echo request or reply message body.
    type icmpEcho struct {
    	ID   int    // identifier
    	Seq  int    // sequence number
    	Data []byte // data
    }
    
    func (p *icmpEcho) Len() int {
    	if p == nil {
    		return 0
    	}
    	return 4 + len(p.Data)
    }
    
    // Marshal returns the binary enconding of the ICMP echo request or
    // reply message body p.
    func (p *icmpEcho) Marshal() ([]byte, error) {
    	b := make([]byte, 4+len(p.Data))
    	b[0], b[1] = byte(p.ID>>8), byte(p.ID&0xff)
    	b[2], b[3] = byte(p.Seq>>8), byte(p.Seq&0xff)
    	copy(b[4:], p.Data)
    	return b, nil
    }
    
    // parseICMPEcho parses b as an ICMP echo request or reply message body.
    func parseICMPEcho(b []byte) (*icmpEcho, error) {
    	bodylen := len(b)
    	p := &icmpEcho{ID: int(b[0])<<8 | int(b[1]), Seq: int(b[2])<<8 | int(b[3])}
    	if bodylen > 4 {
    		p.Data = make([]byte, bodylen-4)
    		copy(p.Data, b[4:])
    	}
    	return p, nil
    }
    
    func Ping(address string, timeout int) bool {
    	err := Pinger(address, timeout)
    	return err == nil
    }
    
    func Pinger(address string, timeout int) error {
    	c, err := net.Dial("ip4:icmp", address)
    	if err != nil {
    		return err
    	}
    	c.SetDeadline(time.Now().Add(time.Duration(timeout) * time.Second))
    	defer c.Close()
    
    	typ := icmpv4EchoRequest
    	xid, xseq := os.Getpid()&0xffff, 1
    	wb, err := (&icmpMessage{
    		Type: typ, Code: 0,
    		Body: &icmpEcho{
    			ID: xid, Seq: xseq,
    			Data: bytes.Repeat([]byte("Go Go Gadget Ping!!!"), 3),
    		},
    	}).Marshal()
    	if err != nil {
    		return err
    	}
    	if _, err = c.Write(wb); err != nil {
    		return err
    	}
    	var m *icmpMessage
    	rb := make([]byte, 20+len(wb))
    	for {
    		if _, err = c.Read(rb); err != nil {
    			return err
    		}
    		rb = ipv4Payload(rb)
    		if m, err = parseICMPMessage(rb); err != nil {
    			return err
    		}
    		switch m.Type {
    		case icmpv4EchoRequest, icmpv6EchoRequest:
    			continue
    		}
    		break
    	}
    	return nil
    }
    
    func ipv4Payload(b []byte) []byte {
    	if len(b) < 20 {
    		return b
    	}
    	hdrlen := int(b[0]&0x0f) << 2
    	return b[hdrlen:]
    }


*附录：*

    package conn
        
        import (
        	"bytes"
        	"net"
        	"os"
        	"time"
        )
        
        const (
        	ICMP_ECHO_REQUEST = 8
        	ICMP_ECHO_REPLY   = 0
        )
        
        // Ping Request
        func makePingRequest(id, seq, pktlen int, filler []byte) []byte {
        	p := make([]byte, pktlen)
        	copy(p[8:], bytes.Repeat(filler, (pktlen-8)/len(filler)+1))
        	p[0] = ICMP_ECHO_REQUEST // type
        	p[1] = 0                 // code
        	p[2] = 0                 // cksum
        	p[3] = 0                 // cksum
        	p[4] = uint8(id >> 8)    // id
        	p[5] = uint8(id & 0xff)  // id
        	p[6] = uint8(seq >> 8)   // sequence
        	p[7] = uint8(seq & 0xff) // sequence
        	// calculate icmp checksum
        	cklen := len(p)
        	s := uint32(0)
        	for i := 0; i < (cklen-1); i += 2 {
        		s += uint32(p[i + 1])<<8|uint32(p[i])
        	}
        	if cklen&1 == 1 {
        		s += uint32(p[cklen - 1])
        	}
        	s = (s>>16)+(s&0xffff)
        	s = s+(s>>16)
        	// place checksum back in header; using ^= avoids the
        	// assumption the checksum bytes are zero
        	p[2] ^= uint8(^s & 0xff)
        	p[3] ^= uint8(^s >> 8)
        	return p
        }
        
        func parsePingReply(p []byte) (id, seq int) {
        	id = int(p[4])<<8|int(p[5])
        	seq = int(p[6])<<8|int(p[7])
        	return
        }
        
        // Ping
        func Ping(addr string, i int) bool {
        	// *IPAddr
        	raddr, e := net.ResolveIPAddr("ip4", addr)
        	if e != nil {
        		return false
        	}
        	// *IPConn
        	ipconn, ee := net.DialIP("ip4:icmp", nil, raddr)
        	if ee != nil {
        		return false
        	}
        	// 保证连接正常关闭
        
        	defer ipconn.Close()
        	// PID
        	sendid := os.Getpid() & 0xffff
        	sendseq := 1
        	pingpktlen := 64
        	for {
        		sendpkt := makePingRequest(sendid, sendseq, pingpktlen, []byte("Go Ping"))
        		// 发送请求
        		n, err := ipconn.WriteToIP(sendpkt, raddr)
        		if err != nil || n != pingpktlen {
        			break
        		}
        		// 超时
        		ipconn.SetDeadline(time.Now().Add(5 * time.Second))
        		// 返回数据
        		resp := make([]byte, 1024)
        		for {
        			// 读取返回
        			_, _, err := ipconn.ReadFrom(resp)
        			if err != nil {
        				break
        			}
        			// 判断状态
        			if resp[0] != ICMP_ECHO_REPLY {
        				continue
        			}
        			// 判断状态
        			rcvid, rcvseq := parsePingReply(resp)
        			if rcvid != sendid || rcvseq != sendseq {
        				break
        			}
        			// 成功返回
        			return true
        		}
        		// 执行次数内未成功返回
        		if i == sendseq {
        			break
        		}
        		// 计数器
        		sendseq++
        	}
        	// 失败返回
        	return false
        }
参考：
- [Go实现 icmp 协议 ping](http://golangtc.com/t/526e1f8b320b522433000003)