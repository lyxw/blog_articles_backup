# Android逆向基础之Dalvik指令集

Dalvik是Google公司自己设计的用于Android平台的虚拟机。Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一。它可以支持已转换为 .dex（即Dalvik Executable）格式的Java应用程序的运行，.dex格式是专为Dalvik设计的一种压缩格式，适合内存和处理器速度有限的系统。Dalvik 经过优化，允许在有限的内存中同时运行多个虚拟机的实例，并且每一个Dalvik 应用作为一个独立的Linux 进程执行。独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。

## 1. 指令特点

Dalvik指令在调用格式上模仿了C语言的调用约定。Dalvik指令的语法与助词符有如下特点：
- 参数采用从目标（destination）到源（source）的方式。
- 根据字节码的大小与类型不同，一些字节码添加了名称后缀以消除岐义。
  + 32位常规类型的字节码末添加任何后缀。
  + 64位常规类型的字节码添加 -wide后缀。
  + 特殊类型的字节码根据具体类型添加后缀。它们可以是 `-boolean，-byte，-char，-short，-int，-long，-float，-double，-object，-string，-class，-void`之一。
- 根据字节码的布局与选项不同，一些字节码添加了字节码后缀以消除岐义。这些后缀通过在字节码主名称后添加斜杠“/”来分隔开。
- 在指令集的描述中，宽度值中每个字母表示宽度为4位。

例如这条指令：`move-wide/from16 vAA, vBBBB`：
- move为基础字节码（base opcode），标识这是基本操作。
- wide为名称后缀（name suffix），标识指令操作的数据宽度（64位）。
- from16为字节码后缀（opcode suffix），标识源为一个16位的寄存器引用变量。
- vAA为目的寄存器，它始终在源的前面，取值范围为v0~v255。
- vBBBB为源寄存器，取值范围为v0~v65535。

Dalvik指令集中大多数指令用到了寄存器作为目的操作数或源操作数，其中：
- `A/B/C/D/E/F/G/H`代表一个4位的数值，可用来表示0～15的数值或v0~v15的寄存器；
- `AA/BB/CC/DD/EE/FF/GG/HH`代表一个8位的数值，可用来表示0~255的数值或v0~v255的寄存器；
- `AAAA/BBBB/CCCC/DDDD/EEEE/FFFF/GGGG/HHHH`代表一个16位的数值，可用来表示0～65535的数值或v0~v65535的寄存器。

**注意：**
- Android官方指令文档描述寄存器时，对不同取值范围的寄存器以括号说明其大小，如A:destination register(4 bits)，A:destination register(16 bits)。
- Dalvik虚拟机中的每个寄存器都是32位的，描述指令时所说的位数表示的是寄存器数值的取值范围。

## 2. 空操作指令

空操作指令的助记符为`nop`。它的值为00，通常nop指令被用来作对齐代码之用，无实际操作。

## 3. 数据操作指令

数据操作指令为`move`。move指令的原型为`move destination,source`，move指令根据字节码的大小与类型不同，后面会跟上不同的后缀。

| 指令           | 说明                                                    |
| :------------- | :------------------------------------------------------ |
| move vA, vB    | 将vB寄存器的值赋给vA寄存器，源寄存器与目的寄存器都为4位 |
| move/from16 vAA, vBBBB  | 将vBBBB寄存器的值赋给vAA寄存器，源寄存器为16位，目的寄存器为8位 |
| move/16 vAAAA, vBBBB  | 将vBBBB寄存器的值赋给vAAAA寄存器，源寄存器与目的寄存器都为16位 |
| move-wide vA, vB      | 为4位的寄存器对赋值。源寄存器与目的寄存器都为4位 |
| move-wide/from16 vAA, vBBBB   | 与`move-wide`相同 |
| move-wide/16 vAAAA, vBBBB     | 与`move-wide`相同 |
| move-object vA, vB | 为对象赋值。源寄存器与目的寄存器都为4位 |
| move-object/from16 vAA, vBBBB | 为对象赋值。源寄存器为16位，目的寄存器为8位 |
| move-object/16 vAA, vBBBB | 为对象赋值。源寄存器与目的寄存器都为16位 |
| move-result vAA | 将上一个invoke类型指令操作的单字非对象结果赋给vAA寄存器 |
| move-result-wide vAA | 将上一个invoke类型指令操作的双字非对象结果赋给vAA寄存器| 
| move-result-object vAA | 将上一个invoke类型指令操作的对象结果赋给vAA寄存器 |
| move-exception vAA | 保存一个运行时发生的异常到vAA寄存器，这条指令必须是异常发生时的异常处理器的一条指令,否则指令无效 |

## 4. 返回指令 

返回指令指的是函数结尾时运行的最后一条指令。它的基础字节码为`teturn`，共有以下四条返回指令：

| 指令              | 说明                                                             |
| :---------------- | :--------------------------------------------------------------- |
| return-void       | 表示函数从一个void方法返回                                       |
| return vAA        | 表示函数返回一个32位非对象类型的值，返回值寄存器为8位的寄存器vAA |
| return-wide vAA   | 表示函数返回一个64位非对象类型的值，返回值为8位的寄存器对vAA     |
| return-object vAA | 表示函数返回一个对象类型的值。返回值为8位的寄存器vAA             |

## 5. 数据定义指令

数据定义指令用来定义程序中用到的常量，字符串，类等数据。它的基础字节码为`const`。

| 指令              | 说明                           |
| :---------------- | :------------------------------- |
| const/4 vA, #+B | 将数值符号扩展为32位后赋给寄存器vA |
| const/16 vAA, #+BBBB | 将数据符号扩展为32位后赋给寄存器vAA |
| const vAA, #+BBBBBBBB | 将数值赋给寄存器vAA |
| const/high16 vAA, #+BBBB0000 | 将数值右边零扩展为32位后赋给寄存器vAA |
| const-wide/16 vAA, #+BBBB | 将数值符号扩展为64位后赋给寄存器对vAA |
| const-wide/32 vAA, #+BBBBBBBB | 将数值符号扩展为64位后赋给寄存器对vAA |
| const-wide vAA, #+BBBBBBBBBBBBBBBB | 将数值赋给寄存器对vAA |
| const-wide/high16 vAA, #+BBBB000000000000 | 将数值右边零扩展为64位后赋给寄存器对vAA |
| const-string vAA, string@BBBB | 通过字符串索引构造一个字符串并赋给寄存器vAA |
| const-string/jumbo vAA, string@BBBBBBBB | 通过字符串索引（较大）构造一个字符串并赋给寄存器vAA |
| const-class vAA, type@BBBB | 通过类型索引获取一个类引用并赋给寄存器vAA |
| const-class/jumbo vAAAA, type@BBBBBBBB | 通过给定的类型索引获取一个类引用并赋给寄存器vAAAA。<br>这条指令占用两个字节，值为0xooff（Android4.0中新增的指令） |

## 6. 锁指令 

锁指令多用在多线程程序中对同一对象的操作。Dalvik指令集中有两条锁指令：

| 指令              | 说明               |
| :---------------- | :----------------- |
| monitor-enter vAA | 为指定的对象获取锁 |
| monitor-exit vAA  | 释放指定的对象的锁 |

## 7. 实例操作指令 

与实例相关的操作包括实例的类型转换，检查及新建等：

| 指令              | 说明               |
| :---------------- | :----------------- |
| check-cast vAA, type@BBBB | 将vAA寄存器中的对象引用转换成指定的类型，如果失败会抛出ClassCastException异常。<br>如果类型B指定的是基本类型，对于非基本类型的A来说，运行时始终会失败 |
| instance-of vA, vB, type@CCCC | 判断vB寄存器中的对象引用是否可以转换成指定的类型，如果可以vA寄存器赋值为1，否则vA寄存器赋值为0 |
| new-instance vAA, type@BBBB | 构造一个指定类型对象的新实例，并将对象引用赋值给vAA寄存器，类型符type指定的类型不能是数组类 |
| check-cast/jumbo vAAAA, type@BBBBBBBB | 指令功能与`check-cast vAA, type@BBBB`相同，只是寄存器值与指令的索引取值范围更大（Android4.0中新增的指令） |
| instance-of/jumbo vAAAA, vBBBB, type@CCCCCCCC | 指令功能与`instance-of vA, vB, type@CCCC`相同，只是寄存器值与指令的索引取值范围更大（Android4.0中新增的指令） |
| new-instance/jumbo vAAAA, type@BBBBBBBB | 指令功能与`new-instance vAA, type@BBBB`相同，只是寄存器值与指令的索引取值范围更大（Android4.0中新增的指令） |

## 8. 数组操作指令

数组操作包括获取数组长度，新建数组，数组赋值，数组元素取值与赋值等操作。

| 指令              | 说明               |
| :---------------- | :----------------- |
| array-length vA, vB | 获取给定vB寄存器中数组的长度并将值赋给vA寄存器，数组长度指的是数组的条目个数 |
| new-array vA, vB, type@CCCC | 构造指定类型（type@CCCC）与大小（vB）的数组，并将值赋给vA寄存器 |
| filled-new-array {vC, vD, vE, vF, vG},type@BBBB | 构造指定类型（type@BBBB）与大小（vA）的数组并填充数组内容。<br>vA寄存器是隐含使用的，除了指定数组的大小外还指定了参数的个数，vC~vG是使用到的参数寄存序列 |
| filled-new-array/range {vCCCC  ..vNNNN}, type@BBBB | 指令功能与`filled-new-array {vC, vD, vE, vF, vG},type@BBBB`相同，只是参数寄存器使用range字节码后缀指定了取值范围 ，vC是第一个参数寄存器，N = A +C -1 |
| fill-array-data vAA, +BBBBBBBB | 用指定的数据来填充数组，vAA寄存器为数组引用，引用必须为基础类型的数组，在指令后面会紧跟一个数据表 |
| new-array/jumbo vAAAA, vBBBB,type@CCCCCCCC | 指令功能与`new-array vA,vB,type@CCCC`相同，只是寄存器值与指令的索引取值范围更大（Android4.0中新增的指令） |
| filled-new-array/jumbo {vCCCC  ..vNNNN},type@BBBBBBBB | 指令功能与`filled-new-array/range {vCCCC  ..vNNNN},type@BBBB`相同，只是索引取值范围更大（Android4.0中新增的指令） |
| arrayop vAA, vBB, vCC | 对vBB寄存器指定的数组元素进入取值与赋值。<br>vCC寄存器指定数组元素索引，vAA寄存器用来存放读取的或需要设置的数组元素的值 |

读取元素使用aget类指令，元素赋值使用aput类指定，根据数组中存储的类型指令后面会紧跟不同的指令后缀，指令列表如下：
```
aget, aget-wide, aget-object, aget-boolean, aget-byte,aget-char, aget-short, 
aput, aput-wide, aput-object, aput-boolean, aput-byte, aput-char, aput-short
``` 

## 9. 异常指令

Dalvik指令集中有一条指令用来抛出异常。

`throw vAA`用来抛出vAA寄存器中指定类型的异常。

## 10. 跳转指令

跳转指令用于从当前地址跳转到指定的偏移处。Dalvik指令集中有三种跳转指令：无条件跳转（`goto`），分支跳转（`switch`）与条件跳转（`if`）。

| 指令              | 说明               |
| :---------------- | :----------------- |
| goto +AA | 无条件跳转到指定偏移处，偏移量AA不能为0 |
| goto/16 +AAAA | 无条件跳转到指定偏移处，偏量AAAA不能为0 |
| goto/32 +AAAAAAAA | 无条件跳转到指定偏移处 |
| packed-switch vAA, +BBBBBBBB | 分支跳转指令。<br>vAA寄存器为switch分支中需要判断的值，<br>BBBBBBBB指向一个packed-switch-payload格式的偏移表，表中的值是有规律递增的 |
| sparse-switch vAA, +BBBBBBBB | 分支跳转指令。<br>vAA寄存器为switch分支中需要判断的值，<br>BBBBBBBB指向一个sparse-switch-payload格式的偏移表，表中的值是无规律的偏移量 |
| if-test vA, vB, +CCCC | 条件跳转指令。<br>比较vA寄存器与vB寄存器的值，如果比较结果满足就跳转到CCCC指定的偏移处。<br>偏移量CCCC不能为0 |
| if-testz vAA, +BBBB | 条件跳转指令。<br>拿vAA寄存器与0比较，如果比较结果满足或值为0时就跳转到BBBB指定的偏移处。<br>偏移量BBBB不能为0 |

if-test类型的指令有以下几条：

| 指令  | 说明                   | Java语法     |
| :---- | :--------------------- | :----------- |
| if-eq | 如果vA等于vB则跳转     | if(vA == vB) |
| if-ne | 如果vA不等于vB则跳转   | if(vA != vB) |
| if-lt | 如果vA小于vB则跳转     | if(vA < vB)  |
| if-ge | 如果vA大于等于vB则跳转 | if(vA >= vB) |
| if-gt | 如果vA大于vB则跳转     | if(vA > vB)  |
| if-le | 如果vA小于等于vB则跳转 | if(vA <= vB) |

if-testz类型的指令有以下几条：

| 指令   | 说明                   | Java语法     |
| :----- | :--------------------- | :----------- |
| if-eqz | 如果vAA为0则跳转       | if(vAA == 0) |
| if-nez | 如果vAA不为0则跳转     | if(vAA != 0) |
| if-ltz | 如果vAA小于0则跳转     | if(vAA < 0)  |
| if-gez | 如果vAA大于等于0则跳转 | if(vAA >= 0) |
| if-gtz | 如果vAA大于0则跳转     | if(vAA > 0)  |
| if-lez | 如果vAA小于等于0则跳转 | if(vAA <= 0) |

## 11. 比较指令 

比较指令用于对两个寄存器的值（浮点型或长整型）进行比较。它的格式为`cmpkind vAA, vBB, vCC`，其中vBB寄存器与vCC寄存器是需要比较的两个寄存器或寄存器对，比较的结果放到vAA寄存器。Dalvik指令集中共有5条比较指令：

| 指令        | 说明               |
| :---------- | :----------------- |
| cmpl-float  | 比较两个单精度浮点数。<br>如果vBB寄存器大于vCC寄存器，结果为-1，相等则结果为0，小于的话结果为1 |
| cmpg-float  | 比较两个单精度浮点数。<br>如果vBB寄存器大于vCC寄存器，则结果为1，相等则结果为0，小于的话结果为-1 |
| cmpl-double | 比较两个双精度浮点数。<br>如果vBB寄存器对大于vCC寄存器对，则结果为-1，相等则结果为0，小于则结果为1 |
| cmpg-double | 比较两个双精度浮点数。<br>如果vBB寄存器对大于vCC寄存器对，则结果为1，相等则结果为0，小于的话，则结果为-1 |
| cmp-long    | 比较两个长整型数。<br>如果vBB寄存器大于vCC寄存器，则结果为1，相等则结果为0，小则结果为-1 |

## 12. 字段操作指令

字段操作指令用来对对象实例的字段进入读写操作。字段的类型可以是Java中有效的数据类型。对普通字段与静态字段操作有两种指令集，分别是`iinstanceop vA, vB, fidld@CCCC`与`sstaticop vAA, field@BBBB`。

普通字段指令的指令前缀为i，如对普通字段读操作使用`iget`指令，写操作使用`iput`指令；静态字段的指令前缀为s，如对静态字段读操作使用 sget 指令，写操作使用 sput 指令。

根据访问的字段类型不同，字段操作指令后面会紧跟字段类型的后缀，如 iget-byte指令表示读取实例字段 的值类型为字节类型，iput-short指令表示设置实例字段的值类型为短整型。两类指令操作结果都是一样，只是指令前缀与操作的字段类型不同。

普通字段操作指令有：
```
iget，iget-wide，iget-object，iget-boolean，iget-byte，iget-char，iget-short，
iput，iput-wide，iput-object，iput-boolean，iput-byte，iput-char，iput-short。
```

静态字段操作指令有：
```
sget，sget-wide，sget-object，sget-boolean，sget-byte，sget-char，sget-short，
sput，sput-wide，sput-object，sput-boolean，sput-byte，sput-char，sput-short。
```

在Android4.0系统中，Dalvik指令集中增加了`iinstanceop/jumbo vAAAA, vBBBB, field@CCCCCCCC`与`sstaticop/jumbo vAAAA, field@BBBBBBBB`两类指令，它们与上面介绍的两类指令作用相同，只是在指令中增加了jumbo字节码后缀，且寄存器值与指令的索引取值范围更大。

## 13. 方法调用指令

方法调用指令负责调用类实例的方法。它的基础指令为`invoke`，方法调用指令有`invoke-kind {vC, vD, vE, vF, vG},meth@BBBB`与`invoke-kind/range {vCCCC  .. vNNNN},meth@BBBB`两类，两类指令在作用上并无不同，只是后者在设置参数寄存器时使用了range来指定寄存器的范围。根据方法类型的不同，共有如下五条方法调用指令：

| 指令        | 说明               |
| :---------- | :----------------- |
| invoke-virtual 或 invoke-virtual/range | 调用实例的虚方法 |
| invoke-super 或 invoke-super/range | 调用实例的父类方法 |
| invoke-direct 或 invoke-direct/range | 调用实例的直接方法 |
| invoke-static 或 invoke-static/range | 调用实例的静态方法 |
| invoke-interface 或 invoke-interface/range | 调用实例的接口方法 |

在Android4.0系统中，Dalvik指令集中增加了`invoke-kind/jumbo {vCCCC  .. vNNNN},meth@BBBBBBBB`这类指令，它与上面介绍的两类指令作用相同，只是在指令中增加了jumbo字节码后缀，且寄存器值与指令的索引取值范围更大。

方法调用指令的返回值必须使用`move-result*`指令来获取。如下面两条指令：
```smali
invoke-static {}, Landroid/os/Parcel;->obtain() Landroid/os/Parcel;
move-result-object v0
```
## 14. 数据转换指令

数据转换指令用于将一种类型的数值转换成另一种类型。它的格式为`unop vA, vB`，vB寄存器或vB寄存器对存放需要转换的数据，转换后的结果保存在vA寄存器或vA寄存器对中。

| 指令            | 说明                               |
| :-------------- | :--------------------------------- |
| neg-int         | 对整型数求补                       |
| not-int         | 对整型数求反                       |
| neg-long        | 对长整型数求补                     |
| not-long        | 对长整型数求反                     |
| neg-float       | 对单精度浮点型数求补               |
| neg-double      | 对双精度浮点型数求补               |
| int-to-long     | 将整型数转换为长整型               |
| int-to-float    | 将整型数转换为单精度浮点型数       |
| int-to-dobule   | 将整型数转换为双精度浮点数         |
| long-to-int     | 将长整型数转换为整型               |
| long-to-float   | 将长整型数转换为单精度浮点型       |
| long-to-double  | 将长整型数转换为双精度浮点型       |
| float-to-int    | 将单精度浮点数转换为整型           |
| float-to-long   | 将单精度浮点数转换为长整型数       |
| float-to-double | 将单精度浮点数转换为双精度浮点型数 |
| double-to-int   | 将双精度浮点数转换为整型           |
| double-to-long  | 将双精度浮点数转换为长整型         |
| double-to-float | 将双精度浮点数转换为单精度浮点型   |
| int-to-byte     | 将整型转换为字节型                 |
| int-to-char     | 将整型转换为字符型                 |
| int-to-short    | 将整型转换为短整型                 |

## 15. 数据运行指令

数据运算指令包括算术运算指令与逻辑运算指令。算术运算指令主要进行数值间如加，减，乘，除，模，移位等运算。逻辑运算指令主要进行数值间与，或，非，抑或等运算。数据运算指令有以下四类（数据运算时可能是在寄存器或寄存器对间进行，下面的指令作用讲解时使用寄存器来描述）：

| 指令                       | 说明                                                |
| :------------------------- | :-------------------------------------------------- |
| binop vAA, vBB, vCC        | 将vBB寄存器与vCC寄存器进行运算，结果保存到vAA寄存器 |
| binop/2addr vA, vB         | 将vA寄存器与vB寄存器进行运算，结果保存到vA寄存器    |
| binop/lit16 vA, vB, #+CCCC | 将vB寄存器与常量 CCCC进行运算，结果保存到vA寄存器   |
| binop/lit8 vAA, vBB, #+CC  | 将vBB寄存器与常量CC进行运算，结果保存到vAA寄存器    |

后面3类指令比第1类指令分别多出了`2addr，lit16，lit8`等指令后缀。四类指令中基础字节码相同的指令执行的运算操作是类似的，第1类指令中，根据数据的类型不同会在基础字节码后面加上数据类型后缀，如 -int 或 -long 分别表示操作的数据类型为整型与长整型。第1类指令可归类如下：

| 指令      | 说明                                            |
| :-------- | :---------------------------------------------- |
| add-type  | vBB寄存器与vCC寄存器值进行加法运算（vBB + vCC） |
| sub-type  | vBB寄存器与vCC寄存器值进行减法运算（vBB - vCC） |
| mul-type  | vBB寄存器与vCC寄存器值进行乘法运算（vBB * vCC） |
| div-type  | vBB寄存器与vCC寄存器值进行除法运算（vBB / vCC） |
| rem-type  | vBB寄存器与vCC寄存器值进行模运算（vBB % vCC）   |
| and-type  | vBB寄存器与vCC寄存器值进行与运算（vBB & vCC）   |
| or-type   | vBB寄存器与vCC寄存器值进行或运算（vBB &#124; vCC）   |
| xor-type  | vBB寄存器与vCC寄存器值进行异或运算（vBB ^ vCC） |
| shl-type  | vBB寄存器值（有符号数）左移vCC位（vBB << vCC ） |
| shr-type  | vBB寄存器值（有符号）右移vCC位（vBB >> vCC）    |
| ushr-type | vBB寄存器值（无符号数）右移vCC位（vBB >>> vCC） |

其中基础字节码后面的`-type`可以是`-int，-long， -float，-double`。后面3类指令与之类似。 

至此，Dalvik虚拟机支持的所有指令就介绍完了。在android4.0系统以前，每个指令的字节码只占用一个字节，范围是0x0~0x0ff。在android4.0系统中，又扩充了一部分指令，这些指令被称为扩展指令，主要是在指令助记符后添加了jumbo后缀，增加了寄存器与常量的取值范围。
