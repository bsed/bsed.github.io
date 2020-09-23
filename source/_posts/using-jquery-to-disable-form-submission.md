---
title: 使用jQuery禁用表单的提交
date: 2018-08-30 01:26:00
updated: 2018-08-30 15:28:38
tags: 
- jquery
- javascript
categories: 
- js

---
```javascript
$('#submitBtn').prop('disabled', true);
$('.reqInput').change(function() {
   checkFields();
});


<!--more-->


function checkFields(){
     var count = 0;
     $('.reqInput').each(function(i){
       if( $(this).val() === '') {
           //show a warning?
           count++;
        }
        if(count == 0){
          $('#submitBtn').prop('disabled', false);
        }else {
          $('#submitBtn').prop('disabled', true);
        }

    });
}
```