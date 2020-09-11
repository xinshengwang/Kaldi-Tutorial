<small>

# Shell基本语法

kaldi中所有执行程序都是通过shell脚本提交，因此了解shell语言的基本语法是入门kaldi的第一步。本文主要介绍Linux标准shell解释器bash的语法. 以下内容大部分整理自[一篇文章让你彻底掌握shell语言](https://www.cnblogs.com/jingmoxukong/p/7867397.html).

## Hello world
```bash
#!/usr/bin/env bash
echo "Hello, world!"
```
在文本编辑器中输入上述两行命令，并将文档命名为 helloworld.sh. 在命令行中执行
```bash
sh path/to/helloworld.sh
```
我们便可以执行shell脚本在屏幕上打印出 "Hello, world". 在该脚本中，我们用到了很有用的命令echo来输出字符串到屏幕上. 另外，第一行用于指定脚本的解释. Shell 的解释器种类众多，常见的有：
- bash (Bourne Again Shell): Linux 标准默认的shell.
- sh (Bourne Shell): sh 是 Unix 标准默认的 shell

**指定脚本解释器** 

在 shell 脚本，#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 解释器。#! 被称作shebang（也称为 Hashbang ）.
- 指定 sh 解释器
  ```bash
  #!/bin/sh
  ```
- 指定 bash 解释器
  ```bash
  #!/bin/bash
  ```
除此之外，也可以用如下方式来指定解释器:
```bash
#! /usr/bin/env bash
```
这样做的好处是，系统会自动在 PATH 环境变量中查找你指定的程序(本例中的bash). 另外，如果安装完新版本的bash，我们可能将其路径添加到PATH中，来“隐藏”老版本。如果直接用#!/bin/bash，那么系统会选择老版本的bash来执行脚本，如果用#!/usr/bin/env bash，则会使用新版本。
## 语法
### 常用命令
**注释**
- 单行注释：以 # 开头，到行尾结束
```bash
# 单行注释
```
- 多行注释：以 :<<EOF 开头，到 EOF 结束。
```bash
:<<EOF
多行注释
多行注释
EOF
```

**echo**

- 输出字符串
```bash
echo "hello, world"
# Output: hello, world
```
- 输出变量
```bash
name=world
echo "hello, ${name}"
# Output: hello, world
```
- 把输出保存到txt文档
  ```bash
  echo "hello, world" > result.txt
  ```

**printf**
printf 用于格式化输出字符串.其并不会像 echo 一样自动添加换行符，如需换行需要手动添加 \n

```bash
# 单引号
printf '%d %s\n' 1 "abc"
#  Output:1 abc

# 双引号
printf "%d %s\n" 1 "abc"
#  Output:1 abc

# 无引号
printf %s abcdef
#  Output: abcdef(并不会换行)

# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出
printf "%s\n" abc def
#  Output:
#  abc
#  def

printf "%s %s %s\n" a b c d e f g h i j
#  Output:
#  a b c
#  d e f
#  g h i
#  j

# 如果没有参数，那么 %s 用 NULL 代替，%d 用 0 代替
printf "%s and %d \n"
#  Output:
#   and 0

# 格式化输出
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876
#  Output:
#  姓名     性别   体重kg
#  郭靖     男      66.12
#  杨过     男      48.65
#  郭芙     女      47.99
```

## 变量

**命名规则** 
命名只能使用英文字母，数字和下划线，首个字符不能以数字开头，变量名不能使用 bash 里的关键字。

**变量声明和访问**
Bash 中没有数据类型，bash 中的变量可以保存一个数字、一个字符、一个字符串等等。同时无需提前声明变量，给变量赋值会直接创建变量。

访问变量的语法形式为：\${var} 和 \$var. 
变量名外面的花括号是可选的，加花括号是为了帮助解释器识别变量的边界。

```bash
word="hello"
echo ${word}
# Output: hello
```

**只读变量**
使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变

```bash
rword="hello"
echo ${rword}
readonly rword
```

**删除变量**
```bash
dword="hello"  # 声明变量
echo ${dword}  # 输出变量值
# Output: hello

unset dword    # 删除变量
echo ${dword}
# Output: （空）
```

**变量类型**
- 局部变量: 局部变量是仅在某个脚本内部有效的变量。它们不能被其他的程序和脚本访问
- 环境变量: 环境变量是对当前 shell 会话内所有的程序或脚本都可见的变量。创建它们跟创建局部变量类似，但使用的是 export 关键字，shell 脚本也可以定义环境变量。

常见的环境变量：
|变量   |描述|
|----   |----|
|$HOME	|当前用户的用户目录
|$PATH	|用分号分隔的目录列表，shell 会到这些目录中查找命令 |
|$PWD	|当前工作目录
|$RANDOM|0 到 32767 之间的整数
|$UID	|数值类型，当前用户的用户 ID
|$PS1	|主要系统输入提示符
|$PS2	|次要系统输入提示符
更多环境变量可参见 [bash 环境变量](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html###sect_03_02_04).


## 字符串
shell 字符串可以用单引号 ''，也可以用双引号 “”，也可以不用引号。
- 单引号的特点
    - 单引号里不能识别变量
    -  单引号里不能出现单独的单引号（使用转义符也不行），但可成对出现，作为字符串拼接使用。
- 双引号的特点
  -  双引号里识别变量
  -  双引号里可以出现转义字符

**拼接字符串**
```bash
# 使用单引号拼接
name1='white'
str1='hello, '${name1}''
str2='hello, ${name1}'
echo ${str1}_${str2}
# Output:
# hello, white_hello, ${name1}

# 使用双引号拼接
name2="black"
str3="hello, "${name2}""
str4="hello, ${name2}"
echo ${str3}_${str4}
# Output:
# hello, black_hello, black
```

**获取字符串长度**
```bash
text="12345"
echo ${#text}
# Output:
# 5
```

**截取子字符串**
```bash
text="world"
echo ${text:0:2} #从第0个开始，截取2个
# Output:
# wo
```

**查找字符串**
```bash
text="hello"
echo `expr index "${text}" ll`  #查找 ll 子字符在 hello 字符串中的起始位置。
# Output:
# 3
```
## 数组

bash 只支持一维数组。数组下标从 0 开始，下标可以是整数或算术表达式，其值应大于或等于 0。

```bash
#创建数组
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")

echo "nums" ${nums[@]}  # 访问数组中所有元素
echo "nums0" ${nums[0]}
echo "nums1" ${nums[1]}
echo "nums2" ${nums[2]}
echo "colors" ${colors[@]}
echo "colors0" ${colors[0]} 
echo "colors1" ${colors[1]}
echo "colors2" ${colors[2]}  

# Outputs:
# nums 0 1 2
# nums0 0
# nums1 1
# nums2 2
# colors red yellow dark blue
# colors0 red
# colors1 yellow
# colors2 dark blue
```
**查看数组长度**
```bash
echo ${#nums[*]}
# Output:
# 3
```
**向数组中添加元素**
```bash
colors=(white "${colors[@]}" green black)
echo ${colors[@]}
# Output:
# white red yellow dark blue green black
```
**从数组中删除元素**
```bash
unset nums[0]
echo ${nums[@]}
# Output:
# 1 2 
```

## 运算符
**算术运算符**
|运算符	|说明	|举例 x=10, y=20|
|----|----|----|----|
|+	|加法	|expr $x + $y 结果为 30。|
|-	|减法	|expr $x - $y 结果为 -10。|
|*	|乘法	|expr $x * $y 结果为 200。|
|/	|除法	|expr $y / $x 结果为 2。|
|%	|取余	|expr $y % $x 结果为 0。|
|=	|赋值	|x=$y 将把变量 y 的值赋给 x。|
|==	|相等。用于比较两个数字，相同则返回 true。	|[ $x == $y ] 返回 false。|
|!=	|不相等。用于比较两个数字，不相同则返回 true。	|[ $x != $y ] 返回 true。|

```bash
x=10
y=20

echo "x=${x}, y=${y}"    #Output: #  x=10, y=20

val=`expr ${x} + ${y}`
echo "${x} + ${y} = $val" #Output: 10 + 20 = 30

val=`expr ${x} - ${y}`
echo "${x} - ${y} = $val" #Output: 10 - 20 = -10

val=`expr ${x} \* ${y}`
echo "${x} * ${y} = $val" #Output: 10 * 20 = 200

val=`expr ${y} / ${x}`
echo "${y} / ${x} = $val" #Output: 20 / 10 = 2

val=`expr ${y} % ${x}`
echo "${y} % ${x} = $val" #Output: 20 % 10 = 0

if [[ ${x} == ${y} ]]
then
  echo "${x} = ${y}" #Output:
fi
if [[ ${x} != ${y} ]]
then
  echo "${x} != ${y}" #Output: 10 != 20
fi

```
**关系运算符**
关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
|运算符	|说明	|举例 x=10,y=20|
|----|----|----|
|-eq	|检测两个数是否相等，相等返回 true。	|[ $x -eq $y ]返回 false。|
|-ne	|检测两个数是否相等，不相等返回 true。	|[ $x -ne $y ] 返回 true。|
|-gt	|检测左边的数是否大于右边的，如果是，则返回 true。	|[ $x -gt $y ] 返回 false。|
|-lt	|检测左边的数是否小于右边的，如果是，则返回 true。	|[ $x -lt $y ] 返回 true。|
|-ge	|检测左边的数是否大于等于右边的，如果是，则返回 true。	|[ $x -ge $y ] 返回 false。|
|-le	|检测左边的数是否小于等于右边的，如果是，则返回 true。	|[ $x -le $y ]返回 true。|

**布尔运算符**
|运算符	|说明	|举例 x=10,y=20|
|----|----|----|
|!	|非运算，表达式为 true 则返回 false，否则返回 true。|	[ ! false ] 返回 true。|
|-o	|或运算，有一个表达式为 true 则返回 true。|	[ $x -lt 20 -o $y -gt 100 ] 返回 true。|
|-a	|与运算，两个表达式都为 true 才返回 true。|	[ $x -lt 20 -a $y -gt 100 ] 返回 false。|

**逻辑运算符**
|运算符	|说明	|举例 x=10,y=20|
|----|----|----|
|&&	|逻辑的 AND	| [ ${x} -lt 100 && ${y} -gt 100] 返回 false|
|\|\|	|逻辑的 OR	| [ ${x} -lt 100 \|\| ${y} -gt 100 ] 返回 true|

## 字符串运算符
|运算符	|说明	|举例 a="abc",y="efg"|
|----|----|----|
|=	|检测两个字符串是否相等，相等返回 true。	|[ $a = $b ] 返回 false。|
|!=	|检测两个字符串是否相等，不相等返回 true。	|[ $a != $b ] 返回 true。|
|-z	|检测字符串长度是否为 0，为 0 返回 true。	|[ -z $a ] 返回 false。|
|-n	|检测字符串长度是否为 0，不为 0 返回 true。	|[ -n $a ] 返回 true。|
|str	|检测字符串是否为空，不为空返回 true。	|[ $a ] 返回 true。|

## 文件测试运算符
|操作符	|说明	|举例 |
|----|----|----|
|-b file	|检测文件是否是块设备文件，如果是，则返回 true。	|[ -b $file ] 返回 false。|
|-c file	|检测文件是否是字符设备文件，如果是，则返回 true。	|[ -c $file ] 返回 false。|
|-d file	|检测文件是否是目录，如果是，则返回 true。	|[ -d $file ] 返回 false。|
|-f file	|检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	|[ -f $file ] 返回 true。|
|-g file	|检测文件是否设置了 SGID 位，如果是，则返回 true。	|[ -g $file ] 返回 false。|
|-k file	|检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	|[ -k $file ]返回 false。|
|-p file	|检测文件是否是有名管道，如果是，则返回 true。	|[ -p $file ] 返回 false。|
|-u file	|检测文件是否设置了 SUID 位，如果是，则返回 true。	|[ -u $file ] 返回 false。|
|-r file	|检测文件是否可读，如果是，则返回 true。	|[ -r $file ] 返回 true。|
|-w file	|检测文件是否可写，如果是，则返回 true。	|[ -w $file ] 返回 true。|
|-x file	|检测文件是否可执行，如果是，则返回 true。	|[ -x $file ] 返回 true。|
|-s file	|检测文件是否为空（文件大小是否大于 0），不为空返回 true。	|[ -s $file ] 返回 true。|
|-e file	|检测文件（包括目录）是否存在，如果是，则返回 true。	|[ -e $file ] 返回 true。|

```bash
file="/etc/hosts"
if [[ -d ${file} ]]; then
   echo "${file} 文件是个目录"
else
   echo "${file} 文件不是个目录"
```

## 控制语句
**条件语句**
由[[ ]]（sh中是[ ]）包起来的表达式被称作检测命令或基元。这些表达式帮助我们检测一个条件的结果.
- if 语句
  ```bash
  # 写成一行
  if [[ 1 -eq 1 ]]; then echo "1 -eq 1 result is: true"; fi
  # Output: 1 -eq 1 result is: true

  # 写成多行
  if [[ "abc" -eq "abc" ]]
  then
    echo ""abc" -eq "abc" result is: true"
  fi
  # Output: abc -eq abc result is: true
  ```
- if else 语句
  ```bash
  if [[ 2 -ne 1 ]]; then
    echo "true"
  else
    echo "false"
  fi
  # Output: true
  ```

- if elif else 语句
  ```bash
  x=10
  y=20
  if [[ ${x} > ${y} ]]; then
    echo "${x} > ${y}"
  elif [[ ${x} < ${y} ]]; then
    echo "${x} < ${y}"
  else
    echo "${x} = ${y}"
  fi
  # Output: 10 < 20
  ```
**循环语句**
Bash中有四种循环: for, while, until 和 select
- for 循环
  ```bash
  # 写成多行
  for arg in elem1 elem2 ... elemN
  do
    ### 语句
  done

  # 写成单行
  for i in {1..5}; do echo $i; done

  # 对于递增数组的循环
  for (( i = 0; i < 10; i++ )); do
    echo $i
  done
  ```
利用for循环移动文件
  ```bash
  # 把所有的.sh文件移动到scripts文件夹中
  DIR=/home/directory
  for FILE in ${DIR}/*.sh; do
    mv "$FILE" "${DIR}/scripts"
  done
  # 将 /home/zp 目录下所有 sh 文件拷贝到 /home/zp/scripts
  ```
- while 循环
```bash
while [[ condition ]]
do
  ### 语句
done
```
- util 循环
  util 循环跟 while 正好相反. 它跟while一样也需要检测一个测试条件，但不同的是，只要该条件为 假 就一直执行循环：
```bash
  x=0
  until [[ ${x} -ge 5 ]]; do
    echo ${x}
    x=`expr ${x} + 1`
  done
  #  Output:
  #  0
  #  1
  #  2
  #  3
  #  4
```
**select 循环**
select 循环帮助我们组织一个用户菜单。它的语法几乎跟 for 循环一致：
```bash
select answer in elem1 elem2 ... elemN
do
  ### 语句
done
```

## 函数

## Debug

shell 提供了用于 debug 脚本的工具。如果想采用 debug 模式运行某脚本，可以在其 shebang 中使用一个特殊的选项：
```bash
#!/bin/bash options
```
options 是一些可以改变 shell 行为的选项。下表是一些可能对你有用的选项：

|Short	|Name	|Description|
|----|----|----|----|
|-f	|noglob	|禁止文件名展开（globbing）|
|-i	|interactive	|让脚本以 交互 模式运行|
|-n	|noexec	|读取命令，但不执行（语法检查）|
|-t	|—	|执行完第一条命令后退出|
|-v	|verbose	|在执行每条命令前，向stderr输出该命令|
|-x	|xtrace	|在执行每条命令前，向stderr输出该命令以及该命令的扩展参数|

举个例子，如果我们在脚本中指定了-x例如：
```bash
#!/bin/bash -x

for (( i = 0; i < 3; i++ )); do
  echo $i
done
```
这会向stdout打印出变量的值和一些其它有用的信息：
```bash
$ ./my_script
+ (( i = 0 ))
+ (( i < 3 ))
+ echo 0
0
+ (( i++  ))
+ (( i < 3 ))
+ echo 1
1
+ (( i++  ))
+ (( i < 3 ))
+ echo 2
2
+ (( i++  ))
+ (( i < 3 ))

```

有时我们值需要 debug 脚本的一部分。这种情况下，使用set命令会很方便。这个命令可以启用或禁用选项。使用-启用选项，+禁用选项：
```bash
# 开启 debug
set -x
for (( i = 0; i < 3; i++ )); do
  printf ${i}
done
# 关闭 debug
set +x
#  Output:
#  + (( i = 0 ))
#  + (( i < 3 ))
#  + printf 0
#  0+ (( i++  ))
#  + (( i < 3 ))
#  + printf 1
#  1+ (( i++  ))
#  + (( i < 3 ))
#  + printf 2
#  2+ (( i++  ))
#  + (( i < 3 ))
#  + set +x

for i in {1..5}; do printf ${i}; done
printf "\n"
#  Output: 12345
```
## 参考资料

https://www.cnblogs.com/jingmoxukong/p/7867397.html





</small>