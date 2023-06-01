# DC-Motor-PID-Control

## 1 目标
实现PID控制直流编码电机

## 2 步骤
### 2.1 按键控制电机速度（参考江科协代码）
- [x] 电机的驱动函数能输入PWM的值作为参数(在PID控制的实例中，实际用不到按键)

### 2.2 编码器接口测速（参考江科协代码）
- [x] 编码器接口计数，通过转换能计算出电机的实际转速。
- [x] 45减速比，4分频，霍尔编码器电机转一圈单相输出13个脉冲,则电机输出轴转一圈最大输出 4x45x13=2340 个计数
- [x] 角速度计算公式 Rps=实际编码器计数/2340 (圈/秒)

### 2.3 串口通讯（参考江科协代码）
- [x] 串口输出编码器的转速值，连接上位机图形化显示 
```Serial_Printf("Rps: %.2f\n", Rps); //串口输出转速，换行打印```
- [ ] 待完成功能：Kp, Ki, Kd参数通过串口输入，而不用每次更改都编译和下载一遍

### 2.4 旋转编码器PID控制电机速度
- [x] 简单粗暴的控制方式，不能让转速稳定在3 round/sec
```
if (Rps > 3.1) Speed--;
if (Rps < 2.9) Speed++;
```
- [x] 旋转编码器PID控制电机速度
```
float Err=0, LastErr=0, NextErr=0, Add=0, Kp=20, Ki=0.5, Kd=0, POut=0, IOut=0, DOut;
int8_t TotalOut=0;

float PID(float Rps, float Target)
{
	Err = Target - Rps; //计算实际值与目标值的偏差值
	POut = Kp * Err; //计算PID的比例值P的输出值
	IOut += Ki * Err; //计算PID的比例值I的输出值
	DOut = Err - LastErr;
	TotalOut = (int8_t)(POut + IOut + DOut);
	LastErr = Err;
	return TotalOut;
}
```

## 3 定时器表资源表：STM32F103C8T6定时器资源：TIM1,TIM2,TIM3,TIM4
```	
	*功能*	  *定时器*	 *类型*
	闲置	   TIM1	   高级定时器
	PWM	    TIM2    通用定时器	
	Encoder	    TIM3    通用定时器	
	Timer	    TIM4    通用定时器	
```
![截屏2023-05-25 下午9 26 11](https://github.com/Kevinyym/DC-Motor-PID-Control/assets/101639215/70501e9c-0ded-49f7-b84e-91fe3f6ec45b)

![截屏2023-05-25 下午9 29 18](https://github.com/Kevinyym/DC-Motor-PID-Control/assets/101639215/1a99db1c-61d8-4b7c-859d-89d0d5e69765)
