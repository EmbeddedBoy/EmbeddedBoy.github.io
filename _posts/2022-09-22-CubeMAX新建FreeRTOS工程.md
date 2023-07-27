---
layout: post
date: 2022-09-22
tags: FreeRTOS
---

# 1、FREERTOS配置

## 1.1、基本属性设置

![1基本属性](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-1%E5%9F%BA%E6%9C%AC%E5%B1%9E%E6%80%A7.png?raw=true)

![2基本属性](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-2%E5%9F%BA%E6%9C%AC%E5%B1%9E%E6%80%A7.png?raw=true)

![3](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-3%E5%9F%BA%E6%9C%AC%E5%B1%9E%E6%80%A7.png?raw=true)

**USE_PREEMPTION**   :  是否支持抢占式调度机制，支持则为Enabled

**TICK_RATE_HZ** : 时钟节拍数，单位HZ，作为延时和时间片调度的基本单位

**MAX_PRIORITIES** : 最大优先级数，默认56，即可用优先级范围为0~55

**MINMAL_STACK_SIZE** : 任务栈大小，128Words * 4 = 512Byte。任务中定义的局部变量，函数调用和参数传递都会使用任务栈

**TOTAL_HEAP_SIZE**  : 定义堆空间大小。创建任务，信号量，消息队列以及软件计数器都需要使用堆空间

**TIMER_TASK_PRIORITY** : 设置软件定时器任务的优先级

## 【注意】

在FreeRTOS中，任务的创建、任务栈的分配以及各种服务系统（信号量、互斥量、消息队列以及软件定时器）的创建，都需要从堆空间分配内存。

如果用户创建了较多的任务和系统服务时，可以根据实际情况，增加**堆空间**的大小

如果单个任务中定义了较多的局部变量或者进行了较多的函数调用时，可以增加**任务栈空间**的大小

## 1.2、任务与队列设置

![4](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-4%E4%BB%BB%E5%8A%A1%E4%B8%8E%E9%98%9F%E5%88%97%E8%AE%BE%E7%BD%AE.png?raw=true)

![image-20220922091751121](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-5%E4%BB%BB%E5%8A%A1%E4%B8%8E%E9%98%9F%E5%88%97%E8%AE%BE%E7%BD%AE.png?raw=true)

![6](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-6%E4%BB%BB%E5%8A%A1%E4%B8%8E%E9%98%9F%E5%88%97%E8%AE%BE%E7%BD%AE.png?raw=true)

# 2、修改HAL库的时间基准

由于FreeRTOS需要使用系统节拍定时器（systick）产生时钟节拍，避免与HAL库冲突，选择TIM定时器作为HAL库的时间基准

![image-20220922092255097](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-7%E6%97%B6%E9%97%B4%E5%9F%BA%E5%87%86.png?raw=true)

# 3、外设配置

![image-20220922092429802](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-8%E5%A4%96%E8%AE%BE%E9%85%8D%E7%BD%AE.png?raw=true)

# 4、程序编写

打开MDK，在app_freertos.c中配置好了任务属性

![image-20220922093545930](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-9%E7%A8%8B%E5%BA%8F%E7%BC%96%E5%86%99.png?raw=true)

只需编写任务函数代码即可

![image-20220922093758161](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-10%E4%BB%BB%E5%8A%A1%E5%87%BD%E6%95%B0.png?raw=true)

![image-20220922093729905](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-11%E4%BB%BB%E5%8A%A1%E5%87%BD%E6%95%B0.png?raw=true)

# 5、实验现象

![image-20220922093850481](https://github.com/EmbeddedBoy/EmbeddedBoy.github.io/blob/main/MyBlogPicBed/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B/2022-09-22-CubeMAX%E6%96%B0%E5%BB%BAFreeRTOS%E5%B7%A5%E7%A8%8B-12%E5%AE%9E%E9%AA%8C%E7%8E%B0%E8%B1%A1.png?raw=true)

“task 01”和“task 02”同时打印，且LED4每隔1秒翻转电平一次