
+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = '三段S曲线速度规划模型'
summary= "This summary is independent of the content."
+++

```tsx
import React, { useState, useEffect } from 'react';
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';

const SCurveCalculator = () => {
  // 状态变量
  const [params, setParams] = useState({
    S: 800, // 轨迹弧长 (米)
    T: 25, // 总飞行时间 (秒)
    vIn: 35, // 进入速度 (米/秒)
    vDes: 25, // 期望速度 (米/秒)
    vMin: 5, // 最小速度 (米/秒)
    vMax: 40, // 最大速度 (米/秒)
    aMax: 2, // 最大加速度 (米/秒²)
    jMax: 1, // 最大加加速度 (米/秒³)
    dt: 0.1, // 仿真步长 (秒)
  });

  const [curveData, setCurveData] = useState([]);
  const [curveInfo, setCurveInfo] = useState({
    type: '',
    Tconv: 0,
    Tj: 0,
    Ta: 0,
    Tprime: 0,
    Dconv: 0,
    totalDistance: 0,
    vDes: 0,
    vDesAdjusted: false,
    scaleFactor: 1.0,
  });

  // 计算S曲线
  const calculateSCurve = (adjustVdes = false, vDesAdjusted = null) => {
    const { S, T, vIn, vMin, vMax, aMax, jMax, dt } = params;
    // 使用调整后的vDes或原始vDes
    const vDes = vDesAdjusted !== null ? vDesAdjusted : params.vDes;
    const deltaV = vDes - vIn;
    const s = Math.sign(deltaV); // 正表示加速，负表示减速
    
    let Tconv = 0;
    let Tj = 0;
    let Ta = 0;
    let Tprime = 0;
    let curveType = '';
    
    const data = [];
    let distance = 0;
    
    // 如果不需要变速
    if (vIn === vDes) {
      const steps = Math.ceil(T / dt);
      for (let k = 0; k <= steps; k++) {
        const t = k * dt;
        data.push({
          time: t,
          velocity: vIn,
          acceleration: 0,
          jerk: 0
        });
        if (k > 0) {
          distance += vIn * dt;
        }
      }
      
      setCurveInfo({
        type: '无需变速',
        Tconv: 0,
        Tj: 0,
        Ta: 0,
        Tprime: 0,
        Dconv: 0,
        totalDistance: distance
      });
    } else {
      // 判断是梯形S曲线还是三角形S曲线
      if (Math.abs(deltaV) >= (aMax * aMax) / jMax) {
        // 梯形S曲线
        curveType = '梯形S曲线';
        Tj = aMax / jMax;
        Ta = Math.abs(deltaV) / aMax - Tj;
        Tconv = 2 * Tj + Ta;
      } else {
        // 三角形S曲线
        curveType = '三角形S曲线';
        Tprime = Math.sqrt(Math.abs(deltaV) / jMax);
        Tconv = 2 * Tprime;
        Tj = Tprime;
      }
      
      const steps = Math.ceil(T / dt);
      let Dconv = 0;
      
      for (let k = 0; k <= steps; k++) {
        const t = k * dt;
        let v, a, j;
        
        if (t <= Tconv) {
          // 变速阶段
          if (curveType === '梯形S曲线') {
            // 梯形S曲线各阶段
            if (t <= Tj) {
              // 上升阶段
              j = s * jMax;
              a = s * jMax * t;
              v = vIn + (s * jMax * t * t) / 2;
            } else if (t <= Tj + Ta) {
              // 恒加速阶段
              j = 0;
              a = s * aMax;
              v = vIn + (s * aMax * aMax) / (2 * jMax) + s * aMax * (t - Tj);
            } else {
              // 下降阶段
              const tau = t - (Tj + Ta);
              j = -s * jMax;
              a = s * aMax - s * jMax * tau;
              v = vIn + (s * aMax * aMax) / (2 * jMax) + s * aMax * Ta + 
                  s * (aMax * tau - (jMax * tau * tau) / 2);
            }
          } else {
            // 三角形S曲线各阶段
            if (t <= Tprime) {
              // 上升阶段
              j = s * jMax;
              a = s * jMax * t;
              v = vIn + (s * jMax * t * t) / 2;
            } else {
              // 下降阶段
              const tau = t - Tprime;
              j = -s * jMax;
              a = s * jMax * (Tprime - tau);
              v = vIn + (s * jMax * Tprime * Tprime) / 2 + 
                  s * (jMax * Tprime * tau - (jMax * tau * tau) / 2);
            }
          }
          
          if (k > 0) {
            Dconv += v * dt; // 累加变速段距离
          }
        } else {
          // 定速阶段
          v = vDes;
          a = 0;
          j = 0;
          if (k > 0) {
            distance += vDes * dt;
          }
        }
        
        // 速度限制
        v = Math.max(vMin, Math.min(vMax, v));
        
        data.push({
          time: t,
          velocity: v,
          acceleration: a,
          jerk: j
        });
      }
      
      // 计算总距离
      const totalDistance = Dconv + vDes * (T - Tconv);
      
      if (adjustVdes) {
        return { 
          Dconv, 
          Tconv,
          totalDistance 
        };
      }
      
      setCurveInfo({
        type: curveType,
        Tconv: Tconv.toFixed(3),
        Tj: Tj.toFixed(3),
        Ta: Ta.toFixed(3),
        Tprime: Tprime.toFixed(3),
        Dconv: Dconv.toFixed(3),
        totalDistance: totalDistance.toFixed(3),
        vDes: vDes.toFixed(3)
      });
    }
    
    setCurveData(data);
  };
  
  // 计算调整后的vDes以匹配目标弧长S
  const calculateAdjustedVdes = () => {
    const { S, T, vIn, vMin, vMax } = params;
    
    // 二分查找法求解合适的vDes
    let lowVdes = vMin;
    let highVdes = vMax;
    let vDesAdjusted = params.vDes;
    let bestDistance = Infinity;
    let bestVdes = params.vDes;
    
    // 最多迭代20次，通常足以得到很高精度
    for (let i = 0; i < 20; i++) {
      const midVdes = (lowVdes + highVdes) / 2;
      const result = calculateSCurve(true, midVdes);
      
      const currentDistance = result.totalDistance;
      const error = Math.abs(currentDistance - S);
      
      // 保存最接近的结果
      if (error < bestDistance) {
        bestDistance = error;
        bestVdes = midVdes;
      }
      
      // 如果误差足够小，提前退出
      if (error < 0.001) {
        break;
      }
      
      if (currentDistance < S) {
        lowVdes = midVdes;
      } else {
        highVdes = midVdes;
      }
    }
    
    // 计算比例因子
    const scaleFactor = bestVdes / params.vDes;
    
    return {
      vDes: bestVdes,
      scaleFactor: scaleFactor
    };
  };
  
  // 当参数改变时重新计算
  useEffect(() => {
    calculateSCurve();
  }, [params]);
  
  // 处理输入变化
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setParams({
      ...params,
      [name]: parseFloat(value)
    });
  };
  
  // 调整vDes并重新计算
  const adjustVdesAndRecalculate = () => {
    const result = calculateAdjustedVdes();
    calculateSCurve(false, result.vDes);
    
    // 更新比例因子到curveInfo
    setCurveInfo(prevInfo => ({
      ...prevInfo,
      scaleFactor: result.scaleFactor.toFixed(4),
      vDesAdjusted: true
    }));
  };

  return (
    <div className="p-4 max-w-6xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">S曲线变速方案计算器</h1>
      
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <div className="bg-gray-100 p-4 rounded-lg">
          <h2 className="text-xl font-semibold mb-2">参数设置</h2>
          <div className="mb-3">
            <button 
              onClick={adjustVdesAndRecalculate}
              className="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded"
            >
              调整期望速度以匹配目标弧长S
            </button>
          </div>
          <div className="grid grid-cols-2 gap-4">
            <div>
              <label className="block text-sm font-medium text-gray-700">轨迹弧长 (S) [米]</label>
              <input
                type="number"
                name="S"
                value={params.S}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">总飞行时间 (T) [秒]</label>
              <input
                type="number"
                name="T"
                value={params.T}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">进入速度 (vIn) [米/秒]</label>
              <input
                type="number"
                name="vIn"
                value={params.vIn}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">期望速度 (vDes) [米/秒]</label>
              <input
                type="number"
                name="vDes"
                value={params.vDes}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">最小速度 (vMin) [米/秒]</label>
              <input
                type="number"
                name="vMin"
                value={params.vMin}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">最大速度 (vMax) [米/秒]</label>
              <input
                type="number"
                name="vMax"
                value={params.vMax}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">最大加速度 (aMax) [米/秒²]</label>
              <input
                type="number"
                name="aMax"
                value={params.aMax}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">最大加加速度 (jMax) [米/秒³]</label>
              <input
                type="number"
                name="jMax"
                value={params.jMax}
                onChange={handleInputChange}
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700">仿真步长 (dt) [秒]</label>
              <input
                type="number"
                name="dt"
                value={params.dt}
                onChange={handleInputChange}
                step="0.01"
                min="0.01"
                className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
              />
            </div>
          </div>
        </div>
        
        <div className="bg-gray-100 p-4 rounded-lg">
          <h2 className="text-xl font-semibold mb-2">计算结果</h2>
          <div className="space-y-2">
            <p><span className="font-medium">曲线类型:</span> {curveInfo.type}</p>
            <p><span className="font-medium">变速时间 (Tconv):</span> {curveInfo.Tconv} 秒</p>
            {curveInfo.type === '梯形S曲线' && (
              <>
                <p><span className="font-medium">加速度上升/下降时间 (Tj):</span> {curveInfo.Tj} 秒</p>
                <p><span className="font-medium">恒加速时间 (Ta):</span> {curveInfo.Ta} 秒</p>
              </>
            )}
            {curveInfo.type === '三角形S曲线' && (
              <p><span className="font-medium">特征时间 (T'):</span> {curveInfo.Tprime} 秒</p>
            )}
            <p><span className="font-medium">变速段距离:</span> {curveInfo.Dconv} 米</p>
            <p><span className="font-medium">总距离:</span> {curveInfo.totalDistance} 米</p>
            <p><span className="font-medium">使用的期望速度:</span> {curveInfo.vDes || params.vDes} 米/秒</p>
            {curveInfo.vDesAdjusted && (
              <p><span className="font-medium">速度比例因子:</span> {curveInfo.scaleFactor} (原始v_des = {params.vDes} 米/秒)</p>
            )}
            <p className={curveInfo.totalDistance != params.S ? "text-red-500 font-bold" : "text-green-500 font-bold"}>
              {curveInfo.totalDistance != params.S ? 
                `警告: 总距离与目标弧长不符 (差值: ${(curveInfo.totalDistance - params.S).toFixed(3)} 米)` : 
                "总距离与目标弧长匹配!"}
            </p>
            {curveInfo.totalDistance != params.S && (
              <p className="text-blue-500">
                点击"调整期望速度"按钮以匹配目标弧长
              </p>
            )}
          </div>
        </div>
      </div>
      
      <div className="space-y-6">
        <div className="bg-white p-4 rounded-lg shadow">
          <h3 className="text-lg font-semibold mb-2">速度曲线</h3>
          <ResponsiveContainer width="100%" height={300}>
            <LineChart data={curveData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis 
                dataKey="time" 
                label={{ value: '时间 (秒)', position: 'insideBottomRight', offset: -5 }} 
              />
              <YAxis 
                label={{ value: '速度 (m/s)', angle: -90, position: 'insideLeft' }} 
              />
              <Tooltip />
              <Legend />
              <Line 
                type="monotone" 
                dataKey="velocity" 
                stroke="#1E88E5" 
                strokeWidth={2} 
                name="速度" 
                dot={false} 
              />
            </LineChart>
          </ResponsiveContainer>
        </div>
        
        <div className="bg-white p-4 rounded-lg shadow">
          <h3 className="text-lg font-semibold mb-2">加速度曲线</h3>
          <ResponsiveContainer width="100%" height={300}>
            <LineChart data={curveData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis 
                dataKey="time" 
                label={{ value: '时间 (秒)', position: 'insideBottomRight', offset: -5 }} 
              />
              <YAxis 
                label={{ value: '加速度 (m/s²)', angle: -90, position: 'insideLeft' }} 
              />
              <Tooltip />
              <Legend />
              <Line 
                type="monotone" 
                dataKey="acceleration" 
                stroke="#43A047" 
                strokeWidth={2} 
                name="加速度" 
                dot={false} 
              />
            </LineChart>
          </ResponsiveContainer>
        </div>
        
        <div className="bg-white p-4 rounded-lg shadow">
          <h3 className="text-lg font-semibold mb-2">加加速度曲线</h3>
          <ResponsiveContainer width="100%" height={300}>
            <LineChart data={curveData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis 
                dataKey="time" 
                label={{ value: '时间 (秒)', position: 'insideBottomRight', offset: -5 }} 
              />
              <YAxis 
                label={{ value: '加加速度 (m/s³)', angle: -90, position: 'insideLeft' }} 
              />
              <Tooltip />
              <Legend />
              <Line 
                type="monotone" 
                dataKey="jerk" 
                stroke="#E53935" 
                strokeWidth={2} 
                name="加加速度" 
                dot={false} 
              />
            </LineChart>
          </ResponsiveContainer>
        </div>
      </div>
    </div>
  );
};

export default SCurveCalculator;
```