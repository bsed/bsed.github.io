---
title: React-Native之Hello World
date: 2017-02-07 15:03:00
updated: 2017-02-18 15:12:51
tags: 
- tail
categories: 
- linux

---
开发环境: ubuntu16.04
IDE： ATOM
React Native项目的目录结构暂时不考虑，打开HelloWorld项目文件夹，找到`index.android.js`文件。
`index.android.js`文件就是React-Native JS 开发之旅的入口文件了。用文本编辑器打开`index.android.js`文件。


<!--more-->


```javascript
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

export default class HelloWorld extends Component {
  render() {
    return (
      <Text style={styles.welcome}>Hello world!</Text>
    );
  }
}

const styles = StyleSheet.create({
  welcome: {
       fontSize: 20,
       textAlign: 'center',
       margin: 10,
       color: 'red',
   }
});

AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```
// 注意，这里用引号括起来的'HelloWorld'必须和你init创建的项目名一致
```javascript
AppRegistry.registerComponent('HelloWorld', () => AwesomeProject);
```
点击模拟器,刷新视图,会看到如下截图：
![react_native-helloworld.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2017/02/1149948231.png