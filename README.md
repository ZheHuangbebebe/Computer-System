# Computer-Systems 笔记
## 第一章 计算机系统漫游
*计算机系统*使由硬件和系统软件组成的，它们共同工作来运行应用程序。
```C
  #include <stdio.h>
  
  int main(){
    printf("hello, world\n");
    return 0;
  }
```
hello程序的生命周期是从一个*源程序*（或者说源文件）开始的，即程序员通过编辑器闯创建并保存的文本文件，文件名是hello.c。源程序实际上就是一个由值0和1组成的位（又称为比特）序列，8和位被组织成一组，称为字节。每个字节表示程序中的某些文本字符。
</br>
像hello.c这样中只由ASCII字符构成的文件称为*文本文件*，所有其他文件都称为*二进制文件*。系统中所有的信息——包括磁盘文件、内存中的程序、内存中存放到用户数据以及网络上传送到数据，都是有一串比特表示对。在不同的上下文中，一个同样的字节序列可能表示一个整数、浮点数、字符串或者机器指令。
</br>

每条C语句都必须被其他程序转化为一系列的低级*机械语言*指令。这四个阶段（预处理器，编辑器，汇编器和链接器）一起构成了*编译系统*（compilation system）
<img src="/img/1132383-20180927113620240-151805730.png" width="70%" height="60%">
* 预处理阶段：根据以字符#开头的命令，修改原始的C程序
* 编译阶段：将文本文件hello.i 翻译成文本文件hello.s，它包含一个*汇编语言程序*。
* 汇编阶段：汇编器将hello.s翻译成机器语言指令，把这些指令打包成一种叫做*可重定位目标程序*（relocatable object program）的格式，并将结果保存在目标文件hello.o中
* 链接阶段：hello程序调用了printf函数，他是每个C编译器都提供的标准C库中的一个函数。printf存在于一个名为printf.o的单独的预编译好了的目标文件中，而这个文件必须以某种方式合并到我们的hello.o程序中。结果就得到hello文件，他是一个*可执行目标文件*（或者简称为可执行文件）。

### 系统的硬件组成
<img src="/img/1297993-20181006103326568-224937041.png" width="70%" height="60%" >


* 总线： 贯穿整个系统的一组电子管道，他携带信息字节并负责在各个部件间传递。通常总线被设计成传送定长的字节快，也就是字（word）。字中的字节数（即字长）是一个基本的系统参数，各个系统中都不尽相同。
* I/O设备：使系统与外部世界的联系通道。最开始，可执行程序hello就存放在磁盘上。每个I/O设备都通过一个*控制器*或*适配器*与I/O总线相连。控制器和适配器之间的区别主要在于它们的封装方式，控制器使I/O设备本是或者系统的主印制电路板（通常称为主板）上的芯片组。而适配器则是一块插在主板上插槽的卡。无论如何，它们的功能都是在I/O设备之间传递信息。
* 主存：是一个临时储存设备，在处理器执行程序时，用来存放程序和程序处理的诗句。从物理上来说，主存使由一组*动态随机存取储存器*（DRAM）芯片组成的。从逻辑上来说，**存储器是一个线性的字节数组，每个字节都有其唯一的地址（数组索引）**，这些地址是从令零开始的，一般来说，组成程序的每条机器指令都由不同适量的字节构成。
* 处理器：*中央处理单元*（CPU），简称处理器，是解释（或执行）存储在主存中指令的引擎。处理器的核心是一个大小为一个字的储存设备（或寄存器），称为*程序计数器*（PC）。在任何时刻，PC都指向主存中的某条机器语言指令（即含有该条指令的地址）。
从系统通电开始，知道系统断电，处理器一直在不断的执行程序计数器指向的指令，再更新程序计数器，使其指向下一条指令。处理器*看上去*是按照一个非常简单的指令执行模型来操作的。这个模型是由*指令集架*决定的。在这个模型中，指令按照严格的顺序执行，而执行一条指令包含一系列的步骤。处理器从程序计数器指向的内存处读取指令，解释指令中的位，执行该指令指示的简单操作，然后更新PC，使其指向下一条指令，而这条指令并不一定和在内存中刚刚执行的指令相邻。  
这样的简单操作并不多，他们围绕着主存、*寄存器文件*（reguster file）和算术/*逻辑单元*（ALU）进行。寄存文件是一个小的储存设备，由一些单个字长的寄存器组成，每个寄存器都有唯一的名字。ALU计算新的数据和地址值。下面这些是一些简单操作的例子，CPU在指令的要求下可能会执行这些操作。
  - 加载：从主存复制一个字节或者一个字到寄存器，以覆盖寄存器原来的内容。
  - 存储：从寄存器赋值一个字节或者一个字到主存的某个位置，以覆盖这个位置上原来得到内容。
  - 操作：把两个寄存器的内容复制到ALU，ALU对这两个字做算术运算，并将结果存放到一个寄存器中，以覆盖该寄存器中原来的内容。
  - 跳转：从指令本身中抽取一个字，并将这个字复制到程序计数器（PC）中，以覆盖PC中原来的值。  

处理器看上去是它的指令集架构的简单实现，但是实际上现代处理器使用了非常复杂的机制来加速程序的执行。因此，我们将处理器的指令集架构和处理器的*微体系结构*区分开来：指令集架构描述的是每条机器代码指令的效果；而微体系架构描述的是处理器实际上是如何实现的。  


### 运行hello程序  
初始时，shell程序执行它的指令，等待我们输入一个指令。当我们在键盘上输入字符串“./hello”后，shell程序将字符逐一读入寄存器，再把它存放到内存中。  
<img src="/img/0.jfif" width="60%" height="40%">  
当我们在键盘上敲回车时，shell程序就知道我们已经结束了命令的输入。然后shell执行一系列指令来加载可执的hello文件，这些指令将hello目标文件中的代码和数据从磁盘复制到主存。数据包括最终会输出的字符串“hello，world\n”。  
<img src="/img/1.jfif" width="60%" height="40%">  
一旦目标文件hello中的代码和数据被加载到主存，处理器就开始执行hello程序的main程序中的机器语言指令。这些指令将“hello，world\n”字符串中的字节从贮存复制到寄存器文件中，再从寄存器文件中复制到显示设备，最终显示在屏幕上。  
<img src="/img/2.jfif" width="60%" height="40%">

### 高速缓存  
根据机械原理，较大的储存设备要比较小的储存设备运行的慢，而快速设备的造价远高于同类的低速设备。一个典型的寄存器文件只存储几百字节的信息，而主存里可以存放几十亿字节。然而处理器从寄存器文件中读数据比从主存中读取几乎要快100倍。更麻烦的是，随着这些年半导体技术的进步，这种*处理器与主存之间的差距*还在持续增大。加快处理器的运行速度比加快主存的运行速度要更容易和便宜的多。  
针对处理器与主存之间的从差异，系统设计者采用了更小更快的储存设备，称你为*高速缓存存储器*（chache memory，简称为cache或高速缓存），作为展示的集结区域，存放处理器近期可能会需要的信息。  
<img src="/img/1092156-20190826203634215-139650438.png" width="60%" height="40%"> 

### 储存设备形成层次结构  
在处理器和一个较大较慢的设备（例如主存）之间插入一个更小更快的储存设备（例如高速缓存）的想法已经成为一个普遍的观念。实际上，每个计算机系统中的存储设备都被组织成了一个*存储器层次结构*。在这个结构中，从上至下，设备的访问速度越来越慢、容量越来越大，并且每字节的造价也越来越便宜。  
<img src="/img/1-6-1.png" width="60%" height="40%">  

### 操作系统管理硬件  
当shell加载和运行hello程序时，以及hello成语输出自己的消息时，shell和hello程序都没有直接访问键盘、显示器、磁盘或者主存。取而代之的是，他们依靠*操作系统*提供的服务。我们可以把操作系统看成是应用程序和硬件之间插入的一层软件。所有应用程序对硬件的操作尝试都必须通过操作系统。  
**操作系统的两个基本功能：** 
1. 防止硬件被失控的应用程序滥用。
2. 向应用程序提供简单一致的机制来控制复杂而又通常大不相同的低级硬件设备。  
操作系统通过几个基本的抽象概念（进程、悉尼内存和文件）来实现这两个功能。
<img src="/img/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA4Mnp5YnBneTFnYm9xMWExNmQ0ajMxMTgwdGk3d2guanBn.jfif" width="30%" height="20%">    

### 进程  
进程是计算机科学中最重要和最成功的概念之一。**进程是操作系统对一个正在运行的程序的一种抽象**。在一个系统上可以同时运行多个进程，而每个进程都好像想在独占地使用硬件。而*并发运行*，则是说一个进程的指令和另一个进程的指令是交错执行的。**在大多数系统中，需要运行的进程数是多于可以运行它们的CPU个数的**。传统系统在一个时刻只能执行一个程序，而先进的*多核*处理器同时能够执行多个程序。无论是在单核还是多核系统中，一个CPU看上去都像是在并发地执行多个进程，这是通过处理器间切换来实现的。操作系统实现这种交错执行的机制称为*上下文切换*。操作系统保持跟踪进程运行所需的所有状态信息。这种状态，也就是*上下文*，包括许多信息，比如PC和寄存器文件的当前值，以及主存的内容。**在任一个时刻，单处理器系统都只能执行一个进程的代码**。当操作系统决定要把控制权从当前进程转移到某个新进程时，就会进行*上下文切换*，及保存当前进程的上下文、回复新进程的上下文，然后将控制权传递到新进程。新进程就会从它上次停止的地方开始。 

<img src="/img/06184742_KhDl.png" width="60%" height="40%">  

从一个进程到另一个进程的转换是由操作系统*内核*（kernel）管理的，内核是操作系统代码常驻主存的部分。当应用程序需要操作系统的某些操作时，比如读写文件，它就执行一条特使的*系统调用*（system call）指令，将控制权传递给内核，然后内核执行被请求的操作并返回应用程序。注意，**内核不是一个独立里的进程**。相反，他是系统管理全部进程所用代码和数据结构的集合。

### 线程  
尽管通常我们认为一个进程只有单一的控制流，但在现代系统中，一个进程实际上可以由多个称为*线程*的执行单位组成，每个线程都运行在进程的上下文中，并共享同样的代码和全局数据。**多线程之间比多进程之间更容易共享数据，线程一般来说都比进程更高效。**  

### 虚拟内存
虚拟内存是一个抽象概念，它为每个进程提供了一个假象，即每个进程都在独占地使用主存。每个进程看到的内存都是一致的，称为*虚拟内存*。  
<img src="/img/unnamed.png" width="60%" height="40%">  
在Linux中，地址空间最上面的区域是保留给操作系统中的代码和数据的，这对所有进程来说都是一样的。地址空间的底部区域存放用户进程定义的代码和数据。
* *程序代码和数据*：对所有进程来说，代码是从同一固定地址开始，紧接着是和C全局变量相对应的数据位置。代码和数据区是直接按照可执行目标文件的内容初始化的，在示例中就是可执行文件hello。
* *堆*：代码和数据区在进程一开始运行时就被指定了大小，与此不同，**当调用像malloc和free这样的C标准库函数时，堆可以在运行时动态地扩展和收缩。**  
* *共享库*： 大约在地址空间的中间部分时一块用来存放像C标准库和数学库这样的共享库的代码和数据的区域。
* *栈*：位于用户虚拟地址空间的顶部的时用户栈，编译器用它来实现函数调用。和堆一样，用户栈在程序执行期间可以动态地扩展和收缩。**特别地，每次我们调用一个函数时，栈就会增大；从一个函数返回时，栈就会收缩。**  
* *内核虚拟内存*：地址空间顶部的区域是为内核保留的。不允许应用程序读写这个区域的内容或者直接调用内核代码定义的函数。相反，它们必须调用内核来执行这些操作。  

虚拟内存的运作需要硬件和才做系统软件之间的精密复杂的交互，包括对处理器生成的每个地址的硬件翻译。基本思想是把一个进程虚拟内存的内容存储在磁盘上，然后用主存作为磁盘的高速缓存。

### 文件
**文件就是字节序列，仅此而已**。每个I/O设备，包括磁盘、键盘、显示器，甚至网络，都可以看成是文件。系统中的所有输入输出都是通过使用以小组称为Unix I/O的系统函数调用读写文件来实现对。








