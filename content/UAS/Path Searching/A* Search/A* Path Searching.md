+++
date = '2025-05-20T11:13:37+08:00'
draft = false
math=true 
title = '基于运动基元的A*算法'
summary= "将基于运动基元的A*算法从四旋翼无人机改进适配到固定翼无人机"
+++


参考的基于运动基元的四旋翼无人机A\*算法：

https://github.com/HKUST-Aerial-Robotics/Fast-Planner/blob/master/fast_planner/path_searching/src/kinodynamic_astar.cpp

 **总体思路**

1. **分析四旋翼A\*算法的核心部分**：了解其状态表示、控制输入、运动模型和运动基元生成方式。

2. **理解固定翼无人机的动力学特性**：明确其状态变量、控制输入、运动学/动力学模型，以及物理和操作限制。

3. **整合调整**：在前两步的基础上，重新定义状态和控制输入，修改运动基元生成方法，调整启发式函数和代价函数，确保算法的合理性和可行性。

---



### **1. 四旋翼A\*算法概述**

**1.1 状态表示**

- **位置**：$\mathbf{p} = [x, y, z]^T$
- **速度**：$\mathbf{v} = [v_x, v_y, v_z]^T$
- **状态向量**：$\mathbf{s} = [\mathbf{p}, \mathbf{v}]^T$，共6维。

**1.2 控制输入**

- **加速度**：$\mathbf{a} = [a_x, a_y, a_z]^T$
- **控制输入集**：在最大加速度范围内进行离散化，生成一系列可能的加速度向量。

**1.3 运动模型**

- 假设加速度在时间段$\tau$内恒定，使用匀加速运动方程进行状态转移：

$$
\begin{cases}
\mathbf{p}(t+\tau) = \mathbf{p}(t) + \mathbf{v}(t) \tau + \frac{1}{2} \mathbf{a} \tau^2 \\
\mathbf{v}(t+\tau) = \mathbf{v}(t) + \mathbf{a} \tau
\end{cases}
$$

**1.4 运动基元生成**

- 对控制输入$\mathbf{a}$和持续时间$\tau$进行离散化，生成一系列可能的运动基元。
- 在节点扩展时，应用这些运动基元进行状态转移，生成新节点。

---

### **2. 固定翼无人机动力学特性**

**2.1 状态表示**

- **位置**：$\mathbf{p} = [x, y, z]^T$
- **航向角**：$\chi$（水平面内的方向）
- **俯仰角**（航迹角）：$\gamma$（垂直方向的角度）
- **速度大小**：$v$（通常假设恒定或在一定范围内）
- **状态向量**：$\mathbf{s} = [x, y, z, \chi, \gamma]^T$，共5维。

**2.2 控制输入**

- **航向角变化率**：$\dot{\chi}$
- **航迹角变化率**：$\dot{\gamma}$
- **控制输入向量**：$\mathbf{u} = [\dot{\chi}, \dot{\gamma}]^T$

**2.3 运动学模型**

假设速度$v$恒定，固定翼无人机的运动学方程为：

$$
\begin{cases}
\dot{x} = v \cos \gamma \cos \chi \\
\dot{y} = v \cos \gamma \sin \chi \\
\dot{z} = v \sin \gamma \\
\dot{\chi} = \dot{\chi} \\
\dot{\gamma} = \dot{\gamma}
\end{cases}
$$

**2.4 动力学限制**

- **最小速度限制**：$v_{\min}$，避免失速。
- **最大速度限制**：$v_{\max}$
- **最大航向角变化率**：$|\dot{\chi}| \leq \dot{\chi}_{\max}$
- **最大航迹角变化率**：$|\dot{\gamma}| \leq \dot{\gamma}_{\max}$

---

### **3. 方案整合与调整**

**3.1 状态和控制输入的重新定义**

- **状态向量**：$\mathbf{s} = [x, y, z, \chi, \gamma]^T$
- **控制输入**：$\mathbf{u} = [\dot{\chi}, \dot{\gamma}]^T$

**3.2 运动基元的生成**

- **控制输入离散化**：

- **航向角变化率 $\dot{\chi}$**：

$$
  \dot{\gamma}_D = \left\{ -\dot{\gamma}_{\max}, -\dot{\gamma}_{\max} + \Delta_{\gamma}, \ldots, 0, \ldots, \dot{\gamma}_{\max} - \Delta_{\gamma}, \dot{\gamma}_{\max} \right\}
  $$

- **航迹角变化率 $\dot{\gamma}$**：

$$
  \dot{\gamma}_D = \left\{ -\dot{\gamma}_{\max}, -\dot{\gamma}_{\max} + \Delta_{\gamma}, \ldots, 0, \ldots, \dot{\gamma}_{\max} - \Delta_{\gamma}, \dot{\gamma}_{\max} \right\}
$$

- **组合控制输入集**：

  $$
  \mathcal{U}_D = \left\{ (\dot{\chi}, \dot{\gamma}) \ \big| \ \dot{\chi} \in \dot{\chi}_D, \ \dot{\gamma} \in \dot{\gamma}_D \right\}
  $$


- **时间步长的确定**：设定固定的时间步长$\tau$，或者根据无人机的速度和环境动态调整。

- **状态转移**：

  - 使用数值积分方法（如四阶Runge-Kutta方法）在时间步长$\tau$内对运动学方程进行积分，计算新状态$\mathbf{s}(t+\tau)$。

  - **状态更新函数**：

    ```cpp
    void stateTransit(const Eigen::VectorXd& state0, Eigen::VectorXd& state1,
                      const Eigen::Vector2d& control_input, double tau) {
      double v = speed_; // 固定翼无人机的速度
      double chi0 = state0(3);
      double gamma0 = state0(4);
      double dot_chi = control_input(0);
      double dot_gamma = control_input(1);
    
      // 更新航向角和航迹角
      double chi1 = chi0 + dot_chi * tau;
      double gamma1 = gamma0 + dot_gamma * tau;
    
      // 使用平均值近似或数值积分计算位置更新
      double avg_chi = (chi0 + chi1) / 2;
      double avg_gamma = (gamma0 + gamma1) / 2;
    
      double x1 = state0(0) + v * cos(avg_gamma) * cos(avg_chi) * tau;
      double y1 = state0(1) + v * cos(avg_gamma) * sin(avg_chi) * tau;
      double z1 = state0(2) + v * sin(avg_gamma) * tau;
    
      state1.resize(5);
      state1 << x1, y1, z1, chi1, gamma1;
    }
    ```

**3.3 节点扩展**

- **扩展函数**：

  ```cpp
  void expandNode(PathNodePtr current_node, std::vector<PathNodePtr>& successors) {
    // 获取当前状态
    Eigen::VectorXd state0 = current_node->state;
  
    // 遍历所有可能的控制输入
    for (const auto& dot_chi : dot_chi_values) {
      for (const auto& dot_gamma : dot_gamma_values) {
        Eigen::Vector2d control_input(dot_chi, dot_gamma);
        Eigen::VectorXd state1;
  
        // 状态转移
        stateTransit(state0, state1, control_input, tau);
  
        // 检查速度限制（如果速度可变）
        // 检查物理可行性，如最小转弯半径
  
        // 碰撞检测
        if (!isCollisionFree(state0, state1)) {
          continue;
        }
  
        // 创建新节点
        PathNodePtr new_node = new PathNode();
        new_node->state = state1;
        new_node->input = control_input;
        new_node->duration = tau;
        new_node->parent = current_node;
  
        // 计算代价
        new_node->g_score = current_node->g_score + costFunction(state0, state1, control_input, tau);
        new_node->f_score = new_node->g_score + heuristic(state1);
  
        // 加入后继节点列表
        successors.push_back(new_node);
      }
    }
  }
  ```

**3.4 代价函数和启发式函数**

- **代价函数**：考虑航向角和航迹角变化率的代价，以及时间代价。

$$
  \text{Cost} = w_{\chi} |\dot{\chi}| + w_{\gamma} |\dot{\gamma}| + w_t \tau
$$

- **启发式函数**：使用三维Dubins路径的长度作为启发式估计。

  - **Dubins路径**：在已知最小转弯半径的情况下，计算从当前状态到目标状态的最短路径长度。

  - **启发式函数实现**：

    ```cpp
    double heuristic(const Eigen::VectorXd& state) {
      // 计算当前状态到目标状态的Dubins路径长度
      double h = computeDubinsPathLength(state, goal_state_, min_turn_radius_);
      return h;
    }
    ```

**3.5 碰撞检测**

- **离散采样**：在状态转移过程中，对轨迹进行离散采样，检查每个采样点是否与障碍物发生碰撞。

- **碰撞检测函数**：

  ```cpp
  bool isCollisionFree(const Eigen::VectorXd& state0, const Eigen::VectorXd& state1) {
    int num_checks = 10; // 采样点数量
    for (int i = 1; i <= num_checks; ++i) {
      double t = (double)i / num_checks * tau;
      Eigen::VectorXd intermediate_state;
      stateTransit(state0, intermediate_state, state1.segment(3,2) - state0.segment(3,2), t);
      Eigen::Vector3d position = intermediate_state.head(3);
      if (!isPositionValid(position)) {
        return false;
      }
    }
    return true;
  }
  ```

**3.6 节点结构调整**

- **节点结构**：根据新的状态和控制输入，调整节点的定义。

  ```cpp
  class PathNode {
   public:
    Eigen::Vector3i index; // 栅格索引
    Eigen::VectorXd state; // 状态向量 [x, y, z, chi, gamma]
    double g_score, f_score;
    Eigen::Vector2d input; // 控制输入 [dot_chi, dot_gamma]
    double duration;
    PathNode* parent;
    char node_state;
  
    // 构造函数和析构函数
  };
  ```

**3.7 A\*算法流程**

- **搜索主循环**：与传统A\*算法类似，使用开启集和关闭集管理节点，按照 $f = g + h$ 的代价进行节点扩展和选择。

- **算法步骤**：

  1. 初始化开启集，将起始节点加入开启集。
  2. 当开启集非空时：
     - 从开启集中取出 $f$ 值最小的节点作为当前节点。
     - 如果当前节点达到目标状态（或在容忍范围内），则回溯路径，结束搜索。
     - 将当前节点加入关闭集。
     - 扩展当前节点，生成后继节点。
     - 对于每个后继节点：
       - 如果节点在关闭集中，跳过。
       - 如果节点不在开启集中，或找到更优路径，更新节点信息并加入开启集。

---

### **4. 方案可行性分析**

**4.1 动力学合理性**

- 重新定义的状态和控制输入符合固定翼无人机的动力学特性。
- 运动基元的生成考虑了固定翼的物理限制，如最小转弯半径和速度限制。

**4.2 算法有效性**

- 通过使用合适的启发式函数（Dubins路径长度），保证算法的效率和最优性。
- 在节点扩展时进行碰撞检测，确保路径的可行性和安全性。

**4.3 实际应用性**

- 方案中各部分的实现细节，如状态转移函数、碰撞检测和启发式函数，都可以在实际代码中具体实现。
- 方案兼顾了计算效率和规划质量，可以适用于实时路径规划。

---

### **5. 示例参数设置**

- **速度**：$v = 15 \ \text{m/s}$

- **最大航向角变化率**：$\dot{\chi}_{\max} = \frac{\pi}{6} \ \text{rad/s}$（30度/秒）

- **最大航迹角变化率**：$\dot{\gamma}_{\max} = \frac{\pi}{18} \ \text{rad/s}$（10度/秒）

- **时间步长**：$\tau = 1 \ \text{s}$

- **控制输入离散化步长**：

  - $\Delta_{\chi} = \frac{\dot{\chi}_{\max}}{2}$
  - $\Delta_{\gamma} = \frac{\dot{\gamma}_{\max}}{2}$

- **控制输入集合**：

  ```cpp
  std::vector<double> dot_chi_values = {-dot_chi_max, -dot_chi_max/2, 0, dot_chi_max/2, dot_chi_max};
  std::vector<double> dot_gamma_values = {-dot_gamma_max, -dot_gamma_max/2, 0, dot_gamma_max/2, dot_gamma_max};
  ```

- **代价函数权重**：

  - $w_{\chi} = 1.0$
  - $w_{\gamma} = 1.0$
  - $w_t = 0.1$

- **最小转弯半径**：

$$
  R_{\min} = \frac{v}{\dot{\chi}_{\max}}
$$

---

### **6. 实现注意事项**

**6.1 数值积分方法**

- 由于航向角和航迹角在时间内线性变化，可以使用解析积分或简单的数值积分方法。

**6.2 速度的可变性**

- 如果需要考虑速度的变化，可以将速度作为状态变量，并引入油门控制输入。

**6.3 启发式函数的一致性**

- 确保启发式函数不超过实际代价，保持算法的可完备性和最优性。

**6.4 碰撞检测的精度**

- 根据环境的复杂程度，调整采样点数量，以在精度和计算效率之间取得平衡。

### **总结**

通过综合以上方案，我们成功地将基于四旋翼运动基元的A\*算法调整为适用于固定翼无人机的版本。关键的调整在于：

1. **重新定义状态和控制输入**：符合固定翼无人机的动力学特性。

2. **修改运动基元生成方式**：基于固定翼的运动学模型，生成物理可行的运动基元。

3. **调整代价函数和启发式函数**：考虑固定翼的运动特性，使用Dubins路径作为启发式估计。

4. **确保算法的可行性和有效性**：通过碰撞检测和合理的参数设置，生成安全可行的路径。

该方案兼顾了理论合理性和实际可行性，可用于固定翼无人机的路径规划，实现自主导航和避障功能。
