# Gymnasium API 工具书

> Gymnasium 是 Farama Foundation 维护的强化学习环境 API。
>
> 官方文档：
> https://gymnasium.farama.org/

---

# 1. 导入

```python
import gymnasium as gym
```

通常使用：

```python
import gymnasium as gym
```

而不是旧版 Gym：

```python
import gym
```

---

# 2. 创建环境

## 2.1 `gym.make()`

最常用的环境创建方法：

```python
env = gym.make("CartPole-v1")
```

完整形式：

```python
env = gym.make(
    "环境名称",
    参数1=值1,
    参数2=值2,
)
```

例如：

```python
env = gym.make(
    "CartPole-v1",
    render_mode="human"
)
```

---

## 2.2 查看环境注册表

查看 Gymnasium 中已经注册的环境：

```python
gym.pprint_registry()
```

---

# 3. 环境的核心 API

一个 Gymnasium 环境最重要的几个 API：

```text
env.reset()
env.step(action)
env.render()
env.close()

env.action_space
env.observation_space
```

---

# 4. `reset()`

## 基本用法

```python
observation, info = env.reset()
```

返回：

```text
observation
info
```

例如：

```python
obs, info = env.reset()

print(obs)
print(info)
```

---

## 指定随机种子

```python
obs, info = env.reset(seed=42)
```

通常在一个环境刚创建后：

```python
env = gym.make("CartPole-v1")

obs, info = env.reset(seed=42)
```

用于保证实验具有可重复性。

---

## 使用 options

```python
obs, info = env.reset(
    options={
        "key": "value"
    }
)
```

是否支持具体的 `options` 取决于环境。

---

# 5. `step()`

这是强化学习中最核心的 API。

```python
observation, reward, terminated, truncated, info = env.step(action)
```

返回五个值：

```text
observation
reward
terminated
truncated
info
```

---

## 5.1 observation

执行动作之后得到的下一个观测：

```python
obs
```

满足：

```python
obs in env.observation_space
```

---

## 5.2 reward

执行动作获得的奖励：

```python
reward
```

通常是：

```python
float
```

---

## 5.3 terminated

表示环境是否因为**任务本身达到终止条件**而结束。

例如：

```text
到达目标
游戏失败
死亡
任务完成
```

---

## 5.4 truncated

表示环境是否因为**外部限制**而结束。

最典型的是：

```text
达到最大时间步数
```

例如：

```text
最大步数 = 500

第 500 步结束
→ truncated = True
```

---

## 5.5 `terminated` 和 `truncated` 的判断

一个 episode 是否结束：

```python
done = terminated or truncated
```

例如：

```python
obs, reward, terminated, truncated, info = env.step(action)

if terminated or truncated:
    obs, info = env.reset()
```

---

# 6. 一个完整的 Gymnasium 循环

```python
import gymnasium as gym

env = gym.make("CartPole-v1")

obs, info = env.reset()

while True:

    action = env.action_space.sample()

    obs, reward, terminated, truncated, info = env.step(action)

    print(
        obs,
        reward,
        terminated,
        truncated
    )

    if terminated or truncated:
        break

env.close()
```

---

# 7. 强化学习中最常见的循环

```python
import gymnasium as gym

env = gym.make("CartPole-v1")

for episode in range(100):

    obs, info = env.reset()

    while True:

        action = env.action_space.sample()

        next_obs, reward, terminated, truncated, info = env.step(action)

        done = terminated or truncated

        # RL算法更新
        # update(obs, action, reward, next_obs, done)

        obs = next_obs

        if done:
            break

env.close()
```

---

# 8. Action Space

环境的动作空间：

```python
env.action_space
```

例如：

```python
env = gym.make("CartPole-v1")

print(env.action_space)
```

可能输出：

```text
Discrete(2)
```

表示：

```text
动作 = 0
动作 = 1
```

---

# 9. `action_space.sample()`

随机采样一个合法动作：

```python
action = env.action_space.sample()
```

例如：

```python
action = env.action_space.sample()

print(action)
```

---

# 10. 判断动作是否合法

```python
env.action_space.contains(action)
```

例如：

```python
action = 0

print(
    env.action_space.contains(action)
)
```

返回：

```text
True
```

---

# 11. Action Space：`Discrete`

离散动作空间：

```python
gym.spaces.Discrete(n)
```

例如：

```python
space = gym.spaces.Discrete(4)
```

表示：

```text
{0, 1, 2, 3}
```

随机采样：

```python
action = space.sample()
```

---

# 12. Action Space：`Box`

连续动作空间：

```python
gym.spaces.Box(
    low,
    high,
    shape,
    dtype
)
```

例如：

```python
from gymnasium import spaces

space = spaces.Box(
    low=-1.0,
    high=1.0,
    shape=(3,),
    dtype=float
)
```

表示：

```text
a ∈ [-1, 1]^3
```

随机采样：

```python
action = space.sample()
```

可能得到：

```text
[ 0.32, -0.71, 0.15 ]
```

---

# 13. Action Space：`MultiDiscrete`

多个离散变量：

```python
space = gym.spaces.MultiDiscrete(
    [3, 4, 2]
)
```

表示：

```text
第一个变量：0,1,2
第二个变量：0,1,2,3
第三个变量：0,1
```

采样：

```python
action = space.sample()
```

---

# 14. Action Space：`MultiBinary`

多个二值变量：

```python
space = gym.spaces.MultiBinary(4)
```

可能采样：

```text
[0, 1, 1, 0]
```

---

# 15. Observation Space

查看观测空间：

```python
env.observation_space
```

例如 CartPole：

```python
env = gym.make("CartPole-v1")

print(env.observation_space)
```

通常为：

```text
Box(...)
```

---

# 16. Observation Space：`Box`

连续状态 / 观测：

```python
gym.spaces.Box(
    low,
    high,
    shape,
    dtype
)
```

例如：

```python
space = gym.spaces.Box(
    low=-1,
    high=1,
    shape=(4,),
    dtype=float
)
```

表示：

```text
s ∈ [-1,1]^4
```

---

# 17. Observation Space：`Discrete`

离散状态：

```python
space = gym.spaces.Discrete(10)
```

表示：

```text
s ∈ {0,1,...,9}
```

---

# 18. Observation Space：`Dict`

多个不同类型的观测组合：

```python
space = gym.spaces.Dict({
    "position": gym.spaces.Box(
        low=-1,
        high=1,
        shape=(2,)
    ),
    "velocity": gym.spaces.Box(
        low=-1,
        high=1,
        shape=(2,)
    )
})
```

观测可能是：

```python
{
    "position": [0.1, 0.2],
    "velocity": [0.3, 0.4]
}
```

---

# 19. Observation Space：`Tuple`

多个空间组成元组：

```python
space = gym.spaces.Tuple((
    gym.spaces.Discrete(5),
    gym.spaces.Box(
        low=-1,
        high=1,
        shape=(2,)
    )
))
```

---

# 20. 查看 Space 属性

对于 `Box`：

```python
env.observation_space.shape
```

例如：

```text
(4,)
```

查看上下界：

```python
env.observation_space.low
env.observation_space.high
```

查看数据类型：

```python
env.observation_space.dtype
```

---

# 21. Space 的随机采样

```python
space.sample()
```

例如：

```python
obs = env.observation_space.sample()
```

或者：

```python
action = env.action_space.sample()
```

---

# 22. Space 的合法性判断

```python
space.contains(x)
```

例如：

```python
action = env.action_space.sample()

env.action_space.contains(action)
```

---

# 23. 设置随机种子

## 环境种子

```python
obs, info = env.reset(seed=42)
```

---

## Action Space 种子

```python
env.action_space.seed(42)
```

之后：

```python
env.action_space.sample()
```

会按照该随机数生成器进行采样。

---

# 24. Render

Gymnasium 支持多种渲染模式。

创建环境时指定：

```python
env = gym.make(
    "CartPole-v1",
    render_mode="human"
)
```

---

# 25. `render_mode="human"`

直接显示环境：

```python
env = gym.make(
    "CartPole-v1",
    render_mode="human"
)
```

然后：

```python
obs, info = env.reset()

for _ in range(100):

    action = env.action_space.sample()

    obs, reward, terminated, truncated, info = env.step(action)

    if terminated or truncated:
        break

env.close()
```

---

# 26. `render_mode="rgb_array"`

返回图像：

```python
env = gym.make(
    "CartPole-v1",
    render_mode="rgb_array"
)
```

然后：

```python
obs, info = env.reset()

frame = env.render()

print(frame.shape)
```

通常得到：

```text
(height, width, 3)
```

可以使用 Matplotlib 显示：

```python
import matplotlib.pyplot as plt

plt.imshow(frame)
plt.show()
```

---

# 27. `render_mode="ansi"`

对于支持该模式的环境，可以得到文本形式的渲染结果：

```python
env = gym.make(
    "环境名称",
    render_mode="ansi"
)
```

---

# 28. `close()`

关闭环境：

```python
env.close()
```

程序结束时建议调用：

```python
env.close()
```

---

# 29. 查看环境信息

## `metadata`

```python
env.metadata
```

---

## `spec`

```python
env.spec
```

---

## 环境名称

```python
env.spec.id
```

---

# 30. Gymnasium Wrapper

Wrapper 用于在不直接修改原环境代码的情况下修改环境行为。

基本形式：

```python
wrapped_env = gym.wrappers.SomeWrapper(env)
```

例如：

```python
env = gym.make("CartPole-v1")

env = gym.wrappers.TimeLimit(
    env,
    max_episode_steps=100
)
```

---

# 31. Wrapper 的基本结构

```text
Agent
  ↓
Wrapper
  ↓
Environment
```

例如：

```text
Agent
  ↓
ObservationWrapper
  ↓
RewardWrapper
  ↓
Environment
```

Wrapper 可以修改：

```text
Action
Observation
Reward
reset()
step()
```

---

# 32. `ObservationWrapper`

用于修改 observation。

```python
class MyObservationWrapper(
    gym.ObservationWrapper
):

    def observation(self, observation):

        return observation * 2
```

使用：

```python
env = gym.make("CartPole-v1")

env = MyObservationWrapper(env)
```

---

# 33. `TransformObservation`

直接使用函数转换 observation：

```python
from gymnasium.wrappers import TransformObservation
```

例如：

```python
env = gym.make("CartPole-v1")

env = TransformObservation(
    env,
    lambda obs: obs * 2,
    env.observation_space
)
```

---

# 34. `FlattenObservation`

将复杂 observation 展平成一维数组：

```python
from gymnasium.wrappers import FlattenObservation

env = gym.make("环境名称")

env = FlattenObservation(env)
```

例如：

```text
原始 observation：

Dict
├── position
└── velocity

        ↓

FlattenObservation

        ↓

一维数组
```

---

# 35. `ActionWrapper`

用于修改 Action。

```python
class MyActionWrapper(
    gym.ActionWrapper
):

    def action(self, action):

        return action * 2
```

使用：

```python
env = MyActionWrapper(env)
```

---

# 36. `TransformAction`

使用函数修改 Action：

```python
from gymnasium.wrappers import TransformAction
```

例如：

```python
env = TransformAction(
    env,
    lambda action: action * 2,
    env.action_space
)
```

---

# 37. `ClipAction`

将 Action 限制在环境 Action Space 的范围内：

```python
from gymnasium.wrappers import ClipAction

env = gym.make("Hopper-v4")

env = ClipAction(env)
```

例如：

```python
action = [5, -5, 0]

env.step(action)
```

Wrapper 会将超出范围的 Action 截断到合法范围。

---

# 38. `RescaleAction`

重新缩放连续 Action：

```python
from gymnasium.wrappers import RescaleAction

env = gym.make("Hopper-v4")

env = RescaleAction(
    env,
    min_action=0,
    max_action=1
)
```

适用于：

```python
gym.spaces.Box
```

类型的 Action Space。

---

# 39. `RewardWrapper`

用于修改 reward：

```python
class MyRewardWrapper(
    gym.RewardWrapper
):

    def reward(self, reward):

        return reward * 2
```

使用：

```python
env = MyRewardWrapper(env)
```

---

# 40. Wrapper 获取底层环境

获取下一层环境：

```python
env.env
```

如果有多层 Wrapper：

```python
env.env.env
```

---

## `unwrapped`

直接获取最底层原始环境：

```python
env.unwrapped
```

例如：

```python
base_env = env.unwrapped
```

---

# 41. 查看当前 Wrapper

```python
print(env)
```

可以查看当前环境经过了哪些包装。

---

# 42. Episode 完整代码模板

```python
import gymnasium as gym

env = gym.make("CartPole-v1")

for episode in range(10):

    obs, info = env.reset()

    episode_reward = 0

    while True:

        action = env.action_space.sample()

        next_obs, reward, terminated, truncated, info = env.step(action)

        episode_reward += reward

        obs = next_obs

        if terminated or truncated:
            break

    print(
        f"Episode {episode}: "
        f"Reward = {episode_reward}"
    )

env.close()
```

---

# 43. Q-Learning 环境循环模板

```python
import gymnasium as gym

env = gym.make("CliffWalking-v1")

for episode in range(1000):

    state, info = env.reset()

    while True:

        # 根据 Q 表选择动作
        action = ...

        next_state, reward, terminated, truncated, info = env.step(action)

        # Q-learning 更新
        # Q[state, action] += ...

        state = next_state

        if terminated or truncated:
            break

env.close()
```

---

# 44. Sarsa 环境循环模板

```python
import gymnasium as gym

env = gym.make("CliffWalking-v1")

for episode in range(1000):

    state, info = env.reset()

    # 根据当前策略选择 A_t
    action = ...

    while True:

        next_state, reward, terminated, truncated, info = env.step(action)

        if terminated or truncated:

            # terminal 状态
            # Q[state, action] += ...

            break

        # 根据行为策略选择 A_{t+1}
        next_action = ...

        # Sarsa 更新
        # Q[state, action] += ...

        state = next_state
        action = next_action

env.close()
```

---

# 45. Gymnasium API 与强化学习对应关系

```text
强化学习
│
├── 状态 S_t
│      ↓
│   observation
│
├── 动作 A_t
│      ↓
│   action
│
├── 环境
│      ↓
│   env.step(action)
│
├── 下一状态 S_{t+1}
│      ↓
│   observation
│
├── 奖励 R_{t+1}
│      ↓
│   reward
│
└── Episode 是否结束
       ├── terminated
       └── truncated
```

因此：

```python
next_obs, reward, terminated, truncated, info = env.step(action)
```

可以理解为：

```text
(S_t, A_t)
      ↓
     环境
      ↓
(S_{t+1}, R_{t+1})
```

其中：

```python
next_obs
```

对应：

$$
S_{t+1}
$$

```python
reward
```

对应：

$$
R_{t+1}
$$

```python
terminated or truncated
```

表示当前 episode 是否结束。

---

# 46. 最常用 API 速查表

| API | 功能 |
|---|---|
| `gym.make()` | 创建环境 |
| `env.reset()` | 重置环境 |
| `env.step(action)` | 执行动作 |
| `env.render()` | 渲染环境 |
| `env.close()` | 关闭环境 |
| `env.action_space` | 动作空间 |
| `env.observation_space` | 观测空间 |
| `env.action_space.sample()` | 随机动作 |
| `env.action_space.contains()` | 判断动作是否合法 |
| `env.observation_space.contains()` | 判断观测是否合法 |
| `env.metadata` | 环境元信息 |
| `env.spec` | 环境规格 |
| `env.unwrapped` | 获取原始环境 |
| `gym.spaces.Discrete()` | 离散空间 |
| `gym.spaces.Box()` | 连续空间 |
| `gym.spaces.MultiDiscrete()` | 多离散空间 |
| `gym.spaces.MultiBinary()` | 多二值空间 |
| `gym.spaces.Dict()` | 字典空间 |
| `gym.spaces.Tuple()` | 元组空间 |
| `gym.Wrapper` | 基础 Wrapper |
| `gym.ObservationWrapper` | 修改 observation |
| `gym.ActionWrapper` | 修改 action |
| `gym.RewardWrapper` | 修改 reward |
| `FlattenObservation` | 展平 observation |
| `TransformObservation` | 转换 observation |
| `TransformAction` | 转换 action |
| `ClipAction` | 截断 action |
| `RescaleAction` | 缩放 action |

---

# 47. 最核心的 5 个 API

如果只记最重要的 Gymnasium API：

```python
env = gym.make("CartPole-v1")

obs, info = env.reset()

next_obs, reward, terminated, truncated, info = env.step(action)

env.render()

env.close()
```

其中强化学习算法最核心的是：

```python
obs, info = env.reset()

next_obs, reward, terminated, truncated, info = env.step(action)
```

即：

$$
S_t
\xrightarrow{A_t}
R_{t+1},S_{t+1}
$$

对应 Gymnasium：

```text
obs
 ↓
action
 ↓
step()
 ↓
next_obs + reward
```

---

# 48. Gymnasium 与旧版 Gym 的重要区别

旧版 Gym 常见写法：

```python
obs = env.reset()

obs, reward, done, info = env.step(action)
```

Gymnasium 新 API：

```python
obs, info = env.reset()

obs, reward, terminated, truncated, info = env.step(action)
```

不要再写：

```python
obs, reward, done, info = env.step(action)
```

对于新的 Gymnasium 代码，应使用：

```python
obs, reward, terminated, truncated, info = env.step(action)

done = terminated or truncated
```

---

# 49. 官方 API 分类

Gymnasium API 可以按照实际使用分成：

```text
Gymnasium
│
├── Environment
│   ├── make()
│   ├── reset()
│   ├── step()
│   ├── render()
│   └── close()
│
├── Spaces
│   ├── Discrete
│   ├── Box
│   ├── MultiDiscrete
│   ├── MultiBinary
│   ├── Dict
│   └── Tuple
│
├── Wrappers
│   ├── ObservationWrapper
│   ├── ActionWrapper
│   ├── RewardWrapper
│   ├── FlattenObservation
│   ├── TransformObservation
│   ├── TransformAction
│   ├── ClipAction
│   └── RescaleAction
│
└── Vector Environment
    └── 同时运行多个环境
```

> 注：Gymnasium 当前 API 还包含 Vector、Utils 等更完整的功能；上面主要整理强化学习学习阶段最常直接使用的部分。