---
title: "Using jQuery to test if an input is focus"
categories: [ "JS" ]
tags: [ "jquery","focus" ]
draft: false
slug: "jquery-to-test-if-an-input-is-focus-using"
date: "2011-03-08 19:59:00"
---

[Using jQuery to test if an input has focus - Stackoverflow](http://stackoverflow.com/questions/967096/using-jquery-to-test-if-an-input-has-focus)

## jQuery 1.6+

jQuery added a [:focus](http://api.jquery.com/focus-selector/) selector so we no longer need to add it ourselves…

    return $('elem').is(':focus') ? true : false;

## jQuery 1.5 and below

    jQuery.expr[':'].focus = function( elem ) {
      return elem === document.activeElement && ( elem.type || elem.href );
    };


<!--more-->


## Any jQuery

If you just want to figure out which element has focus, you can use

    $(document.activeElement)

If you aren't sure if the version will be 1.6 or lower, you can add the :focus selector if it is missing:

    (function ( $ ) {
        var filters = $.expr[":"];
        if ( !filters.focus ) { 
            filters.focus = function( elem ) {
               return elem === document.activeElement && ( elem.type || elem.href );
            };
        }
    })( jQuery );