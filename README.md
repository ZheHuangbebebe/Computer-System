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
<p align="center">
<img src="/img/1132383-20180927113620240-151805730.png" width="70%" height="60%">
</p>


* 预处理阶段：根据以字符#开头的命令，修改原始的C程序
* 编译阶段：将文本文件hello.i 翻译成文本文件hello.s，它包含一个*汇编语言程序*。
* 汇编阶段：汇编器将hello.s翻译成机器语言指令，把这些指令打包成一种叫做*可重定位目标程序*（relocatable object program）的格式，并将结果保存在目标文件hello.o中
* 链接阶段：hello程序调用了printf函数，他是每个C编译器都提供的标准C库中的一个函数。printf存在于一个名为printf.o的单独的预编译好了的目标文件中，而这个文件必须以某种方式合并到我们的hello.o程序中。结果就得到hello文件，他是一个*可执行目标文件*（或者简称为可执行文件）。

### 系统的硬件组成
<p align="center">
<img src="/img/1297993-20181006103326568-224937041.png" width="70%" height="60%" >
</p>


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
<p align="center">
<img src="/img/0.jfif" width="60%" height="40%">  
</p>


当我们在键盘上敲回车时，shell程序就知道我们已经结束了命令的输入。然后shell执行一系列指令来加载可执的hello文件，这些指令将hello目标文件中的代码和数据从磁盘复制到主存。数据包括最终会输出的字符串“hello，world\n”。  
<p align="center">
<img src="/img/1.jfif" width="60%" height="40%">  
</p>


一旦目标文件hello中的代码和数据被加载到主存，处理器就开始执行hello程序的main程序中的机器语言指令。这些指令将“hello，world\n”字符串中的字节从贮存复制到寄存器文件中，再从寄存器文件中复制到显示设备，最终显示在屏幕上。  
<p align="center">
<img src="/img/2.jfif" width="60%" height="40%">
</p>


### 高速缓存  
根据机械原理，较大的储存设备要比较小的储存设备运行的慢，而快速设备的造价远高于同类的低速设备。一个典型的寄存器文件只存储几百字节的信息，而主存里可以存放几十亿字节。然而处理器从寄存器文件中读数据比从主存中读取几乎要快100倍。更麻烦的是，随着这些年半导体技术的进步，这种*处理器与主存之间的差距*还在持续增大。加快处理器的运行速度比加快主存的运行速度要更容易和便宜的多。  
针对处理器与主存之间的从差异，系统设计者采用了更小更快的储存设备，称你为*高速缓存存储器*（chache memory，简称为cache或高速缓存），作为展示的集结区域，存放处理器近期可能会需要的信息。  
<p align="center">
<img src="/img/1092156-20190826203634215-139650438.png" width="60%" height="40%"> 
</p>

### 储存设备形成层次结构  
在处理器和一个较大较慢的设备（例如主存）之间插入一个更小更快的储存设备（例如高速缓存）的想法已经成为一个普遍的观念。实际上，每个计算机系统中的存储设备都被组织成了一个*存储器层次结构*。在这个结构中，从上至下，设备的访问速度越来越慢、容量越来越大，并且每字节的造价也越来越便宜。  
<p align="center">
<img src="/img/1-6-1.png" width="60%" height="40%">  
</p>

### 操作系统管理硬件  
当shell加载和运行hello程序时，以及hello成语输出自己的消息时，shell和hello程序都没有直接访问键盘、显示器、磁盘或者主存。取而代之的是，他们依靠*操作系统*提供的服务。我们可以把操作系统看成是应用程序和硬件之间插入的一层软件。所有应用程序对硬件的操作尝试都必须通过操作系统。  
**操作系统的两个基本功能：** 
1. 防止硬件被失控的应用程序滥用。
2. 向应用程序提供简单一致的机制来控制复杂而又通常大不相同的低级硬件设备。  
操作系统通过几个基本的抽象概念（进程、悉尼内存和文件）来实现这两个功能。
<p align="center">
<img src="/img/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA4Mnp5YnBneTFnYm9xMWExNmQ0ajMxMTgwdGk3d2guanBn.jfif" width="30%" height="20%">    
</p>

### 进程  
进程是计算机科学中最重要和最成功的概念之一。**进程是操作系统对一个正在运行的程序的一种抽象**。在一个系统上可以同时运行多个进程，而每个进程都好像想在独占地使用硬件。而*并发运行*，则是说一个进程的指令和另一个进程的指令是交错执行的。**在大多数系统中，需要运行的进程数是多于可以运行它们的CPU个数的**。传统系统在一个时刻只能执行一个程序，而先进的*多核*处理器同时能够执行多个程序。无论是在单核还是多核系统中，一个CPU看上去都像是在并发地执行多个进程，这是通过处理器间切换来实现的。操作系统实现这种交错执行的机制称为*上下文切换*。操作系统保持跟踪进程运行所需的所有状态信息。这种状态，也就是*上下文*，包括许多信息，比如PC和寄存器文件的当前值，以及主存的内容。**在任一个时刻，单处理器系统都只能执行一个进程的代码**。当操作系统决定要把控制权从当前进程转移到某个新进程时，就会进行*上下文切换*，及保存当前进程的上下文、回复新进程的上下文，然后将控制权传递到新进程。新进程就会从它上次停止的地方开始。 

<p align="center">
<img src="/img/06184742_KhDl.png" width="60%" height="40%">  
</p>

从一个进程到另一个进程的转换是由操作系统*内核*（kernel）管理的，内核是操作系统代码常驻主存的部分。当应用程序需要操作系统的某些操作时，比如读写文件，它就执行一条特使的*系统调用*（system call）指令，将控制权传递给内核，然后内核执行被请求的操作并返回应用程序。注意，**内核不是一个独立里的进程**。相反，他是系统管理全部进程所用代码和数据结构的集合。

### 线程  
尽管通常我们认为一个进程只有单一的控制流，但在现代系统中，一个进程实际上可以由多个称为*线程*的执行单位组成，每个线程都运行在进程的上下文中，并共享同样的代码和全局数据。**多线程之间比多进程之间更容易共享数据，线程一般来说都比进程更高效。**  

### 虚拟内存
虚拟内存是一个抽象概念，它为每个进程提供了一个假象，即每个进程都在独占地使用主存。每个进程看到的内存都是一致的，称为*虚拟内存*。  
<p align="center">
<img src="/img/unnamed.png" width="60%" height="40%">  
</p>  
  
在Linux中，地址空间最上面的区域是保留给操作系统中的代码和数据的，这对所有进程来说都是一样的。地址空间的底部区域存放用户进程定义的代码和数据。
* *程序代码和数据*：对所有进程来说，代码是从同一固定地址开始，紧接着是和C全局变量相对应的数据位置。代码和数据区是直接按照可执行目标文件的内容初始化的，在示例中就是可执行文件hello。
* *堆*：代码和数据区在进程一开始运行时就被指定了大小，与此不同，**当调用像malloc和free这样的C标准库函数时，堆可以在运行时动态地扩展和收缩。**  
* *共享库*： 大约在地址空间的中间部分时一块用来存放像C标准库和数学库这样的共享库的代码和数据的区域。
* *栈*：位于用户虚拟地址空间的顶部的时用户栈，编译器用它来实现函数调用。和堆一样，用户栈在程序执行期间可以动态地扩展和收缩。**特别地，每次我们调用一个函数时，栈就会增大；从一个函数返回时，栈就会收缩。**  
* *内核虚拟内存*：地址空间顶部的区域是为内核保留的。不允许应用程序读写这个区域的内容或者直接调用内核代码定义的函数。相反，它们必须调用内核来执行这些操作。  

虚拟内存的运作需要硬件和才做系统软件之间的精密复杂的交互，包括对处理器生成的每个地址的硬件翻译。基本思想是把一个进程虚拟内存的内容存储在磁盘上，然后用主存作为磁盘的高速缓存。

### 文件
**文件就是字节序列，仅此而已**。每个I/O设备，包括磁盘、键盘、显示器，甚至网络，都可以看成是文件。系统中的所有输入输出都是通过使用以小组称为Unix I/O的系统函数调用读写文件来实现对。

### Amdahl定律
该定律的主要思想是，当我们对系统的某个部分加速时，其对系统整体性能的影响取决于该部分的重要性和加速程度。若系统执行某应用程序需要时间为T<sub>old</sub>。假设系统某部分所需执行时间与该时间的比例为a，而该部分性能提升比例为k。即该部分初始所需时间为aT<sub>old</sub>，现在所需时间为(aT<sub>old</sub>)/k。因此，总执行时间为  
T<sub>new</sub> = (1-a)T<sub>old</sub> + (aT<sub>old</sub>)/k = T<sub>old</sub>[(1-a) + a/k]  
由此，可以计算加速比 S=T<sub>old</sub>/T<sub>new</sub>为  

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\Large&space;x=\frac{1}{(1-a)+a/k}" title="\Large x=\frac{1}{(1-a)+a/k}"/>  
</p>

Amdahl定律主要观点之一——想要显著加速整个系统，必须提升全系统中相当大的部分的速度。  

### 并发和并行
我们用的术语*并发*（concurrency）是一个通用的概念，只一个同时具有多个活动的系统；而术语*并行*(parallelism)指的是用并发来使一个系统运行得更快。并行可以在计算机系统的多个抽象层次上运用。
1. **线程级并发** ：使用线程，我们甚至能够正在一个进程中执行多个控制流。传统意义上，这种并发执行只是*模拟*出来的，是通过使一台计算机在它正在执行的进程间快速切换来实现的。在以前，即使处理器必须在多个任务间切换，大多数实际的计算也都是有一个处理器来完成的。这种配置称为*单处理器系统*。当构建一个单操作系统内核控制的多处理器组成的系统是，我们就得到了一个多处理器系统。*超线程*（hyperthreading），有时称为*同时多线程*（simultaneous nulti-threading），是一项允许一个CPU执行多个控制流的技术。多处理器的使用可以从两方面提高系统性能。首先，它减少了在执行多个任务时模拟并发的需要。其次，它可以使应用程序运行的更快，当然，这必须要求程序是以多线程的方式来书写的，这些线程可以并行地高效执行。
<p align="center">
<img src="/img/多核处理器的组织结构.JPG" width="30%" height="20%">  
</p>

2. **指令级并行**：在较低的抽象层次上，现代处理器可以同时执行多条指令的属性称为*指令级并行*。在*流水线*（pipelining）中，将执行一条指令所需要的活动划分成不同的步骤，将处理器的硬件组织成一系列的阶段，每个阶段执行一个步骤。这些阶段可以并行地操作，用来处理不同指令的不同部分。我们会看到一个箱单简单的硬件设计，它能够达到接近于一个时钟周期一条指令的执行速率。如果处理器可达到比一个周期一条指令更快的执行速率，就称之为*超标量*（super-scalar）处理器。  
3. **单指令、多数据并行**：也称为SIMD并行，即在最低层次上，许多现代处理器拥有特殊的硬件，允许一条指令产生多个可以并行执行的操作。

### 抽象
抽象的使用是计算机科学中最为重要的概念之一。在处理器里，*指令集架构*提供了对实际处理器硬件的抽象。使用这个抽象，机器代码程序表现得就好像运行在一个一次只执行一条指令的处理器上。
**虚拟机**，它提供对整个计算机的抽象，包括才做系统、处理器和程序。

## 第二章 信息的表示和处理
*无符号*（unsigned）编码基于传统的二进制表示法，表示大于或者等于零的数字。*补码*（two's-complement）编码时表示有符号整数的最常见的方式，有符号整数就是可以为正或者为负的数字。*浮点数*（floating-point）编码是表示实数的科学计数法的一2为基数的版本。  
计算机的表示法使用有限数量的位来对一个数字编码，因此，当结果太大以至不能表示时，某些运算就会*溢出*（overflow）。整数的表示虽然只能编码一个相对较小的数值范围，但是这种表示是精确的；而浮点数虽然可以编码一个较大的数值范围，但是这种表示只是近似的  
### 信息储存  
大多是计算机使用8位的块，或者*字节*（byte），作为最小的可寻址的内存单位，而不是访问内存中单独的位。机器及程序将内存视为一个非常大的字节数组，称为*虚拟内存*（virtual memory）。内存的每个字节都由一个唯一的数字来标识，称为它的*地址*（address），所有可能地址的集合就成为虚拟地址空间（virtual address space）。  
### 字数据大小
每台计算机都有一个*字长*（word size），指明指针数据的标称大小（norminal size）。因为虚拟地址是以这样的一个字来编码的，所以子长决定的最重要的系统参数就是虚拟地址空间的最大大小。  
### 寻址和字节顺序
对于跨越多字节的程序对象，我们必须建立两个规则：这个对象的地址是什么，以及在内存中如何排列这些字节。某些机器选择在内存中按照从最低有效字节到最高有效字节的顺序储存对象，而另一些机器则按照从最高有效字节到最低有效自己的顺序储存。前一种规则——最低有效字节在最前面的方式，称为*小端法*（little endian）。后一种规则——最高有效字节在最前面的方式，称为*大端法*（big endian）。许多比较新的微处理器是*双端法*（bi-endian），也就是说可以把它们配置成作为大端或者小端的机器去运行。**对大多数应用程序员来说，其机器所使用的字节顺序是完全不可见的。无论为那种类型的机器所编译的程序都会得到相同的结果**。  

### 移位运算
| 操作 | 值 |
|:---:|:--:|
| 参数x | [01100011] [10010101] |
| x << 4 | [00110000] [01010000]|
| x >> 4(逻辑右移) |[00000110] [00001001] |
| x >>4(算术右移)| [00000110] [11111001] |

实际上，**几乎所有的编译器/及其组合都对有符号数使用算术右移，且许多程序员也都假设机器会使用这种右移**。另一方面，**对于无符号数，右移必须是逻辑的**。

## 第三章 程序的机器级表示
### 程序编码
首先，C *预处理器*扩展源代码，插入所有用#include命令指定的文件，并扩展所有用#define声明指定的宏。其次，*编译器*产生两个源文件的汇编代码，名字分别为p1.s和p2.s。接下来，*汇编器*会将汇编代码转化成二进制*目标代码*文件p1.o和p2.o。目标代码是机器代码的一种形式，它包含所有指令的二进制表示，但是还没有填入全局值的地址。最后，*链接器*将两个目标代码文件与实现库函数(例如printf)的代码合并并产生最终的可执行代码文件p(由命令行指示符 -o p 指定的)。

### 机器级代码
对于机器级编程来说，其中两种抽象尤为重要。第一种是由*指令集体系结构或指令集架构*(Instruction Set Architecture, ISA)来定义机器级程序的格式和行为，它定义了处理器状态、指令的格式，以及每条指令对状态的影响。大多数ISA，将程序的行为描述成好像每条指令都是按顺序执行的，一条指令结束后，下一条才开始。处理器的硬件远比描述的精细复杂，它们并发地执行许多指令，但是可以采取措施保证整体行为与ISA指定的顺序执行的行为完全一致。第二种抽象是，机器级程序使用的内存地址是虚拟地址，提供的内存模型看上去是一个非常大的字节数组。储存器系统的实际实现是将多个硬件储存器和操作系统软件组合起来。
