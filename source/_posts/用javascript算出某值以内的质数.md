---
title: "用javascript算出某值以内的质数"
categories: [ "JS" ]
tags: [ "javascript","prime" ]
draft: false
slug: "javascript-calculate-the-value-of-a-prime-number"
date: "2008-01-01 08:00:00"
---

## js 算出某值以内的质数

    //算出 num 以内的所有质数 
    function prime(num){
        var list = [];
        for(var i = 2; i <= num; i++){ list.push(i); } //create a Array
        
        for(var i = 0; i < list.length; i++){
            for(var j = 2; j < list[i]; j++){
                if(list[i] % j == 0){
                    list.splice(i,1); //delete the non prime number.it will change the index of other elements
                    j = 2; //由于splice导致list[i+1]的index变为i,因此把j置为2以便对list[i+1]进行重新计算
            }
        }
    }