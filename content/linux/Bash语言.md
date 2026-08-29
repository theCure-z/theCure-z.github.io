Q:Bash是程序吗
A:Bash本身就是一个系统软件,属于shell类别,

Bash遵循REPL交互模式:

1. READ:读取输入命令
2. EVAL:执行命令
3. PRINT:输出结果
4. LOOP:等待下一个输入


# Variable
## 常用Variable

\$HOME     # 当前用户家目录
\$PWD      # 当前工作目录
\$OLDPWD   # 上一个目录
\$PATH     # 可执行文件搜索路径
\$USER     # 当前用户名
\$UID      # 用户ID
\$SHELL    # 当前使用的shell
\$HOSTNAME	 #主机名称
\$?	#上一个命令的返回值 0=>成功 非0=>失败
\$@	#所有参数的列表
\$#	#输入参数数量
**使用变量时请"$Varible"否则可能导致优化某些符号**

## 声明Variable

```bash
//直接声明
foo='hello   world'
echo "$foo" => hello world

//取消
unset foo

//使用其他程序
foo=$(ls -a)
```

# 语法
## for

```bash
//类似python的for-in-loop
code:
for var in a b c
do
		echo "$var"
done

output:
a
b
c

//类似c的c-loop
code:
max=5
for ((i=0;i < max;i++)) #双括号能解析变量不需要$
do 
	echo "$i"
done

output:
1
2
3
4
5
```

## 运行时输入

```bash
#内置函数类似scanf
func:
read -p 'Enter: ' foo
echo "$foo"

#可以通过重定向完成输入流
echo var | ./foo

output:
var
```

## 命令行参数

```bash
#通过终端完成调用时传参
code:
name=$1
echo "hello $name"

input:
./foo.sh zzz

output:
hello zzz
---------------------------
code:
for var in "$@":
do
	echo "now $var"
done

input:
./foo.sh 1 2 3 4

output:
now 1
now 2
now 3
now 4
```

## 函数

```bash
code:
greet(){
	local name=$1 #默认为全局参数,添加local避免污染
	echo "hello $name"
	return 5
}

for name in "$@"
do
	greet "$name" >> text.txt #函数就像迷你程序,拥有参数列表$@和标准输入输出流
done
var=$(greet var) #通过输出流完成变量声明
echo "var is $var"

echo "$?" #函数同样影响命令的返回值

input:
./foo.sh 1 2 3

output:
hello 1
hello 2
hello 3
var is hello var
5
```

## 条件判断

```bash
a=2
b=2
if [[ $a == $b ]] //OR// [[ $a != $b ]]
then
	echo "same"
fi

if [[ -f file.txt ]] #-f检查文件是否存在
then
	do something
fi

while [[ -f file.txt ]]
do
 do something
done

if true #也可以不加条件直接使用$?,true也是一个程序return 0哦
then 
	truly do something
else
	falsely do something 
fi
```