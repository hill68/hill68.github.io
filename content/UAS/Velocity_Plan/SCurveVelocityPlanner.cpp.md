+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = '三段S曲线速度规划模型'
summary= "This summary is independent of the content."
+++

```cpp

#include <iostream>
#include <fstream>
#include <iomanip>
#include <cmath>
#include <vector>
#include <string>

// 结构体，保存每个时刻的规划状态，同时增加累计飞行距离字段
struct SCurveOutput {
    double time;         // 当前时间（秒）
    double distance;     // 累计飞行距离（米）
    double velocity;     // 速度（米/秒）
    double acceleration; // 加速度（米/秒²）
    double jerk;         // 加加速度（米/秒³）
};

// S 曲线速度规划器类
class SCurveVelocityPlanner {
public:
    // 模型输入参数
    double S;      // 总轨迹弧长（米）
    double T;      // 总飞行时间（秒）
    double vIn;    // 进入速度（米/秒）
    double vDes;   // 期望速度（米/秒）——后段定速
    double vMin;   // 最小速度（米/秒）
    double vMax;   // 最大速度（米/秒）
    double aMax;   // 最大加速度（米/秒²）
    double jMax;   // 最大加加速度（米/秒³）
    
    // 预计算的变速阶段参数
    double Tconv;  // 变速阶段时长（S 曲线部分）
    double Tj;     // 上升/下降阶段时长（加加速度阶段时长）
    double Ta;     // 梯形 S 曲线中恒加速阶段时长（若为三角形则为 0）
    
    bool isTrapezoidal; // 若采用梯形 S 曲线则为 true，否则为三角形
    
    // 构造函数：初始化输入参数，并计算变速阶段参数
    SCurveVelocityPlanner(double S_, double T_, double vIn_, double vDes_,
                          double vMin_, double vMax_, double aMax_, double jMax_)
    : S(S_), T(T_), vIn(vIn_), vDes(vDes_), vMin(vMin_), vMax(vMax_), aMax(aMax_), jMax(jMax_) {
        computeTransitionTime();
    }
    
    // 根据 vDes 与 vIn 的差值，判断采用梯形或三角形 S 曲线，并计算相应参数
    void computeTransitionTime() {
        double deltaV = vDes - vIn;
        double s = (deltaV >= 0) ? 1.0 : -1.0;
        if (std::fabs(deltaV) < 1e-6) {
            // 无需变速
            Tconv = 0.0;
            Tj = 0.0;
            Ta = 0.0;
            isTrapezoidal = true; // 任意选择
        } else {
            if (std::fabs(deltaV) >= (aMax * aMax) / jMax) {
                // 梯形 S 曲线
                isTrapezoidal = true;
                Tj = aMax / jMax;
                Ta = std::fabs(deltaV) / aMax - Tj;
                Tconv = 2 * Tj + Ta;
            } else {
                // 三角形 S 曲线
                isTrapezoidal = false;
                Tj = std::sqrt(std::fabs(deltaV) / jMax);
                Tconv = 2 * Tj;
                Ta = 0.0;
            }
        }
    }
    
    // 根据当前时间 t 计算 S 曲线状态输出（不计算累计距离，由外部积分）
    SCurveOutput compute(double t) {
        SCurveOutput out;
        out.time = t;
        double s = (vDes - vIn >= 0) ? 1.0 : -1.0;
        
        // 当无变速需求时
        if (std::fabs(vDes - vIn) < 1e-6) {
            out.velocity = vIn;
            out.acceleration = 0;
            out.jerk = 0;
        }
        else if (t <= Tconv) {
            // 处于 S 曲线变速阶段
            if (isTrapezoidal) {
                // 梯形 S 曲线
                if (t <= Tj) {
                    // 上升阶段：加加速阶段
                    out.jerk = s * jMax;
                    out.acceleration = s * jMax * t;
                    out.velocity = vIn + s * (jMax * t * t) / 2;
                }
                else if (t <= Tj + Ta) {
                    // 恒定加速阶段
                    out.jerk = 0;
                    out.acceleration = s * aMax;
                    double vRamp = vIn + s * (jMax * Tj * Tj) / 2; // 上升阶段末速度
                    out.velocity = vRamp + s * aMax * (t - Tj);
                }
                else {
                    // 下降阶段：加速度衰减阶段
                    double tau = t - (Tj + Ta);
                    out.jerk = -s * jMax;
                    out.acceleration = s * aMax - s * jMax * tau;
                    double vRamp = vIn + s * (jMax * Tj * Tj) / 2;
                    out.velocity = vRamp + s * aMax * Ta + s * (aMax * tau - (jMax * tau * tau) / 2);
                }
            } else {
                // 三角形 S 曲线
                if (t <= Tj) {
                    // 上升阶段
                    out.jerk = s * jMax;
                    out.acceleration = s * jMax * t;
                    out.velocity = vIn + s * (jMax * t * t) / 2;
                } else {
                    // 下降阶段
                    double tau = t - Tj;
                    out.jerk = -s * jMax;
                    out.acceleration = s * jMax * (Tj - tau);
                    double vRamp = vIn + s * (jMax * Tj * Tj) / 2;
                    out.velocity = vRamp + s * (jMax * Tj * tau - (jMax * tau * tau) / 2);
                }
            }
        } else {
            // 定速阶段，保持期望速度
            out.velocity = vDes;
            out.acceleration = 0;
            out.jerk = 0;
        }
        
        // 对速度做上下限保护
        if (out.velocity < vMin) out.velocity = vMin;
        if (out.velocity > vMax) out.velocity = vMax;
        
        return out;
    }
};

// 计算整段轨迹的 S 曲线数据（按步长 dt 计算每个时间点），并返回 vector
std::vector<SCurveOutput> computeTrajectory(SCurveVelocityPlanner &planner, double dt) {
    std::vector<SCurveOutput> trajectory;
    int steps = static_cast<int>(std::ceil(planner.T / dt));
    double cumulativeDistance = 0.0;
    SCurveOutput prevOutput = {0.0, 0.0, planner.vIn, 0.0, 0.0};
    
    for (int i = 0; i <= steps; i++) {
        double t = i * dt;
        if (t > planner.T) t = planner.T; // 确保最后时刻不超出 T
        SCurveOutput out = planner.compute(t);
        // 用简单梯形积分计算累计距离（可以使用平均值提高精度）
        if (i == 0) {
            cumulativeDistance = 0.0;
        } else {
            // 采用前后速度平均近似积分
            double avgV = (prevOutput.velocity + out.velocity) / 2.0;
            cumulativeDistance += avgV * dt;
        }
        out.distance = cumulativeDistance;
        trajectory.push_back(out);
        prevOutput = out;
    }
    return trajectory;
}

// 输出 CSV 文件，保存轨迹数据
bool outputTrajectoryToCSV(const std::vector<SCurveOutput> &trajectory, const std::string &filename) {
    std::ofstream file(filename);
    if (!file.is_open()) {
        std::cerr << "无法打开文件 " << filename << " 进行写入！" << std::endl;
        return false;
    }
    
    // 写入 CSV 头部
    file << "time,distance,velocity,acceleration,jerk\n";
    file << std::fixed << std::setprecision(6);
    
    // 写入每个时刻数据
    for (const auto &pt : trajectory) {
        file << pt.time << ","
             << pt.distance << ","
             << pt.velocity << ","
             << pt.acceleration << ","
             << pt.jerk << "\n";
    }
    
    file.close();
    return true;
}

// 示例：计算整段轨迹数据，并可选输出为 CSV 文件
int main() {
    // 参数设置（示例）
    double S = 800;       // 总轨迹弧长，单位米
    double T = 25;        // 总飞行时间，单位秒
    double vIn = 35;      // 进入速度，单位米/秒
    double vDes = 25;     // 期望速度（后段定速），单位米/秒
    double vMin = 5;      // 最小速度，单位米/秒
    double vMax = 40;     // 最大速度，单位米/秒
    double aMax = 2;      // 最大加速度，单位米/秒²
    double jMax = 1;      // 最大加加速度，单位米/秒³
    double dt = 0.1;      // 仿真步长，单位秒

    // 创建 S 曲线速度规划器实例
    SCurveVelocityPlanner planner(S, T, vIn, vDes, vMin, vMax, aMax, jMax);
    
    // 一次性计算整段轨迹数据
    std::vector<SCurveOutput> trajectory = computeTrajectory(planner, dt);
    
    // 输出部分数据到控制台（例如前 10 个点）
    std::cout << "time(s)   distance(m)   velocity(m/s)   acceleration(m/s^2)   jerk(m/s^3)" << std::endl;
    for (size_t i = 0; i < trajectory.size() && i < 10; i++) {
        std::cout << std::fixed << std::setprecision(3)
                  << trajectory[i].time << "       "
                  << trajectory[i].distance << "        "
                  << trajectory[i].velocity << "           "
                  << trajectory[i].acceleration << "                "
                  << trajectory[i].jerk << std::endl;
    }
    
    // 可选：输出 CSV 文件
    std::string csvFilename = "trajectory.csv";
    if (outputTrajectoryToCSV(trajectory, csvFilename)) {
        std::cout << "轨迹数据已成功输出到 " << csvFilename << std::endl;
    } else {
        std::cerr << "CSV 输出失败！" << std::endl;
    }
    
    return 0;
}
```