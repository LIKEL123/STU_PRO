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





