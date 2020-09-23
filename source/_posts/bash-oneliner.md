---
title: Bash下的终端技巧（Bash-Oneliner 翻译）
date: 2019-10-30 09:42:00
updated: 2019-10-30 11:16:29
tags: 
- vue
- jspdf
- html2canvas
categories: 
- js

---
> 原文：[https://onceupon.github.io/Bash-Oneliner/](https://onceupon.github.io/Bash-Oneliner/)

## 终端技巧

### 使用 Ctrl 键


<!--more-->


```php
Ctrl + n : 类似向下的键
Ctrl + p : 类似向上的键
Ctrl + r : 反向搜索命令的历史记录（按住 Ctrl + r )
Ctrl + s : 终端停止输出.（译者注：如 apt / yum，nload，watch 等，按 Enter 继续输出）
Ctrl + q : 在 Ctrl + s 之后重新恢复之前的 terminal.
Ctrl + a : 移动光标到行的开始处
Ctrl + e : 移动光标到行的结尾处
Ctrl + d : 如果当前的 terminal 命令行有输入，Ctrl + d 或删除光标处的字符，否则会退出当前的 terminal
Ctrl + k : 删除从当前光标到结尾的所有字符
Ctrl + x + backspace : 删除当前光标到行开始的所有字符
Ctrl + t : 交换当前光标下的字符和其前面字符的位置。Esc + t 交换光标前面的两个单词
Ctrl + w : 剪切光标之前的单词，然后 Ctrl + y 粘贴它
Ctrl + u : 剪切光标之前的行; 然后 Ctrl + y 粘贴它
Ctrl + _ : 撤销之前的操作
Ctrl + l : 相当于清除
Ctrl + x + Ctrl + e : 召唤起 $EDITOR 环境变量设置的编辑器程序，对多行命令有效
```

### 更改大小写

```bash
Esc + u
# 将文本从光标的开始到结尾的单词转换为大写
Esc + l
# 将文本从光标的开始到结尾的单词转换为小写
Esc + c
# 将光标下的字母转换为大写
```

### 运行历史记录编号 (例如 e.g. 53)

```bash
!53
```

### 运行最后一条命令

```bash
!!
```

### 运行最后一个命令并且使用参入号替换之前运行的参数 (例如最后一个命令 : echo 'aaa' -> 现在运行: echo 'bbb')

```bash
#最后的一条命令: echo 'aaa'
^aaa^bbb
# 结果如下:
echo 'bbb'
bbb

#注意只有唯一的第一个 aaa 将会被替代，如果你想替代所有的 aaa，使用「:&」替代：
^aaa^bbb^:&
#或者
!!:gs/aaa/bbb/
```

### 运行以开头的命令 (e.g. cat 文件名)

```bash
!cat
# 或者
!c
#再次运行cat文件名
```

### Bash 通配符

```bash
# '*' 用作文件名扩展的 "通配符" 。
/b?n/?at      #/bin/cat

# '?' 用作文件名扩展的单字符 "通配符" 。
/etc/pa*wd    #/etc/passwd

# ‘[]’ 用于匹配范围内的字符。
ls -l [a-z]*   #列出所有文件名中带有字母的文件。

# ‘{}’ 可用于匹配多个模式的文件名 
ls {*.sh,*.py}   #列出所有.sh和.py文件
```

### 一些方便的环境变量

```bash
$0   :shell或shell脚本的名称。
$1, $2, $3, ... :位置参数。
$#   :位置参数的数量。
$?   :最新的管道退出状态。
$-   :为shell设置的当前选项。
$$   :当前shell（不是subshell）的pid。
$!   :最新后台命令的PID。

$DESKTOP_SESSION     当前显示管理器
$EDITOR   首选文本编辑器。
$LANG   当前语言。
$PATH   搜索可执行文件的目录列表（即准备运行的程序）
$PWD    当前目录
$SHELL  当前 shell
$USER   当前用户名
$HOSTNAME   当前主机名
```

## 筛选

### 筛选类型

```bash
grep = grep -G # Basic Regular Expression (BRE)
fgrep = grep -F # fixed text, ignoring meta-charachetrs
egrep = grep -E # Extended Regular Expression (ERE)
pgrep = grep -P # Perl Compatible Regular Expressions (PCRE)
rgrep = grep -r # recursive
```

### 筛选并计算空行数

```bash
grep -c "^$"
```

### 筛选且仅返回整数

```bash
grep -o '[0-9]*'
#or
grep -oP '\d'
```

### 筛选带一定位数的整数（例如 3）

```bash
grep ‘[0-9]\{3\}’
# or
grep -E ‘[0-9]{3}’
# or
grep -P ‘\d{3}’
```

### 筛选仅 IP 地址

```bash
grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
# or
grep -Po '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'
```

### 筛选整个单词（例如 “target” ）

```bash
grep -w 'target'

#or using RE
grep '\btarget\b'
```

### 筛选匹配前后返回的行（例如 “bbo”）

```bash
# return also 3 lines after match
grep -A 3 'bbo'

# return also 3 lines before match
grep -B 3 'bbo'

# return also 3 lines before and after match
grep -C 3 'bbo'
```

### 查找开始字符（例如 “S”）

```bash
grep -o 'S.*'
```

### 单词中截取文本（例如 w1，w2 ）

```bash
grep -o -P '(?<=w1).*(?=w2)'
```

### 查找不包含单词的行（例如 bbo ）

```bash
grep -v bbo filename
```

### 查找不以某字符串开头的行（例如 # ）

```bash
grep -v '^#' file.txt
```

### 查找变量（例如 bbo="some strings" ）

```bash
grep "$boo" filename
#remember to quote the variable!
```

### 查找仅有一个或者第一个匹配（例如 bbo ）

```bash
grep -m 1 bbo filename
```

### 查找并返回匹配的行数（例如 bb ）

```bash
grep -c bbo filename
```

### 出现次数 （例如一行出现 3 次则记为 3 次）

```bash
grep -o bbo filename |wc -l
```

### 区分大小写查找（例如 bbo/BBO/Bbo ）

```bash
grep -i "bbo" filename
```

### 匹配颜色标记（例如 bbo )

```bash
grep --color bbo filename
```

### 查找文件夹内所有文件（例如 bbo ）

```bash
grep -R bbo /path/to/directory
# or
grep -r bbo /path/to/directory
```

### 查找文件夹内所有文件，不输出文件名（例如 bbo ）

```bash
grep -rh bbo /path/to/directory
```

### 查找文件夹内所有文件，**只**输出匹配的文件名（例如 bbo ）

```bash
grep -rl bbo /path/to/directory
```

### 或查找（例如 A 或 B 或 C 或 D ）

```php
grep 'A\|B\|C\|D'
```

### 与查找（例如 A 与 B ）

```bash
grep 'A.*B'
```

### 正则匹配任意字符（例如 ACB 或 AEB ）

```bash
grep 'A.B'
```

### 正则匹配一个确定或者不确定的字符（例如 color 或 colour ）

```bash
grep ‘colou?r’
```

### 在文件 B 中查找文件 A 的内容

```bash
grep -f fileA fileB
```

### 查找制表符

```bash
grep $'\t'
```

### 在一个变量中查找另一个变量

```bash
$echo "$long_str"|grep -q "$short_str"
if [ $? -eq 0 ]; then echo 'found'; fi
#grep -q will output 0 if match found
#remember to add space between []!
```

### 查找括号内的内容

```bash
grep -oP '\(\K[^\)]+'
```

### 数字字符范围查找（例如 AAEL0000-RA ）

```bash
grep -o -w "\w\{10\}\-R\w\{1\}"
# \w 文字字符 [0-9a-zA-Z_] \W 非文字字符
```

### 跳过文件目录（例如 bbo ）

```bash
grep -d skip 'bbo' /path/to/files/*
```

## 流编辑器

### 删除第一行

```bash
sed 1d filename
```

### 删除前 100 行（删除第 1-100 行）

```bash
sed 1,100d filename
```

### 删除带字符串的行 (例如： bbo)

```bash
sed "/bbo/d" filename
- case insensitive:
sed "/bbo/Id" filename
```

### 删除第 n 个字符不等于值的行（例如第 5 个字符不等于 2）

```bash
sed -E '/^.{5}[^2]/d'
#aaaa2aaa (you can stay)
#aaaa1aaa (delete!)
```

### 编辑内嵌（编辑并保存）

```bash
sed -i "/bbo/d" filename
```

### 使用变量（例如 $i）时，请使用双引号 " "

```bash
# e.g. add >$i to the first line (to make a bioinformatics FASTA file)
sed "1i >$i"
# notice the double quotes! in other examples, you can use a single quote, but here, no way!
# '1i' means insert to first line
```

### 同时使用环境变量和尾符号

```bash
# 使用反斜杠 $ 符，同时使用双引号来标记变量
sed -e "\$s/\$/\n+--$3-----+/"
```

### 删除空行

```bash
sed '/^\s*$/d'

# 或

sed '/^$/d'
```

### 删除最后一行

```bash
sed '$d'
```

### 删除文件的最后一个字符

```bash
sed -i '$ s/.$//' filename
```

### 文件头部添加字符（例如『[』）

```bash
sed -i '1s/^/[/' file
```

### 指定的行增加字符串（例如添加「 something 」到第 1 行和第 3 行）

```bash
sed -e '1isomething -e '3isomething'
```

### 添加字符串到文件的结尾（例如『]』）

```bash
sed '$s/$/]/' filename
```

### 添加新行到结尾

```bash
sed '$a\'
```

### 添加字符串到没一行（例如 bbo）

```bash
sed -e 's/^/bbo/' file
```

### 添加字符串到没一行的结尾（例如『}』）

```bash
sed -e 's/$/\}\]/' filename
```

### 每第 n 个字符添加 \n (例如每四个字符 t 添加 \n)

```bash
sed 's/.\{4\}/&\n/g'
```

### 使用分隔符和下一行 连接 / 结合 / 合并文件。例如 (用「,」去分割)

```bash
sed -s '$a,' *.json > all.json
```

### 替换 (例如用 B 去替换 A)

```bash
sed 's/A/B/g' filename
```

### 使用通配符进行替换 (例如 aaa = 开头的行替换成 aaa=/my/new/path)

```bash
sed "s/aaa=.*/aaa=\/my\/new\/path/g"
```

### 选择以字符串开头的行 (例如 bbo)

```bash
sed -n '/^@S/p'
```

### 删除带有字符串的行

```bash
sed '/bbo/d' filename
```

### 打印 / 获取 / 截取一定范围内的行 (例如 500-5000 之间)

```bash
sed -n 500,5000p filename
```

### 每 n 行打印一次

```bash
sed -n '0~3p' filename

# catch 0: start; 3: step
```

### 打印所有奇数行

```bash
sed -n '1~2p'
```

### 每隔三行打印一次，包括第一行

```bash
sed -n '1p;0~3p'
```

### 删除前导空格和制表符

```bash
sed -e 's/^[ \t]*//'
# Notice a whitespace before '\t'!!
```

### 只删除前导空格

```bash
sed 's/ *//'

# 注意'*'前的空格!!
```

### 删除结尾逗号

```bash
sed 's/,$//g'
```

### 在结尾添加一列

```bash
sed "s/$/\t$i/"
# $i 是你要添加的值

# 将文件名添加到文件的最后一列
for i in $(ls);do sed -i "s/$/\t$i/" $i;done
```

### 将文件的扩展名添加到最后一列

```bash
for i in T000086_1.02.n T000086_1.02.p;do sed "s/$/\t${i/*./}/" $i;done >T000086_1.02.np
```

### 删除换行符

```bash
sed ':a;N;$!ba;s/\n//g'
```

### 打印某一行 (例如：第 123 行)

```bash
sed -n -e '123p'
```

### 打印若干行 (例如：第 10 行到第 33 行)

```bash
sed -n '10,33p' <filename
```

### 改变分隔符

```bash
sed 's=/=\\/=g'
```

### 使用通配符替换 (例如: `A-1-e` 、 `A-2-e` 或者 `A-3-e` ....)

```bash
sed 's/A-.*-e//g' filename
```

### 删除文件的最后一个字符

```bash
sed '$ s/.$//'
```

### 在文件的指定位置插入字符 (例如: AAAAAA—> AAA#AAA)

```bash
sed -r -e 's/^.{3}/&#/' file
```

## Awk

### 设置 tab 为分隔符

```bash
awk -F $'\t'
```

### 设置 tab 为输出字段分隔符

```bash
awk -v OFS='\t'
```

### 传递变量

```bash
a=bbo;b=obb;
awk -v a="$a" -v b="$b" "$1==a && $10=b" filename
```

### 输出行号和每行的字符数

```bash
awk '{print NR,length($0);}' filename
```

### 输出列数

```bash
awk '{print NF}'
```

### 调整列输出顺序

```bash
awk '{print $2, $1}'
```

### 检查列中字符是否含有逗号（例如检查第一列）

```bash
awk '$1~/,/ {print}'
```

### 切割字符，并循环输出

```bash
awk '{split($2, a,",");for (i in a) print $1"\t"a[i]}' filename
```

### 在某个字符出现次数少于 N 次时，打印数据 (例如在 bbo 出现 7 次之后，停止输出数据)

```bash
awk -v N=7 '{print}/bbo/&& --N<=0 {exit}'
```

### 输出目录下所有为文件名和每个文件的最后一行

```bash
ls|xargs -n1 -I file awk '{s=$0};END{print FILENAME,s}' file
```

### 在列的前面加字符 （例如在第三列前面加 “chr”）

```bash
awk 'BEGIN{OFS="\t"}$3="chr"$3'
```

### 删除含有字符的行 (例如删除含有 bbo 的行)

```bash
awk '!/bbo/' file
```

### 删除最后一列

```bash
awk 'NF{NF-=1};1' file
```

### NR 和 FNR 的用法

```bash
# 例如以下2个文件:
# fileA:
# a
# b
# c
# fileB:
# d
# e
awk 'print FILENAME, NR,FNR,$0}' fileA fileB
# fileA    1    1    a
# fileA    2    2    b
# fileA    3    3    c
# fileB    4    1    d
# fileB    5    2    e
```




### 逻辑与

```bash
# 比如下面这两个文件:
# 文件 A:
# 1    0
# 2    1
# 3    1
# 4    0
# 文件 B:
# 1    0
# 2    1
# 3    0
# 4    1

awk -v OFS='\t' 'NR=FNR{a[$1]=$2;next} NF {print $1,((a[$1]=$2)? $2:"0")}' fileA 文件 B
# 1    0
# 2    1
# 3    0
# 4    0
```

### 将文件中所有的数字进行四舍五入操作

```bash
awk '{while (match($0, /[0-9]+\[0-9]+/)){
    \printf "%s%.2f", substr($0,0,RSTART-1),substr($0,RSTART,RLENGTH)
    \$0=substr($0, RSTART+RLENGTH)
    \}
    \print
    \}'
```

### 给每一行编码 / 索引

```bash
awk '{printf("%s\t%s\n",NR,$0)}'
```

### 将列数据分解为行

```bash
#例如分开一下内容:
# David    cat,dog
# into
# David    cat
# David    dog

awk '{split($2,a,",");for(i in a)print $1"\t"a[i]}' file

# 详情介绍请点击这里:　http://stackoverflow.com/questions/33408762/bash-turning-single-comma-separated-column-into-multi-line-string
```

### 平均一个文件 (每一行只能包含一个数字)

```bash
awk '{s+=$1}END{print s/NR}'
```

### 以字符串开始打印字段 (例如 Linux)

```bash
awk '$1 ~ /^Linux/'
```

### 排序一行数字 (例如 1 40 35 12 23 --> 1 12 23 35 40)

```bash
awk ' {split( $0, a, "\t" ); asort( a ); for( i = 1; i <= length(a); i++ ) printf( "%s\t", a[i] ); printf( "\n" ); }'
```

### 减去之前的行值 (column6，它等于 column4 减去最后一列 5)

```bash
awk '{$6 = $4 - prev5; prev5 = $5; print;}'
```

## Xargs

### 将选项卡设置为分隔符 (默认：空格)

```bash
xargs -d\t
```

### 每行显示三个项目

```bash
echo 1 2 3 4 5 6| xargs -n 3
# 1 2 3
# 4 5 6
```

### 提示在执行之前

```bash
echo a b c |xargs -p -n 3
```

### 打印命令和输出

```bash
xargs -t abcd
# bin/echo abcd
# abcd
```

### 查找且删除

```bash
find . -name "*.html"|xargs rm

# when using a backtick
rm `find . -name "*.html"`
```

### 删除文件名中有空格的文件 (例如 「hello 2001」)

```bash
find . -name "*.c" -print0|xargs -0 rm -rf
```

### 显示限制

```bash
xargs --show-limits
```

### 从文件夹中移除文件

```bash
find . -name "*.bak" -print 0|xargs -0 -I {} mv {} ~/old

# or
find . -name "*.bak" -print 0|xargs -0 -I file mv file ~/old
```

### 将第 100 个文件移到另一个目录

```bash
ls |head -100|xargs -I {} mv {} d1
```

### 平行

```bash
time echo {1..5} |xargs -n 1 -P 5 sleep

# a lot faster than:
time echo {1..5} |xargs -n1 sleep
```

### 将所有文件从 A 复制到 B

```bash
find /dir/to/A -type f -name "*.py" -print 0| xargs -0 -r -I file cp -v -p file --target-directory=/path/to/B

# v: verbose|
# p: keep detail (e.g. owner)
```

### sed 相关

```bash
ls |xargs -n1 -I file sed -i '/^Pos/d' filename
```

### 在文件的第一行增加文件的名称

```bash
ls |sed 's/.txt//g'|xargs -n1 -I file sed -i -e '1 i\>file\' file.txt
```

### 统计所有的文件

```bash
ls |xargs -n1 wc -l
```

### 将输出变成一行

```bash
ls -l| xargs
```

### 对目录中的文件进行统计

```bash
echo mso{1..8}|xargs -n1 bash -c 'echo -n "$1:"; ls -la "$1"| grep -w 74 |wc -l' --
# "--" 信号选项结束，并进一步进行选项的处理
```

### 计算所有文件中的行数，也统计总行数。

```bash
ls|xargs wc -l
```

### Xargs 以及 grep 命令

```bash
cat grep_list |xargs -I{} grep {} filename
```

### Xargs 和 sed 命令 (将所有旧的 ip 地址替换成在 etc 文件下面新的 ip 地址)

```bash
grep -rl '192.168.1.111' /etc | xargs sed -i 's/192.168.1.111/192.168.2.111/g'
```

## Find（查询）


### 列出当前目录中的所有子目录 / 文件

```bash
find .
```

### 列出当前目录下的所有文件

```bash
find . -type f
```

### 列出当前文件下的所有目录

```bash
find . -type d
```

### 编辑当前目录下的所有文件（例如，将 “www” 替换为 “ww”）

```bash
find . -name '*.php' -exec sed -i 's/www/w/g' {} \;

# 如果没有子目录
replace "www" "w" -- *
# a space before *
```

### 查询文件 并 打印文件名（例如 “mso”）

```bash
find mso*/ -name M* -printf "%f\n"
```

### 查找 并 删除 文件大小 小于（例如 74 字节）的文件

```bash
find . -name "*.mso" -size -74c -delete

# M 代表 MB, 等等
```

## Condition and loop (条件与循环)


### If 语句

```bash
# 使用 if 和 else 来进行条件判断 
if [[ "$c" == "read" ]]; then outputdir="seq"; else outputdir="write" ; fi

# 判断myfile是否包含字符串“test”
if grep -q hello myfile; then …

# 判断mydir 是否是一个目录, 修改 mydir 的内容 并且 执行其他操作:
if cd mydir; then
  echo 'some content' >myfile
else
  echo >&2 "Fatal error. This script requires mydir."
fi

# 判断 variable(变量)  是否为空
if [ ! -s "myvariable" ]
#返回指定对象的长度  如果是 "字符串"  返回 0.

# 判断文件是否存在
if [ -e 'filename' ]
then
  echo -e "file exists!"
fi

# 判断 myfile 文件是否存在   或者  myfile 连接是否存在 
if [ -e myfile ] || [ -L myfile ]
then
  echo -e "file exists!"
fi

# 判断 变量 x  是否大于 等于 5
if [ "$x" -ge 5 ]; then …

#  在 bash/ksh/zsh 中 判断 变量 x 是否大于等于 5,:
if ((x >= 5)); then …

# 使用  (( ))(双括号) 进行数学运算  将u+2的计算结果赋值给  j 
if ((j==u+2))

# 使用 [[ ]](双中括号)  进行数值比较   在[[]] 中 会将 特殊符号 自动转换为 比较符号 （例如  = 转换为 == ）  
if [[ $age -gt 21 ]]
```

[More if commands](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html)

### For 语法

```bash
for i in $(ls); do echo file $i;done
#或者
for i in *; do echo file $i; done

# 按任意键继续执行遍历
for i in $(cat tpc_stats_0925.log |grep failed|grep -o '\query\w\{1,2\}');do cat ${i}.log; read -rsp $'Press any key to continue...\n' -n1 key;done

# 当按下一个键会逐行打印文件
oifs="$IFS"; IFS=$'\n'; for line in $(cat myfile); do ...; done
while read -r line; do ...; done <myfile

# If only one word a line, simply （ 遍历文件内容 一行一行的遍历）
for line in $(cat myfile); do echo $line; read -n1; done

#遍历一个数组
for i in "${arrayName[@]}"; do echo $i;done
```

### While 语句，

```bash
# 文件的列减法 (例如 a 3 columns file)
while read a b c; do echo $(($c-$b));done < <(head filename)
#在两个 "<" 的中间有个空格 

# 汇总 列 减法
i=0; while read a b c; do ((i+=$c-$b)); echo $i; done < <(head filename)

# 继续检查正在运行的进程（例如perl），并在启动后立即启动另一个新进程（例如python）。 （最好使用wait命令！Ctrl + F " wait"）
while [[ $(pidof perl) ]];do echo f;sleep 10;done && python timetorunpython.py
```

### switch (case in bash)

```bash
read type;
case $type in
  '0')
    echo 'how'
    ;;
  '1')
    echo 'are'
    ;;
  '2')
    echo 'you'
    ;;
esac
```

## 变量


### 引号内变量解析

```bash
# foo=bar
 echo "'$foo'"
#'bar'
# 单引号/双引号  quotes around single quotes make the inner single quotes expand variables（在单引号内使用变量 变量会被解析）
```

### 获取变量长度

```bash
var="some string"
echo ${#var}
# 11
```

### 获取变量的第一个字节

```bash
var=string
echo "${var:0:1}"
#s

# or
echo ${var%%"${var#?}"}
```

### 从第一位 或最后一位 开始删除变量中的 n 个字节

```bash
var="some string"
echo ${var:2}
#me string
```

### 替换 (例如。删除第一个位置的 0)

```bash
var="0050"
echo ${var[@]#0}
#050
```

### 替换所有 (例如。将 字符 "a" 替换为 字符 ",")

```bash
{var/a/,}
# 使用 grep 
 test="god the father"
 grep ${test// /\\\|} file.txt
 # turning the space into 'or' (\|) in grep （在grep 的空间中将 替换   or 和 (\|) ）
```

### 将变量中的字符串改为小写（参数扩展）

```bash
var=HelloWorld
echo ${var,,}
helloworld
```

### Expand and then execute variable/argument （先执行脚本进行赋值 再输出变量内容）

```bash
cmd="bar=foo"
eval "$cmd"
echo "$bar" # foo
```

## 数学


### bash 中的算术展开 (运算符: +, -, *, /, %, 等等)

```undefined
#总结： a++ 先运算a，后a的值加1；++a，则相反，先加一，再参与运算。同理a--,与--a
echo $(( 10 + 5 ))  #15
x=1
echo $(( x++ )) #1 , 注意它仍然是1，因为它是后递增的
echo $(( x++ )) #2
echo $(( ++x )) #4 , 注意，它不是3，因为它是预增量
echo $(( x-- )) #4
echo $(( x-- )) #3
echo $(( --x )) #1
x=2
y=3
echo $(( x ** y )) #8
```

### 打印出一个数的质因数 (比如： 50)

```undefined
factor 50
```

### 用于产生从某个数到另外一个数之间的所有整数。 (比如： seq 10)

```undefined
seq 10|paste -sd+|bc
```

### 汇总一个文件（文件中每行仅包含一个数字）

```undefined
awk '{s+=$1} END {print s}' filename
```

### 列减法

```undefined
cat file| awk -F '\t' 'BEGIN {SUM=0}{SUM+=$3-$2}END{print SUM}'
```

### 用 expr 进行简单数学运算

```undefined
expr 10+20 #30
expr 10\*20 #600
expr 30 \> 20 #1 (true)
```

### 更多关于 BC 的数学运算

```undefined
# 小数位数/有效数字
echo "scale=2;2/3" | bc
#.66

# 指数运算符
echo "10^2" | bc
#100

# 使用变量
echo "var=5;--var"| bc
#4
```

## Time


### 返回执行命令所需的时间

```bash
time echo hi
```

### 等待一段时间（例如 10 秒）

```bash
sleep 10
```

### 在一段时间后注销你的帐户（例如 10 秒）

```bash
TMOUT=10
# 一旦你设置了这个变量，注销计时器开始运行！
```

### 设置您要运行命令的时长

```bash
# 仅仅运行 `sleep 10` 一秒。
timeout 1 sleep 10
```

### 设置何时要运行命令（例如，从现在开始 1 分钟）

```bash
at now + 1min  # 时间单位可以是 minutes, hours, days, 或 weeks
⚠️: 命令将使用 /bin/sh
at> echo hihigithub >~/itworks
at> <EOT>   # 按 Ctrl + D 退出
job 1 at Wed Apr 18 11:16:00 2018
```

## Download


### Download the content of this README.md (the one your are viewing now)

```bash
curl https://raw.githubusercontent.com/onceupon/Bash-Oneliner/master/README.md | pandoc -f markdown -t man | man -l -

# or w3m (a text based web browser and pager)
curl https://raw.githubusercontent.com/onceupon/Bash-Oneliner/master/README.md | pandoc | w3m -T text/html

# or using emacs (in emac text editor)
emacs --eval '(org-mode)' --insert <(curl https://raw.githubusercontent.com/onceupon/Bash-Oneliner/master/README.md | pandoc -t org)

# or using emacs (on terminal, exit using Ctrl + x then Ctrl + c)
emacs -nw --eval '(org-mode)' --insert <(curl https://raw.githubusercontent.com/onceupon/Bash-Oneliner/master/README.md | pandoc -t org)
```

### Download all from a page

```bash
wget -r -l1 -H -t1 -nd -N -np -A mp3 -e robots=off http://example.com

# -r: recursive and download all links on page
# -l1: only one level link
# -H: span host, visit other hosts
# -t1: numbers of retries
# -nd: don't make new directories, download to here
# -N: turn on timestamp
# -nd: no parent
# -A: type (separate by ,)
# -e robots=off: ignore the robots.txt file which stop wget from crashing the site, sorry example.com
```

### Upload a file to web and download (https://transfer.sh/)

```bash
#  Upload a file (e.g. filename.txt):
curl --upload-file ./filename.txt https://transfer.sh/filename.txt
# the above command will return a URL, e.g: https://transfer.sh/tG8rM/filename.txt

# Next you can download it by:
curl https://transfer.sh/tG8rM/filename.txt -o filename.txt
```

### Download file if necessary

```bash
data=file.txt
url=http://www.example.com/$data
if [ ! -s $data ];then
    echo "downloading test data..."
    wget $url
fi
```

### Wget to a filename (when a long name)

```bash
wget -O filename "http://example.com"
```

### Wget files to a folder

```bash
wget -P /path/to/directory "http://example.com"
```

### Instruct curl to follow any redirect until it reaches the final destination:

```bash
curl -L google.com
```

 



## 随机


### 随机生成密码（例如，生成 5 个长度为 13 的密码）

```bash
sudo apt install pwgen
pwgen 13 5
#sahcahS9dah4a xieXaiJaey7xa UuMeo0ma7eic9 Ahpah9see3zai acerae7Huigh7
```

### 从文件中随机选择 100 行

```bash
shuf -n 100 filename
```

### 随机排序

```bash
for i in a b c d e; do echo $i; done| shuf
```

### 随机选择在一定范围内的数字（例如，从 0-100 内随机选择 15 个数字）

```bash
shuf -i 0-100 -n 15
```

### 产生一个随机数

```bash
echo $RANDOM
```

### 产生一个 0-9 内的随机数

```bash
echo $((RANDOM % 10))
```

### 产生一个 1-10 内的随机数

```bash
echo $(((RANDOM %10)+1))
```

## Xwindow


X11 GUI 应用程序！如果你对纯文本的环境感到厌烦，这里有一些适合你的 GUI 工具。

### 启用 X11 转发，以便在服务器上使用图形应用程序。

```bash
ssh -X user_name@ip_address

# 或者通过 xhost 设置
# --> Install the following for Centos:
# xorg-x11-xauth
# xorg-x11-fonts-*
# xorg-x11-utils
```

### 小 xwindow 工具

```bash
xclock
xeyes
xcowsay
```

### 从 ssh 服务器中打开图片 / 图像

```bash
1\. ssh -X user_name@ip_address
2. apt-get install eog
3. eog picture.png
```

### 在服务器上观看视频

```bash
1\. ssh -X user_name@ip_address
2. sudo apt install mpv
3. mpv myvideo.mp4
```

### 在服务器上使用 gedit (GUI 编辑)

```bash
1\. ssh -X user_name@ip_address
2. apt-get install gedit
3. gedit filename.txt
```

### 从 ssh 服务器上打开 PDF

```bash
1\. ssh -X user_name@ip_address
2. apt-get install evince
3. evince filename.pdf
```

### 从 ssh 服务器上使用谷歌浏览器

```bash
1\. ssh -X user_name@ip_address
2. apt-get install libxss1 libappindicator1 libindicator7
3. wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
4. sudo apt-get install -f
5. dpkg -i google-chrome*.deb
6. google-chrome
```
## 系统


### 跟踪服务的最新日志

```bash
journalctl -u <service_name> -f
```

### 消除僵尸进程

```bash
# 僵尸进程已经死了，所以你不能杀死它。您可以消除通过杀死其父进程。
# 首先，找到僵尸进程的PID
ps aux| grep 'Z'
# 接下来发现僵尸的父进程的PID
pstree -p -s <zombie_PID>
# 然后你可以杀死它的父进程，你会发现僵尸进程已经不见了。
sudo kill 9 <parent_PID>
```

#### 显示内存使用情况

```bash
free -c 10 -mhs 1
# 每隔1秒打印10次
```

### 显示设备和分区的 CPU 和 IO 统计信息。

```bash
#每秒刷新一次
iostat -x -t 1
```

### 显示网络接口上的带宽使用情况（例如 enp175s0f0）

```bash
iftop -i enp175s0f0
```

### 告知系统已运行多长时间以及用户数量

```bash
uptime
```

### 检查是否是 root 用户运行

```bash
if [ "$EUID" -ne 0 ]; then
        echo "Please run this as root"
        exit 1
fi
```

### 更改 shell 用户 (例如： bonnie)

```bash
chsh -s /bin/sh bonnie
# /etc/shells: valid login shells
```

### 更改 root / 虚拟 root / 监狱 (e.g. 更改 root 为 newroot)

```bash
chroot /home/newroot /bin/bash

# To exit chroot
exit
```

### 显示文件（例如 filename.txt）的文件状态（大小；访问、修改和更改时间等）

```bash
stat filename.txt
```

### 当前进程的快照

```bash
ps aux
```

### 显示进程树

```bash
pstree
```

### 找到的最大进程数

```bash
cat /proc/sys/kernel/pid_max
```

### 打印或控制内核环缓冲区

```bash
dmesg
```

### 查看 ip 地址

```bash
$ip add show

# or
ifconfig
```

### 打印以前和当前的 SysV 运行级别

```bash
runlevel

#或者
who -r
```

### 改变 SysV 运行级别 (例如修改为 5)

```bash
init 5
#或者
telinit 5
```

### 在所有运行级别上显示所有可用的服务

```bash
chkconfig --list
# 相当于 ubntu 中 chkconfig 的 update-rc.d
```

### 查看系统的版本

```bash
cat /etc/*-release
```

### Linux 程序员的手册：文件系统的层次结构的说明

```bash
man hier
```

### 控制系统和服务管理器

```bash
#  检查 cron 的状态
systemctl status cron.service

#  停止一个 cron 服务
systemctl stop cron.service
```

### 任务列表

```bash
jobs -l
```

### 运行一个修改优先级的程序 (例如 ./test.sh)

```bash
# 较好的值在 -20 （最有利）到 19 之间调整
# 应用程序越好，优先级越低
# 默认值:10 ,默认优先级:80

nice -10 ./test.sh
```

### 导出路径

```bash
export PATH=$PATH:~/path/you/want
```

### 让文件可执行

```bash
chmod +x filename
# 现在你可以执行 ./filename
```

### 打印系统信息

```bash
uname -a

# 检查系统硬件平台 (x86-64)
uname -i
```

### 上网

```bash
links www.google.com
```

### 增加用户，设置密码

```bash
useradd username
passwd username
```

### 编辑 bash 变量（例如显示整个路径）

```bash
1\. joe ~/.bash_profile
2. export PS1='\u@\h:\w\$'
# $PS1 是一个定义命令提示符外观和样式的变量
3\. source ~/.bash_profile
```

### 编辑环境设置 (例如 alias)

```bash
1\. joe ~/.bash_profile
2. alias pd="pwd" //no more need to type that 'w'!
3\. source ~/.bash_profile
```

### 打印所有的别名

```bash
alias -p
```

### 无别名 (例如别名 ls='ls --color=auto' 之后)

```bash
unalias ls
```

### 设置和取消设置 shell 选项

```bash
# 打印所有的 shell 选项
shopt

# 取消(或者停止)别名
shopt -u expand_aliases

# 设置(或者开始)别名
shopt -s expand_aliases
```

### 列出环境变量 (例如 PATH)

```bash
echo $PATH
#用冒号分隔的目录列表
```

### 当前用户所有环境变量列表

```bash
env
```

### Unset 环境变量 (e.g. unset 变量 'MYVAR')

```bash
unset MYVAR
```

### 显示区分格式

```bash
lsblk
```

### 通知系统分区表更改

```bash
partprobe
```

### 软链接到 bin

```bash
ln -s /path/to/program /home/usr/bin
# 必须是程序的绝对路径
```

### 显示数据的十六进制视图

```bash
hexdump -C filename.class
```

### 跳转到其他节点

```bash
rsh node_name
```

### 检查端口（占用的网络端口）

```bash
netstat -tulpn
```

### 打印已解析的符号链接或着规范文件名

```bash
readlink filename
```

### 查找命令的类型及链接到的位置 (e.g. python)

```bash
type python
# python 是 /usr/bin/python
# 这里有5中不同的类型，使用 'type -f' 标志进行检查
# 1\. alias    (shell alias)
# 2\. function (shell function, 也会打印函数主体)
# 3\. builtin  (shell builtin)
# 4\. file     (disk file)
# 5\. keyword  (shell reserved word)

# 你也可以使用 `which`
which python
# /usr/bin/python
```

### 列出所有的函数名

```bash
declare -F
```

### 列出目录的大小信息

```bash
du -hs .

# or
du -sb
```

### 复制使用权限目录

```bash
cp -rp /path/to/directory
```

### 储存当前目录

```bash
pushd .

# then pop
popd

#或着使用dirs显示当前所在目录的列表。
dirs -l
```

### 显示磁盘使用情况

```bash
df -h

# 或者
du -h

#或者
du -sk /var/log/* |sort -rn |head -10
```

### 显示当前运行状态

```bash
runlevel
```

### 切换运行级别

```bash
init 3

#或者
telinit 3
```

### 永久修改运行级别

```bash
1\. edit /etc/init/rc-sysinit.conf
2. env DEFAULT_RUNLEVEL=2
```

### 切换 root 用户

```bash
su
```

### 切换其他用户

```bash
su somebody
```

### 反馈用户状态信息

```bash
repquota -auvs
```

### 获取数据库中重要信息

```bash
getent database_name

# (e.g.  'passwd' 数据库)
getent passwd
# 列出所有用户帐户（所有本地帐户和LDAP）

# (e.g. 获取用户组列表)
getent group
# 在 'group' 数据库保存
```

### 改变文件的权限

```bash
chown user_name filename
chown -R user_name /path/to/directory/
# 改变用户组名称
```

### 挂载和取消挂载

```bash
# 例如 挂载 /dev/sdb 到 /home/test
mount /dev/sdb /home/test

# 例如 取消挂载 /home/test
umount /home/test
```

### 列出当前挂载细节

```bash
mount
# 或者
df
```

### 列出当前用户名和用户编号

```bash
cat /etc/passwd
```

### 获取所有的用户名

```bash
getent passwd| awk '{FS="[:]"; print $1}'
```

### 展示所有的用户

```bash
compgen -u
```

### 展示所有组

```bash
compgen -g
```

### 展示所有的用户组

```bash
group username
```

### 显示 uid，gid，用户组

```bash
id username
```

### 检测当前是否是 root

```bash
if [ $(id -u) -ne 0 ];then
    echo "You are not root!"
    exit;
fi
# 'id -u' output 0 if it's not root
```

### 找出 CPU 信息

```bash
more /proc/cpuinfo

# 或者
lscpu
```

### 为用户设置配额 (例如磁盘软大小限制: 120586240; 硬限制: 125829120)

```bash
setquota username 120586240 125829120 0 0 /home
```

### 显示用户配额

```bash
quota -v username
```

### 显示缓存中华当前库

```bash
ldconfig -p
```

### 打印共享库依赖项 (例如 for 'ls')

```bash
ldd /bin/ls
```

### 检查用户登录

```bash
lastlog
```

### 编辑所有用户的路径

```bash
joe /etc/environment
#编辑这个文件
```

### 显示和设置用户限制

```bash
ulimit -u
```

### 哪些端口正在监听来自网络的 TCP 连接

```bash
nmap -sT -O localhost
#notice that some companies might not like you using nmap
```

### 打印内核 / 处理器的数量

```bash
nproc --all
```

### 检查每一个核心状态

```php
1\. top
2. press '1'
```

### 展示任务和 PID

```bash
jobs -l
```

### 列出所有正在执行服务

```bash
service --status-all
```

### 计划关闭服务器

```bash
shutdown -r +5 "Server will restart in 5 minutes. Please save your work."
```

### 取消与预定关闭

```bash
shutdown -c
```

### 向所有用户广播

```bash
wall -n hihi
```

### 终止一个用户的所有进程

```bash
pkill -U user_name
```

### 终止程序的所有进程

```bash
kill -9 $(ps aux | grep 'program_name' | awk '{print $2}')
```

### 在服务器上设置 gedit 首选项

```php
# 你可能需要去安装以下软件:

apt-get install libglib2.0-bin;
# 或者
yum install dconf dconf-editor;
yum install dbus dbus-x11;

# 检查列表
gsettings list-recursively

# 修改一些设置
gsettings set org.gnome.gedit.preferences.editor highlight-current-line true
gsettings set org.gnome.gedit.preferences.editor scheme 'cobalt'
gsettings set org.gnome.gedit.preferences.editor use-default-font false
gsettings set org.gnome.gedit.preferences.editor editor-font 'Cantarell Regular 12'
```

### 把用户增加到一个分组 (例如 把用户名为「nice」的永不添加到分组「docker」, 这样此用户就可以在不用 sudo 的情况下运行 docker)

```bash
sudo gpasswd -a nice docker
```

### 安装没有根目录的 python 包

```bash
1\. pip 安装 --用户 package_name
2. 你可能需要将  ~/.local/bin/ 导出到  PATH: export PATH=$PATH:~/.local/bin/
```

### 删除旧的 Linux 内核 (当 /boot 几乎满的时候...)

```bash
1\. uname -a  #检查当前内核，哪些是不能移除的
2\. sudo apt-get purge linux-image-X.X.X-X-generic  #替代掉旧的版本
```

### 更改主机名

```bash
sudo hostname your-new-name

#如果不起作用，也可以:
hostnamectl set-hostname your-new-hostname
# 然后检查:
hostnamectl
# 或者检查 /etc/hostname

# 如果一直不工作....,编辑：
/etc/sysconfig/network
/etc/sysconfig/network-scripts/ifcfg-ensxxx
#增加 主机名="你的新主机名称"
```

### 安装包列表

```bash
apt list --installed

# 或者在 Red Hat:
yum list installed
```

### 检查哪个文件使设备繁忙

```bash
lsof /mnt/dir
```

### 当声音不工作的时候

```bash
killall pulseaudio
# 然后按下 Alt-F2 并输入 pulseaudio
```

### 当声音不工作的时候

```bash
killall pulseaudio
```

### 列出关于 SCSI 设备信息列表

```bash
lsscsi
```

### 设置你自己的 DNS 服务器教程

[http://onceuponmine.blogspot.tw/2017/08/se...](http://onceuponmine.blogspot.tw/2017/08/set-up-your-own-dns-server.html)

### 创建一个简单守护进程的教程

[http://onceuponmine.blogspot.tw/2017/07/cr...](http://onceuponmine.blogspot.tw/2017/07/create-your-first-simple-daemon.html)

### 一个使用你的邮箱发送邮件的教程

[http://onceuponmine.blogspot.tw/2017/10/se...](http://onceuponmine.blogspot.tw/2017/10/setting-up-msmtprc-and-use-your-gmail.html)

### 使用 telnet 去测试开放的端口，测试是否可以连接到服务器例如服务器 (192.168.2.106) 端口 (53)

```bash
telnet 192.168.2.106 53
```

### 改变网络最大可传输单位 (mtu) (例如修改到 9000)

```bash
ifconfig eth0 mtu 9000
```

### 或者正在执行的进程 pid (例如 python)

```bash
pidof python

# 或者
ps aux|grep python
```

### NTP

```bash
# Start ntp:
ntpd

# 检查 ntp:
ntpq -p
```

### 清除你服务器中不必要的文件

```bash
sudo apt-get autoremove
sudo apt-get clean
sudo rm -rf ~/.cache/thumbnails/*

# 删除旧的内核
sudo dpkg --list 'linux-image*'
sudo apt-get remove linux-image-OLDER_VERSION
```

### 增大调整根分区的大小 (根分区是 LVM 逻辑卷)

```bash
pvscan
lvextend -L +130G /dev/rhel/root -r
# 添加 -r 将在调整卷大小后增加到文件系统
```

### Create a UEFI Bootable USB drive (e.g. /dev/sdc1)

```bash
sudo dd if=~/path/to/isofile.iso of=/dev/sdc1 oflag=direct bs=1048576
```

### Locate and remove a package

```bash
sudo dpkg -l | grep <package_name>
sudo dpkg --purge <package_name>
```

### Create a ssh tunnel

```bash
ssh -f -L 9000:targetservername:8088 root@192.168.14.72 -N
#-f: run in background; -L: Listen; -N: do nothing
#the 9000 of your computer is now connected to the 8088 port of the targetservername through 192.168.14.72
#so that you can see the content of targetservername:8088 by entering localhost:9000 from your browser.
```

### Get process ID of a process (e.g. sublime_text)

```bash
#pidof
pidof sublime_text

#pgrep, you don't have to type the whole program name
pgrep sublim

#pgrep, echo 1 if process found, echo 0 if no such process
pgrep -q sublime_text && echo 1 || echo 0

#top, takes longer time
top|grep sublime_text
```

### Some benchmarking tools for your server

[aio-stress](https://openbenchmarking.org/test/pts/aio-stress) - AIO benchmark. 
[bandwidth](https://zsmith.co/bandwidth.html) - memory bandwidth benchmark.
[bonnie++](https://www.coker.com.au/bonnie++/) - hard drive and file system performance benchmark.
[dbench](https://dbench.samba.org/) - generate I/O workloads to either a filesystem or to a networked CIFS or NFS server.
[dnsperf](https://www.dnsperf.com/) - authorative and recursing DNS servers.
[filebench](https://github.com/filebench/filebench) - model based file system workload generator. 
[fio](https://linux.die.net/man/1/fio) - I/O benchmark. 
[fs_mark](https://github.com/josefbacik/fs_mark) - synchronous/async file creation benchmark.
[httperf](https://github.com/httperf/httperf) - measure web server performance.
[interbench](https://github.com/ckolivas/interbench) - linux interactivity benchmark.
[ioblazer](https://labs.vmware.com/flings/ioblazer) - multi-platform storage stack micro-benchmark.
[iozone](http://www.iozone.org/) - filesystem benchmark. 
[iperf3](https://iperf.fr/iperf-download.php) - measure TCP/UDP/SCTP performance.
[kcbench](https://github.com/knurd/kcbench) - kernel compile benchmark, compiles a kernel and measures the time it takes. [lmbench](http://www.bitmover.com/lmbench/) - Suite of simple, portable benchmarks.
[netperf](https://github.com/HewlettPackard/netperf) - measure network performance, test unidirectional throughput, and end-to-end latency. 
[netpipe](https://linux.die.net/man/1/netpipe) - network protocol independent performance evaluator. 
[nfsometer](http://wiki.linux-nfs.org/wiki/index.php/NFSometer) - NFS performance framework.
[nuttcp](https://www.nuttcp.net/Welcome Page.html) - measure network performance. 
[phoronix-test-suite](https://www.phoronix-test-suite.com/) - comprehensive automated testing and benchmarking platform. [seeker](https://github.com/fidlej/seeker) - portable disk seek benchmark. 
[siege](https://github.com/JoeDog/siege) - http load tester and benchmark.
[sockperf](https://github.com/Mellanox/sockperf) - network benchmarking utility over socket API.
[spew](https://linux.die.net/man/1/spew) - measures I/O performance and/or generates I/O load.
[stress](https://people.seas.harvard.edu/~apw/stress/) - workload generator for POSIX systems.
[sysbench](https://github.com/akopytov/sysbench) - scriptable database and system performance benchmark. [tiobench](https://github.com/mkuoppal/tiobench) - threaded IO benchmark. 
[unixbench](https://github.com/kdlucas/byte-unixbench) - the original BYTE UNIX benchmark suite, provide a basic indicator of the performance of a Unix-like system.
[wrk](https://github.com/wg/wrk) - HTTP benchmark.

### Show a listing of last logged in users.

```bash
lastb
```

### Show a listing of current logged in users, print information of them

```bash
who
```

### Show who is logged on and what they are doing

```bash
w
```

### Print the user names of users currently logged in to the current host.

```bash
users
```

 



### Stop tailing a file on program terminate

```bash
tail -f --pid=<PID> filename.txt
# replace <PID> with the process ID of the program.
```

### List all enabled services

```bash
systemctl list-unit-files|grep enabled
```

## Hardware

[[back to top](https://github.com/onceupon/Bash-OneLiner#handy-bash-one-liners)]

### Collect and summarize all hardware info of your machine

```bash
lshw -json >report.json
# Other options are: [ -html ]  [ -short ]  [ -xml ]  [ -json ]  [ -businfo ]  [ -sanitize ] ,etc
```

### Finding Out memory device detail

```bash
sudo dmidecode -t memory
```

### Print detail of CPU hardware

```bash
dmidecode -t 4
#          Type   Information
#          0   BIOS
#          1   System
#          2   Base Board
#          3   Chassis
#          4   Processor
#          5   Memory Controller
#          6   Memory Module
#          7   Cache
#          8   Port Connector
#          9   System Slots
#         11   OEM Strings
#         13   BIOS Language
#         15   System Event Log
#         16   Physical Memory Array
#         17   Memory Device
#         18   32-bit Memory Error
#         19   Memory Array Mapped Address
#         20   Memory Device Mapped Address
#         21   Built-in Pointing Device
#         22   Portable Battery
#         23   System Reset
#         24   Hardware Security
#         25   System Power Controls
#         26   Voltage Probe
#         27   Cooling Device
#         28   Temperature Probe
#         29   Electrical Current Probe
#         30   Out-of-band Remote Access
#         31   Boot Integrity Services
#         32   System Boot
#         34   Management Device
#         35   Management Device Component
#         36   Management Device Threshold Data
#         37   Memory Channel
#         38   IPMI Device
#         39   Power Supply
```

### Count the number of Segate hard disks

```bash
lsscsi|grep SEAGATE|wc -l
# or
sg_map -i -x|grep SEAGATE|wc -l
```

 



### 或者硬盘的 UUID (例如 sdb)

```bash
blkid /dev/sdb
```

### 打印所有硬盘的详细信息

```bash
lsblk -io KNAME,TYPE,MODEL,VENDOR,SIZE,ROTA
#其中 ROTA 表示旋转设备/旋转硬盘 (1 为真, 0 为假)
```

### 列出所有的 PCI (外围设置互连) 设备

```bash
lspci
# 列出关于 NIC 信息
lspci | egrep -i --color 'network|ethernet'
```

### 列出所有 USB 设备

```bash
lsusb
```

### Linux 模块

```bash
# 显示 Linux 内核中模块状态
lsmod

# 从 Linux 内核中增加或者移除模块
modprobe

# 或者
# Remove a module
rmmod

# 插入模块
insmod
```

### 控制 IPMI-enabled 设备 (e.g. BMC)

```bash
# 远程查看服务器的电源状态
ipmitool -U <bmc_username> -P <bmc_password> -I lanplus -H <bmc_ip_address> power status

# 远程开启服务器
ipmitool -U <bmc_username> -P <bmc_password> -I lanplus -H <bmc_ip_address> power on

# 打开面板识别灯(默认 15秒)
ipmitool chassis identify 255

#或者服务器传感器温度
ipmitool sensors |grep -i Temp

# 重置 BMC
ipmitool bmc reset cold

# Prnt BMC 网络
ipmitool lan print 1

# 设置 BMC 网络
ipmitool -I bmc lan set 1 ipaddr 192.168.0.55
ipmitool -I bmc lan set 1 netmask 255.255.255.0
ipmitool -I bmc lan set 1 defgw ipaddr 192.168.0.1
```

## 网络


### 显示 IP 地址

```bash
ip a
```

### 显示路由表

```bash
ip r
```

### 显示 ARP 缓存 (ARP 缓存显示你连接到的同一网络设备的 MAC 地址)

```bash
ip n
```

### 增加临时 IP 地址 (重启后重置) (例如 增加 192.168.140.3/24 到 设备 eno16777736)

```bash
ip address add 192.168.140.3/24 dev eno16777736
```

### 持久化网络配置的更改

```bash
sudo vi /etc/sysconfig/network-scripts/ifcfg-enoxxx
# 然后编辑字段: BOOTPROT, DEVICE, IPADDR, NETMASK, GATEWAY, DNS1 etc
```

### 刷新 NetworkManager

```bash
sudo nmcli c reload
```

### 重新启动所有界面

```bash
sudo systemctl restart network.service
```

### 同时查看 hostname, OS, kernal, architecture !

```bash
hostnamectl
```

### 设置主机名 (一次设置所有临时，静态，漂亮的主机名)

```bash
hostnamectl set-hostname "mynode"
```

## Others

### Bash auto-complete (e.g. show options "now tomorrow never" when you press'tab' after typing "dothis")

[More examples](https://iridakos.com/tutorials/2018/03/01/bash-programmable-completion-tutorial.html)

```bash
complete -W "now tomorrow never" dothis
# ~$ dothis  
# never     now       tomorrow
# press 'tab' again to auto-complete after typing 'n' or 't'
```

### Repeat printing string n times (e.g. print 'hello world' five times)

```bash
printf 'hello world\n%.0s' {1..5}
```

### Encode strings as Base64 strings

```bash
echo test|base64
#dGVzdAo=
```

### Do not echo the trailing newline

```bash
username=`echo -n "bashoneliner"`
```

### Get parent directory of current directory

```bash
dirname `pwd`
```

### Copy a file to multiple files (e.g copy fileA to file(B-D))

```bash
tee <fileA fileB fileC fileD >/dev/null
```

### Remove newline / nextline

```bash
tr --delete '\n' <input.txt >output.txt
```

### Replace newline

```bash
tr '\n' ' ' <filename
```

### To uppercase/lowercase

```bash
tr /a-z/ /A-Z/
```

### Translate a range of characters (e.g. substitute a-z into a)

```bash
echo 'something' |tr a-z a
# aaaaaaaaa
```

### Compare two files (e.g. fileA, fileB)

```bash
diff fileA fileB
# a: added; d:delete; c:changed

# or
sdiff fileA fileB
# side-to-side merge of file differences
```

 



### Compare two files, strip trailing carriage return/ nextline (e.g. fileA, fileB)

```bash
 diff fileA fileB --strip-trailing-cr
```

### Number a file (e.g. fileA)

```bash
nl fileA

#or
nl -nrz fileA
# add leading zeros

#or
nl -w1 -s ' '
# making it simple, blank separate
```

### Join two files field by field with tab (default join by the first column of both file, and default separator is space)

```bash
# fileA and fileB should have the same ordering of lines.
join -t '\t' fileA fileB

# Join using specified field (e.g. column 3 of fileA and column 5 of fileB)
join -1 3 -2 5 fileA fileB
```

### Combine/ paste two or more files into columns (e.g. fileA, fileB, fileC)

```bash
paste fileA fileB fileC
# default tab separate
```

### Reverse string

```bash
echo 12345| rev
```

### Read .gz file without extracting

```bash
zmore filename

# or
zless filename
```

### Run command in background, output error file

```bash
some_commands  &>log &

# or
some_commands 2>log &

# or
some_commands 2>&1| tee logfile

# or
some_commands |& tee logfile

# or
some_commands 2>&1 >>outfile
#0: standard input; 1: standard output; 2: standard error
```

### Run multiple commands in background

```bash
# run sequentially
(sleep 2; sleep 3) &

# run parallelly
sleep 2 & sleep 3 &
```

 



### Run process even when logout (immune to hangups, with output to a non-tty)

```bash
# e.g. Run myscript.sh even when log out.
nohup bash myscript.sh
```

### Send mail

```bash
echo 'heres the content'| mail -a /path/to/attach_file.txt -s 'mail.subject' me@gmail.com
# use -a flag to set send from (-a "From: some@mail.tld")
```

### Convert .xls to csv

```bash
xls2csv filename
```

### Append to file (e.g. hihi)

```bash
echo 'hihi' >>filename
```

### Make BEEP sound

```bash
speaker-test -t sine -f 1000 -l1
```

### Set beep duration

```bash
(speaker-test -t sine -f 1000) & pid=$!;sleep 0.1s;kill -9 $pid
```

### History edit/ delete

```bash
~/.bash_history

#or
history -d [line_number]
```

### Interacting with history

```bash
# list 5 previous command (similar to `history |tail -n 5` but wont print the history command itself)
fc -l -5
```

### Get last history/record filename

```bash
head !$
```

### Clean screen

```bash
clear

# or
Ctrl+l
```

### Send data to last edited file

```bash
cat /directory/to/file
echo 100>!$
```

### Extract .xf

```php
unxz filename.tar.xz
# then
tar -xf filename.tar
```

### Install python package

```bash
pip install packagename
```

### Delete current bash command

```bash
Ctrl+U

# or
Ctrl+C

# or
Alt+Shift+#
# to make it to history
```

 



### Add something to history (e.g. "addmetohistory")

```bash
# addmetodistory
# just add a "#" before~~
```

### Sleep awhile or wait for a moment or schedule a job

```bash
sleep 5;echo hi
```

### Backup with rsync

```bash
rsync -av filename filename.bak
rsync -av directory directory.bak
rsync -av --ignore_existing directory/ directory.bak
rsync -av --update directory directory.bak

rsync -av directory user@ip_address:/path/to/directory.bak
# skip files that are newer on receiver (i prefer this one!)
```

### Make all directories at one time!

```bash
mkdir -p project/{lib/ext,bin,src,doc/{html,info,pdf},demo/stat}
# -p: make parent directory
# this will create project/doc/html/; project/doc/info; project/lib/ext ,etc
```

### Run command only if another command returns zero exit status (well done)

```bash
cd tmp/ && tar xvf ~/a.tar
```

### Run command only if another command returns non-zero exit status (not finish)

```bash
cd tmp/a/b/c ||mkdir -p tmp/a/b/c
```

### Extract to a path

```bash
tar xvf -C /path/to/directory filename.gz
```

### Use backslash "" to break long command

```bash
cd tmp/a/b/c \
> || \
>mkdir -p tmp/a/b/c
```

### Get pwd

```bash
VAR=$PWD; cd ~; tar xvf -C $VAR file.tar
# PWD need to be capital letter
```

### List file type of file (e.g. /tmp/)

```bash
file /tmp/
# tmp/: directory
```

### Bash script

```bash
#!/bin/bash
file=${1#*.}
# remove string before a "."
```

### Python simple HTTP Server

```bash
python -m SimpleHTTPServer
# or when using python3:
python3 -m http.server
```

 



### 读取用户输入

```bash
read input
echo $input
```

### 生成序列 1-10

```bash
seq 10
```

### 查找输入列表 / 文件的平均值

```bash
i=`wc -l filename|cut -d ' ' -f1`; cat filename| echo "scale=2;(`paste -sd+`)/"$i|bc
```

### 生成所有组合 (e.g. 1,2)

```bash
echo {1,2}{1,2}
# 1 1, 1 2, 2 1, 2 2
```

### 生成所有组合 (e.g. A,T,C,G)

```bash
set = {A,T,C,G}
group= 5
for ((i=0; i<$group; i++));do
    repetition=$set$repetition;done
    bash -c "echo "$repetition""
```

### 将文件内容读取到变量

```bash
foo=$(<test1)
```

### 输出变量

```bash
echo ${#foo}
```

### 打印标签

```bash
echo -e ' \t '
```

### 数组

```bash
declare -a array=()

# 或者
declare array=()

# 或者关联数组
declare -A array=()
```

### 发送一个目录

```bash
scp -r directoryname user@ip:/path/to/send
```

### 将文件分割成较小的文件

```bash
# 按行分割 (e.g. 1000 lines/smallfile)
split -d -l 1000 largefile.txt

# 按字节分割而不会在文件间断行
split -C 10 largefile.txt
```

### 创建大量的测试文件 (e.g 100000 个文件，10 字节分割):

```bash
#1\. 创建文件
dd if=/dev/zero of=bigfile bs=1 count=1000000

#2\. 将大文件拆分为100000个10字节文件
 split -b 10 -a 10 bigfile
```

### 重命名所有文件 (e.g. 将所有文件重命名为 ABC)

```bash
rename 's/ABC//' *.gz
```

### 删除文件扩展名 (e.g 删除 filename.gz 的.gz)

```bash
basename filename.gz .gz

zcat filename.gz> $(basename filename.gz .gz).unpacked
```
### Fork bomb

```bash
# Don't try this at home!
# It is a function that calls itself twice every call until you run out of system resources.
# A '# ' is added in front for safety reason, remove it when seriously you are testing it.
# :(){:|:&};:
```

### Add file extension to all file(e.g add .txt)

```bash
rename s/$/.txt/ *
# You can use rename -n s/$/.txt/ * to check the result first, it will only print sth like this:
# rename(a, a.txt)
# rename(b, b.txt)
# rename(c, c.txt)
```

### Use the squeeze repeat option (e.g. /t/t --> /t)

```bash
tr -s "/t" < filename
```

### Do not print nextline with echo

```bash
echo -e 'text here \c'
```

### Use the last argument

```bash
!$
```

### Check last exit code

```bash
echo $?
```

### View first 50 characters of file

```bash
head -c 50 file
```

### Group/combine rows into one row

```bash
# e.g.
# AAAA
# BBBB
# CCCC
# DDDD
cat filename|paste - -
# AAAABBBB
# CCCCDDDD
cat filename|paste - - - -
# AAAABBBBCCCCDDDD
```

### Fastq to fasta

```bash
cat file.fastq | paste - - - - | sed 's/^@/>/g'| cut -f1-2 | tr '\t' '\n' >file.fa
```

### Cut and get last column

```bash
cat file|rev | cut -d/ -f1 | rev
```

### Add one to variable/increment/ i++ a numeric variable (e.g. $var)

```bash
((var++))
# or
var=$((var+1))
```

### Clear the contents of a file (e.g. filename)

```bash
>filename
```

### Unzip tar.bz2 file (e.g. file.tar.bz2)

```bash
tar xvfj file.tar.bz2
```

### Unzip tar.xz file (e.g. file.tar.xz)

```bash
unxz file.tar.xz
tar xopf file.tar
```

### Output a y/n repeatedly until killed

```bash
# 'y':
yes

# or 'n':
yes n

# or 'anything':
yes anything

# For example:
yes | rm -r large_directory
```

 



### Create dummy file of certain size instantly (e.g. 200mb)

```bash
dd if=/dev/zero of=//dev/shm/200m bs=1024k count=200
# or
dd if=/dev/zero of=//dev/shm/200m bs=1M count=200

# Standard output:
# 200+0 records in
# 200+0 records out
# 209715200 bytes (210 MB) copied, 0.0955679 s, 2.2 GB/s
```

### Cat to a file

```bash
cat >myfile
let me add sth here
exit by control + c
^C
```

### Keep /repeatedly executing the same command (e.g Repeat 'wc -l filename' every 1 second)

```bash
watch -n 1 wc -l filename
```

### Print commands and their arguments when execute (e.g. echo `expr 10 + 20 `)

```bash
set -x; echo `expr 10 + 20 `
```

### Print some meaningful sentences to you (install fortune first)

```bash
fortune
```

### Colorful (and useful) version of top (install htop first)

```bash
htop
```

### Press any key to continue

```bash
read -rsp $'Press any key to continue...\n' -n1 key
```

### Run sql-like command on files from terminal

```bash
# download:
# https://github.com/harelba/q
# example:
q -d "," "select c3,c4,c5 from /path/to/file.txt where c3='foo' and c5='boo'"
```

### Using Screen for multiple terminal sessions

```bash
# Create session and attach:
screen

# Create detached session foo:
screen -S foo -d -m

# Detached session foo:
screen: ^a^d

# List sessions:
screen -ls

# Attach last session:
screen -r

# Attach to session foo:
screen -r foo

# Kill session foo:
screen -r foo -X quit

# Scroll:
Hit your screen prefix combination (C-a / control+A), then hit Escape.
Move up/down with the arrow keys (↑ and ↓).

# Redirect output of an already running process in Screen:
 (C-a / control+A), then hit 'H'

# Store screen output for Screen:
Ctrl+A, Shift+H
# You will then find a screen.log file under current directory.
```

### Using

 



### Using Tmux for multiple terminal sessions

```bash
# Create session and attach:
tmux

# Attach to session foo:
tmux attach -t foo

# Detached session foo:
^bd

# List sessions:
tmux ls

# Attach last session:
tmux attach

# Kill session foo:
tmux kill-session -t foo

# Create detached session foo:
tmux new -s foo -d

# Send command to all panes in tmux:
Ctrl-B
:setw synchronize-panes

# Some tmux pane control commands:
Ctrl-B
#   Panes (splits), Press Ctrl+B, then input the following symbol:
#   %  horizontal split
#   "  vertical split
#   o  swap panes
#   q  show pane numbers
#   x  kill pane
#   space - toggle between layouts

#   Distribute Vertically (rows):
select-layout even-vertical
#   or
Ctrl+b, Alt+2

# Distribute horizontally (columns):
select-layout even-horizontal
#   or
Ctrl+b, Alt+1

# Scroll
Ctrl-b then \[ then you can use your normal navigation keys to scroll around.
Press q to quit scroll mode.
```

### Cut the last column

```bash
cat filename|rev|cut -f1|rev
```

### Pass password to ssh

```bash
sshpass -p mypassword ssh root@10.102.14.88 "df -h"
```

### Wait for a pid (job) to complete

```bash
wait %1
# or
wait $PID
wait ${!}
#wait ${!} to wait till the last background process ($! is the PID of the last background process)
```

### Convert pdf to txt

```bash
sudo apt-get install poppler-utils
pdftotext example.pdf example.txt
```

### List only directory

```bash
ls -ld -- */
```

### Capture/record/save terminal output (capture everything you type and output)

```bash
script output.txt
# start using terminal
# to logout the screen session (stop saving the contents), type exit.
```

 



### 以树状格式列出目录的内容。

```bash
tree
# 转到要列出的目录，然后键入 tree (sudo apt-get install tree)
# output:
# home/
# └── project
#     ├── 1
#     ├── 2
#     ├── 3
#     ├── 4
#     └── 5
#

# 设置目录深度等级（例如1级）
tree -L 1
# home/
# └── project
```

### 为 Python 设置 virtualenv（sandbox）

```bash
# 1\. 安装 virtualenv.
sudo apt-get install virtualenv
# 2\. 为新的隔离环境建立目录（将其命名为 .venv 或您想要的任何名称）。
virtualenv .venv
# 3\. 导入 virtual 执行目录
source .venv/bin/activate
# 4\. 您可以检查一下是否现在在沙盒中
type pip
# 5\. 现在您可以安装您的 pip 包, 这里的 requirements.txt 只是一个包含您想要的所有软件包的 txt 文件 (例如 tornado==4.5.3)。
pip install -r requirements.txt
```

### 使用 json 数据

```bash
# 安装非常有用的 jq 包
# sudo apt-get install jq
# e.g. to get all the values of the 'url' key, simply pipe the json to the following jq command(you can use .[]. to select inner json, i.e jq '.[].url')
# 例如，要获取 url 键的所有值，只需将 json 通过管道传递给以下 jq 命令（您可以使用 [] 选择内部 json，即 jq '[].url'）
jq '.url'
```

### 编辑 history

```bash
history -w
vi ~/.bash_history
history -r
```

### 十进制转换为二进制（例如，获取 5 的二进制 ）

```bash
D2B=({0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1}{0..1})
echo -e ${D2B[5]}
#00000101
echo -e ${D2B[255]}
#11111111
```

### 把输入的行换行以适应指定的宽度 (例如每行 4 个整数)

```bash
echo "00110010101110001101" | fold -w4
# 0011
# 0010
# 1011
# 1000
# 1101
```

### 按列对文件进行排序，并保持原始顺序

```bash
sort -k3,3 -s
```

### 列右对齐 (第二列右对齐)

```bash
cat file.txt|rev|column -t|rev
```

### 查看和存储输出

```bash
echo 'hihihihi' | tee outputfile.txt
# tee 带 “-a” 可以附加到文件中
```

### 使用 cat 显示非打印（Ctrl）字符

```bash
cat -v filename
```

### 将制表符转换为空格

```bash
expand filename
```

### 将空格转换为制表符

```bash
unexpand filename
```

### 以八进制显示文件（您也可以使用 od 显示十六进制，十进制等）

```bash
od filename
```

### 反转 `cat` 的结果

```bash
tac filename
```

### 反转 `uniq -c` 的结果

```bash
while read a b; do yes $b |head -n $a ;done <test.txt
```