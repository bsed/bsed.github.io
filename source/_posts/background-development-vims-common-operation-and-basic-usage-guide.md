---
title: 后台开发中，vim的常用操作和基本用法指南
date: 2012-10-19 21:19:00
updated: 2015-10-19 21:20:04
tags: 
- ubuntu
- vim
categories: 
- vim

---
## 1. vim的三种模式

+ vim有三种模式，不同的模式对应着不同的操作，完成不同的功能；这三种模式分别是：一般模式，编辑模式和命令模式。  

+ 一般模式：当我们使用vim打开文件时，就处于一般模式，一般模式是三种模式中的桥梁，也就是说可以从一般模式切换到命  
令模式或者编辑模式，但命令模式和编辑模式之间的切换必须经过一般模式，即先切换到一般模式，然后再进入另一种模式，编  
辑模式和命令模式之间是无法直接切换的。如何从另两种模式进入一般模式呢？按ESC即可。

+ 编辑模式：可以对文件的内容进行编辑操作，比如增加、删除和修改，就像使用notepad编辑文件一样；从一般模式进入编辑  
模式的命令主要有：i, I, a, A, o, O, s, S;（后面会详解）。

+ 命令模式：该模式下主要是用于文件的保存退出，从一般模式进入命令模式的方法是：冒号(:)。
	
## 2. 一般模式下的常用操作：

### 2.1 光标移动

	h, j, k, l: 向左、下、上、右移动一个字符；前面加上数字n，表示移动n个字符，如3j表示向下移动三个字符，即三行；


<!--more-->


	
	b, B: 表示begin，从字符所在的单词开始，b表示移动到前一个短单词的第一个字符处；B表示移动到前一个长单词的第一个字符处，两个空白之间的单词为一个长单词，比如hello how're you, how,'和re分别为三个短单词，但只是一个长单词；
	e, E: 与b, B类似，从字符所在的单词开始，分别向后移动一个短单词、长单词；
	
	(, ): 从光标所在的语句块开始，(表示移动到前一个语句块，)表示移动到后一个语句块；
	{, }: 从光标所在的段落开始，{表示向前移动一个段落，}表示向后移动一个段落；可以理解为从一个空行移动到前一个或者后一个空行；
	%: 跳转到匹配的(, ), {, }, [, ]
	
	CTRL+f: 屏幕向下翻页，相当于PgUp;
	CTRL+b: 屏幕向上翻页，相当于PgDn；
	

	0: 移动到光标所在行的第一个字符处；
	$: 移动到光标所在行的最后一个字符处；
	^: 移动到光标所在行的第一个非空字符处；
	
	gg: 移动到文件的第一行（且是第一个字符处）；
	G: 移动到文件的最后一行（且是第一个字符处）；
	数字n+G: 表示移动到第n行，如5G，表示移动到文件的第5行；
	说明：以上这些命令非常常用，熟记的同时要熟练；
	
	数字n+空格space: 表示向右移动n个字符，即n+space，等价于nl；
	数字n+回车Enter: 表示向下移动n行，等价于nj；

### 2.2 查找替换

	/word: 从光标所在处向下搜索关键字word；
	?word: 从光标所在处向上搜索关键字word；
	n: 向下搜索，查找下一个匹配项；
	N: 向上搜索，查找上一个匹配项；
	*：对光标所在的单词，向下查找；
	#: 对光标所在的单词，向上查找；
	说明：一般情况下，/word和n、N配合完成查找，而?word用得较少；
	
	:n1,n2s/src/dest/g: 在n1行到n2行之间（包括n1行和n2行），查找src，并将所有的src替换成dest；
	:1,$s/src/dest/g: 查查找范围为第一行到最后一行，将所有的src替换为dest，只是在替换时会有确认；
	:.,$s/src/dest/g: 查找范围为当前行到最后一行，将所有的src替换为dest；(因为在命令模式下，.表示当前行)
	
### 2.3 删除、复制与粘贴

	x, X: x表示删除光标后的一个字符，X表示删除光标前的一个字符，前面可以加上数字n，表示删除n个字符， 即nx, nX；
	dd: 表示删除光标所在的行，前面带上数字n，表示删除当前行往下的n行（包括当前行）；
	d$: 表示删除光标所在的位置到该行的最后一个字符；
	d0: 表示删除光标所在的位置到该行的第一个字符处；
	d^: 表示删除光标所在的位置到该行的第一个非空字符处；
	D: 删除光标所在字符到该行最后一个字符，等价于d$；
	dt+字符c: dt后接一个字符c，表示从光标所在字符开始，删除该行的字符知道遇到第一个为c的字符为止，如果在该行，光标所在字符后不存在字符c，则什么也不做；
	dG: 表示删除当前行到最后一行；
	dgg: 表示删除当前行与第一行；
	dnG: 表示删除当前行与第n行；
	
	yy: 复制光标所在那一行的内容；
	yG: 复制光标所在行与最后一行之间的内容；
	ygg: 复制光标所在行与第一行之间的内容；
	ynG: 复制光标所在行与第n行之间的内容；
	y$: 复制光标所在的字符到该行的最后一个字符；
	y0: 复制光标所在的字符到该行的第一个字符；
	y^: 复制光标所在的字符到该行的第一个非空字符；
	p, P: p表示将复制的数据粘贴在光标所在行的下一行；P表示将复制的数据粘贴在光标所在行的上一行；
	说明：如果复制的内容是以行为单位，则p和P就粘贴在当前行的下一行或上一行，如果复制的内容是以字符为单位，即针对y$, y0或者y^，p和P会粘贴在光标的后面。
	
	u: 复原上一个操作，类似于word中的还原；
	CTRL+r: 重做上一个操作；
	.: 重复前一个动作；相当于重新执行一遍前一个操作，比如前一个操作时dd，则.命令会再执行一遍dd；
	r,R: r表示替换光标所在字符，R表示替换光标所在字符及其后的字符，直到按ESC为止；
	
### 2.4 块选择

	v: 字符选择，从光标所在字符开始，配合h, j, k, l进行字符的选择；
	V: 行选择，将光标经过的行选中；
	CTRL+v: 块选择，从光标所在字符开始，选中块；
	y: 复制选中的内容；
	p: 粘贴选中的内容；
	
## 3. 编辑模式下的常用操作

	在编辑模式下，就是对文件的内容进行增删改，没有特殊的操作，光标的移动还是得进入到一般模式；
	从一般模式进入编辑模式的命令有：
	i, I: i表示在光标所在字符之前插入，I表示在光标所在行的第一个非空字符前插入；
	a, A: a表示在光标所在字符之后插入，A表示在光标所在行的最后一个非空字符后插入；

	o, O: o表示在光标所在行的下一行插入，O表示在光标所在行的上一行插入；
	s, S: s表示删除光标所在字符并进入插入模式，S表示删除光标所在行并进入插入模式；
	说明：s和S使用地稍微较少一些；
	
	CTR+n或者CTRL+p: 在编辑模式下，输入一些文字后，按CTRL+n或者CTRL+p可以出现提示文字；

## 4. 命令行模式下的常用操作

### 4.1 保存退出
	:w  保存文件内容，但不退出；
	:q  退出vim；如果文件内容没有被修改，则直接退出，如果文件内容修改了但没有保存，则会提示先保存后退出；
	:q!  强制退出vim；如果修改过文件但是没有保存，则不保存且强制退出；
	:e!  将文件恢复到最原始的状态，即上次保存的状态；
	:wq  保存后退出；
	:x  保存后退出；
	说明：其实最常用的命令当属:w, :q, :x，如果是有意的修改，则应该随时注意保存，即:w，离开时尽量使用:q，如果是有意修改，应该已经保存了，如果作了无意修改，则会提示，比较安全，只有在修改完之后，确定保存并离开，可以使用:x，不过还是尽量少用；
	
### 4.2 文件读写

	:w filename 将文件的内容另存为另一个文件
	:n1,n2 w filename 将文件的n1和n2行之间的内容另存为另一个文件；
	:w>>filename  将文件内容追加到另一个文件中；
	:r filename  将另一个文件的内容读入到光标所在的下一行；
	:f  显示当前文件的文件名
	
### 4.3 其它常用

	:set nu  显示行号
	:set nonu  隐藏行号
	:set hlsearch  查找时高亮显示匹配项
	:set nohlsearch  查找时不高亮显示匹配项
	说明：以上命令一般写入vim的配置文件里，这样就不需要每次都调这些命令了。
	
## 5. 多文件及分屏编辑

	:r filename命令可以读入整个文件的内容，但如果指向复制或者重用另一个文件的部分内容，怎么办呢？可以使用多文件编辑或者分屏编辑；
	
	多文件：vim后接多个文件名，则可以在一个vim里打开多个文件，可以进行文件间的编辑；
	:n  显示下一个文件；
	:N  显示上一个文件；
	:f  显示当前文件的文件名；
	:files  显示所有的文件名；
	说明：这样，可以从一个文件里复制部分内容，然后切换到另一个文件，将文件内容粘贴，实现多文件的编辑；
	
	分屏：在一个屏幕上同时显示多个文件的内容，可以对照着编辑，很方便；
	分屏显示有两种方式：
	[1] 先使用vim打开一个文件，然后在命令行模式下可输入的命令有：
	:sp filename  读入另一个文件，两个文件分水平的上下两屏来显示；如果不跟文件名，则新屏里也显示当前文件的内容；
	:vsp filename 或者 :vsplit filename  读入另一个文件，两个文件分垂直的左右两屏来显示；如果不跟文件名，则新屏里也显示当前文件的内容；
	分屏之后的常用命令有：
	CTRL+w,[h,j,k,l]: 如果是横屏，则CTRL+w,j表示光标移入下方的屏，CTRL+w,k表示光标移入上方的屏；CTRL+w,h表示光标移入左侧的屏，CTRL+w,l表示光标移入右侧的屏；
	CTRL+w,w: 表示在两个屏之间来回切换；
	说明：CTRL+w,h的按键方式为：同时按下CTRL和w，放开或不放开都行，然后按下h；
	CTRL+w,=: 表示将两个屏幕的宽度调整为相等；
	[2] vim启动的时候同时打开多个文件：
	$ vim -On file01 file02  分垂直两屏显示两个文件；
	$ vim -on file01 file02  分水平两屏显示两个文件；

> 说明：以上这些命令都是基础且常用的，不要死记硬背，应该在vim下多练习，直到输入这些命令成为一种无意识的行为；vi
m的功能很强大，而且支持丰富的插件，比如ctags等，有兴趣的可以找相关的文章参考；