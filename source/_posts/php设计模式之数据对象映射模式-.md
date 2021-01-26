---
title: "php设计模式之数据对象映射模式 "
categories: [ "PHP" ]
tags: [ "php","designer" ]
draft: false
slug: "data-object-mapping-model-for-php-design-patterns"
date: "2016-08-11 11:12:00"
---

```php
<?php

namespace DesignModel;

/*
数据对象映射模式
数据对象映射模式是将对象和数据存储映射起来，对一个对象的操作会映射为对数据存储的操作
*/


<!--more-->


//User对象映射到数据库中的user表
class User{
	public $id;
	public $username;
	public $password;

	protected $db;

	//类实例化是赋值对象属性
	function  __construct($id)
	{
		$this->db = new \DesignModel\MYSQLI();
		$this->db->connect("localhost","root","","test");
		$res = $this->db->query("select * from user limit 1");
		$data = $res->fetch_assoc();
		$this->id=$data['id'];
		$this->username = $data['username'];
		$this->password = $data['password'];
	}

	//类销毁时更新对象到数据库
	function __destruct()
	{
		$sql = "update user set username='{$this->username}',password='{$this->password}' where id={$this->id}";
		$this->db->query($sql);
		echo $sql;
	}
}

$user = new \DesignModel\User(1);
var_dump($user->id,$user->username,$user->password);
$user->username = "zhangsan";
$user->password = "password";
```