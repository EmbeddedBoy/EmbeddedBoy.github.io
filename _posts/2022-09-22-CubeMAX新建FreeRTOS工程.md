# 新建FreeRTOS工程

## 1、FREERTOS配置

### 1.1、基本属性设置

![image-20220922083123746](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922083123746.png)

![image-20220922085206368](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922085206368.png)

![image-20220922090904153](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922090904153.png)

**USE_PREEMPTION**   :  是否支持抢占式调度机制，支持则为Enabled

**TICK_RATE_HZ** : 时钟节拍数，单位HZ，作为延时和时间片调度的基本单位

**MAX_PRIORITIES** : 最大优先级数，默认56，即可用优先级范围为0~55

**MINMAL_STACK_SIZE** : 任务栈大小，128Words * 4 = 512Byte。任务中定义的局部变量，函数调用和参数传递都会使用任务栈

**TOTAL_HEAP_SIZE**  : 定义堆空间大小。创建任务，信号量，消息队列以及软件计数器都需要使用堆空间

**TIMER_TASK_PRIORITY** : 设置软件定时器任务的优先级



#### 【注意】

在FreeRTOS中，任务的创建、任务栈的分配以及各种服务系统（信号量、互斥量、消息队列以及软件定时器）的创建，都需要从堆空间分配内存。

如果用户创建了较多的任务和系统服务时，可以根据实际情况，增加**堆空间**的大小

如果单个任务中定义了较多的局部变量或者进行了较多的函数调用时，可以增加**任务栈空间**的大小



### 1.2、任务与队列设置

![image-20220922091725874](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922091725874.png)

![image-20220922091751121](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922091751121.png)

![image-20220922091800777](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922091800777.png)

## 2、修改HAL库的时间基准

由于FreeRTOS需要使用系统节拍定时器（systick）产生时钟节拍，避免与HAL库冲突，选择TIM定时器作为HAL库的时间基准

![image-20220922092255097](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922092255097.png)

## 3、外设配置

![image-20220922092429802](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922092429802.png)



## 4、程序编写

打开MDK，在app_freertos.c中配置好了任务属性

![image-20220922093545930](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922093545930.png)

只需编写任务函数代码即可

![image-20220922093758161](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922093758161.png)

![image-20220922093729905](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922093729905.png)

## 5、实验现象

![image-20220922093850481](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220922093850481.png)

“task 01”和“task 02”同时打印，且LED4每隔1秒翻转电平一次