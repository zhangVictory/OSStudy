<center><font size="8" color="#e9c341">for循环</font></center>

[TOC]

# 1 for循环基本格式

for循环语句在窗口和bat脚本中是有差别的：

在cmd窗口中，格式如下：

`FOR %variable IN (set) DO command [command-parameters]`

在bat脚本中，格式如下：

`FOR %%variable IN (set) DO command [command-parameters]`

例如：`for %i in (1 2 3) do @echo %i`

> 注意：
>
> 1.  %i中的i只能是一个字符，建议使用26个英文字母，不要使用数字，%i区分大小写
>
> 		    2. in和do之间的set表示的字符串或变量可以是一个，也可以是多个，每一个字符串或变量，我们称之为一个元素，每个元素之间，用空格 键、跳格键、逗号、分号或等号分隔；
>    
>       		    3. for语句依次提取command1中的每一个元素，把它的值赋予形式变量I,带到do后的command2中参与命令的执行；并且每次只提取一个元素，然后执行一次do后的命令语句，而无论这个元素是否被带到command2中参与了command2的运行；当执行完一次do后的语句之后，再提取command1中的下一个元素，再执行一次command2，如此循环，直到command1中的所有元素都已经被提取完毕，该for语句才宣告执行结束；

# 2 for简单应用

## 2.1 列出本机所有磁盘分区

```bat
@echo off
chcp 65001 
set str=c d e f g h i j k l m n o p q r s t u v w x y z
echo 当前硬盘的分区有：
for %%i in (%str%) do if exist %%i: echo %%i:
pause
```

## 2.2 列出当前文件夹的所有文件

```bat
@echo off
for %%i in (*.*) do echo %%i
```

## 2.3 列出指定后缀名的文件

```bat
@echo off
for %%i in (*.txt) do echo %%i
```

## 2.4 使用通配符

2.2 和2.3中的*号，表示任意字符，出现任意次数

```bat
@echo off
rem 列出文件名称最多只有两个字符的文件
for %%i in (??.txt) do echo %%i
```

？号表示任意字符，最多出现一次

# 3 高级用法

## 3.1 for /f文本解析

逐行打印文本内容

保存一下内容到1.txt

> 论坛的目标是：不求最大，但求最好，做最实用的批处理论坛。
> 论坛地址：bbs.bathome.net。
> 这里是：新手晋级的福地，高手论剑的天堂。

```bat
@echo off
for /f %%i in(1.txt) do echo %%i & pause
```

## 3.2 字符串切分：delims=

```bat
@echo off
for /f "delims=，" %%i in(1.txt) do echo %%i & pause
```

> 结果为：
>
> 论坛的目标是：不求最大
> 论坛地址：bbs.bathome.net。
> 这里是：新手晋级的福地

"delims=，"会按照中文逗号，对数据进行切分，

```bat
@echo off
for /f "delims=，." %%i in(1.txt) do echo %%i & pause
```

执行过程是：逐行读取test.txt中的内容，以点号和逗号切分每一行的内容（不存在点号和逗号的行，则不再切分，为了描述的方便，我们把被点号或逗号切分的一个一个的字符串片段，称之为节），然后，for /f 会提取第一节的内容作为最终结果，显示在屏幕上。需要注意的是，在这里，所有行的字符串被切分成了两个以上的节，但是 for /f 语句默认只提取第一节的字符串。

## 3.3 字符串提取：tokens=

 for /f 语句默认只提取第一节的字符串，如何提取其他节的内容呢？可以使用tokens=节的编号(从1开始)

例如：有以下文本1.txt

> 尺有所短，寸有所长，学好批处理没商量，考虑问题复杂化，解决问题简洁化。

如何提取出**学好批处理没商量**，这句话呢？参考以下代码：

```bat
@echo off
for /f "delims=， tokens=3" %%i in (1.txt) do echo %%i
pause
```

但我们要先显示多个节呢？可以在tokens后面是有逗号分隔，添加其他节的编号，例如tokens=3，4，此时还需要注意，如果 tokens= 后面指定了多个数字，如果形式变量为%%i，那么，第一个数字指代的内容用第一个形式变量%%i来接收，第二个数字指代的内容用第二个形式变量%%j来接收，第三个数字指代的内容用第三个形式变量%%k来接收……第N个数字指代的内容用第N个形式变量来接收，其中，形式变量遵循字母的排序，第N个形式变量具体是什么符号，由第一个形式变量来决定：如果第一个形式变量是%%i，那么，第二个形式变量就是%%j；如果第一个形式变量用的是%%x，那么，第二个形式变量就是%%y。

例如，把1.txt中文本中的逗号，替换成空格，然后打印出来的代码可以参考：

```bat
@echo off
for /f "delims=， tokens=1,2,3,4,5" %%i in (1.txt) do echo %%i %%j %%k %%l %%m
pause
```

tokens=1,2,3,4,5可以简化为`tokens=1-5`，除此之外，类似于1-5的简化符号可以和逗号拼接联合使用，例如：`tokens=1-3,4,7`

还有一种特殊写法：

```bat
@echo off
for /f "delims=， tokens=1,*" %%i in (1.txt) do echo %%i %%j
pause
```

`tokens=1,*`，表示提取第一节，剩余的不需要切分，保持原样

> tokens=后面所接的星号具备这样的功能：字符串从左往右被切分成紧跟在*之前的数值所表示的节数之后，字符串的其余部分保持不变，整体被*所表示的一个变量接收。

## 3.4 跳行：skip=n

`for /f`默认一行一行的处理文本，当已经知道某些行是不需要的时候，可以直接跳过，`skip=n`可以跳过前n行，从n+1行开始处理

## 3.5 指定忽略行规则：elo=

cmd窗口使用`for /?`，可以查看for的帮助文档。其中elo的作用是，忽略以指定字符开头的行，只能指定一个字符。`for /f`默认会忽略以`;`开头的行，如何让`for /?`不默认忽略以`;`开头的行呢？可以使用`delims=;`，也可以让elo=一个非分号的字符，或直接写elo=

## 3.7 句式选择

for /f %%i in (……) do (……) 语句有好几种变形语句，不同之处在于第一个括号里的内容：有的是用单引号括起来，有的是用双引号包住，有的不用任何符号包裹，具体格式为：

> 1、for /f %%i in (文件名) do (……)
> 2、for /f %%i in ('命令语句') do (……)
> 3、for /f %%i in ("字符串") do (……)

1. 当你希望读取文本文件中的内容的话，第一个括号中不用任何符号包裹，应该使用的是第1条语句；例如：你想显示test.txt中的内容，那么，就使用 for /f %%i in (test.txt) do echo %%i；
2. 当你读取的是命令语句执行结果中的内容的话，第一个括号中的命令语句必须使用单引号包裹，应该使用的是第2条语句；例如：你想显示当前目录下文件名中含有test字符串的文本文件的时候，应该使用 for /f %%i in ('dir /a-d /b *test*.txt') do echo %%i 这样的语句；
3. 当你要处理的是一个字符串的时候，第一个括号中的内容必须用双引号括起来，应该是用的是第3条语句；例如：当你想把bbs.bathome.net这串字符中的点号换为短横线并显示出来的话，可以使用 for /f "delims=. tokens=1-3" %%i in ("bbs.bathome.net") do echo %%i-%%j-%%k 这样的语句。

很显然，第一个括号里是否需要用符号包裹起来，以及使用什么样的符号包裹，取决于要处理的对象属于什么类型：**如果是文件，则无需包裹；如果是命令语句，则用单引号包裹；如果是字符串，则使用双引号括起来**。

当文件名中有类似于空格，&等特殊字符时，需要使用弄一个关键字来指定`usebackq`。usebackq 是一个增强型参数，当使用了这个参数之后，原来的for语句中第一个括号内的写法要做如下变动：如果第一个括号里的对象是一条命令语句的话，原来的单引号'要改为后引号`；如果第一个括号里的对象是字符串的话，原来的双引号"要改为单引号'；如果第一个括号里的对象是文件名的话，要用双引号"括起来。

例如`set`命令会打印处当前系统的环境变量，当我们需要获取set命令的输出的时候，可以参考下面的代码：

```bat
@echo off
for /f "usebackq delims==" %%i in (`set`) do echo 环境变量名称：%%i
pause;
```

加上`usebackq`，`for /f`命令一共有下面几种格式：

> 　　1、for /f %%i in (文件名) do (……)
> 　　2、for /f %%i in ('命令语句') do (……)
> 　　3、for /f %%i in ("字符串") do (……)
> 　　4、for /f "usebackq" %%i in ("文件名") do (……)
> 　　5、for /f "usebackq" %%i in (`命令语句`) do (……)
> 　　6、for /f "usebackq" %%i in ('字符串') do (……)