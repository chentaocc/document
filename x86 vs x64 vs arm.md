## 中央处理单元CPU（Central Processing Unit）

- 运算器（ALU）：算术逻辑单元Arithmetic Logic Unit，负责运算

- 控制器（CU）：控制单元Control Unit，负责发送CPU指令

- 寄存器：暂时保存运算和控制过程中的原始数据，中间结果，最终结果以及控制、状态信息，保证更高的速度

- 中断系统：响应输入输出设备发出的中断请求

  

## x86 VS arm

|                             X86                              |        Arm（Advanced RISC Machine进阶精简指令集机器）        |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                 X86追求性能，功耗大，不节能                  |            追求节能，低功耗，但和X86相比性能较差             |
|           主要应用于Intel，AMD等PC机，X86服务器中            |           主要应用于移动终端之中，类如手机，平板等           |
| 采用CISC复杂指令集计算机，指令较多，CPU电路设计复杂，但是对应编译器的设计简单。 | 采用的是RISC精简指令集计算机，指令较少，功耗比较小，但编译器设计很复杂，它的关键在与流水线操作能在一个时钟周期完成多条指令 |



## CISC VS RISC

- CISC(Complex Instruction Set Computers，复杂指令集计算机)：以Intel，AMD的X86 CPU为代表
- RISC(Reduced Instruction Set Computers，精简指令集计算机)：以ARM为代表



## x86 VS x64 VS x86-64

- x86：是指intel的开发的一种32位指令集
- x64：于1999年由AMD设计，AMD首次公开64位集以扩展x86，称为“AMD64”
- x86-64：是x86架构的64位拓展，向后兼容于16位及32位的x86架构
- 32位系统最多只能发挥4G内存的效用，如果电脑安装了大于4G的内存，32位的操作系统是不能更好的利用大于4G的那部分内存的效用的，所以会造成资源浪费，需要安装64位的操作系统



## Arm32  VS Arm64

- arm32：32位处理器需要armv7或者armv7s架构
- arm64：64位处理器需要arm64架构