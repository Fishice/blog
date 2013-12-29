#AWK 教程
##第一章 awk概述
###为什么使用awk

awk 是一种程序语言。 它具有一般程序语言常见的功能。 因awk语言具有某些特点， 如：使用直译器(Interpreter)不需先行编译； 变量无类型之分(Typeless)， 可使用文字当数组的下标 (Associative Array)。。。等特色。 因此，使用awk撰写程序比起使用其它语言更简洁便利且节省时间。awk还具有一些内建功能，使得awk擅于处理具数据行(Record)， 字段(Field)型态的资料； 此外， awk 内建有管道（pipe） 的功能，可将处理中的数据传送给外部的Shell命令加以处理， 再将Shell命令处理后的数据传回awk程序， 这个特点也使得awk程序很容易使用系统资源。

由于awk具有上述特色，在问题处理的过程中，可轻易使用awk来撰写一些小工具；这些小工具并非用来解整个大问题，它们只扮演解个别问题过程的某些角色， 可藉由Shell所提供的管道（pipe）将数据按需要传送给不同的小工具进行处理，以解决整个大的问题。 这种解决方式， 使得这些小工具可因不同需求而被重复组合及重用(reuse)； 也可藉此方式来先行测试大程序原型的可行性与正确性，将来若需要较高的执行速度时再用C语言来改写。这是awk最常被应用之处。 若能常常如此处理问题，  读者可以以更高的角度来思考抽象的问题，不会被拘泥于细节的部份。 

###如何取得awk
一般的LINUX/UNIX操作系统， 都默认安装了awk。 只不过awk版本可能不太相同。

###awk如何工作
为方便解释awk程序的框架结构和有关术语(terminology)， 先以一个员工薪工资档案(emp.dat )， 进行介绍，文件内容如下：

| ID | Name   | M | H |
|----|--------|---|---|
|A101|chenying|100|210|
|A304|linxiyu |110|215|
|S283|degnming|130|209|
|S267|liuchao |125|220|
|B108|hejing  |95 |210|

文件中各字段依次为：员工ID， 姓名， 薪资率，及实际工时。 ID中的第一码为部门名称代码，。 “A”，”B”，“S”分别表示”“accp部门”，“benet部门”，“市场部”。

这里主要说明awk程序的主要架构及工作原理， 并对一些重要的名词进行必要的解释。  并体会一下awk语言的主要精神和awk与其它语程序言的不同之处。 首先了解一下几个名词定义：

**名词定义**

* 数据行: awk从数据文件上读取数据的基本单位，awk在进行数据处理的时候是以行为单位的，grep也是以行为单位进行数据处理的。以emp.dat为例， awk读入的第一个数据行是第一行 “A125 Jenny 100 210″ ，第二个数据行是第二行 “A341 Dan 110 215″ ，其他的依次类推，
* 字段(Field) : 为数据行上被分隔开的子字符串。 以数据行”A125 Jenny 100 210″为例， 第一栏 第二栏 第三栏 第四栏的内容分别是： “A125″ ，”Jenny”， 100， 210 ，默认情况下，awk以空格分隔各个字段。



##第二章 awk的运行方式

在linux/UNIX 的命令行上输入一下格式的指令： ( “$”表Shell命令行上 的提示符号)

`$awk   ‘awk程序’   数据文件名`
	
上面这条语句中，awk会先编译该程序， 然后执行该程序来处理所指定的数据文件。

###awk程序的主要结构
awk程序中主要语法是 `Pattern {Actions}`， 所以常见的awk 程序的机构如下：

```
Pattern1 { Actions1 }
Pattern2 { Actions2 }
……
Pattern3 { Actions3 }
```
	
**Pattern 是什么?**

awk 可接受许多不同型态的Pattern。 一般常使用 “关系表达式”(Relational expression) 来当成 Pattern。 awk 提供C 语言中常见的关系运算符(Relational Operators) 如 >，<，>=，<=，==，!=，例如： 
	
	1. x > 34 是一个Pattern， 判断变x与34是否存在大于的关系。 
	2. x == y是一个Pattern， 判断变量x与变量y是否存在等于的关系。

此外， awk 还提供~ (匹配match) 及 !~(非匹配not match) 二个关系运算符。 其用法与涵义如下：

如果A 为一字符串， B 为一正则表达式(Regular Expression) 

* A ~ B 判断字符串A 中是否包含能匹配(match)B表达式的子字符串。 
* A !~ B 判断字符串A 中是否不包含能匹配(match)B表达式的子字符串。 

例如：”banana” ~ /an/ 整个是一个Pattern。 因为”banana”中含有可以匹配/an/的子字符串，所以此关系式成立  (true)，整个Pattern的值为true。

有少数awk文章， 把~， !~ 当成另一类的操作符（Operator），而不作为一种 Relational Operator。在这里将这两个运算符当成一种Relational Operator。  
	 
**Actions 是什么?**

Actions 是由许多awk指令构成。awk 的指令与C 语言中的指令十分类似。 例如：awk 的I/O指令： print， printf( )， getline……

####awk的流程控制指令：
1. if(…){…} else{…}， while(…){…}…

**awk 如何处理模式和动作的呢？**

对于``Pattern {Action}` ?`，awk 会先判断Pattern的值， 如果Pattern的值为true (或不为0的数字，或不是空的字符串)，则awk将执行该Pattern所对应的Actions。否则，如果Pattern之值不为true， 则awk将不执行该Pattern所对应的Actions。
	 
```
例如：如果awk程序中有下列两指令 50 > 23 {print “Hello! The word!!” } “banana” ~ /123/ { print “Good morning !” }
awk会先判断50 >23 是否成立，因为该式成立，所以awk将打印出”Hello! The word!!”。 而另一Pattern 为”banana” ~/123/， 因为”banana” 内未含有任何子字符串可匹配（match）/123/，所以Pattern 的值是false，awk将不会打印出 “Good morning !” 
``` 
**awk 如何处理{ Actions } 的语法是什么呢？**

有时语法``Pattern {Action}``中，Pattern 部分会省略，只剩下 {Actions}。这种情况表示”无条件执行这个Actions”。  
 

####awk 的字段变量
awk 所内建的字段变量及其涵意如下：

|字段变量|含义								   |
|------ | ------------------------------------ |
|$0	 | 一字符串，其内容为目前awk 所读入的数据行。 |
|$1	 | $0 上第一个字段的数据。				 |
|$2	 | $0 上第二个字段的数据				   |
	
其余类推 。
	读入数据行时，awk如何更新(update)这些内建的字段变量?
	当awk 从数据文件中读取一个数据行时，awk 会使用内建变量$0 进行记录。当$0 被改动时(例如：读入新的数据行或改变$0的值…)， awk 会立刻重新分析$0的字段情况，并将$0上各字段的数据用$1，$2…进行记录。  

**awk 的内建变量**
	
awk 提供了许多内建变量，使用者可以在程序中使用这些变量来取得相关信息。常见的内建变量有：

|内建变量	| 含义|
|-----------|-------------------------------------------------------|
|NF		 | Number of Fields 为一整数，其值表示$0上所存在的字段数目。	|
|NR		 | Number of Records为一整数，其值表示awk 已读入的数据行数目。  |
|FILENAME   | awk正在处理的数据文件文件名。							  |

```
例如：awk 从资料文件emp.dat 中读入第一笔数据行“A101 chenying 100 210”之后，程序中：$0 的值就是“A101 chenying 100 210”，$1的值是 “A101″，$2 的值是 “chenying”，$3的值是100，$4的值是210，$NF的值是4，$NR 的值是1，$FILENAME 的值是”emp.dat”。
```
   
**awk 的工作流程**

执行awk 时，它会反复进行下列四步骤：

1. 自动从指定的数据文件中读取一个数据行。   
2. 自动更新(Update)相关的内建变量之值。 如：NF，NR，$0…
3. 依次执行程序中所有的`Pattern {Action}` 指令。   
4. 当执行完程序中所有`Pattern {Action}` 时，如果数据文件中还有未读取的数据，则反复执行步骤1到步骤4。awk会自动重复进行上述4个步骤，使用者不须于程序中编写这个循环(Loop)。  

**实例**

打印文件中指定的字段数据并加以计算

```
awk 处理数据时，它会自动从数据文件中一次读取一行记录，并将该数据切分成一个个的字段；程序中可使用$1，$2，…直接取得各个字段的内容。这个特色让使用者轻易地使用awk 编写reformatter 来改变数据格式。
例子：以文件emp.dat 为例，计算每人应发工资并打印报表。
分析：awk 会自行一次读入一行数据，所以程序中仅需告诉awk 如何处理所读入的数据行。执行如下命令：
	$ awk  ‘{ print $2，$3 * $4 }’  emp.dat
	执行结果时，屏幕出现 ：
	chenying   21000
	linxiy	 23650
	……………
```

linux/UNIX命令行上，执行awk 的语法为：$awk  ’awk程序’  欲处理的资料文件文件名；本例中程序部分是{print $2， $3 * $4}。 把程序置于命令行时，程序之前后须以’ 括住。emp.dat 为指定给该程序处理的数据文件文件名。本程序中使用：`Pattern {Action}` 语法，但是Pattern 部分被省略，表无任何限制条件，所以awk读入每笔数据行后都将无条件执行这个Actions。 print为awk所提供的输出指令，会将数据输出到标准输出stdout(屏幕)。 print 的参数间彼此以”，” (逗号) 隔开，打印出数据时彼此间会以空白隔开。
	如果将上述的程序部分储存于文件pay1.awk 中，执行命令时再指定awk 程序文件的文件名。 这是执行awk 的另一种方式，适用于程序较大的情况， 其语法如下：
	
	`$ awk   -f   awk程序文件名  数据文件文件名
   
awk 中也提供与 C 语言中类似用法的printf()函数，使用该函数可进一步控制数据的输出格式。编辑另一个awk程序如下，并取名为pay2.awk  

`{printf(“%10s Work hours： %3d Pay： %5d\n”， $2，$3， $3* $4)}`  

执行下列命令	  

`$awk -f  pay2.awk  emp.dat`

执行结果屏幕出现：   
	 
```
  chenying Workhours: 100 and pay: 21000  
  linxiyu  Workhours: 110 and pay: 23650   
  degnming Workhours: 130 and pay: 27170  
  liuchao  Workhours: 125 and pay: 27500
  hejing   Workhours: 95  and pay: 19950
```

##第三章 awk如何选择正确的行

在awk使用方法中，`Pattern {Action}`是awk使用的最主要语法。如果Pattern的值为真则执行它后方的Action。awk中常使用”关系表达式” (Relational Expression)当做Pattern。 
 
awk 中除了>，<，==，!= 等关系运算符(Relational Operators)外，另外提供 ~(匹配match)，!~(不匹配Not Match)二个关系运算符。利用这两个运算符，可判断某字符串是否包含能匹配所指定正则表达式的子字符串。 有这么多关系运算符，使用awk可以轻易的进行字符串的比较，并编写字符串处理的程序。  
例如，单位的职工如下所示：

[root@benet pub]# cat emp.dat

```
A101 chenying 100 210
A304 linxiyu  110 215
S283 dengming 130 209
S267 liuchao  125 220
B108 hejing	95 210
```

其中A开头的表示accp部门工作人员，工资加薪5%，加薪后员工薪资仍低于110的，以110计。编写awk程序打印新的员工薪资率报表。

[分析]：这个程序须先判断所读入的数据行是否合于指定条件，再进行某些动作。awk中`Pattern {Actions}` 的语法已涵盖这种 `if (condition) {action}`的架构。编写awk脚本程序adjust1.awk，脚本内容如下：
[root@benet pub]# cat adjust1.awk

```
#!/bin/awk -f
{
	if($0~/A/){$3*=1.05}
	if($3<110){$3=110}
	printf(“%s %-8s %d\n”,$1,$2,$3)
}
```

接下来，执行程序脚本：
[root@benet pub]# awk -f adjust1.awk emp.dat

结果如下：

```
A101 chenying 110
A304 linxiyu  115
S283 degnming 130
S267 liuchao  125
B108 hejing   110
```
通过以上输出，可以发现，数值改变的只有在工号中有A的行。下面来看一下awk执行的步骤：首先，awk从数据文件中每次读入一个数据行， 依序执行程序中所有的 `Pattern {Action}`指令：

```
if($0~/A/){$3*=1.05}
if($3<110){$3=110}
printf(“%s %-8s %d\n”,$1,$2,$3)
```
再从数据文件中读取下一行记录继续进行处理。

* if($0~/A/){$3*=1.05}，
    * if($0~/A/)是一个Pattern，用来判断该笔数据行的第一行是否包含”A”。 其中~/A/是一个正则表达式Regular Expression，用来表示在整行数据中含有A的字符串。
    * Actions部分为{$3\*=1.05}，$3\*=1.05与$3=$3\*1.05意义相同。 运算符”*=” 的用法与C语言中的用法一样。此后与C语言中用法相同的运算子或语法将不予赘述。  
* if($3<110){$3=110}，如果第三栏的数据内容(表薪资率)小于110， 则调整为110。
* printf(“%s %-8s %d\n”,$1,$2,$3)，省略了Pattern(无条件执行Actions)， 故所有数据行调整后的数据都将被印出。


##第四章 在awk中使用数组

awk程序中允许使用字符串当做数组的下标，这个特点有助于资料的统计。首先建立一个名为kecheng.dat数据文件，内容是学生选课的内容；第一栏为学生姓名，其后为该生所学课程，内容如下：
[root@benet pub]# cat kecheng.dat

```
zhangsan	math 		english		chinese
lisi		computer 	chinese 	english
wangwu 		electronic 	chinese 	math
zhaoliu 	environment english 	chinese
```
awk中数组不需要声明，也不用指定数组的大小，直接使用字符串当数组的下标。 以上边学生选课为数据文件统计一下kecheng.dat 中学习各门课程的人数。这种情况，有二项信息必须储存： (a) 课程名称， 如：math，english，共有哪些课程事先并不明确。 (b)各课程的选修人数。 如： 有几个人选修了“math”。  

在awk 中只用一个数组就可同时记录上述信息。 方法如下：使用数组Number[ ]：以课程名称当Number[ ]的下标。 以Number[ ] 中不同下标所对映的元素代表各门课程的人数。 例如：有2个学生学习“math”，则以Number["math"]=2表示。如果学习math的人数增加一人，则Number[“math”]=Number[“math”]+1 或Number[“math”]++ 将学习该门课程的人数加1。

那么如何读出数组中储存的信息呢？以math为例，声明int Arr[100]数组后，如果想显示数组中的数值，使用一个for循环就可以了，如：for(i=0; i<100; i++) printf(“%d\n”， Arr[i])； 即可。上式中：数组Arr[ ] 的下标 ：0，1，2，…，99， 数组Arr[ ] 中各下标所对应的值：Arr[0]，Arr[1]，…，Arr[99]。

上面说了awk 中使用数组不须声明，以数组number[]为例，程序执行前，并不知有哪些课程名称可能被当成Number[ ]的下标。在awk中提供了一个指令，通过该指令awk会自动找寻数组中所有使用过的下标。以Number[ ] 为例，awk将会找到“math”，“english”，使用该指令时，只要首先指定要找寻的数组和变量即可，awk会记录从数组中找到的每一个下标。

例如：`for(course in Number){…}`

指定使用course 来记录awk 从Number[ ]中找到的下标，awk每找到一个下标，就用course记录该下标的值且执行{…}中的指令。

例子：统计kecheng.dat中每门课程有多少学生学习，并输出结果。
处理方法：建立使用awk编写的脚本程序，脚本的内容如下：

[root@benet pub]# cat course.awk

```
#!/bin/awk -f
BEGIN{ FS=" " }
{
	for(i=2;i<=NF;i++){number[$i]++;}
}
END{
	for(course in number) printf("%10s %d\n",course,number[course]);
}
```
    在程序中需要注意，awk程序主要有三部分组成，BEGIN，中央处理部分和END三个部分，BEGIN和END是awk的保留字，后面必须是"{"，如果没有紧跟着"{"，执行程序的时候，会报错；FS是_field separator_的缩写，用来存储域分隔符。
    执行结果如下：
[root@benet pub]# awk -f course.awk kecheng.dat

```
  computer 1
   english 3
    dianzi 1
   chinese 4
      math 2
  huanjing 1
```

程序解释：这程序包含三个`Pattern {Action}`指令。

* 第一个`Pattern {Action}`指令中的FS上面已经说过了，后面跟着分隔数据文件的分隔符，在这里，是以空格来进行分隔数据行的没一个部分的，所以FS=" "；如果是分隔/etc/passwd这个文件，那么FS=":"，在以后分隔数据文件的时候，一定要选择正确的域分隔符，并用FS进行设置。
* 第二个`Pattern {Action}`指令中省略了Pattern 部分，所以每行数据读入后，都会在Actions部分将逐次无条件执行。awk在读入第一行数据zhangsan math english chinese的时候，因为该行数据有NF=4个字段，所以Action 的for循环中i从2开始，因为第一个字段是学生的名字，不用进行统计；而其后的各个域需要统计，所以for循环中i取值为2,3,4。Number[$i]++ 这句中，在i=2时，$2是第二个域的值，即$2=math，Number[math]的值从默认的0，++变成了1 ; i=3时$i=english，Number[english]的值默认是0，++变成了1 ; 同理，i=4时 $i=chinese，Number[chinese]的值从默认的0，++后变成了1 ; 第一行数据处理完后，再次读入下一行，根据$i的内容，如果数组中没有，就会新添加一个课程，并将选择给课程的学生数加1，如果该课程在数组中已经存在，只将改课程的人数加1。
* 第三个`Pattern{Actions}`指令中END 为awk的保留字，而且必须是大写，是Pattern的一种。END 成立(其值为true)的条件是： “awk处理完所有数据，即将离开程序时”，平常读入数据行时，END并不成立，所以END后面的Actions 并不被执行；只有当awk读完所有数据时，Actions才会被执行。

BEGIN与END 有点类似，是awk 中另一个保留的Pattern。 唯一不同的是：“以BEGIN 为Pattern的Actions 在程序一开始的时候被执行一次”， NF 为awk 的内建变量，用来表示awk正在处理的数据行中所包含的字段的个数。  

awk程序中以$开头的变量， 都是下面这种意思：以i=2 为例，$i=$2 表示第二个字段数据(实际上，$在awk 中 为一运算符(Operator)，用以取得字段数据)。

## 第五章 在awk中使用shell命令

awk程序中允许使用Shell指令，使用管道在awk和系统中进行数据传递，所以awk可以很容易的使用系统资源。 比如写一个awk程序来打印出当前系统上有多少用户登录。awk的脚本文件名为usernumber.awk，脚本内容如下：

[root@benet pub]# cat usernumber.awk

```
#!/bin/awk -f
BEGIN{
	while("who"|getline) n++;
	print n;
}
```
执行结果如下：

```
[root@benet pub]# awk -f usernumber.awk
2 #即有两个用户登录了系统
```
解释：awk 程序并不一定要处理数据文件，上例中没有输入任何数据文件。BEGIN 和END是awk 中的一种Pattern。以BEGIN 为Pattern的Actions，只有在awk开始执行程序，尚未输入任何文件前，执行一次且仅被执行一次) ，“|”和在shell中一样，在awk中也表示管道。awk把|之前的字符串“who”当成Shell的命令，并将该命令送往Shell执行，执行的结果通过管道输出到awk程序中。

`getline`为awk所提供的输入指令。getline 一次读取一行数据，若读取成功则return 1，若读取失败则return -1，若遇到文件结束(EOF)，则return 0;

**getline的用法和例子**

* 当getline左右没有重定向符|或<时，getline读去当前文件的第一行并将数据保存到变量中，如果没有变量，则数据保存到$0中；由于awk在处理getline之前已经读入了一行，所以getline得到的返回结果是隔行的。
* 当getline左右有重定向符|或<时，getline作用于定向输入文件，由于该文件是刚打开，awk并没有读入一行数据，而getline读入了一行数据，那么getline返回的是该文件的第一行，而不是隔行。

```
[root@myfreelinux pub]# awk ‘BEGIN{“cat kecheng.dat”|getline var;print var;}’
[root@myfreelinux pub]# awk ‘BEGIN{“cat kecheng.dat”|getline;print $0;}’
[root@myfreelinux pub]# awk ‘BEGIN{getline var<”kecheng.dat”;print var;}’
[root@myfreelinux pub]# awk ‘BEGIN{getline <”kecheng.dat”;print $0;}’
```

以上四行awk程序都是将kecheng.dat的第一行数据打印出来，结果是：zhangsan math english chinese

```
[root@myfreelinux pub]# awk ‘{getline var;print $0;print var;}’ kecheng.dat
zhangsan math     english chinese
lisi     computer chinese english
wangwu   dianzi   chinese math
zhaoliu  huanjing english chinese

[root@myfreelinux pub]# awk ‘{getline var;print var;}’ kecheng.dat
lisi     computer chinese english
zhaoliu  huanjing english chinese
```

以上两个例子可以看出来，awk和getline是分别取数据文件中的行数据，而且是awk首先从数据文件中取数据，后getline取下一行数据。

getline在不同环境下影响到awk中的值的对应关系如下图：

| format           | setting       |
|------------------|---------------|
| getline          | $0,NF,NR,FNR  |
| getline var      | var,NR,FNR    |
| getline<file     | $0,$1…$NF,NF  |
| getline var<file | var           |
| cmd|getline      | $0,NF         |
| cmd|getline var  | var           |

awk '{print $NF}' means print the last field in a space delemmited file.

NR     ordinal number of the current record

| **mark**   | **explain**                                                         |
|------------|---------------------------------------------------------------------|
| FNR        | ordinal number of the current record in the current file            |
| FILENAME   | the name of the current input file                                  |
| RS         | input record separator (default newline)                            |
| OFS        | output field separator (default blank)                              |
| ORS        | output record separator (default newline)                           |
| OFMT       | output format for numbers (default %.6g)                            |
| SUBSEP     | separates multiple subscripts (default 034)                         |
| ARGC       | argument count, assignable                                          |
| ARGV       | argument array, assignable; non-null members are taken as filenames |
| ENVIRON    | array of environment variables; subscripts are names.               |
| FS         | Field Seperator                                                     |
| IGNORECASE | Ignore case                                                         |
| NF         | Number of Fields                                                    |
| NR         | Number of Records                                                   |
| OFMT       | Number Format, default value is "%.6g"                              |
| RSTART     | The first index by match()                                          |  
| RLENGTH    | 由match() 匹配的串的长度 SUBSEP 下标分隔符，缺省为"�34"                  |
 
| Function               | Description                                                       |
|------------------------|-------------------------------------------------------------------|
|gsub(r,s,t)             | 在字符串t中，用字符串s替换和正则表达式r匹配的所有字符串。返回替换的个数。如果没有给出t，缺省为$0|
|index(s,t)              | 返回s 中字符串t 的位置，不出现时为0                                    |
|length(s)               | 返回字符串s 的长度，当没有给出s时，返回$0的长度                          |
| match(s,r)             | 返回r 在s 中出现的位置，不出现时为0。设置RSTART和RLENGTH的值             |
| split(s,a,r)           | 利用r 把s 分裂成数组a，返回元素的个数。如果没有给出r，则使用FS。数组分割和字段分割采用同样的方式|
| sprintf(fmt,epr_list) | 根据格式串fmt，返回经过格式编排的                                       |
| expr_listsub(r,s,t)    | 在字符串t中用s替换正则表达式t的首次匹配。如果成功则返回1，否则返回0。如果没有给出t，默认为$0|
| substr(s,p,n)          | 返回字符串s中从位置p开始最大长度为n的字串。如果没有给出n，返回从p开始剩余的字符|
| tolower(s)             | 将串s 中的大写字母改为小写，返回新串                                    |
| toupper(s)             | 将串s 中的小写字母改为大写，返回新串                                    |

## 第六章 awk编程的几个实例

在这里举个例子，统计上班到达时间及迟到次数的程序。这程序每日被执行时将读入二个文件：员工当日上班时间的数据文件 ( arrive.dat ) 存放员工当月迟到累计次数的文件当程序执行执完毕后将更新第二个文件的数据(迟到次数)， 并打印当日的报表。
    此程序的步骤分析如下：
    [6.1] 在上班数据文件arrive.dat之前增加一行标题 “ID Number Arrvial Time”，并产生报表输出到文件today_result1中。   
    [6.2] 将today_result1上的数据按员工代号排序， 并加注执行当日日期；  产生文件today_result2
    [6.3] 将awk程序包含在一个shell script文件中
    [6.4] 在today_result2 每日报表上，迟到者之前加上”*”，并加注当日平均上班时间；产生文件today_result3
    [6.5] 从文件中读取当月迟到次数， 并根据当日出勤状况更新迟到累计数。


上班时间数据文件arrive.dat的格式是：第一列是员工代号，第二列是到达时间，内容如下：

```
[root@myfreelinux pub]# cat arrive.dat
A034 7:26
A025 7:27
A101 7:32
A006 7:45
A012 7:46
A028 7:49
A051 7:51
A029 7:57
A042 7:59
A008 8:01
A052 8:05
A005 8:12
```

**说 明**
awk程序中，文件名称today_result1的前后需要用""(双引号)括起来，表示today_result1是一个字符串常量。如果没有用双括号括起来，today_result1将被awk解释为一个变量名称。在awk中，**变量不需要事先声明**，变量的初始值为空字符串(Null string) 或0。

因此awk程序中如果没有将today_result1用双括号括起来，那么awk将today_result1作为一个变量来使用，它的值是空字符串，那么在执行时造成错误(Unix无法开启一个以空字符串为文件名的文件)。因此在编写awk程序时，一定要将文件名用双括号括起来。BEGIN是awk 的保留字，是Pattern的一种。以BEGIN为Pattern的Actions在awk程序刚被执行尚未读取数据文件时被执行一次，此后便不再被执行。本程序中若使用”>” 将数据重定向到today_result1，awk第一次执行该指令时会产生一个新文件today_result1，再执行该指令时则把数据追加到today_result1的文件结尾，而不是每执行一次就打开一次该文件。而”>>”和“>”的差异仅在执行该指令时，如果已存在today_result1则awk将直接把数据append在原文件的末尾。

### awk 中如何利用系统资源

awk程序中可以很容易的使用系统资源。比如

1. 在程序中途调用Shell命令来处理程序中的部分数据；
2. 在调用Shell命令后将其产生的结果交回awk 程序(不需将结果暂存于某个文件)。这一过程是使用awk 所提供的管道(类似于Unix中的管道，但又有些不同)，和一个从awk 中调用Unix的Shell命令的语法来实现的。
    例:承上题，将数据按员工ID排序后再输出到文件today_result2， 并在表头上添加执行时的日期。

**分 析**

awk 提供了和UNIX用法类似的管道命令"|"。在awk中管道的用法和含意如下：awk程序中可接受下列两种语法：
    * [a语法] awk output 指令| "Shell 接受的命令" ( 如: print $1，$2 | ""sort -k 1″ )  
    * [b语法] "Shell 接受的命令" | awk input 指令 ( 如: "ls"| getline)  。
    
awk的input指令只有getline 这一个输入指令。awk的output 指令有print，printf() 二个。
    
在a语法中，awk所输出的数据将转送往Shell ，由Shell的命令进行处理。以上例而言，print所输出的数据将经由Shell 命令"sort – k 1"排序后再输出到屏幕(stdout)。上例awk程序中，"print $1,$2" 可能反复执行很多次，输出的结果将先暂存在pipe中，等程序结束后，才会一并进行排序"sort -k 1"。须注意二点：不论print $1,$2被执行几次，”sort -k 1″的执行时间是”awk程序结束时”，”sort -k 1″ 的执行次数是”一次”。  
    
在b语法中，awk将先调用Shell命令，执行结果将通过pipe传送到awk程序，以上例而言，awk先让Shell执行”ls”，Shell执行后将结果保存在pipe中，awk的输入指令getline再从pipe中读取数据。使用本语法时应注意：在上例中，awk”立刻”调用Shell 来执行 ”ls”，执行次数是一次。getline则可能执行多次(如果pipe 中存在多行数据)。
除以上a，b两种语法外，awk程序中其它地方如出现像”date”，”ls”…这样的字符串，awk只把它当成一般字符串处理。  
    
根据以上分析，建立awk程序脚本内容如下：
    
```
[root@myfreelinux pub]# cat reformat2.awk
#!/bin/awk -f
BEGIN{
    "date" | getline;   #在shell中执行date指令，并将结果存储到$0中。
    print "Today is",$2,$3 > "today_result2";
    print "ID Number    Arrival Time">"today_result2";
    print "=========================">"today_result2";
    close("today_result2");
}
{
    printf("%s\t\t%s\n",$1,$2) | "sort -k 1 >> today_result2";
}
```

执行该程序脚本：

`[root@myfreelinux pub]# awk -f reformat2.awk arrive.dat`
    
执行后，程序将sort后的数据追加到文件today_result2最末端。today_result2内容如下：

```
[root@myfreelinux pub]# cat today_result2
Today is Dec 23
ID Number    Arrival Time
=========================
A005        8:12
A006        7:45
A008        8:01
A012        7:46
A025        7:27
A028        7:49
A029        7:57
A034        7:26
A042        7:59
A051        7:51
A052        8:05
A101        7:32
```

**解释说明**：awk程序由三个主要部分构成：

1. Pattern { Action} 指令，
2. 函数主体。例如：function double( x ){ return 2*x }
3. 注释Comment ( 以# 开头识别之 )。

awk 的输入指令getline，每次读取一列数据。如果getline之后没接任何变量，读入的数据将存储到$0中，否则以所指定的变量储存储。比如在执行`"date" | getline`后，$0 的值是2010年 06月 17日 星期四 10:43:16 CST，当$0被更新时，awk将自动更新相关的内建变量，如：$1，$2，…，NF。所以$2的值是”06月”，$3的值是“17日”。

本程序中printf() 指令会被执行12次(因为有arrive.dat中有12行数据)，在awk结束该程序时会close这个pipe ，此时才将12行数据一次送往系统，并由`sort -k 1 >> today_result2`进行处理，注意”>>”的左侧一定要有空格，否则在执行的时候会报错. **sort, 选项需要一个参数 -k**

awk还提供了另一种调用Shell命令的方法，即使用awk 函数`system("shell 命令")` ，例如：

`awk 'BEGIN{system("date>date.dat");getline<"date.dat";print "Today is ",$2,$3}'`

但使用 system( “shell 命令” ) 时，awk无法直接将执行中的数据输出给Shell 命令。且Shell命令执行的结果也无法直接输入到awk 中。

### 执行awk 程序的几种方式 
下部分将介绍如何将awk程序直接写在shell script 之中，这样在执行的时候就不需要在命令行每次都输入” awk -f program  datafile” 了。script中还可包含其它Shell 命令，这样可增加执行过程的自动化。建立一个简单的awk程序mydump.awk，如下： {print}这个程序执行时会把数据文件的内容print到屏幕上( 与cat功用类似  )。print 之后未接任何参数时，表示 “print $0″。如果要执行这个awk程序输出today_result1和today_result2 的内容时，在unix命令行上，有以下几种方式：

1. awk -f mydump.awk today_result1 today_result2 
2. awk ‘{print}’ today_result1 today_result2, 第二种方法将awk 程序直接写在Shell的命令行上，这种方法仅适合awk程序较短的时候。
3. 使用shell脚本。建立一个shell脚本mydisplay，内容如下：

```
[root@myfreelinux pub]# cat mydisplay
#!/bin/bash        # 注意awk 与’ 之间须有空白隔开
awk ‘{print}’ $*   # 注意’ 与$* 之间须有空白隔开
```

在执行mydisplay 之前，需要给它添加可执行权限，首先执行如下命令： [root@myfreelinux pub]# chmod +x mydisplay，这样mydisplay才有可执行权限，[root@myfreelinux pub]# ./mydisplay today_result1 today_result2就可以执行了，当然，如果没有给他可执行权限的话，也可以使用一下方法来执行：[root@myfreelinux pub]# bash mydisplay today_result1 today_result2
     
**说明**

1. 在script文件mydisplay 中，指令"awk"与第一个'之间须有空格(Shell中并无"awk'"指令)。第一个'用来通知Shell其后为awk程序，第二个' 则表示awk 程序结束。所以awk程序中一律以”括住字符串或字符，而不能使用’括住字符串或字符，以免引起Shell混淆。
2. $* 是shell script 中的用法，可用来代表命令行上"mydisplay之后的所有参数"。例如执行：[root@myfreelinux pub]# ./mydisplay today_result1 today_result2事实上Shell 已先把该指令转换成：awk '{print}' today_result1 today_result2 。本例中，$\*代表"today_result1 today_result2"。在Shell的语法中， $1代表第一个参数，$2 代表第二个参数。当不确定命令行上的参数个数时，可使用$*代表。awk命令行上可同时指定多个数据文件。以awk -f dump.awk today_result1 today_result2hf 为例，awk会先处理today_result1，再处理today_result2，如果文件不存在或无法打开，会提示相应的错误，比如awk: (FILENAME=today_result1 FNR=14) fatal: cannot open file `today_result’ for reading (没有那个文件或目录)。
    
有些awk程序`仅`包含以BEGIN为Pattern的指令，这种awk 程序执行时不须要数据文件，如果在命令行上指定一个不存在的数据文件，awk不会产生`无法打开文件`的错误(事实上awk并未打开该文件) 。例如执行：[root@myfreelinux pub]#awk 'BEGIN {print "Hello，World!!"}' file_no_exist ，该程序中仅包含以BEGIN 为Pattern的指令，awk 执行时并不会开启任何数据文件； 所以不会因不存在文件file_no_exit 产生"无法打开文件"错误。

awk会将Shell 命令行上awk程序(或 -f 程序文件名)之后的所有字符串，视为将输入awk进行处理的数据文件文件名。如果执行awk 的命令行上"未指定任何数据文件文件名"，则将stdin作为数据源，直到输入end of file( Ctrl-D )为止。比如执行如下命令：

`[root@myfreelinux pub]#awk -f mydump.awk  #(未接任何数据文件文件名)`
或
`[root@myfreelinux pub]#./mydisplay  #(未接任何数据文件文件名) `

会发现：此后键入的任何数据将逐行复印一份显示到屏幕上，这种情况是因为执行时没有指定数据文件文件名，awk 便以stdin(键盘上的输入)做为数据来源。那么我们可利用这个特点，设计与awk即时聊天的程序:mrgreen:

### 改变awk 切割字段的方式
#### 自定义函数

awk不仅能自动分割字段，也允许使用者改变其字段切割方式以适应各种格式的需要。
“”
**例题：承接6.2的例子，如果八点为上班时间，那么请在迟到的记录前加上“\*”，并计算平均上班时间**

```
    分析：八点整到达者，不算迟到，所以只根据到达的小时数来判断是不够得，还需要应参考到达时的分钟数。如果“将到达时间转换成以分钟为单位”，进行判断是否迟到和计算到达平均时间比较容易。到达时间($2)的格式为dd：dd 或d：dd，数字当中含有一个":"，awk无法对这些数据进行处理 (注：awk中字符串"26"与数字26，并无差异，可直接做字符串或数学运算，这是awk重要特色之一。 但awk对文本数字交杂的字符串无法正确进行数学运算)，那么可以使用一下方法解决：
    [方法一] 对到达时间($2) d:dd 或dd:dd 进行字符串运算，分别取出到达的小时数和分钟数。  首先判断到达小时数是一位还是两位字符，再调用函数分别截取分钟数和小时数，需要用到下列awk字符串函数：
    length( 字符串) ：返回该字符串的长度；
    substr( 字符串，起始位置，长度) ：返回从起始位置起，指定长度之子字符串；若未指定长度，则返回从起始位置到字符串末尾的子字符串。
    所以：小时数=substr( $2，1，length($2) – 3 ) ，分钟数=substr( $2，length($2) – 2)
[root@myfreelinux pub]# awk 'BEGIN{"date" | getline; print substr($4,1,2)*60+substr($4,4,2)}'
    [方法二]改变输入列字段的切割方式，使awk切割字段后分别将小时数及分钟数隔开于二个不同的字段。字段分隔字符FS (field seperator) 是awk 的内建变量，其默认值是空白及tab。awk每次切割字段时都会先参考FS的内容。若把":"也当成分隔字符，则awk 便能自动把小时数及分钟数分隔成不同的字段。
    故令FS = "[ \t：]+" (注： [ \t：]+ 是一个正则表达式Regular Expression ) Regular Expression中使用中括号[] 表示一个字符集合，用以表示任意一个位于两中括号间的字符。所以可用"[ \t：]+"表示一个空格， tab 或":" ，Regular Expression中"+"表示其前方的字符可出现一次或一次以上。所以"[ \t：]+" 表示由一个或多个"空格，tab 或 : " 所组成的字符串。设定FS =”[ \t：]+” 后，数据行如：”1034 7：26″ 将被分割成3个字段，$1是1034，$2是7，$3是26，显然，awk程序中使用方法二要比方法一更简洁方便。所以本例中使用方法二，来介绍改变字段切割方式的用法。
```
     编写awk程序reformat3，如下：
     
```
[root@myfreelinux pub]# cat reformat3.awk
#!/bin/bash
awk 'BEGIN{
    FS="[ \t:]+";
    "date" | getline;
    print "Today is",$2,$3 > "today_result3";
    print "==================" > "today_result3";
    print "ID Number  Arrival Time" > "today_result3";
    close("today_result3");
}
{
    arrival=HM_TO_M($2,$3);
    printf("%s\t\t%s:%s %s\n",$1,$2,$3,arrival>"480"?"*":" ") | "sort -k 1 >> today_result3";
    total+=arrival;
}
END{
    close("sort -k 1 >> today_result3");
    printf("Average arrival time:  %d:%d\n",total/NR/60,total/NR%60) >> "today_result3";
    close("today_result3");
}
function HM_TO_M(hour,min){
    return hour*60 + min
}' $*
```
并执行如下指令:

```
[root@myfreelinux pub]# bash reformat3.awk arrive.dat
or
[root@myfreelinux pub]# ./reformat3.awk arrive.dat
```
执行后，文件 today_result3 的内容如下： 

```
[root@myfreelinux pub]# cat today_result3
Today is Dec 23
==================
ID Number  Arrival Time
A005		8:12 *
A006		7:45
A008		8:01 *
A012		7:46
A025		7:27
A028		7:49
A029		7:57
A034		7:26
A042		7:59
A051		7:51
A052		8:05 *
A101		7:32
Average arrival time:  7:49
```

**说明**

1. awk 中允许自定义函数，函数定义方式可参考本程序，function是awk的保留字。HM_TO_M() 这函数负责将传入的小时和分钟数转换成以分钟为单位的时间。在printf()中使用的`arrival > 480 ? ${op1}:${op2}`是一个三元运算符，如果arrival 大于480则op1, 否则op2
2. % 是awk的运算符(operator)，作用与C 语言中的% 相同(取余数)。
3. NR(Number of Record) 为awk 的内建变量，表示awk执行该程序后所读入的记录笔数。  
4. close的语法有二种：close( filename ) 和close( pipe之前的command ) 。本程序使用了两个close( ) 指令：指令close("sort -k 1 >> today_result3")，意思是close程序置于”sort -k 1 >> today_result3 ” 之前的Pipe ， 并立刻调用Shell 来执行"sort -k 1 >> today_result3"。 因为 Shell 排序后的数据也要写到 today_result3， 所以awk必须先关闭 使用中的today_result3 以使 Shell 正确将排序后的数据追加到 today_result3否则2个不同的 process 同时打开一个文件进行输出将会 产生不可预期的结果。 读者应留心上述两点，才可正确控制数据输出到文件中的顺序。指令close("sort -k 1 >> today_result3")中字符串 "sort +0n >>  today_result3" 必须与pipe |后方的Shell Command名称一字不差，否则awk将视为二个不同的pipe。

### 使用getline 来读取数据 
**范例： 承上题，从文件中读取当月迟到次数，并根据当日出勤状况更新迟到累计数。(按不同的月份累计于不同的文件)**

分析： 程序中自动抓取系统日期的月份名称，连接上"late.dat"， 形成累计迟到次数的文件名称(如：09月late.dat, ...)， 并以变量late_file记录该文件名。累计迟到次数的文件中的数据格式为：员工代号(ID) 迟到次数。例如，执行本程序前文件09月late.dat 的内容如下：
[root@myfreelinux pub]# cat late.dat
A005  2
A006  1 
A008  2
A012  0 
A025  0 
A028  1 
A029  2 
A034  0 
A042  0 
A051  0 
A052  3
A101  0

编写程序reformat4 如下：

```
[root@myfreelinux pub]# cat reformat4.awk
#!/bin/bash
awk 'BEGIN{
    sys_sort="sort -k 1 >> today_result4";
    result="today_result4";
    FS="[ \t:]+";#改变字段切割的方式
    "date"|getline;#令Shell执行"date";getline读取结果，并以$0记录结果
    print "Today is",$2,$3 > result;
    print "=======================">result;
    print "ID Number  Arrival Time"> result;
    close(result);
    late_file=$2"late.dat";
    while(getline<late_file >0) #从文件按中读取迟到数据，并用数组cnt[]记录。
        cnt[$1]=$2 #数组cnt[]中以员工代号为下标，所对应的值为该员工之迟到次数
    close(late_file)
}
{
    arrival=HM_TO_M($2,$3);#已更改字段切割方式，$2表小时数，$3表分钟数
    if(arrival>480)
        {mark="*"; #若当天迟到，应再增加其迟到次数，令mark为"*"
        cnt[$1]++;}
    else
        mark=" ";
    message=cnt[$1]?cnt[$1]"times":" ";# message用以显示该员工的迟到累计数，若未曾迟到message为空字符串
    printf("%s\t\t%2s:%2s %5s %s\n",$1,$2,$3,mark,message)|sys_sort;
    total+=arrival;
}
END{
    close(result);
    close(sys_sort);
    printf("Arrivage arrival time: %d:%d\n",total/NR/60,total/NR%60) >> result;
    for(any in cnt) #将数组cnt[]中新的迟到数据写回文件中
        print any,cnt[any] > late_file;
}
function HM_TO_M(hour,minute){return hour*60+minute;}
' $*
```

P.S written by binyu

```
#!/bin/bash
awk 'BEGIN{
    FS="[ :\t]+";
    print "ID        Late Times" > "late";
    print "=======================" >> "late";
    close("late");
}
{
    min=HM_TO_MIN($2,$3);
    times[$1]+=min > 480 ? 1 : 0;
}
END{
    for (id in times){
        printf("%s\t%s\n", id, times[id]) >> "late";
    }
    close("late");
}
function HM_TO_MIN(hour,min){
    return hour*60+min;
}
' $*
```


执行后结果如下：

```
[root@myfreelinux pub]# bash reformat4.awk arrive.dat
[root@myfreelinux pub]# cat today_result4
Today is 06月 18日
=======================
ID Number  Arrival Time
A005   8:12     * 1times
A006   7:45       
A008   8:01     * 1times
A012   7:46       
A025   7:27       
A028   7:49       
A029   7:57       
A034   7:26       
A042   7:59       
A051   7:51       
A052   8:05     * 1times
A101   7:32       
Arrivage arrival time: 7:49
06月late.dat的内容如下：
[root@myfreelinux pub]# cat 06月late.dat
A028
A029
A012
A005 1
A042
A051
A006
A101
A052 1
A025
A034
A008 1
```

**说 明**

由于文件06月late.dat中保存了一些数据内容，在这里我没有做更多的修改，所以每次执行[root@myfreelinux pub]# bash reformat4.awk arrive.dat的时候，迟到次数都会增加，您可以根据实际情况，再做一下修改，做的更完美。

late_file是一变量，记录迟到次数的文件的文件名。late_file值由两部分构成，前半部是当月月份名称(由调用”date”取得)后半部固定为”late.dat” 如：06月late.dat。指令getline < late_file 表示从late_file所代表的文件中读取一笔记录，并存放于$0。awk会自动对新置入$0 的数据进行字段分割，之后程序中可用$1， $2，。。来表示数据的第一栏，第二 栏，。。，

```
注： 有少数awk版本不容许用户自行将数据置于$0，这种情况可改用gawk或nawk。执行getline指令时， 若成功读取记录，它会返回1；若遇到文件结束，它返回0；无法打开文件则返回-1。利用 while( getline < filename >0 ) {…}可读入文件中的每一笔数据并进行处理。这是awk 中用户自行读取数据文件的一个重要模式。数组 cnt[ ] 以员工ID，下标(index)对应值表示其迟到的次数。执行结束后，利用 for(Variable in array ){...}的语法 for( any in cnt ) print any， cnt[any] > late_file 将更新过的迟到数据重新写回记录迟到次数的文件。
```


##第七章 awk对多行数据的处理实例

awk 每次从数据文件中只读取一行数据进行处理，这是因为awk中有一个内建变量RS(Record Separator) ，RS将文件中的数据分隔成以行为单位的记录record。RS默认值以"\n"(跳行符号)分隔数据文件中的信息，所以默认情况下awk 中一行数据就是一行Record。但有些文件中一行Record涵盖了多行数据，这种情况下不能再以"\n" 来分隔Records。最常使用的方法是相邻的Records之间改用一个空白行来分隔。在awk程序中，令RS= ""(空字符串)后，awk把会空白行当成来文件中Record的分隔符。显然awk对RS=”"另有深意，简单来说是这样的，当RS=”" 时：多个相邻的空白行，awk仅作为一个Record Saparator(awk不会在多个相邻的空白行之中选取一行做为空的Record) ；awk会略过(skip)文件头和文件尾的空白行，所以不会因为有这样的空白行，造成awk多读了二行空的数据。下面举个例子看一下，首先建立一个数据文件myfreelinux.dat，内容如下:

```
[root@myfreelinux pub]# cat myfreelinux.dat
wanger 
linux_basic
lisan 
linux_server
windows_server
\n
zhaosi 
awk_tools
grub
regular_expression
```
该文件的开头有3行空白行， 各行Record之间分别用2个和1个空白行隔开。那么下面，通过几个例子来看一下。首先编辑一个awk程序脚本report1.awk，内容如下：

```
[root@myfreelinux pub]# cat report1.awk
#!/bin/sh
awk ‘BEGIN{
FS="\n";
RS="";
split("one:.two:.three:.four:.five:.six:.seven:.eight:.nine:.ten:.",number,".");
}
{
printf(“\n%s reporter is : %s\n”,number[NR],$1);
for(i=2;i<=NF;i++)
 printf(“%d %s\n”,i-1,$i);
}’ $*
```

执行该程序脚本和产生的结果如下：

```
[root@myfreelinux pub]# bash report1.awk myfreelinux.dat
one: reporter is : wanger 
1 linux_basic
two: reporter is : lisan 
1 linux_server
2 windows_server
three: reporter is : zhaosi 
1 awk_tools
2 grub
3 regular_expression
```

解释说明：上面这个程序的字段分隔字符是( FS= “\n” )，这样的话一行数据就是一个field，而且RS=“”，所以这三个用户的记录是通过空行来分隔的。那么awk读入的第一行Record 为

```
wanger 
linux_basic
```
其中$1的值是”wanger”，$2的值是：“ linux_basic”，程序中的number[ ]是一个数组(array)，用来记录英文数字，比如number[1]=one:，number[2]=two:等等，这个是使用awk的字符串函 数split()来把英文数字放进数组number[ ]中的。

**函数split( )用法如下：**

split( 原字符串，数组名，分隔字符(field separator) ) 
awk将根据指定的分隔字符(field separator)分隔原字符串成一个个的字段(field)， 并将各字段记录到数组中。              

           
## 第八章 awk如何读取命令行的参数

在linux/unix中大部分的应用程序都允许用户在命令之后增加一些参数，在执行awk 程序是，也可以在awk程序后增加一些参数，这些参数一般是用来指定数据文件的文件名。这里，我们看一下awk程序是如何使用这些参数的。   

建立文件analyse.awk，内容如下：

```
root@myfreelinux pub]# cat analyse.awk
#!/bin/bash
awk ‘BEGIN{
for(i=0;i<ARGC;i++)
 print ARGV[i];# 依次印出awk所记录的参数
}’ $*
```
**执行结果如下**

```
[root@myfreelinux pub]# bash analyse.awk first-arg second-arg
awk
first-arg
second-arg
```
解释说明：

* ARGC，ARGV[ ]是awk的内建变量。
* ARGC ：是一整数，代表命令行上除了选项-v， -f 及其对应的参数之外所有参数的个数。
* ARGV[ ] 是一字符串数组，ARGV[0]，ARGV[1]，。。。ARGV[ARGC-1]分别代表命令行上相对应的参数。   

比如在这里执行的命令`[root@myfreelinux pub]# bash analyse.awk first-arg second-arg`，ARGC的值是3，ARGV[0]是"awk"，ARGV[1]的值为"first-arg"， ARGV[2]的值是"second-arg"。

再比如`#awk -vx=21-f program fir-data sec-data` 和 `#awk ‘{ print $1 ,$2 }’  fir-data sec-data` 这两条ARGC 值都是3，ARGV[0]是"awk"，ARGV[1]是"fir-data"，ARGV[2]是"sec-data"，命令行上的"-f program"，"-vx=21"，程序部分 '{ print $1，$2}' 都不会被列入ARGC和ARGV[ ]中。

awk 利用ARGC 来判断要打开的数据文件的个数，但是用户可以强行更改ARGC的值；比如将ARGC的值被用户设置为1，那么awk将被蒙骗，误以为命令行上没有数据文件文件， 所以不会以 ARGV[1]，ARGV[2]等作为文件名来打开文件并读取数据；但是在程序中可以使用ARGV[1]，ARGV[2]等变量来取得命令行上数据文件的数据。

现在有一个awk程序内容如下：

```
[root@myfreelinux pub]# cat test1.awk
#!/bin/awk -f
BEGIN{
for(i=0;i<ARGC;i++)
 print ARGV[i];
}
```

执行以上程序的结果如下：

```
[root@myfreelinux pub]# awk -f test1.awk arrive.dat today_result1
awk
arrive.dat
today_result1
```
加入将test1.awk的内容更改成test2.awk的内容如下：

```
[root@myfreelinux pub]# cat test2.awk
#!/bin/awk -f
BEGIN{
    number=ARGC;     #用number 记住实际的参数个数
    ARGC=2;          #设置ARGC=2，awk将以为只有一个资料文件
    for(i=0;i<ARGC;i++)
        print ARGV[i];
}
```

执行并查看运行结果：

```
[root@myfreelinux pub]# awk -f test2.awk arrive.dat today_result1 today_result2
awk
arrive.dat
```

这个时候会发现，虽然同样ARGC=3，但是人为的设置ARGC=2，后，awk在执行的时候，只认为有一个参数arrive.dat。
将test2.awk修改成以下内容：

```
[root@myfreelinux pub]# cat test3.awk
#!/bin/awk -f
BEGIN{
    number=ARGC;
    ARGC=2;
    for(i=0;i<ARGC;i++)
        print ARGV[i];
    for(i=ARGC;i<number;i++)
        print ARGV[i];
}
```
运行并查看运行结果如下：

```
[root@myfreelinux pub]# awk -f test3.awk arrive.dat today_result1 today_result2
awk
arrive.dat
today_result1
```

显然，通过修改ARGC可以修改awk能够识别的参数的个数，但是实际存在的ARGV的内容，仍然可以访问的。比如在这里ARGC设置为2后，awk只能打开ARGV[1]=arrive.dat，但是我们可以使用ARGV[2]，ARGV[3]取得命令行上的参数today_result1，today_result2。


# 第九章 使用awk编写可交互的程序
在执行编写的awk程序时，awk会自动从数据文件中读取数据并进行处理，直到文件结束。实际上，只要将awk读取数据的来源改成键盘输入，那么就可以设计与awk 交互的程序了。
首先看一个交互的程序。这个系程序能够实现输入一个英文单词，程序打印出该词对应的汉语意思，并继续等待用户输入新的英文单词。首先编辑一个数据文档data.dat，内容如下：

```
[root@myfreelinux pub]# cat data.dat
man 男人
girl 女孩
boy 男孩
rose 玫瑰
apple 苹果
banana 香蕉
```

编写一个互动的awk程序，内容如下：

```
[root@myfreelinux pub]# cat china-eng.awk
#!/bin/bash
awk ‘BEGIN{
    while(getline<ARGV[1])
    {
         English[++n]=$1;   #从数据文件中读取需要使用的数据保存在两个数组中
         Chinese[n]=$2;     #n最后的值作为题目数量，在question中使用
    }
    ARGV[1]="-";    # "-"表示由stdin(键盘输入)
    srand();        # 以系统时间为随机数启动的种子
    question();     #产生考题
}
{
    #awk读入数据，即回答的答案
    if($1!=English[ind])
        print "Try again!"
    else {print "\n You are right!! Press Enter to continue……";
        getline;
        question();
    }
}
function question()
{
    ind=int(rand()*n)+1;    #以随机数选取考题
    system("clear");        #系统清屏
    print "Press \"ctrl+d\" to exit";
    printf("\n %s",Chinese[ind] " 的英文字是：")
}’ $*
```

下面执行一下这个程序：

```
[root@myfreelinux pub]# bash china-eng.awk data.dat
Press "ctrl+d" to exit
 香蕉 的英文字是：apple
 You are right!! Press Enter to continue……
Press "ctrl+d" to exit
 男人 的英文字是：men
Try again!
man
 You are right!! Press Enter to continue……
```

说明： 参数data.dat (ARGV[1])是存储考题数据的数据文件文件，awk从数据文件上取得数据后将英文数据存储到English的数组中，将中文数据存储到Chinese的数组中，然后将ARGV[1] 改成"-"，"-" 表示从stdin键盘读入数据，>对于ARGV在awk的第八部分中有很多说明。对于srand();    # 以系统时间为随机数启动的种子，在这个程序里，其实没有多大的用处，作为一个知识点，先介绍一下。

在BEGIN中，最后一行是question（），此函数是自定义的一个函数，此函数首先产生一个随机数，是通过ind=int(rand()\*n)+1，这个语句产生的，对于n，和数据文件有关，在这里，数据文件只有六行，所以n为6，对于随机函数取整后+1，是因为English和Chinese这两个数组的下表是从1开始的，而rand()函数产程的数值从0~1，所以int(rand()*n)的值是从0~5，+1后的下标才和两个数组的下标值的范围相同。system("clear")是awk调用系统的清屏函数。printf("\n %s",Chinese[ind] " 的英文字是：")是输出一个汉语单词，等待用户输入英文单词，输入后的数据存储在$1中，并和同下标的English数组中的数据比较，如果正确， Press Enter to continue……提示按任何键退出，否则>会提示再输入一次答案，知道答对为止，答对后，会再次调用question()函数，产生下一个问题，知道在键盘输入结束符号 (End  of file)是ctrl+d，当awk 读到ctrl+d时就停止由键盘读取数据，程序结束。

* awk 的数学函数中提供两个与随机数有关的函数。
* srand( )：以当前的系统时间作为随机数的种子
* rand( ) ：返回介于0与1之间的(近似)随机数值。
           

## 第十章 使用awk编写递归程序的实例

awk 中除了函数的参数列(Argument List)上的参数(Arguments)外，所有变量无论在什么地方出现，均被视为全局变量。全局变量的生命周期持续到程序结束。全局变量不论在function外还是function内都可以使用，只要变量名称相同，所使用的就是同一个变量。但是递归函数会调用会调用到函数本身，所以编写这里函数是需要特别注意。

例如：编辑一个awk脚本程序，内容如下：

```
[root@myfreelinux pub]# cat argument.awk
#!/bin/awk -f
BEGIN{
    x=35;
    y=45;
    test_variable(x)
    printf("Return to main: arg1=%d,x=%d,y=%d,z=%d\n",arg1,x,y,z)
}
function test_variable(arg1)
{
     arg1++;    #arg1是参数列上的参数，是local variable，离开此函数后将消失
     y++;       #改变主程序中的变量y
     z=55；     #z为该函数中新使用的变量，主程序中的变量z 仍可被使用
     printf("Inside the function: arg1=%d,x=%d,y=%d,z=%d\n",arg1,x,y,z);
}
```

执行以上awk程序，并查看结果如下：

```
[root@myfreelinux pub]# awk -f argument.awk
Inside the function: arg1=36,x=35,y=46,z=55
Return to main: arg1=0,x=35,y=46,z=55
```

由上以上结果可推断出：在自定义的函数内部可以随意使用主程序中的任何变量，在自定义函数内使用的变量(除参数外)，在该函数外仍然可以使用。这种特性喜忧搀半，坏处是主程序中的变量不易被保护，特别是递归调用本身，执行子函数时会破坏父函数内的变量。

一个变通的方法是：在函数的参数列中虚列一些参数。函数执行中使用这些虚列的参数来记录不想被破坏的数据，这样执行子函数时就不会破坏父函数中的参数。此外awk 并不会检查调用函数时所传递的参数个数是否一致。
例如定义递归函数如下：

```
function demo( arg1 ){
    # 最常见的错误例子
    for(i=1; i< 20 ; i++){
        function(x) # 又呼叫本身。因为i 是global variable，所以执行完该子函数后原函数中的i已经被坏，所以本函数无法正确执行
    }
}
```

可将上列函数中的i虚列在该函数的参数列上，这样i便是一个局部变量，不会因执行子函数而被破坏。将上列函数修改如下：

```
function function( arg1，i ){
    for(i=1; i< 20; i++){
        function(x)     #awk不会检查呼叫函数时，所传递的参数个数是否一致
    }
}
```
$0, $1, ..., NF, NR等都是awk的全局变量global variable，在递归函数中如果使用了这些awk的内部变量，可以新建一些局部变量来保存内部变量的值，以免这些内部变量的值遭到破坏。

例如：要求输入一串数据(各数据间用空白隔开) 然后打印出这些数据的所有可能的排列。使用awk编程如下

```
[root@myfreelinux pub]# cat sort.awk
#!/bin/bash
awk ‘
BEGIN{
    print "please input some word,and each word separate with space:";
    getline;
    sort($0,"");
    printf("\n There are %d way to permutation these word\n",counter);
}
function sort(all_word,buffer,new_all_word,nf,i,j)
{
     $0=all_word;   #all_word给$0之后awk将自动进行字段分割,默认使用空格分割
     nf=NF;         #分割后NF是all_word上的单词个数
     if(nf==1)      #只有最后一个单词时，
     {
          print buffer all_word;    #buffer的内容再加上all_word是完成一次排列的结果
          counter++;
          return;
     }
     #一般情况:每次从all_word中取出一个元素放到buffer中,再用all_word中剩下的元素new_all_word往下进行排列
     else for(i=1;i<=nf;i++)
     {
          $0=all_word;          #$0作为全局变量，内容发生改变，所以重新把all_word赋给$0，令awk再做一次字段分割
          new_all_word="";
          for(j=1;j<=nf;j++)    #连接new_all_word
            if(j!=i) new_all_word=new_all_word " " $j;
          sort(new_all_word,buffer " " $i);
     }
}’ $*
```

执行该程序，并查看运行结果：

```
[root@myfreelinux pub]# bash sort.awk
please input some word,and each word separate with space:
my you he
my you he
my he you
you my he
you he my
he my you
he you my
There are 6 way to permutation these word
```
    解释说明：某些旧版awk，不允许更改$0的内容，可改用gawk或nawk，也可以使用split() 函数来分割all_word。为避免执行子函数时破坏new_all_word，nf，i，j，所以将这些变量也写在参数列上。这样new_all_word，nf，i，j将被当成局部变量， 不会受到子函数中同名变量的影响。
    在声明函数时，参数列上可以将这些"虚列的参数"与真正用于传递信息的参数间以较长的空白隔开，以便于区别。awk 中要将字符串concatenation(连接)时，直接将两字符串放在一起用空格隔开即可(Implicit Operator)。
    比如：[root@myfreelinux pub]# awk ‘BEGIN{a="I"; b=" am"; c=a b " a student"; print c}’
I am a student
    需要注意c=a b " a student"，这一句中a，b " a student"之间要有空格将他们隔开，否则awk把他们当成一个单词了。
   awk编写的函数可以重复使用，比如把函数部分单独编写在一文件中，当需要用到这个函数时使用include，将这个函数文件包含进来，例如： $ awk -f 函数文件名 -f awk主程序文件名 数据文件名，这样，以前编写的函数程序就可以使用了，而不必重复编写这个函数了。
   
   
## 第十一 awk 中常用的模式
awk 通过判断模式（Pattern）的值来决定是否执行其后对应的动作（Actions）。首先来看一下awk中几个常见的模式，在前十部分中，有一些模式已经做了介绍，在这里再总结一下：

1. BEGIN是awk 的保留字，是一种特殊的模式。

BEGIN 成立(其值为true)的时机是："awk 程序一开始执行，还没有读取任何数据之前"。 所以在BEGIN{ Actions} 语法中，Actions只在程序一开始执行时被执行一次。当awk 从数据文件读入数据行后，BEGIN 便不再成立，所以不论数据文件有多少数据行数据，Actions也不会被再次执行。一般情况下，把"与数据文件内容无关"和"只需执行ㄧ次"的部分放在以BEGIN 为模式的Actions中。
    比如：[root@myfreelinux pub]# cat BEGIN.awk

```
#!/bin/awk -f
BEGIN{
    FS="[ \t:]+"; #设置awk分割字段的默认方式
    RS=""  #设置awk分割数据行的方式

    count=10; #设置count的初始值
    print "====This is title====" #打印标题行
}
```

有些awk程序不需要读入任何数据行，这情况可把整个程序写在以BEGIN 为模式的函数中，有时候也可以写在以END为模式的函数中，END后面介绍。
例如：[root@myfreelinux pub]# awk 'BEGIN{print "hello world!"}'会打印出"hello world！"，在awk语句后面则不需要有数据文件，这就是BEGIN模式的特点。

2. END式

END是awk 的保留字，也是一个特殊的模式。END 成立(其值为true)的时机和BEGIN成立的时机正好相反，END成立的时机是："awk 处理完所有数据，即将离开程序时"，在平常读入数据行时，END模式不成立，所以END对应的Actions 并不被执行；只有当awk处理完所有数据后，END对应的Actions才会被执行。和BEGIN模式一样，不管数据文件有多少行数据，该Actions只被执行一次。

3. 关系表达式

awk 中提供了很多关系运算符(Relation Operator)

| 运算符 | 含意              |
| ------ | ----------------- |
| >      | 大于              |
| <      | 小于              |
| >=     | 大于或等于        |
| <=     | 小于或等于        |
| ==     | 等于              |
| !=     | 不等于            |
| ~      | 匹配 match        |
| !~     | 不匹配not match   |

以上关系运算符除~(match)与!~(not match)外，和C 语言中的含意一样。 ~(match) 与!~(match) 在awk的含意简述如下： 如果A 为一字符串，B 为一正则表达式，那么A~B就是判断字符串A 中是否包含能匹配(match)B式样的子字符串；而A!~B是判断 字符串A 中是否没有包含能匹配(match)B式样的子字符串。

例如：[root@myfreelinux pub]# awk 'BEGIN{A="abcdef";B="cd";if(A~B){print A}}'，是判断A中是否有可以匹配B的子字符串，即"abcdef中是否包含有"cd"，如果有，则打印出A字符串。

再比如$0 ~ /program[0-9]+\.c/，模式中被用来比对的字符串为$0 时，可只以正则表达式部分表示整个模式。所以这个表达式可以将模式部分$0 ~/program[0-9]+\.c/仅用/program[0- 9]+\.c/来表示，因为正则表达式中"."有特殊含义，所以在这里使用\转义一下。

4. 正则表达式
在awk中可以直接使用正则表达式当成模式；这是$0~ 正则表达式的简写。这种模式用来判断$0(数据行) 中是否含有匹配该正则表达式的子字符串；如果含有则成立(true)并执行其对应的Actions。
例如：
```
[root@myfreelinux pub]# echo "123″>inte
[root@myfreelinux pub]# awk '{if(/^[0-9]+$/)print "this line is integer!";}' inte
this line is integer!
```
与
```
[root@myfreelinux pub]# awk '{if($0~/^[0-9]+$/)print "this line is integer!";}' inte
```
相同

5、混合模式

以上介绍的4中模式计算后结果为一逻辑值(True or  False)。awk 各个逻辑值间可通过&&(and)， ""(or)，  !(not) 结合成一个新的逻辑值，所以可以将不同的逻辑值通过上述结合符号来结合成一个新的模式，这样可进行复杂的条件判断。

例如： `FNR >= 23 && FNR <= 28 { print "     " $0 }`

上式利用&& (and) 将两个模式求值的结果合并成一个逻辑值，该式将数据文件中第23行到28行向右移5格(先输出5个空白字符) 后输出。

关于FNR在这里介绍一下，FNR同NR一样都是awk 的内建变量，awk有时候处理多个文件，NR会记录所有文件的行数，而FNR则每打开一个文件，FNR会重新计数打开文件的行数，举个例子看看：

```
[root@myfreelinux pub]# cat inte
123
324
[root@myfreelinux pub]# cat integer
222
333
444
[root@myfreelinux pub]# awk '{print NR,FNR}' inte integer
1 1
2 2
3 1
4 2
5 3
```
上边结果中，左侧是NR输出地是行数，一直在增加，右侧是每个文件的行数，这就是NR和FNR的区别。

6. 模式1 ， 模式2

遇到这种模式，awk 会设立一个switch(或flag)。当awk读入的数据行使得模式1 成立时，awk 会打开(turn on)这switch。当awk读入的数据行使得模式2 成立时，awk 会关上(turn off)这个switch。该模式成立的条件是：当这个switch 被打开(turn on)时 (包括模式1， 或模式2 成立的情况)

例如：`FNR >= 23 && FNR <= 28 { print "     " $0 }` 可改写为 `FNR == 23 ， FNR == 28 { print "     " $0 }`

说明：当FNR >= 23 时，awk 就turn on 这个switch；因为随着数据行的读入，awk不停的累加FNR。当 FNR = 28 时，模式2 (FNR == 28)  便成立，这时awk 会关上这个switch。 当switch 打开期间，awk 会执行  print "     " $0  ，关闭后，就不再打印了，也同样达到了相同的效果。





        

          

          

       
              






















































































