# gdb

## 一、安装

```
sudo apt-get install gdb
// 需要调试其他架构下的elf程序时
sudo apt-get install gdb-multiarch
```

还可以从github上下载一些插件，peda、gef、pwndbg。

## 二、常用命令

```
gdb program -q // 安静模式调试程序
quit // 退出调试器
run(r) arguments // 运行被调试程序，带参数
attach pid // 调试进程号为pid的进程
detach // 脱离进程
break(b) *address // 下断点
b function_name // 在函数处下断点
tbreak // 设置临时断点，第一次命中后会自动删除，用法同break
break … if cond // 设置条件断点
watch(wa) v // 设置观察点，如果程序中的变量v改变，程序将中断
watch *(data type*)address // 如果内存地址address处改变，程序将中断
watch expr thread threadnum // 只观察对应线程的变量值变化，如果变化程序中断
rwatch(rw) // 设置读观察点，如果程序中的变量或者内存被读取，程序将中断
awatch(aw) // 设置读写观察点，如果变量或者地址被读取或者改变，程序将中断
catch fork/vfork/exec // 为fork、vfork、exec函数设定catchpoint，遇到这些函数时程序将暂停
catch syscall name|num // 当发生对应name或num系统调用时，程序暂停
ignore bnum count // 忽略断点，bnum为断点编号，count表示之后count次触发断点都将忽略
info b // 查看断点
info display // 打印自动显示的表达式列表
info threads // 打印出所有线程的信息
info frame // 打印出指定栈帧的详细信息
info proc // 查看proc里的进程信息
info functions // 列出可执行文件的所有函数名
info proc mappings // 查看进程的内存映射信息
info files/target // 更详细地输出进程的内存信息，包括引用的动态链接库等等
delete number // 删除某个断点
stepi(si) // 单步步入
nexti(ni) // 单步步过
continue(c) // 继续执行
finish // 执行到函数返回处
info register // 查看寄存器
i all-registers // 查看所有寄存器
backtrace(bt) // 打印函数调用栈回溯
print(p) /x expr/file::v // 计算表达式expr的值并打印，打印file文件的变量值，格式为o（八进制）、d（十进制）、x（十六进制）、u（无符号十六进制）、t（二进制）、f（浮点数）、a（地址）、c（字符）
x/CountFormatSize address // 以指定格式Format打印address处指定大小Size、指定数量Count的对象，Size取值，b（字节）、h（半字）、w（字）、g（8字节），Format取值，o（八进制）、d（十进制）、x（十六进制）、u（无符号十进制）、t（二进制）、f（浮点数）、a（地址）、i（指令）、c（字符）、s（字符串）
display/fmt expr/addr // 每次程序停止时打印表达式的值，格式为o（八进制）、d（十进制）、x（十六进制）、u（无符号十六进制）、t（二进制）、f（浮点数）、a（地址）、c（字符）
disassemble(disas) // 反汇编代码
set $reg=value // 修改寄存器的值
set {type}(address)=value // 修改内存地址address处的值为value，类型为type指定，例如int
set var variable=expr // 修改代码中的变量值
shell command // 执行shell命令
set follow-fork-mode parent|child // 当发生fork时，指示调试器跟踪父进程还是子进程
handler SIGALRM ignore // 忽视SIGALRM，调试器接收到的SIGALRM信号不会发送到被调试程序
target remote ip:port // 连接远程调试
thread apply all bt // 打印出所有线程的堆栈信息
generate-core-file(gcore) // 将调试中的进程生成内核转储文件
maint info program-spaces // 打印当前所有被调试的进程信息
i signals/handle // 查看如何处理进程收到的信号
handle signal stop/nostop // 设置信号发生时是否暂停程序
handle signal print/noprint // 设置信号发生时是否打印信号信息
handle signal pass(noignore)/nopass(ignore) // 设置信号发生时是否把信号丢给程序处理
```

## 三、技巧

（1）有时候attach到一个进程时可能会出现报错`ptrace: Operation not permitted.`，这是因为开启了内核参数`ptrace_scope`，通过查看`cat /proc/sys/kernel/yama/ptrace_scope`，如果返回值为1表示开启了，此时普通用户进程是无法对其他进程进行attach操作的，解决办法有两个，一个是使用root用户，另一个是关闭，使用命令`echo 0 > /proc/sys/kernel/yama/ptrace_scope`即可；

（2）带源码调试方法，使用gcc编译时必须加上-g参数，例如`gcc -g test.c -o test`，然后就可以使用`b 行号`来下断点，使用`list 开始行号,结束行号`来查看源码，或者直接`list`查看当前执行位置后10行源码；

（3）进入不带调试信息的函数，有时候调试时无法s进不带调试信息的函数，比如printf，使用命令`set step-mode on`，即可s进入不带调试信息的函数中；

（4）退出正在调试的函数，一种是使用finish，这样是执行完该函数并且会打印返回值，也可以使用return命令，这样函数就不会继续执行下去，而是跳过当前函数，直接到返回的位置，可以带返回值`return expr`，这样就直接指定了函数返回值，在调试的时候比较有用；

（5）可以使用`call func`或`print func`直接调用函数执行；

（6）可以使用`frame n`切换堆栈帧层级，使用`up n`向上切换n层堆栈帧，使用`down n`向下切换n层堆栈帧；

（7）对于没有调试信息的程序，如果不知道main函数的位置，可以在程序入口处打断点，先通过`readelf`获得程序入口地址，然后下地址断点即可；

（8）保存已经设置的断点，当设置断点较多并且不会改变太大时，可以使用命令`save breakpoints file-name-to-save`将断点信息保存在文件中，下次调试时，使用如下命令批量设置保存的断点，`source file-name-to-save`；

（9）有些程序不想被gdb调试，它们就会在程序中调用“ ptrace  ”函数，一旦返回失败，就证明程序正在被
gdb等类似的程序追踪，所以就直接退出，破解这类程序的办法就是为 ptrace  调用设置 catchpoint  ，通过修改 ptrace  的返回值，达到目的；

（10）gdb打印数组的默认长度为200个，当需要打印是数组较大时可以使用`set print elements number-of-elements`设置最大长度，或者`set print elements 0`设置没有最大限制，或者`set print elements unlimited`设置没有最大限制；

（11）打印数组中任意连续元素的值，使用`p array[index]@num`，index为数组索引，表示打印开始处，num为打印的个数；

（12）默认不打印数组的索引下标，可以设置`set print array-indexes on`打印索引下标；

（13）打印局部变量，一个是使用`bt full`，打印所有局部变量的值，另一个是使用`info locals`，打印当前函数的局部变量；

（14）查看变量类型，使用`whatis v`，查看详细变量类型信息使用`ptype v`；

（15）默认情况打印结构体比较混乱，可以设置`set print pretty on`，就能分行显示结构体每一个变量；

（16）打印对象时，可以使用`set print object on`，按照派生类型打印对象；

（17）指定程序的输入输出设备，先打开一个终端，使用`tty`命令查看终端号，然后使用命令指定输入输出设备`gdb -tty /dev/pts/2 ./a.out`，或者在gdb命令行下使用`tty /dev/pts/2`，`/dev/pts/2`为对应终端设备号；

（18）" x  "命令会把最后检查的内存地址值存在“ $_  ”这个变量中，并且会把这个地址中的内容放在“ $__  ”这个变量中，有些隐式调用“ x ”命令的命令也会出现这种情况，比如info line或info b，使用p $\_和p $__就可以打印两个值；

（19）如果需要同时调试父子进程，可以设置`set detach-on-fork off`，这样gdb就能同时调试父子进程，并且在调试一个进程时，另外一个进程处于挂起状态，如果想让父子进程都同时运行，可以使用`set schedule-multiple on`；

（20）只允许一个线程运行，设置`set scheduler-locking on`；

（21）$_thread变量保存当前正在调试的线程号；

（22）一个会话中同时调试多个程序，首先调试a程序，然后使用命令`add-inferior [ -copies n ] [ -exec executable ]`加载另一个程序，n默认为1，也可以使用命令`clone-inferior [ -copies n ] [ infno ]`克隆现有的程序，使用`i inferiors`，查看本次会话有多少程序，使用`inferior num`命令切换进程；

（23）当被调试的程序正常退出时，gdb会使用 $_exitcode  变量记录程序退出时的“ exit code  ”；

（24）调试core dump文件，使用命令`gdb path/to/the/executable path/to/the/coredump`，也可以gdb起来后加载程序和core，使用file加载程序，使用core加载core dump；

（25）设置汇编指令格式，默认是AT&T格式，使用`set disassembly-flavor intel`可以改为intel格式，使用att可以改回，目前“set disassembly-flavor”命令只能用在Intel x86处理器上，并且取值只有“intel”和“att”；

（26）`b func`与`b *func`的区别，前者会断在函数起实际作用的第一条汇编语句，而后者会断在函数的第一条汇编语句；

（27）在任意情况下反汇编后面要执行的代码，设置`set disassemble-next-line on`，如果要在后面的代码没有源码的情况下才反汇编后面要执行的代码，设置`set disassemble-next-line auto`，关闭这个功能，设置`set disassemble-next-line off`；

（28）将源程序和汇编指令映射起来，使用命令`disas /m fun`；

（29）显示将要执行的汇编指令，使用命令`display /ni $pc`显示当程序停止时，将要执行的n条汇编指令，取消可以使用`undisplay`；

（30）打印单个寄存器的值，`i registers regname`或`p $regname`；

（31）缺省情况下gdb是以只读方式加载程序的，可以设置为可写，命令为`gdb -write ./exec`，也可以在gdb命令中设置并重新加载程序`set write on`；

（32）PC寄存器会存储程序下一条要执行的指令的地址，可以修改PC寄存器的值，控制程序执行流，命令为`set var $pc=address`；

（33）command breaknum，在断点breaknum处中断时需要执行的命令，之后需要输入命令，使用end结束命令；

（34）可以使用$_siginfo变量来获取当前信号的一些信息；

（35）显示共享链接库信息，使用`info sharedlibrary regex`命令可以显示程序加载的共享链接库信息，其中 regex  可以是正则表达式，意为显示名字符合 regex  的共享链接库，如果没有 regex  ，则列出所有的库，带“ *  ”表示库缺少调试信息；

（36）设置按何种方式解析脚本，`set script-extension off/soft/strict`，off表示所有的脚本文件都解析成gdb的命令脚本， soft表示根据脚本文件扩展名决定如何解析脚本，如果gdb支持解析这种脚本语言，就按这种语言解析，否则就按命令脚本解析，strict表示根据脚本文件扩展名决定如何解析脚本，如果gdb支持解析这种脚本语言，就按这种语言解析，否则不解析；

（37）保存历史命令，`set history save on`；

（38）设置源文件查找路径，`directory path`；

（39）进入和退出图形界面，gdb时使用-tui参数，或者gdb运行过程中使用`Crtl+X+A`组合键，退出相同；

（40）使用图形界面时可以使用命令显示汇编代码窗口，`layout asm`，如果既想显示汇编代码又想显示源码可以使用命令`layout split  `，使用`layout regs`显示寄存器窗口，使用`tui reg float`显示浮点寄存器窗口，使用`tui reg system`显示系统寄存器窗口，如果想切换回通用寄存器可以使用`tui reg general`命令；

（41）一些命令的缩写：

```
b -> break
c -> continue
d -> delete
f -> frame
i -> info
j -> jump
l -> list
n -> next
p -> print
r -> run
s -> step
u -> until
aw -> awatch
bt -> backtrace
dir -> directory
disas -> disassemble
fin -> finish
ig -> ignore
ni -> nexti
rw -> rwatch
si -> stepi
tb -> tbreak
wa -> watch
win -> winheight
```

（42）不离开gdb直接执行shell命令，使用`shell command`或者`!command`感叹号后不能有空格；

（43）设置被调试程序的参数，`gdb -args ./a.out a b c`，或者`(gdb) set args a b c`，显示参数`show args`，也可以在程序运行时直接指定，`r a b c`，如果想让参数为空，直接`set args`；

（44）设置程序运行时的环境变量，`set env varname=value`，例如：

```
set env LD_PRELOAD=/lib/x86_64-linux-gnu/libpthread.so.0
```

（45）使用`apropos regexp`命令查找所有符合 regexp  正则表达式的命令信息；

（46）可以使用`set logging on`把执行gdb的过程记录下来，默认保存在gdb.txt中；

## 四、peda命令

（1）aslr，显示/设置 gdb 的 ASLR；

（2）asmsearch，在内存中搜索反汇编代码，例如：

```
asmsearch "int 0x80"
asmsearch "add esp, ?" libc
```

（3）assemble，直接执行一些汇编指令，end结束；

（4）checksec，检查二进制文件的安全选项；

（5）cmpmem，比较一段内存与一个文件，例如：

```
cmpmem 0x08049000 0x0804a000 data.mem
```

（6）distance，计算两个地址之间的距离；

（7）dumpargs，在调用指令停止时显示传递给函数的参数；

（8）dumprop，在特定的内存范围显示 ROP gadgets；

（9）elfheader，获取正在调试的 ELF 文件的头信息；

（10）elfsymbol，从 ELF 文件中获取没有调试信息的符号信息；

（11）jmpcall，在内存中搜索jmp/call指令，例如：

```
jmpcall
jmpcall eax
jmpcall esp libc
```

（12）loadmem，将一个文件加载到指定内存，例如：

```
loadmem stack.mem 0xbffdf000
```

（13）lookup，搜索属于内存范围的地址的所有地址/引用；

（14）nextcall，一直步入执行到 call 指令，nextjmp，一直步入执行到 j 指令；

（15）patch，

（16）pattern，生成，搜索或写入循环 pattern 到内存；

（17）procinfo，显示调试进程的 /proc/pid/；

（18）pshow，显示各种 PEDA 选项和其他设置，pset，设置各种 PEDA 选项和其他设置；

（19）readelf，获取文件头、段信息；

（20）ropgadget，获取二进制或库的常见 ROP gadgets；

（21）ropsearch，搜索内存中的 ROP gadgets；

（22）searchmem|find，搜索内存中的 pattern，支持正则表达式搜索；

（23）shellcode，生成或下载常见的 shellcode，例如：

```
shellcode x86/linux exec
```

（24）skeleton，生成 python exploit 代码模板，例如：

```
skeleton argv exploit.py
```

（25）stepuntil，步入直到遇见指定的汇编代码，例如：

```
stepuntil cmp
```

（26）strings，显示内存中可打印的字符串；

（27）vmmap，在调试过程中获取段的虚拟映射地址范围；

```
vmmap libc
vmmap stack
vmmap binary
```

（28）xormem，用一个 key 来对一个内存区域执行 XOR 操作，例如：

```
xormem 0x08049000 0x0804a000 “thekey”
```

