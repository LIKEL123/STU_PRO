# Shell 基础编程

### 第三章 系统预定义变量

export 设定为全局变量

unset 撤销变量

特殊变量

```properties
1: $n	（功能描述：n为数字，$0代表该脚本名称，$1-$9代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}）
2: $#	（功能描述：获取所有输入参数个数，常用于循环）。
3 :$*	（功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）
4: $@	（功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）
5: $？	（功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）
```

### 第四章 运算符

基本语法： “$((运算式))”或“$[运算式]” 

```properties
计算（2+3）X4的值
s=$((1+1))
[rootu@hadoop101 datas]# S=$[(2+3)*4]
[root@hadoop101 datas]# echo $S
```

### 第五章 条件判断

```properties
1）基本语法
（1）test condition   test 3 -gt 5
（2）[ condition ]（注意condition前后要有空格）  [ 3 -gt 5 ]
注意：条件非空即为true，[ atguigu ]返回true，[] 返回false。
2）常用判断条件
（1）两个整数之间比较
= 字符串比较
-lt 小于（less than）			-le 小于等于（less equal）
-eq 等于（equal）				-gt 大于（greater than）
-ge 大于等于（greater equal）		-ne 不等于（Not equal）
（2）按照文件权限进行判断
-r 有读的权限（read）			-w 有写的权限（write）
-x 有执行的权限（execute）
（3）按照文件类型进行判断
-f 文件存在并且是一个常规的文件（file）
-e 文件存在（existence）		-d 文件存在并是一个目录（directory）
```

```shell
 [ -x zookeeper.out ] && echo true || echo false  # 判断这个文件是否具有x执行权限
```

### 第六章 流程控制

#### if 判断

基本语法：

```shell
if [ 条件判断式 ];then 
  程序 
fi 
或者 
if [ 条件判断式 ] 
  then 
    程序 
elif [ 条件判断式 ]
	then
		程序
else
	程序
fi
	注意事项：
（1）[ 条件判断式 ]，中括号和条件判断式之间必须有空格
（2）if后要有空格
```



#### case语句

基本语法：

```shell
case $变量名 in 
  "值1"） 
    如果变量的值等于值1，则执行程序1 
    ;; 
  "值2"） 
    如果变量的值等于值2，则执行程序2 
    ;; 
  …省略其他分支… 
  *） 
    如果变量的值都不是以上的值，则执行此程序 
    ;; 
esac
注意事项：
（1）case行尾必须为单词“in”，每一个模式匹配必须以右括号“）”结束。
（2）双分号“;;”表示命令序列结束，相当于java中的break。
（3）最后的“*）”表示默认模式，相当于java中的default。
```

#### for循环

基本语法1 ：

```shell

for (( 初始值;循环控制条件;变量变化 )) 
  do 
    程序 
  done
```

基本语法2：

```shell
相当于python的for循环
for 变量 in 值1 值2 值3… 
  do 
    程序 
  done
```

#### while循环

基本语法：

```shell
while [ 条件判断式 ] 
  do 
    程序
  done
```

### 第七章  read读取控制台输入

```properties
基本语法
read(选项)(参数)
选项：
-p：指定读取值时的提示符；
-t：指定读取值时等待的时间（秒）。
参数
	变量：指定读取值的变量名
```

```shell
提示7秒内，读取控制台输入的名称
[atguigu@hadoop101 datas]$ touch read.sh
[atguigu@hadoop101 datas]$ vim read.sh

#!/bin/bash

read -t 7 -p "Enter your name in 7 seconds " NAME
echo $NAME

[atguigu@hadoop101 datas]$ ./read.sh 
Enter your name in 7 seconds xiaoze
xiaoze
```

### 第八章 函数

```she
系统函数 	
basename
基本语法
basename [string / pathname] [suffix]  	（功能描述：basename命令会删掉所有的前缀包括最后一个（‘/’）字符，然后将字符串显示出来。


dirname
基本语法
	dirname 文件绝对路径		（功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））
```

```shell
自定义函数
1）基本语法
[ function ] funname[()]
{
	Action;
	[return int;]
}
funname
2）经验技巧
（1）必须在调用函数地方之前，先声明函数，shell脚本是逐行运行。不会像其它语言一样先编译。
（2）函数返回值，只能通过$?系统变量获得，可以显示加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。return后跟数值n(0-255)
3）案例实操
计算两个输入参数的和
[atguigu@hadoop101 datas]$ touch fun.sh
[atguigu@hadoop101 datas]$ vim fun.sh

#!/bin/bash
function sum()
{
    s=0
    s=$[ $1 + $2 ]
    echo "$s"
}

read -p "Please input the number1: " n1;
read -p "Please input the number2: " n2;
sum $n1 $n2;

[atguigu@hadoop101 datas]$ chmod 777 fun.sh
[atguigu@hadoop101 datas]$ ./fun.sh 
Please input the number1: 2
Please input the number2: 5
7
```

### 第九章 Shell工具 重点**

##### 

+ cut 

基础语法：

```shell
基本用法
cut [选项参数]  filename
说明：默认分隔符是制表符
2）选项参数说明
选项参数	功能
-f	列号，提取第几列
-d	分隔符，按照指定分隔符分割列
-c	指定具体的字符
```

案例实操：

```shell
案例实操
（1）数据准备
[atguigu@hadoop101 datas]$ touch cut.txt
[atguigu@hadoop101 datas]$ vim cut.txt
dong shen
guan zhen
wo  wo
lai  lai
le  le
（2）切割cut.txt第一列
[atguigu@hadoop101 datas]$ cut -d " " -f 1 cut.txt 
dong
guan
wo
lai
le
（3）切割cut.txt第二、三列
[atguigu@hadoop101 datas]$ cut -d " " -f 2,3 cut.txt 
shen
zhen
wo
lai
le
（4）在cut.txt文件中切割出guan
[atguigu@hadoop101 datas]$ cat cut.txt | grep "guan" | cut -d " " -f 1
guan
（5）选取系统PATH变量值，第2个“：”开始后的所有路径：
[atguigu@hadoop101 datas]$ echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/atguigu/bin

[atguigu@hadoop102 datas]$ echo $PATH | cut -d: -f 3-
/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/atguigu/bin
（6）切割ifconfig 后打印的IP地址
[atguigu@hadoop101 datas]$ ifconfig ens33 | grep netmask | cut -d "n" -f 2 | cut -d " " -f 2
192.168.1.102
```

+ awk

  **基本用法**

  ```shell
  awk [选项参数] ‘pattern1{action1}  pattern2{action2}...’ filename
  pattern：表示AWK在数据中查找的内容，就是匹配模式
  action：在找到匹配内容时所执行的一系列命令
  
  选项参数说明
  选项参数	功能
  -F	指定输入文件折分隔符
  -v	赋值一个用户定义变量
  ```

  ```shell
  （1）数据准备
  [atguigu@hadoop102 datas]$ sudo cp /etc/passwd ./
  （2）搜索passwd文件以root关键字开头的所有行，并输出该行的第7列。
  [atguigu@hadoop102 datas]$ awk -F: '/^root/{print $7}' passwd 
  /bin/bash
  （3）搜索passwd文件以root关键字开头的所有行，并输出该行的第1列和第7列，中间以“，”号分割。
  [atguigu@hadoop102 datas]$ awk -F: '/^root/{print $1","$7}' passwd 
  root,/bin/bash
  注意：只有匹配了pattern的行才会执行action
  （4）只显示/etc/passwd的第一列和第七列，以逗号分割，且在所有行前面添加列名user，shell在最后一行添加"dahaige，/bin/zuishuai"。
  [atguigu@hadoop102 datas]$ awk -F : 'BEGIN{print "user, shell"} {print $1","$7} END{print "dahaige,/bin/zuishuai"}' passwd
  user, shell
  root,/bin/bash
  bin,/sbin/nologin
  。。。
  atguigu,/bin/bash
  dahaige,/bin/zuishuai
  注意：BEGIN 在所有数据读取行之前执行；END 在所有数据执行之后执行。
  （5）将passwd文件中的用户id增加数值1并输出
  [atguigu@hadoop102 datas]$ awk -v i=1 -F: '{print $3+i} ' passwd
  1
  2
  3
  4
  4）awk的内置变量
  变量	说明
  FILENAME	文件名
  NR	已读的记录数
  NF	浏览记录的域的个数（切割后，列的个数）
  5）案例实操
  （1）统计passwd文件名，每行的行号，每行的列数
  [atguigu@hadoop102 datas]$ awk -F: '{print "filename:"  FILENAME ", linenumber:" NR  ",columns:" NF}' passwd 
  filename:passwd, linenumber:1,columns:7
  filename:passwd, linenumber:2,columns:7
  filename:passwd, linenumber:3,columns:7
  （2）切割IP
  [atguigu@hadoop102 datas]$ ifconfig ens33 | grep netmask | awk -F " " '{print $2}'
  192.168.1.102
  （3）查询sed.txt中空行所在的行号
  [atguigu@hadoop102 datas]$ awk '/^$/{print NR}' sed.txt 
  5
  ```

  





