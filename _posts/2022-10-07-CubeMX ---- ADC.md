# CubeMX ---- ADC

## 一、ADC基础认识

### **电压转换**

​		ADC的数字存储是12位的 也就是说转换器通过采集转换所得到的最大值是4095 “111111111111”=4095 二进制的12位可表示0-4095个数， 对应着所测电压的实际值，转换的电压范围是0v-3.3v的话，转换器就会把0v-3.3v平均分成4096份。设转换器所得到的值为x，所求电压值为y
$$
y = x / 4096 *3.3
$$

### **分辨率**

读出的数据的长度，如8位就是最大值为255的意思，即范围[0,255]

### **输入电压范围**

ADC所能测量的电压范围就是VREF-≤ VIN ≤ VREF+，把VSSA 和VREF-接地，把VREF+和VDDA 接3V3，得到ADC的输入电压范围为：0~3.3V。

### **输入通道**

ADC的信号时通过输入通道进入单片机内部的，单片机通过ADC模块将模拟信号转换为数字信号。

### **转换通道**

外部的16个通道在转换时又分为**规则通道**和**注入通道**，其中规则通道最多有16路，注入通道最多有4路

#### **规则通道**

规则通道顾名思义就是，最平常的通道、也是最常用的通道，平时的ADC转换都是用规则通道实的。

#### **注入通道**

注入通道是相对于规则通道的，注入通道可以在规则通道转换时，强行插入转换，相当于一个“中断通道”。当有注入通道需要转换时，规则通道的转换会停止，优先执行注入通道的转换，当注入通道的转换执行完毕后，再回到之前规则通道进行转换。

## 二、ADC转换模式

**单次转换模式**：ADC只执行一次转换

**连续转换模式**：转换结束之后马上开始新的转换；

**扫描模式：**ADC扫描被规则通道和注入通道选中的所有通道，在每个组的每个通道上执行单次转换。在每个转换结束时，这一组的下一个通道被自动转换。如果设置了CONT位（开启了连续 转换模式），转换不会在选择组的最后一个通道上停止，而是再次从选择组的第一个通道继续转换。

**间断模式：**触发一次，转换一个通道，在触发，在转换。在所选转换通道循环，由触发信号启动新一轮的转换，直到转换完成为止。



## 三、ADC设置

### **1、 ADC时钟配置**

ADC 的转换时间跟 ADC 的输入时钟和采样时间有关。

公式为：**Tconv = 采样时间 + 12.5 个周期**。

当 ADCLK = 16MHZ ，采样时间设置为 3.5 周期，那么总的转换时间  Tconv = 3.5 周期 + 12.5 周期 = 16 周期 = 16* 1/16M = 1us。

![image-20220925105235551](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220925105235551.png)

### **2、ADC设置**

![image-20220925105529020](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220925105529020.png)

**Clock Prescaler  时钟预分频** 

为保证采集精度最好使分频后时钟在36Mhz以下

**Resolution   分辨率** 

有8位，10位，12位的，这里选择最高的就行

**Date Alignment   数据对齐方式** 

我们ADC转换后的数据存在寄存器中，12位的，这12位是左对齐右对齐都行

**Scan Conversion Mode   扫描模式**

当我们使用多通道采集的时候需要使能他去轮询读取每个通道值

**Continous Conversion Mode    持续转换模式**

一般我们都是连续转换，这个我们需要使能他

**Discontinous Conversion Mode 间断转换模式**

**DMA Continous Requests** 

不经过cpu去提取转换的数据，直接交给DMA操作







## 四、ADC四种用法

### 1、**轮询模式**

​		**缺点：占用CPU的使用率**

​		软件开始ADC转换后，一直等到转换完成后，才向后执行，这个代码在初始化ADC之后执行一次校准（不执行这一步也可以，但精度可能会低一些）；然后就可以使用ADC轮询转换了，只需要三步：**启动转换**、**等待转换完成**、**读取转换数据**，即可完成一次ADC转换。

```c
__IO uint32_t AD_value;
float ADC_Vol;    // 用于保存转换计算后的电压值

MX_ADC1_Init();       //ADC初始化

HAL_ADCEx_Calibration_Start(&hadc1);      //ADC校准

while(1)
{
	HAL_ADC_Start(&hadc1);    //启动转换
	
	if(HAL_OK == HAL_ADC_PollForConversion(&hadc1 , 50))//等待转换完成，超时50ms
	{
		AD_value = HAL_ADC_GetValue(&hadc1);    //读取ADC转换结果	
        ADC_Vol =(float) AD_value/4096*3.3;     // 读取转换的AD值
	}
	HAL_Delay(100);
}
```

### 2、**中断模式**

​		打开 `stm32f1xx_it.c` 中断服务函数文件，找到 ADC1 中断的服务函数 `ADC1_0_IRQHandler()`
 中断服务函数里面就调用了 ADC 中断处理函数 `HAL_ADC_IRQHandler()`，打开 `stm32f1xx_hal_adc.c` 文件，找到 ADC 中断处理函数原型 `HAL_ADC_IRQHandler()`，其主要作用就是判断是哪个 ADC 产生中断，清除中断标识位，然后调用中断回调函数 `HAL_ADC_ConvCpltCallback()`。

![image-20220925001758297](C:\Users\zxr021109\AppData\Roaming\Typora\typora-user-images\image-20220925001758297.png)

​		`HAL_ADC_ConvCpltCallback()` 按照官方提示我们应该再次定义该函数，`__weak` 是一个弱化标识，带有这个的函数就是一个弱化函数，就是你可以在其他地方写一个名称和参数都一模一样的函数，编译器就会忽略这一个函数，而去执行你写的那个函数；而 `UNUSED(hadc)` ，这就是一个防报错的定义，当传进来的ADC号没有做任何处理的时候，编译器也不会报出警告。其实我们在开发的时候已经不需要去理会中断服务函数了，只需要找到这个中断回调函数并将其重写即可而这个回调函数还有一点非常便利的地方这里没有体现出来，就是当同时有多个中断使能的时候STM32CubeMX会自动地将几个中断的服务函数规整到一起并调用一个回调函数，也就是无论几个中断，我们只需要重写一个回调函并判断传进来的定时器号即可。

​		在主循环前，启动一次中断转换；然后在主循环中检查标志位，是否已经完成转换（ADC转换完成中断）；如果已经转换完成，则读取结果，上传；再启动下一次中断转换：

**中断回调函数**

```c
extern uint8_t ADC_Flag;    //调用标志位
void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef *hadc)
{
	ADC_Flag = 1;
}
```

**主函数**

```c
uint8_t ADC_Flag;

__IO uint32_t AD_value;
float ADC_Vol;    // 用于保存转换计算后的电压值  

MX_ADC1_Init();       //ADC初始化

HAL_ADCEx_Calibration_Start(&hadc1);      //ADC校准
HAL_ADC_Start_IT(&hadc1);      //启动ADC中断模式转换

while(1)
{
	if(ADC_Flag == 1)
	{
		ADC_Flag = 0;
		AD_value = HAL_ADC_GetValue(&hadc1);    //读取ADC转换结果	
        ADC_Vol =(float) AD_value/4096*3.3;     // 读取转换的AD值
	}
}
```

### 3、DMA**模式**



### 4、**定时器中断**
