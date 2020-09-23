---
title: linux上开发系列之 安装使用 YUI Compressor
date: 2016-09-08 22:41:00
updated: 2016-09-09 22:42:44
tags: 
- proxy
- atom
categories: 
- vim

---
> YUI Compressor是开源的JavaScript压缩工具，使用java编写。它可以移除js/css文件中的注释和空行/空格，使用尽可能短的字符串混淆变量名。平均可以压缩20% 。



YUI Compressor的源代码托管在Github： [https://github.com/yui/yuicompressor](https://github.com/yui/yuicompressor)

其它压缩工具：[JSMin](https://github.com/douglascrockford/)，[Pyjsmin](https://pypi.python.org/pypi/jsmin)

## Ubuntu 安装 YUI Compressor

```bash
$ sudo apt-get install yui-compressor
```
## 使用YUI Compressor

**查看帮助**：

```bash
$ yui-compressor help

Usage: java -jar yuicompressor-x.y.z.jar [options] [input file]

Global Options
  -h, --help                Displays this information
  --type <js|css>           Specifies the type of the input file
  --charset <charset>       Read the input file using <charset>
  --line-break <column>     Insert a line break after the specified column number
  -v, --verbose             Display informational messages and warnings
  -o <file>                 Place the output into <file>. Defaults to stdout.
                            Multiple files can be processed using the following syntax:
                            java -jar yuicompressor.jar -o '.css$:-min.css' *.css
                            java -jar yuicompressor.jar -o '.js$:-min.js' *.js

JavaScript Options
  --nomunge                 Minify only, do not obfuscate
  --preserve-semi           Preserve all semicolons
  --disable-optimizations   Disable all micro optimizations

If no input file is specified, it defaults to stdin. In this case, the 'type'
option is required. Otherwise, the 'type' option is required only if the input
file extension is neither 'js' nor 'css'.
```

## 压缩js：

```bash
$ yui-compressor --type js --charset utf-8 -v js.js -o js-min.js
```

## 压缩css：

```bash
$ yui-compressor --type css --charset utf-8 -v css.css -o css-min.css
```

## js脚本压缩（comjs.sh）：

```
if [ $# -eq 0 ]; then
	echo "要压缩的文件." 1>&2
	exit 1
fi

for file in "$@";
do
if [ -f "$file" ]; then
		yui-compressor -o "${file%%.*}-min.js" "$file"
		if (( $? )); then
			echo "$file 不嫩压缩" >&2
			exit 1
		else
			echo "$file 压缩为 ${file%%.*}-min.js"
		fi
	else
		echo "找不到 '$file' 文件." >&2
fi
done;
exit 0
```

## 使用方法：

```bash
$ comjs.sh file.js
$ comjs.sh file1.js file2.js file3.js
```

## css脚本压缩（comcss.sh）：

```bash
if [ $# -eq 0 ]; then
	echo "要压缩的文件." 1>&2
	exit 1
fi

for file in "$@";
do
if [ -f "$file" ]; then
		yui-compressor -o "${file%%.*}-min.css" "$file"
		if (( $? )); then
			echo "$file 不嫩压缩" >&2
			exit 1
		else
			echo "$file 压缩为 ${file%%.*}-min.css"
		fi
	else
		echo "找不到 '$file' 文件 '$file'." >&2
fi
done;
exit 0
```

