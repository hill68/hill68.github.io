+++
date = '2025-05-23T17:07:50+08:00'
draft = false
title = '小型固定翼无人机飞行动力学模型'
summary= "为小型固定翼无人机仿真系统提供逼真的飞行动力学与飞行控制方案，替代当前的简单PID+运动学模型。文档涵盖系统需求、6-DOF动力学模型、级联控制架构、模块接口、参数标定及实现建议等内容。"
+++


## 1. 概述

本设计文档旨在为小型固定翼无人机仿真系统提供逼真的飞行动力学与飞行控制方案，替代当前的简单PID+运动学模型，指导后续具体实现与集成。文档涵盖系统需求、6-DOF动力学模型、级联控制架构、模块接口、参数标定及实现建议等内容。


[C++代码实现](https://github.com/flitai/uav-flight-sim-v1)

---

## 2. 系统需求

* **实时性**：仿真步长 ≤ 0.01s，支持 6-DOF 状态更新与控制运算。
* **精度**：考虑升力、阻力、侧力及机动特性，航向/姿态/速度跟踪误差 ≤ 5%。
* **可扩展性**：支持载荷变化、风场扰动、不同气动数据库切换。
* **接口兼容**：保留现有接口文档中输入/输出字段，向后兼容。

---

## 3. 飞行动力学模型（6-DOF）

### 3.1 状态变量定义

* **位置与速度**：$\mathbf{r}=[x,y,z]^T$, $\mathbf{v}=[u,v,w]^T$（机体坐标系）。
* **姿态与角速度**：Euler 角 $[\phi,\theta,\psi]^T$，角速率 $[p,q,r]^T$。
* **总状态向量**：$\mathbf{x}=[\mathbf{r},\mathbf{v},\phi,\theta,\psi,p,q,r]^T$。

### 3.2 动力学方程

1. **平动方程**：
   $m\dot{\mathbf{v}}=\mathbf{F}_a+\mathbf{F}_g+\mathbf{F}_t$

   * $\mathbf{F}_a$：气动力，包含升力、阻力、侧力，由攻角 $\alpha$、侧滑角 $\beta$ 及控制面偏角计算。
   * $\mathbf{F}_g$：重力，在机体坐标系下投影。
   * $\mathbf{F}_t$：推进力，由推力曲线或简单发动机模型获得。

2. **转动方程**：
   $\mathbf{I}\dot{\boldsymbol{\omega}}+\boldsymbol{\omega}\times(\mathbf{I}\boldsymbol{\omega})=\mathbf{M}_a+\mathbf{M}_t$

   * $\mathbf{M}_a$：气动力矩，依赖于控制面夹角与角速率。
   * $\mathbf{M}_t$：发动机或尾舵力矩。

3. **姿态更新**：
   $\dot{\mathbf{R}}=\mathbf{R}[\boldsymbol{\omega}]_\times$
   或使用Euler角微分：
   $\begin{bmatrix}\dot{\phi}\\\dot{\theta}\\\dot{\psi}\end{bmatrix}=\mathbf{E}(\phi,\theta)\begin{bmatrix}p\\q\\r\end{bmatrix}.$

### 3.3 气动系数获取与典型值

| 系数               | 物理含义            | 典型值                     | 单位  |
| :----------------- | :------------------ | :------------------------- | :---- |
| $C_{L0}$           | 零攻角升力系数      | 0.2                        | —     |
| $C_{L_{\alpha}}$   | 升力曲线斜率        | 5.7                        | 1/rad |
| $C_{D0}$           | 零升阻力系数        | 0.02                       | —     |
| $k$                | 诱导阻力因子        | $1/(\pi AR e)\approx0.066$ | —     |
| $AR$               | 展弦比              | 6                          | —     |
| $e$                | 诱导阻力效率因子    | 0.8                        | —     |
| $C_{m0}$           | 零攻角俯仰力矩系数  | 0.05                       | —     |
| $C_{m_{\alpha}}$   | 俯仰力矩斜率        | -0.38                      | 1/rad |
| $C_{L_{\delta_e}}$ | 升力—升降舵偏导     | 0.8                        | 1/rad |
| $C_{m_{\delta_e}}$ | 俯仰力矩—升降舵偏导 | -1.1                       | 1/rad |

> 注：数值来源于 Beard & McLain (2012)；Stevens & Lewis (2003)，可根据实际试验标定。

### 3.4 数值集成

* 推荐使用 4/5 阶 Runge–Kutta 积分器，步长 0.005–0.01s，保证数值稳定性与精度。

---

## 4. 级联控制架构与典型增益

采用三级级联 PID 控制：外环生成姿态/速率指令，中环生成速率指令，内环生成舵面输出。

### 4.1 外环（航迹／航路跟随，20 Hz）

1. **航向控制**

   * 误差：$\tildeψ=ψ_d-ψ$
   * 控制：$φ_d=K_{p,ψ}\tildeψ+K_{i,ψ}\int\tildeψdt$
   * 增益：$K_{p,ψ}=1.2,;K_{i,ψ}=0.01$

2. **高度控制**

   * 误差：$\tilde h=h_d-h$
   * 控制：$θ_d=K_{p,h}\tilde h+K_{i,h}\int\tilde h dt$
   * 增益：$K_{p,h}=0.8,;K_{i,h}=0.005$

3. **空速控制**

   * 误差：$\tilde V=V_d-V$
   * 控制：$δ_T=K_{p,V}\tilde V+K_{i,V}\int\tilde V dt$
   * 增益：$K_{p,V}=0.5,;K_{i,V}=0.02$

4. **偏航跟踪**

   * 误差：$\tildeψ=ψ_d-ψ$
   * 控制：$r_d=K_{p,ψ_r}\tildeψ+K_{i,ψ_r}\int\tildeψdt$
   * 增益：$K_{p,ψ_r}=0.5,;K_{i,ψ_r}=0.02$

### 4.2 中环（姿态／速率生成，50 Hz）

1. **横滚角生成**

   * 误差：$\tildeφ=φ_d-φ$
   * 控制：$p_d=K_{p,φ}\tildeφ+K_{d,φ}\dot{\tildeφ}$
   * 增益：$K_{p,φ}=5.5,;K_{d,φ}=1.2$

2. **俯仰角生成**

   * 误差：$\tildeθ=θ_d-θ$
   * 控制：$q_d=K_{p,θ}\tildeθ+K_{i,θ}\int\tildeθdt+K_{d,θ}\dot{\tildeθ}$
   * 增益：$K_{p,θ}=6.0,;K_{i,θ}=0.2,;K_{d,θ}=1.0$

3. **偏航速率辅助**

   * 可结合侧滑角环或直接使用 $r_d$。

### 4.3 内环（角速率控制，100 Hz）

1. **横滚速率控制**

   * 误差：$\tilde p=p_d-p$
   * 控制：$δ_a=K_{p,p}\tilde p+K_{d,p}\dot{\tilde p}$
   * 增益：$K_{p,p}=8.0,;K_{d,p}=1.5$

2. **俯仰速率控制**

   * 误差：$\tilde q=q_d-q$
   * 控制：$δ_e=K_{p,q}\tilde q+K_{d,q}\dot{\tilde q}$
   * 增益：$K_{p,q}=9.0,;K_{d,q}=1.8$

3. **偏航速率控制**

   * 误差：$\tilde r=r_d-r$
   * 控制：$δ_r=K_{p,r}\tilde r+K_{d,r}\dot{\tilde r}$
   * 增益：$K_{p,r}=4.0,;K_{d,r}=0.5$

### 4.4 执行周期与时序

* 外环 20 Hz, 中环 50 Hz, 内环 100 Hz
* 主循环示例：

  ```
  while(sim) {  
    read_state();  
    if(time%0.05==0) external_loop();  
    if(time%0.02==0) attitude_loop();  
    rate_loop();  
    integrate_dynamics(Δt);  
    log();  
  }  
  ```

---

## 5. 接口与模块划分

| 模块       | 输入                                        | 输出                           |
| :--------- | :------------------------------------------ | :----------------------------- |
| 动力学模型 | 状态 $\mathbf{x}$，控制 $[δ_T,δ_a,δ_e,δ_r]$ | 更新后状态 $\mathbf{x}_{t+Δt}$ |
| 外环控制   | 目标航迹/姿态，当前状态                     | $ψ_d,θ_d,V_d,h_d,r_d,p_d,q_d$  |
| 中环控制   | $φ_d,θ_d,r_d$，当前姿态                     | $p_d,q_d,r_d$                  |
| 内环控制   | $p_d,q_d,r_d$，当前角速                     | $δ_a,δ_e,δ_r$                  |

---

## 6. 参数标定与验证

1. **开环仿真**：验证动力学模型平飞稳定性。
2. **阶跃响应测试**：分析外环/中环/内环步响应特性。
3. **闭环跟踪**：执行航线跟随、高度保持、空速保持任务，记录误差。
4. **蒙特卡洛测试**：加入风/参数扰动，评估鲁棒性。

---

## 7. 实现建议

* 使用 **Eigen** 实现矩阵运算与 RK 积分。
* 将控制器与动力学模型封装为模块化接口（虚基类+派生）。
* 参数通过 JSON/YAML 配置加载，并支持在线调参。
* 日志与可视化：保存关键变量，用于离线分析与调优。

---

[C++代码实现](https://github.com/flitai/uav-flight-sim-v1)

## 8. 参考文献

1. Stevens, B.L., & Lewis, F.L. (2003). *Aircraft Control and Simulation*. Wiley.
2. Beard, R.W., & McLain, T.W. (2012). *Small Unmanned Aircraft: Theory and Practice*. Princeton University Press.
3. JSBSim User Guide, PRIMES, Inc.
