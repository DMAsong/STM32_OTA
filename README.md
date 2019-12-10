# STM32 -OTA

原理:利用IAP, 将接收到的bin文件存储到flash或者SRAM, 然后跳转到对应的地址执行程序.

代码分区:

| flash adder        | 功能       |
| ------------------ | ---------- |
| 0x8000000---n      | BootLoader |
| 0x8000000+n---m    | App1       |
| ........           | ....       |
| 0x8000000+n+...--- | Appn       |

分区中要规划好地址,最少需要有BootLoader和一个App区.

这个要看具体的MCU性能.

> stmf103c8t6 ROM 64K RAM 20k
>
> BootLoader的代码尽量小分配20k左右.
>
> 由于RAM 只有20k,每次传输的数据大概在十几k左右
>
> App区设置超过20k,可以尝试将bin文件分离,发多次.

这里关键的代码在BootLoader

```c
uart_init(115200);
```

本次使用串口传输,波特率根据需求来设置.

```c
#define FLASH_APP1_ADDR		0x08008000 
```

App1的分区首地址,根据分区来配置

```c
#define STM32_FLASH_SIZE 64 	 		
```

根据flash的大小来设置

```c
u8 USART_RX_BUF[USART_REC_LEN] __attribute__ ((at(0X20002000)));
```

配置接收文件数据的数组首地址,根据RAM来设置

最后就是在BootLoader里设置启动逻辑



App程序

在main的最前面加上

```c
SCB->VTOR= FLASH_BASE | 0x8000;
```

0x8000为便宜地址,根据App所在的首地址设置

**当App程序要进行升级时,需要先跳转的BootLoader程序,跳转前需要把所有的中断标志位清除**
