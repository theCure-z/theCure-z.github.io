# 原理

假定机器人以若干组当前容许范围内的速度搜索空间 $(v,w)$ 进行移动。
对这若干组速度进行轨迹计算。
根据评分机制选择最好的轨迹所对应的速度进行 DWA 输出。
优点：考虑到速度与加速度的限制，只有安全轨迹会被考虑，复杂度低。
缺点：动态避障效果差；只适用于差速和全向移动，对于阿克曼不行；并非全局最优路径。

# 速度采样及轨迹生成

以差速模型为例，机器人只能进行旋转或向前移动，经过一段时间后，机器人状态可表示为：

$$
\begin{bmatrix}
x(t+\Delta t)\\
y(t+\Delta t)\\
\theta(t+\Delta t)\\
v(t+\Delta t)\\
w(t+\Delta t)
\end{bmatrix}
=
\begin{bmatrix}
x(t)+v(t)\cos(\theta(t))\Delta t\\
y(t)+v(t)\sin(\theta(t))\Delta t\\
\theta(t)+w(t)\Delta t\\
v(t)+a(t)\Delta t\\
w(t)+\dot{w}(t)\Delta t
\end{bmatrix}
$$

$\Delta t$ 足够小时，近似为真实运动方程。

# 速度空间限制

完成了任意速度空间下机器人状态空间推导，对机器人速度空间也有相应限制。

1. 机器人受自身速度最值限制。

$$
S_{v,w}=\{(v,w)\mid v\in[v_{min},v_{max}]\land w\in[w_{min},w_{max}]\}
$$

2. 机器人受加速度性能影响。

$$
S_{v,w}
=
\left\{
(v,w)\mid
v\in[v_{now}-\dot{v}_{min}\Delta t,v_{now}+\dot{v}_{max}\Delta t]
\land
w\in[w_{now}-\dot{w}_{min}\Delta t,w_{now}+\dot{w}_{max}\Delta t]
\right\}
$$

3. 机器人需能在障碍物前停下。

$$
S_{v,w}
=
\left\{
(v,w)\mid
v\leq\sqrt{2dist(v,w)\dot{v}_{min}}
\land
w\leq\sqrt{2dist(v,w)\dot{w}_{min}}
\right\}
$$

其中 $dist(v,w)$ 分别代表了速度空间 $(v,w)$ 对应的轨迹上距障碍物的最小距离和最小转角。

## 评价函数

引入评价函数对采样轨迹进行打分选取最优轨迹。

$$
G(v,w)
=
\sigma\left(
\alpha\times heading(v,w)
+\beta\times dist(v,w)
+\gamma\times vel(v,w)
\right)
$$

其中 $heading(v,w)$ 为方位角评价函数，评价在当前速度空间下轨迹末端朝向与目标点之间的角度差距。
$dist(v,w)$ 为距离评价函数，评价在当前速度空间下机器人处于轨迹末端点位置时与地图上最近障碍物的距离并进行惩罚。
$velocity(v,w)$ 为速度评价函数，评价在当前速度空间下机器人的速度大小，促进机器人更快达到目标点。
$\sigma,\alpha,\beta,\gamma$ 均为权重参数。