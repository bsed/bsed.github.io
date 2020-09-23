---
title: AudioAPI+UserMedia 录音与回放
date: 2014-05-05 15:02:00
updated: 2015-02-04 15:02:44
tags: 
- android
- 幸运破解器
categories: 
- android

---
　UserMedia可以请求用户的麦克风，成功时可以得到一个音频流。AudioAPI可以从一个音频流上创建一个数据源节点。那么我们就可以把麦克风的输入数据放入AudioAPI中处理。于是通过AudioAPI的数据处理节点就可以写出录音与回放的程序。


<!--more-->


    <input value="录音" type="button" id="record" />
    <input value="播放" type="button" id="play" />
    <script>
    var AudioContext=AudioContext||webkitAudioContext;
    var context=new AudioContext;
    //调整兼容
    navigator.getUserMedia=
      navigator.getUserMedia||
      navigator.webkitGetUserMedia||
      navigator.mozGetUserMedia;
    //请求麦克风
    navigator.getUserMedia({audio:true},function(e){
      var data,p;
      //从麦克风的输入流创建源节点
      var stream=context.createMediaStreamSource(e);
      //用于录音的processor节点
      var recorder=context.createScriptProcessor(1024,1,0);
      recorder.onaudioprocess=function(e){
        if(record.value=="停止")data.push(e.inputBuffer.getChannelData(0));
      };
      //用于播放的processor节点
      var player=context.createScriptProcessor(1024,0,1);
      player.onaudioprocess=function(e){
        if(!data)return;
        var i,s=data[p++];
        if(!s)return play.value=="停止"&&play.click();
        var buffer=e.outputBuffer.getChannelData(0);
        for(i=0;i<s.length;i++)buffer[i]=s[i];
      };
      //录音/停止 按钮点击动作
      record.onclick=function(){
        if(record.value=="录音")
          return data=[],stream.connect(recorder),this.value="停止";
        if(record.value=="停止")
          return stream.disconnect(),this.value="录音";
      };
      //播放/停止 按钮点击动作
      play.onclick=function(){
        if(this.value=="播放")
          return p=0,player.connect(context.destination),this.value="停止";
        if(this.value=="停止")
          return player.disconnect(),this.value="播放";
      };
    },function(e){
      console.log("请求麦克风失败");
    });
    </script>