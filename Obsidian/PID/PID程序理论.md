### 方式
确定一个调控周期T，每隔时间T，程序执行一次PID调控
1. 使用Delay延时函数
2. 定时器定时中断，中断内进行PID调控，注意资源访问冲突
3. 定时器定时中断，传递标志位
### 位置式PID程序实现
1. 位置式PID公式
2. Target目标值、Actual实际值、Out输出值
3. Kp比例项、Ki积分项、Kd微分项的权重
4. Error0本次误差、Error1上次误差、ErrorInt误差积分
5. 定时器初始化
6. 设定Target
7. 定时器中断
	1. Actual = 读取传感器
	2. 获取本次误差和上次误差
		1. Error1 = Error0
		2. Error0 = Target - Actual
	3. 误差积分（累加）
		1. ErrorInt += Error0
	4. PID计算
		1. Out = Kp x Error0 + Ki x ErrorInt + Kd x (Error0 - Error1)
	5. 输出限幅
		1. if(Out > 上限) Out = 上限
		2. if(Out < 下限) Out = 下限
	6. 执行控制
		1. 输出至被控对象(Out)
	7. 清除定时器中断标志位
		1. TIM_ClearITPendingbit
		2. (TIM2, TIM_IT_Update)
### 增量式PID程序实现
控制器内积分，输出全量
1. 增量式PID公式
2. Target目标值、Actual实际值、Out输出值
3. Kp比例项、Ki积分项、Kd微分项的权重
4. Error0本次误差、Error1上次误差、Error2上上次误差
5. 定时器初始化
6. 设定Target
7. 定时器中断
	1. Actual = 读取传感器
	2. 获取本次误差、上次误差和上上次误差
		1. Error2 = Error1
		2. Error1 = Error0
		3. Error0 = Target - Actual
	3. PID计算
		1. Out += Kp x （Error0 - Error1）+ Ki x Error0 + Kd x (Error0 - 2 x Error1 + Error2)
		2. 此处对Out积分，Out可以直接输出给对象，即输出全量
		3. 若需要实现纯增量PID，则只需要“=”， 不需要“+=”，但是要注意，被控制的对象是否能接收并处理增量
	4. 输出限幅
		1. if(Out > 上限) Out = 上限
		2. if(Out < 下限) Out = 下限
	5. 执行控制
		1. 输出至被控对象(Out)
	6. 清除定时器中断标志位
		1. TIM_ClearITPendingbit
		2. (TIM2, TIM_IT_Update)