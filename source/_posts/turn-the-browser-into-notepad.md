---
title: 将浏览器变成记事本
date: 2013-10-07 21:30:00
updated: 2015-11-06 21:31:23
tags: 
- firefox
- ubuntu
- jdk
categories: 
- linux

---
在瀏覽器開啟新的分頁並在url tab中輸入 :
	

    data:text/html, <html contenteditable>

或改變字型 ：
	
    data:text/html, <html contenteditable style='font-family: monospace'>  <!-- change font -->

瀏覽器變成了可以輸入文字的記事本了！
可以用來暫存一些資訊和文字。

這一行使用了 data URI scheme 的語法來讓瀏覽器知道此頁面編碼的方式，這樣的方法多用在當網頁有許多靜態檔案（img,css等）需要載入時，可以用這樣的方法減少 http 的 request，例如當有一個 <img> tag 時，瀏覽器會根據其 src 的值發出 request，若使用 data URI 將圖檔內容放置在此標籤的 src 中，可加入編碼格式如 base64 之編碼方式，這樣瀏覽器便會直接將此 img 標籤以此編碼方式將這個圖形顯示出來，並不會發出 request 來取得圖片。這樣的語法也有其他應用，例如可以將HTML <table> 標籤直接轉換成 csv 格式呈現在瀏覽器上讓使用者進行下載，如 ：
	

    'data:text/csv;charset=utf-8,' + encodeURIComponent(data);

這樣的瀏覽器筆記本方法都可已將它放置在書籤中，需要時可以馬上點開作使用，此外還有別人完成的類似 sublime text 2 外觀的版本 XD 並加入了 ruby 的 syntax highlighter ，超酷
