`vim test.sh`

```shell
#!/bin/bash
echo "hello shell!"
```

`sh test.sh`

# 变量、字符串、数组

```shell
str="hello shell"
echo $str  #echo ${str}   使用变量时在变量前加$
readonly str	#将变量设置为只读
unset str  		#删除变量,但不能删除只读变量
#用语句给变量赋值
for file in `ls /etc`
for file in $(ls /etc)
```

```shell
str1="this is a string"	
#单引号字符串会原样输出,字符串中的变量是无效的
#双引号字符串可以有变量

#使用双引号拼接字符串
str2="hello ,${str1} !"
str3="hello ,"$str" !"
#使用单引号拼接字符串
str4='hello ,'$str' !'

#提取字符串长度
string="abcd"
echo ${#string} #输出 4
#提取子字符串 第2个字符开始截取2个字符
echo ${string:1:2} # 输出 bc
#查找子字符串 查找b的位置
echo `"string" b`  #输出1
```

```shell
#数组名=(值1 值2...)
array_name=(value0 value1 value2 value3)
#读取数组 ${数组名[下标]}
valuen=${array_name[1]}
echo ${array_name[@]}#使用@符号可以获得数组中的所有元素

#获取数组的长度
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}

:<<EOF
长注释方式
EOF
#EOF可以是其它符号
```

# 运算符

```shell
#原生bush不支持简单的数学运算,需要利用其它命令
#expr是一款表达式计算工具
a=`expr 10 + 10`
echo "10+10= $a"  #输出20
#表达式与运算符之间要有空格
b=20
#判断a  b是否相等 条件表达式要放在方括号之间
if [$a == $b]
then
	echo "a等于b"
else
	echo "a不等于b"
fi

c=`expr a \* b`  #使用*时要用到转移符号\
```

以下运算符全部用在方括号之间,且与变量之间存在空格

| 关系运算符(仅支持数字) | 说明         |
| ---------------------- | ------------ |
| `-eq`                  | 是否相等     |
| `-ne`                  | 是否不等     |
| `-gt`                  | 是否大于     |
| `-lt`                  | 是否小于     |
| `-ge`                  | 是否大于等于 |
| `-le`                  | 是否小于等于 |

| 布尔运算符 | 说明 |
| ---------- | ---- |
| !          | 非   |
| -o         | 或   |
| -a         | 与   |

| 逻辑运算符 | 说明    |
| ---------- | ------- |
| &&         | 逻辑AND |
| \|\|       | 逻辑OR  |

| 字符串运算符 | 说明            |
| ------------ | --------------- |
| =            | 字符串是否相等  |
| !=           | 字符串是否不等  |
| -z           | 字符串是否为0   |
| -n           | 字符串是否不为0 |
| $            | 字符串是否为空  |

| 文件检测运算符 | 说明               |
| -------------- | ------------------ |
| -d             | 文件是否为目录     |
| -f             | 文件是否为普通文件 |
| -r             | 文件是否可读       |
| -w             | 文件是否可写       |
| -x             | 文件是否可执行     |
| -s             | 文件是否为空       |
| -e             | 文件是否存在       |

# 流程控制

```shell
#if else-if else 语法格式
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi

#for 循环
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

#while
while condition
do
    command
done

#until
until condition
do
    command
done

#case
case 值 in
模式1)
    commandN
    ;;
模式2）
    commandN
    ;;
esac
```

# 传递参数

脚本内获取参数的格式为:  $n

```shell
#!/bin/bash
# $0 为执行的文件名
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```

`sh test.sh 1 2 3`

```shell
Shell 传递参数实例！
执行的文件名：./test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3
```

| 参数处理 | 说明                                   |
| -------- | -------------------------------------- |
| $#       | 传递到脚本的参数个数                   |
| $*       | 以一个单字符串显示所有向脚本传递的参数 |
| $$       | 脚本运行的当前进程ID号                 |
| $!       | 后台运行的最后一个进程的ID号           |
| $@       | 与$*相似                               |
| $-       | 显示shell使用的当前选项                |
| $?       | 显示最后命令的退出状态                 |

# 函数

类似于c语言中的函数

```shell
sum(){
	echo "a= $1"
	echo "b= $2"
	c=`expr $1 + $2`
	echo "a+b= $c"
}
sum 5 10
```

```
a= 5
b= 10
a+b= 15
```

注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

# 输入/输出重定向

| 命令            | 说明                                             |
| --------------- | ------------------------------------------------ |
| command > file  | 将输出重定向到file(覆盖)                         |
| command < file  | 将输入重定向到file                               |
| command >> file | 将输出以追加的方式重定向到 file                  |
| n > file        | 将文件描述符为 n 的文件重定向到 file             |
| n >> file       | 将文件描述符为 n 的文件以追加的方式重定向到 file |
| n>&m            | 将输出文件 m 和 n 合并。                         |
| n<&m            | 将输入文件 m 和 n 合并。                         |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入 |

#  文件包含

Shell 可包含外部脚本

```shell
. filename   # 注意点号(.)和文件名中间有一空格
#或
source filename
```

