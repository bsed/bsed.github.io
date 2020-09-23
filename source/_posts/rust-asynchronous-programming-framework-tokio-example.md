---
title: Rust 异步编程框架 Tokio 初次尝试 
date: 2019-07-09 16:46:00
updated: 2019-07-09 16:47:48
tags: 
- ffmpeg
categories: 
- linux

---
Tokio 是 Rust 中的异步编程框架，它将复杂的异步编程抽象为 Futures、Tasks 和 Executor，并提供了 Timer 等基础设施。Tokio 快速、可靠，且可扩展。
![43800-awnjg1f70hk.png](https://imgs.gnux.cn/usr/uploads/2019/07/2954550103.png)
Tokio 是一个事件驱动的非阻塞 I/O 平台，用于使用 Rust 编程语言编写异步应用。在高层设计上，它提供了一些主要组件：

多线程、工作窃取（work-stealing）的 task [scheduler](https://tokio-rs.github.io/tokio/tokio/runtime/index.html) 。


<!--more-->


由操作系统的事件队列（epoll，kqueue，IOCP 等）支撑的 [reactor](https://docs.rs/tokio/0.1.1/tokio/reactor/index.html) 。

异步 [TCP 和 UDP](https://docs.rs/tokio/0.1/tokio/net/index.html) 套接字。

这些组件提供构建异步应用所需的运行时组件。

示例1:
```rust
extern crate tokio;

use tokio::io;
use tokio::net::TcpListener;
use tokio::prelude::*;

fn main() 
{
    let listen = "127.0.0.1:3000";
    let addr = listen.parse().unwrap();
    let listener = TcpListener::bind(&addr).expect("Could not bind");

    let server = listener.incoming().for_each(|socket| {
        let (reader,writer) = socket.split();

        let no_bytes = io::copy(reader,writer);

        let msg = no_bytes.then(|result|{
            match result{
                Ok((no_bytes,_,_)) => println!("No of bytes:{}",no_bytes),
                Err(e)             => println!("{}",e),
            }

            Ok(())
    });

    tokio::spawn(msg);
    Ok(())
    })
    .map_err(|err| {
        println!("accept error = {:?}", err);
    });

    println!("Server listening on : {}",addr);

    tokio::run(server);
    
}
```

示例2：
```rust
use std::io::{Read, Write};
use std::thread;
use std::time::Duration;

use futures::Future;
use futures::Stream;
use log::{debug, error, info, trace, warn};
use tokio::io as tokio_io;
use tokio::io::{copy, AsyncRead, AsyncWrite};
use tokio::net::TcpListener;
use yamux::{config::Config, session::Session, stream::StreamHandle};

fn main() {
    env_logger::init();
    if std::env::args().nth(1) == Some("server".to_string()) {
        info!("Starting server ......");
        run_server();
    } else {
        info!("Starting client ......");
        run_client();
    }
}

fn run_server() {
    // Bind the server's socket.
    let addr = "127.0.0.1:12345".parse().unwrap();
    let listener = TcpListener::bind(&addr).expect("unable to bind TCP listener");

    // Pull out a stream of sockets for incoming connections
    let server = listener
        .incoming()
        .map_err(|e| eprintln!("accept failed = {:?}", e))
        .for_each(|sock| {
            info!("accepted a socket: {:?}", sock.peer_addr());
            let session = Session::new_server(sock, Config::default());
            // Split up the reading and writing parts of the
            // socket.
            let fut = session
                .for_each(|stream| {
                    info!("Server accept a stream from client: id={}", stream.id());
                    let fut = tokio_io::read_exact(stream, [0u8; 3])
                        .and_then(|(stream, data)| {
                            info!("[server] read data: {:?}", data);
                            Ok(stream)
                        })
                        .and_then(|mut stream| {
                            info!("[server] send 'def' to remote");
                            stream.write(b"def").unwrap();
                            stream.flush().unwrap();
                            Ok(stream)
                        })
                        .and_then(|stream| {
                            tokio_io::read_exact(stream, [0u8; 2]).and_then(|(stream, data)| {
                                info!("[server] read again: {:?}", data);
                                Ok(stream)
                            })
                        })
                        .map_err(|err| {
                            error!("server stream error: {:?}", err);
                            ()
                        })
                        .map(|_| ());
                    tokio::spawn(fut);
                    Ok(())
                })
                .map_err(|err| {
                    error!("server stream error: {:?}", err);
                    ()
                });

            // Spawn the future as a concurrent task.
            tokio::spawn(fut)
        });

    // Start the Tokio runtime
    tokio::run(server);
}

fn run_client() {
    use tokio::net::TcpStream;
    let addr = "127.0.0.1:12345".parse().unwrap();
    let socket = TcpStream::connect(&addr)
        .and_then(|sock| {
            info!("[client] connected to server: {:?}", sock.peer_addr());
            let mut session = Session::new_client(sock, Config::default());

            let mut stream = session.open_stream().unwrap();
            info!("[client] send 'abc' to remote");
            stream.write(b"abc").unwrap();

            info!("[client] reading data");
            let fut = tokio_io::read_exact(stream, [0u8; 3])
                .and_then(|(mut stream, data)| {
                    info!("[client] read data: {:?}", data);
                    stream.shutdown().unwrap();
                    Ok(())
                })
                .map_err(|_| ());
            tokio::spawn(fut);

            session.for_each(|stream| {
                info!("[client] accept a stream from server: id={}", stream.id());
                Ok(())
            })
        })
        .map_err(|err| {
            error!("[client] error: {:?}", err);
            ()
        });
    tokio::run(socket);
}
```

Cargo.toml
```toml
[package]
name = "tokio"
version = "0.1.0"
authors = ["kelvin <me@gnux.cn>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
crossbeam-channel = "0.3"
bytes = "0.4"
byteorder = "1.2"
fnv = "1.0"
futures = "0.1"
tokio = "0.1.22"
tokio-codec = "0.1.1"
tokio-io = "0.1.12"
log = "0.4.6"

[dev-dependencies]
env_logger = "0.6"
```
