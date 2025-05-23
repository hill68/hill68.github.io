+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = 'C++ Code README'
summary= "This summary is independent of the content."
+++

该代码基于三段 S 曲线速度规划模型，实现以下功能：

- 根据给定的参数一次性计算整段轨迹（总飞行时间 T 内，每个步长 dt）的速度、加速度、加加速度，并计算每个步长累计飞行距离；
- 可选输出为 CSV 文件（示例中将结果写入 "trajectory.csv"），CSV 文件中包含时间、累计飞行距离、速度、加速度和加加速度。

你可以将该代码集成到固定翼无人机仿真系统中，每次仿真开始时调用 computeTrajectory() 计算整段数据，再按步长下发给飞控模块。


---

### 代码说明

1. **SCurveVelocityPlanner 类**  
   - 构造时传入总距离 S、总时间 T、进入速度 vIn、期望定速 vDes、速度上下限以及最大加速度和最大加加速度；
   - computeTransitionTime() 根据 \( \Delta v = vDes - vIn \) 判断采用梯形或三角形 S 曲线，并计算 Tj、Ta 与变速段时长 Tconv；
   - compute(t) 根据当前时间 t 计算目标状态（速度、加速度、加加速度）。

2. **computeTrajectory() 函数**  
   - 以步长 dt 遍历 t ∈ [0, T]，调用 compute(t) 得到每个时刻状态；
   - 同时通过梯形积分计算累计飞行距离，并填充到输出结构中。

3. **outputTrajectoryToCSV() 函数**  
   - 将 trajectory 中所有数据写入 CSV 文件，列为 time, distance, velocity, acceleration, jerk。

4. **main() 函数**  
   - 设置示例参数，创建规划器实例，并计算整段轨迹数据；
   - 输出部分数据到控制台，并生成 CSV 文件 "trajectory.csv"。

这种模块化代码可方便地嵌入固定翼无人机仿真系统中，每次仿真前调用 computeTrajectory() 一次性生成整段数据，并在每个步长下发相应的控制指令，同时可用于数据分析或离线调试。
