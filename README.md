#STM32_OTA
This is an ota project based on stm32f103c8t6

stm32的ota采用的是IAP,启动时执行BootLoader,当bin文件接收完成时,会跳转到bin文件的烧录区,执行bin代码.

##编辑BootLoader时要注意一下问题
1. 通信接口,本次采用的是uart通信,需要修波特率,可以提高传输效率
2. 将flash的地址分配
>本次分两个区,BootLoader ,App
