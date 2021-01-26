---
title: "利用jQuery对checkbox列表全选或者反选"
categories: [ "JS" ]
tags: [ "jquery","js","checkbox" ]
draft: false
slug: "the-checkbox-list-to-select-or-deselect-the-use-of-jquery"
date: "2013-08-20 18:14:00"
---

*JQuery:*

    $(document).ready(function() {
        $('#selecctall').click(function(event) {  //on click
            if(this.checked) { // check select status
                $('.checkbox1').each(function() { //loop through each checkbox
                    this.checked = true;  //select all checkboxes with class "checkbox1"              
                });
            }else{
                $('.checkbox1').each(function() { //loop through each checkbox
                    this.checked = false; //deselect all checkboxes with class "checkbox1"                      
                });        
            }
        });
       
    });


<!--more-->


或者：

    $(document).ready(function(){
        $("#selecctall").change(function(){
          $(".checkbox1").prop('checked', $(this).prop("checked"));
          });
    });

*HTML:*

    <ul class="chk-container">
    <li><input type="checkbox" id="selecctall"/> Selecct All</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item1"> This is Item 1</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item2"> This is Item 2</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item3"> This is Item 3</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item4"> This is Item 4</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item5"> This is Item 5</li>
    <li><input class="checkbox1" type="checkbox" name="check[]" value="item6"> This is Item 6</li>
    <li><input class="checkbox2" type="checkbox" name="check[]" value="item6"> Do not select this</li>
    </ul>
