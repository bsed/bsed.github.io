---
title: "如何正确的使用golang mysql driver"
categories: [ "Golang" ]
tags: [ "mysql","golang" ]
draft: false
slug: "how-to-use-mysql-driver-correctly"
date: "2019-06-18 16:12:00"
---

官方文档`https://golang.org/pkg/database/sql/#DB`，虽然写了如何使用它来执行SQL数据库查询和语句的例子，但是没有很好的说明如何正确的配置sql.DB改善性能，`SetMaxOpenConns()`, `SetMaxIdleConns()` and `SetConnMaxLifetime()`这三个方法也通常被初学者忽略。
![golang_mysql_driver.jpg][1]
## Open and idle connections:
一个sql.DB是包含许多'open'和'idle'连接的数据库连接池的对象。 当你使用它执行数据库任务时（例如执行SQL语句或查询行），*连接标记为打开。 任务完成后，连接变为空闲状态*。

当你使用sql.DB执行数据库任务时，它将首先检查连接池中是否有**空闲连接**。 如果有一个可用，那么Go将重新使用现有连接并在任务持续期间将其标记为打开。 如果连接池中没有空闲的连接，那么Go会创建一个新的连接并'打开'它。


<!--more-->


MySQL短连接每次请求操作数据库都需要建立与MySQL服务器建立TCP连接，这是需要时间开销的。TCP连接需要3次网络通信。这样就增加了一定的延时和额外的IO消耗。请求结束后会关闭MySQL连接，还会发生3/4次网络通信。close操作不会增加响应延时，原因是close后是由操作系统自动进行通信的，应用程序感知不到，长连接就可以避免每次请求都创建连接的开销，节省了时间和IO消耗。
```golang
type Config struct {
  DSN         string
  Active      int            // pool
  Idle        int            // pool
  IdleTimeout xtime.Duration // connect max life time.
}

// NewMysql initialize mysql connection .
func NewMysql(c *Config) (db *sql.DB) {
  // TODO add query exec and transaction timeout .
  db, err := Open(c)
  if err != nil {
    panic(err)
  }
  return
}

func Open(c *Config) (db *sql.DB, err error) {
  db, err = sql.Open("mysql", c.DSN)
  if err != nil {
    log.Error("sql.Open() error(%v)", err)
    return nil, err
  }
  db.SetMaxOpenConns(c.Active)
  db.SetMaxIdleConns(c.Idle) // 默认情况下，sql.DB允许在连接池中保留最多2个空闲连接
  db.SetConnMaxLifetime(time.Duration(c.IdleTimeout))
  return db, nil
}
```
从理论上讲，在连接池中允许更多的空闲连接会提高性能，因为它不太可能需要从头开始建立新的连接，因此有助于节省资源。

**那么我们应该保持一个大的空闲连接池？**

其实不然，保持空闲连接是有代价的，它需要占用的内存的，这点需要注意。设置多大的Idle应该根据自身应用程序来定。如果连接闲置时间过长，则可能无法使用。例如 **MySQL**的`wait_timeout`设置会自动关闭任何8小时未使用的连接（默认情况下），所以我们看到上面的代码我们设置了自己的超时时间。发生这种情况时，`sql.DB`会优雅地的关掉它。在关闭之前，连接会自动重试两次，此时Go将从连接池中移除连接并创建一个新连接。因此，如果将`MaxIdleConns`设置得太高，*实际上可能会导致连接变得无法使用，并且比使用更少空闲连接池（使用更频繁的连接数更少）时使用的资源更多*。

## 总结：

对于大多数使用`SetMaxOpenConns()` 来限制打开连接的最大数量的程序，都会对性能产生负面影响，但如果数据库资源比较紧张的情况下，这么做还是有好处的。
如果程序突发或定期同时执行两个以上的数据库任务，那么通过`SetMaxIdleConns()`增加空闲连接池的大小可能会产生积极的性能影响。 但是需要注意的是设置过大可能会适得其反。**上线之前最好做个压测已到达最佳性能**。
对于大多数通过`SetConnMaxLifetime()`设置连接超时的应用程序，都会对性能产生负面影响。 但是，如果你的数据库本身强制实现一个短的连接生命周期，那么在`sql.DB`对它进行设置是有价值的，以避免尝试和重试错误连接的开销。
如果希望程序在数据库达到硬连接限制时等待连接释放（而不是返回错误），则应该明确设置`SetMaxOpenConns()`和`SetMaxIdleConns()`。
## Demo
**mysql.go：**

对 `github.com/go-sql-driver/mysql` 进行简单的封装 可以通过配置文件初始化Config。
```golang
package mysql

import (
  "database/sql"
  "time"
  _ "github.com/go-sql-driver/mysql"
)

// Config is mysql config.
type Config struct {
  DSN         string
  Active      int            // pool
  Idle        int            // pool
  IdleTimeout time.Time    // connect max life time.
}

// NewMysql initialize mysql connection .
func NewMysql(c *Config) (db *sql.DB) {
  // TODO add query exec and transaction timeout .
  db, err := Open(c)
  if err != nil {
    panic(err)
  }
  return
}

func Open(c *Config) (db *sql.DB, err error) {
  db, err = sql.Open("mysql", c.DSN)
  if err != nil {
    log.Error("sql.Open() error(%v)", err)
    return nil, err
  }
  db.SetMaxOpenConns(c.Active)
  db.SetMaxIdleConns(c.Idle)
  db.SetConnMaxLifetime(time.Hour)
  return db, nil
}
```
dao.go

**初始化sql.db： **
```
import (
  "database/sql"

  "xxx/xxx/conf"
  "xxx/xx/mysql"
)

type Dao struct {
  c      *conf.Config
  db     *sql.DB
}

// New initial mysql dao .
func New(c *conf.Config) (d *Dao) {
  d = &Dao{
    c:      c,
    db:     mysql.NewMysql(c.Mysql.Master),
  }
  return
}

// Ping check mysql health.
func (d *Dao) Ping() (err error) {
  return  d.db.Ping()
}

// Close release all mysql resource .
func (d *Dao) Close() {
  d.db.Close()
}

// BeginTran begin mysql transaction .
func (d *Dao) BeginTran() (*sql.Tx, error) {
  return d.db.Begin()
}
```
**xxx.go：**
```golang
const (
  _inSQL          = "INSERT INTO xxx (mobile,password) VALUES (?,?)"
)

// InsertUser insert user .
func (d *Dao) InsertUser(mobile, password string) (affect int64, err error) {
  var res sql.Result
  if res, err = d.db.Exec(_inSQL, mobile, password); err != nil {
    log.Error("db.Exec error(%v)", err)
    return
  }
  return res.RowsAffected()
}
```
原文： [https://xiequan.info/go%E5%A6%82%E4%BD%95%E6%AD%A3%E7%A1%AE%E7%9A%84%E4%BD%BF%E7%94%A8mysql-driver/](https://xiequan.info/go%E5%A6%82%E4%BD%95%E6%AD%A3%E7%A1%AE%E7%9A%84%E4%BD%BF%E7%94%A8mysql-driver/)


  [1]: https://imgs.gnux.cn/usr/uploads/2019/06/3802870333.jpg