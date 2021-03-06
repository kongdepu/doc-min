---
title: 第五章 输入输出以及中断技术
---
## 简单题

### 什么是总线，简述各类总线的应用场合
1. 总线是连接计算机各个部件的公共通道，是CPU，内存，输入，输出设备传递数据的公共通道。
2. 根据总线的功能划分
- 地址总线
- 数据总线
- 控制总线
3. 根据总线的层次接口划分
- 片内总线：微机系统中速度最快的总线，在CPU内部，连接CPU的内部部件。
- 系统总线：提供CPU和主板器件之间以及CPU到高速外设之间的快速信息通道。
- 通信总线：微机和微机，以及微机和外设之间的通信的总线。
4. 主机将各个部件通过总线连接，外部设备通过相应的接口在和总线相连接，构成了计算机系统
5. 使用总线接口的优点
- 简化系统结构
- 大大减少了连线数目
- 便于接口设计
- 便于设备的软件设计
- 便于故障诊断和维修


## 输入输出
### 8086CPU与外设进行数据交换的方式。程序控制传送方式有哪几种? 基本输入/输出有几种？
1. 无条件传送：不查询外设状态，认为外设已经准备就绪，直接与外设传送数据。
(硬件最少，接口简单,适用于随时读写的设备)
2. 查询传送：CPU不断查询外设端口的状态，状态不符合时候，CPU需要等待，直到状态符合时，CPU完成相应操作。
(CPU利用率低, 实时性差，适用于工作不太繁忙的系统)
3. 中断控制：是软件和硬件相结合的技术。外设在准备就绪的条件下通过请求CPU，主动向CPU提出交换数据，CPU与外设之间能并行工作。
(每十秒进行数据采集，对于大量的数据高速的传送不适合)
4. DMA方式: CPU不参于数据传送，而是由DMA控制器完成内存与外设之间数据交换，适用于大量数据高速传送，CPU与外设之间能并行工作，提高CPU的效率(需要DMA控制器，硬件电路比较复杂)

### 相对于条件传送方式，中断方式有什么优点？和DMA方式比较，中断方式又有什么不足?
相对于条件传送方式，中断方式可以使CPU的工作效率大大提高。但是和DMA方式相比较，
其缺点是没进行一次数据传送，CPU就要执行一次中断服务程序，CPU都要保护断点和现场，
才能保证中断处理完成后正确返回主程序，显然，这些操作会花费CPU的大量时间，这也会造成
数据传输效率的降低，不能满足高速外设与内存之间批量数据的传送

### 查询传送的工作原理，与查询方式相比，中断传送方式的优点？
1. 查询传送方式在传送数据前先查询外设的状态，当外设准备好时，CPU执行输入输出指令，若没有准备好，则CPU将进行等待。
2. 优点：能较好的协调外设和CPU之间的定时关系，因此无条件传送方式可以实现准确传送
3. 缺点：该方式需要不断的去查询外设的状态，大量的时间花费在等待循环中，大大降低了CPU的效率
2. 中断方式传送方式的优点：提高了CPU的效率。CPU与外设，外设与外设只能可以并行进行

### 中断传送方式主要优点
1. 实现实时处理。CPU一旦接受到I/O接口的中断请求，立即响应中断
2. 实现分时处理。CPU同时接受多个I/O接口的中断请求时，先判优在执行中断
3. 实现故障处理。在工作过程当中一旦发生故障，如断电，可以通过中断方式请求CPU处理。
## 中断技术
### 什么是中断，使用中断的好处？有哪些中断？
1. 所谓中断，是指计算器在正常运行执行中，由于种种原因，使CPU暂时停止处理当前程序的执行，而去转向临时发生的程序，处理完毕后，在返回去处理执行暂停的程序
2. 好处
- 使用中断技术，使得外部设备与CPU不再是串行工作，而是分时操作，从而大大提高了计算机的效率
- 主机与外设并行处理
- 实现实施处理
- 硬件故障处理
3. 常见的中断源
- 一般的输入/输出设备请求中断
- 实时时钟请求中断
- 软件中断和外部中断

### 中断系统，中断系统的功能
1. 实现中断及返回
- 若允许响应这个中断请求，CPU必须在现行的指令完成后，把下一次要执行的指令IP和CS以及各个寄存器的内容和状态标志推入堆栈保存下来，称保护断点和现场
- 当中断处理完成后，在恢复被保存下来的各个寄存器和标志位的状态(称恢复现场)
2. 实现优先权排队
- 根据轻重缓急给每个中断源确定一个中断级别，即优先权
3. 高级中断源能中断低级中断处理

### CPU对外部可屏蔽中断的响应及中断过程 一般中断处理子程序在结构上是怎样一种模式？
1. 不可屏蔽中断，一旦发生就向CPU发中断请求，CPU应在当前正在执行的指令结束后响应这一中断请求，进行中断处理。不受中断标志位的影响。
2. 可屏蔽中断，一旦发生就向CPU发中断请求，CPU根据中断的优先权来决定是否响应该中断。受中断标志位的影响。
    CPU在现行指令结束后响应中断
    1. 关中断 CLI IF = 0
    2. 保留断点
      CPU响应中断后，把IP和CS推入堆栈保留，以便中断处理完毕后，能返回被中断程序
    3. 保护现场
      8086要把用到的寄存器的内容用PUSH指令推入堆栈，而标志位的状态是在保留断点的同时由硬件推入堆栈的
    4. 给出中断入口地址，转入相应的中断服务程序
      8086是根据中断源提供的中断向量类型码读取中断向量表得到中断服务程序的入口地址
    5. 恢复现场
      POP指令弹出内部寄存器的内容和状态标志为的状态。8086的标志位的状态由硬件恢复
    6. 中断返回
      在中断服务程序的最后要安排一条中断返回指令

### 中断的处理过程
1. 中断请求
- 内部中断不需要中断请求，CPU内部的中断控制逻辑直接接受处理
- 外部中断请求由中断源提出，通过INTR引脚发送给CPU，INTR中断请求信号保持到该请求被CPU响应为止 
2. 中断的判优
- CPU识别中断的中断源，根据中断判优的方法找出优先级最高的中断源进行响应，处理完成后再去响应级别低的中断源的请求
3. 中断的响应
- 中断优先级确定后，优先级最高的中断请求被送到CPU的中断请求引脚INTR上，CPU在当前指令结束后，通过INTA
发送中断请求响应信息
- 关中断，保护断点，获取中断服务程序入口地址
4. 中断的处理
保护现场，开中断(为了实现中断嵌套)，执行中断处理程序，关中断，恢复现场
5. 中断的返回
- 当中断处理完成必须安排一条中断返回的指令
- 自动恢复断点信息和FLAGS，CPU从断点处继续往下执行。
### 内部中断的种类及特点，分为哪三种?
内部中断又称软件中断，是通过软件调用的不可屏蔽中断，或者是指令执行过程过程中发生了某些错误。
1. 中断类型码或者包含在指令中，或者是隐含的
2. 不执行 INTA总线周期
3. 除单步中断外，任何内部中断都无法禁止；
4. 除单步中断外，任何内部中断的优先级都比任何外部中断的高。
- 中断类型号0-除法错误
- 中断类型号1-单步中断(优先级最低)
- 中断类型号2-NMI(不可屏蔽中断)
- 中断类型号3-断点中断
- 中断类型号4-溢出中断

故障、陷阱、异步中止
### 硬件中断和软件中断的区别
1. 硬件中断由硬件产生，通过INTR和NMI引脚送给CPU，软件中断由软件的中断指令或其他异常产生
2. 硬件中断的中断号由中断控制器提供；软件中断的中断号在指令中提供或隐含，不需要中断控制提
3. 硬件中断具有随机性，软件中断具有确定性
4. 大部分硬件中断需要CPU发送响应信息，软件中断不需要
5. 硬件中断除NMI之外均可以屏蔽，软件中断不能屏蔽
### 中断源是什么？如何识别中断源? 19年
1. 引起中断的原因或发出中断请求的来源，称做中断源
2. (1)每个中断源都有一条中断请求信号线，且固定一个中断服务程序的入口地址。CUP一旦检测到某条信号线有中断申请，就进入相应的中断服务程序 (2)向量中断，使用向量中断系统的中断源，除了能输出中断请求信息外，还能在CPU响应了它的中断请求后输出一个中断向量。
(3)CPU根据这个中断向量能够获取该中断源程序的入口地址，从而为其服务

### 中断类型码、中断向量、中断向量表？中断类型码和中断向量关系
1. 中断类型码：处理机处理的每种中断的编号是中断类型码
2. 中断向量：中断处理服务程序的入口地址,每一种中断服务程序在内存中的起始地址称为中断向量
3. 中断向量表：是中断服务子程序的入口地址表,8086系统共有中断类型码256级，0~255，每一个中断类型码对应一个向量地址，每一向量地址占4个字节，按顺序存放0000H:0000H~0000H:03FFH，容量大小是1KB
4. (中断号)中断类型码*4就是中断向量地址，从此处读出4个字节即是中断向量,前两个字节是中断子程序偏移地址，后两个字节是中断子程序的段地址
5. 当中断源发出中断请求时，即可以通过中断类型码查找中断向量表，获取中断向量，转入响应的中断程序服务
6. 中断向量地址：内存中存放中断服务程序入口地址的地址

### 中断嵌套是，使用它的好处，实现的条件是
1. 微处理器的在处理低级别中断的过程中，如果出现了级别高的中断请求，微处理器停止执行低级中断的处理
程序，而是处理高级中断，等高级中断处理完成后，再去处理级别低的中断，这种处理过程叫做中断嵌套
2. 好处是提高中断响应的实时性，对于某些对实时性较高的操作，必须设置较高的优先级和采用中断嵌套的方式，才能保证系统能够及时响应中断请求。
3. 条件
- CPU处于开中断状态(IF=1)
- 没有被中断处理器屏蔽
- 中断请求的优先级要大于当前处理中断请求的优先级
- 没有不可屏蔽的中断请求

## 8259A可编程的中断控制器
### 8259A的可编程的中断控制器基本组成部分
他具有8级优先权控制，通过级联可扩展到64级优先权控制,每一级中断都可以屏蔽或允许，CPU响应中断后，进入中断响应周期INTA，8259A提供响应的中断向量，从而迅速地转至中断服务程序
1. IRR(Interrupt request register)，8位中断请求寄存器，用来存放从外设来的中断请求信息IR0-IR7；
2. IMR(Interrupt mask register)，8位中断屏蔽寄存器，用来存放CPU送来的屏蔽信息；
3. ISR(Interrupt service register)，8位中断服务寄存器，用来记忆正在处理中的中断级别；
4. PR，优先级分析器；
5. 控制逻辑
6. 读写逻辑
7. 级联缓冲器/比较器
### 8259A的初始化命令字和操作命令字有哪些，功能是?
1. 初始化命令字ICW ICW1-ICW4（1偶3奇）
- ICW1: 确定8259的触发方式和工作方式（单片和级联）
- ICW2：确定中断向量号（中断类型码）的高五位
- ICW3: 确定主片上的信息线连接从片的方式
- ICW4：确定8259的全嵌套方式，缓冲方式和结束方式。
2. 操作控制字OCW OCW1-OCW3(1奇2偶)。在工作8259工作期间，可通过操作命令字按不同的方式进行操作。
- OCW1: 是对IMR置位复位的命令字，置位位对应的中断被屏蔽;
- OCW2：是中断结束（EOI）的命令字，用于复位ISR及改变优先级
- OCW3: 是读ISR和IRR的命令字
<!-- ![8259A](/img/8259A.png) -->
### 8259A对优先级的管理方式有哪几种，各有什么含义?
1. 普通全嵌套方式
- 8259A的中断优先权顺序固定不变，从高IRO到低IR7；禁止同级和低级优先权的中断，但允许高级优先权中断的嵌套
2. 特殊全嵌套方式
- CPU正在处理某一级中断时，只可对本级中断进行屏蔽，允许比它高的或比它低的中断开放
3. 优先级自动循环方式
- 初始优先级顺序为IR0最高，IR7最低
- 当某一个中断服务处理完成后，将该中断的优先级改为最低，按优先级顺序继续执行
4. 优先级特殊循环方式
- 与优先级自动循环方式基本相同，不同点在于可以根据用户的要求将最低优先级赋于某一中断源
### 8259A 引入中断请求的方式有哪几种？如果对 8259A 用查询方式引入中断请求，那会有什么特点？中断查询方式用在什么场合？
1. 边沿触发方式，电平触发方式，中断查询方式。
特点：
- 设备仍然通过往8259A发中断请求信号要求CPU服务，但8259A不使用INT信号
向 CPU 发中断请求信号。
- CPU 内部的中断允许触发器复位，所以禁止了外部对 CPU 的中断请求。
- CPU 要使用软件查询来确认中断源，从而实现对设备的中断服务。 中断查
询方式一般用在多于 64 级中断的场合，也可以用在一个中断服务程序中 的
几个模块分别为几个中断设备服务的情况

### 8259A的初始化命令字和操作命令字有什么差别?他们分别对应于编程结构中哪些内部寄存器?
1. 8259A的初始化命令字是在8259A启动之前对8259A进行初始化，初始化命令字一旦设定，一般在工作过程中就不在改变。
2. 操作命令字是程序设定的。根据不同的操作命令字来实现不同的操作。在一个系统运行过程，操作命令字可以多次设置
3. 初始化命令字对应ICW1、ICW2、ICW3、ICW4
4. 操作命令字对应OCW1、OCW2、OCW3