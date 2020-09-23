---
title: 如何用Json和jQuery实现及时搜索
date: 2015-09-10 18:24:00
updated: 2016-06-02 11:13:15
tags: 
- java
- abator
categories: 
- java

---
今天我将学习如何使用jQuery和JSON实现实时搜索JSON，JSON结构如下：

*.JSON:*

     {
        "name":"Azhagu pandian",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },
      {
        "name":"Azhagu",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },


<!--more-->


      {
        "name":"pandian",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },
      {
        "name":"Ramkumar",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },
      {
        "name":"Jai",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },
      {
        "name":"Arun",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      },
      {
        "name":"David",
        "avatar": "http://0.gravatar.com/avatar/43a5669f2e4d2342701ed560d453a0dd?s=64&d=&r=G",
        "location":"Chennai, India"
      }
    ]

这段代码是存储在`data.json`文件里。只要在搜索框中键入*文本*进行JSON Ajax请求，以获取和真正则表达式相匹配的数据，然后使用jQuery的JSON响应搜索。jQuery代码如下：

*js:*

    $(window).load(function(){
            $('#search').keyup(function(){
                var searchField = $('#search').val();
                var regex = new RegExp(searchField, "i");
                var output = '<div class="row">';
                var count = 1;
                $.getJSON('data.json', function(data) {
                  $.each(data, function(key, val){
                    if ((val.name.search(regex) != -1) || (val.location.search(regex) != -1)) {
                      output += '<div class="col-md-6 well">';
                      output += '<div class="col-md-3"><img class="img-responsive" src="'+val.avatar+'" alt="'+ val.name +'" /></div>';
                      output += '<div class="col-md-7">';
                      output += '<h5>' + val.name + '</h5>';
                      output += '<p>' + val.location + '</p>'
                      output += '</div>';
                      output += '</div>';
                      if(count%2 == 0){
                        output += '</div><div class="row">'
                      }
                      count++;
                    }
                  });
                  output += '</div>';
                  $('#results').html(output);
                }); 
            });
          });

*html:*

    <form role="form">
            <div class="form-group">
              <input type="email" class="form-control input-lg" id="search" placeholder="Sart type 'a' ....">
            </div>
    </form>

over...
