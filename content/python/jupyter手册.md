# Jupyter / IPython 命令手册

## 1. Magic 命令

> Magic 命令以 `%` 开头，由 IPython 提供。

---

### `%time`

执行一次代码并统计运行时间。

```python
%time sum(range(1000000))
```

---

### `%timeit`

多次执行代码并统计平均运行时间。

```python
%timeit sum(range(1000))
```

适合比较不同代码的运行效率。

---

### `%run`

运行 Python 文件。

```python
%run test.py
```

带参数：

```python
%run test.py arg1 arg2
```

---

### `%load`

将 Python 文件内容加载到当前 Cell。

```python
%load test.py
```

也可以加载网络资源：

```python
%load https://example.com/test.py
```

---

### `%save`

将当前 Cell 保存为 Python 文件。

```python
%save test.py
```

保存指定 Cell：

```python
%save test.py 1-5
```

---

### `%history`

查看输入历史。

```python
%history
```

查看最近几条：

```python
%history ~1/1
```

查看当前 Notebook 的历史：

```python
%history
```

---

### `%rerun`

重新执行之前的代码。

```python
%rerun
```

重新执行指定历史：

```python
%rerun 1-5
```

---

### `%recall`

取出之前执行过的代码，但不执行。

```python
%recall 1
```

---

## 2. 文件与目录

### `%pwd`

查看当前工作目录。

```python
%pwd
```

---

### `%cd`

切换工作目录。

```python
%cd /Users/faust/code/rl_learn
```

切换到上一级：

```python
%cd ..
```

---

### `%ls`

查看当前目录。

```python
%ls
```

---

### `%ll`

以详细格式查看当前目录。

```python
%ll
```

---

### `%mkdir`

创建目录。

```python
%mkdir test
```

---

### `%rm`

删除文件。

```python
%rm test.py
```

删除目录：

```python
%rm -r test
```

> 注意：删除操作通常无法恢复。

---

## 3. 查看变量

### `%who`

查看当前定义的变量。

```python
%who
```

---

### `%whos`

查看变量的详细信息。

```python
%whos
```

例如：

```text
Variable   Type      Data/Info
--------------------------------
x          int       10
y          list      [1, 2, 3]
```

---

### `%reset`

删除当前环境中的变量。

```python
%reset
```

执行前会询问是否确认。

---

### `%reset -f`

强制删除变量，不询问。

```python
%reset -f
```

---

## 4. 查看帮助

### `?`

查看对象或函数的帮助信息。

```python
len?
```

```python
np.array?
```

---

### `??`

查看更加详细的信息，通常会尝试显示源代码。

```python
np.array??
```

---

### `%pinfo`

查看对象信息。

```python
%pinfo np.array
```

等价于：

```python
np.array?
```

---

### `%pdoc`

查看对象的文档。

```python
%pdoc np.array
```

---

### `%psource`

查看对象的源代码。

```python
%psource function_name
```

---

### `%pfile`

查看定义对象的源文件。

```python
%pfile function_name
```

---

### `%history`

查看历史输入：

```python
%history
```

---

## 5. Shell 命令

> 在 Cell 中使用 `!` 可以执行操作系统 Shell 命令。

---

### 查看目录

```python
!ls
```

详细查看：

```python
!ls -lh
```

---

### 查看当前目录

```python
!pwd
```

---

### 查看 Python 版本

```python
!python --version
```

---

### 查看 pip

```python
!pip --version
```

---

### 查看已安装包

```python
!pip list
```

---

### 安装 Python 包

```python
!pip install numpy
```

---

### 升级 Python 包

```python
!pip install -U numpy
```

---

### 卸载 Python 包

```python
!pip uninstall numpy
```

---

### 执行其他 Python 文件

```python
!python test.py
```

---

### 查看文件内容

```python
!cat test.py
```

---

### 创建目录

```python
!mkdir test
```

---

### 删除文件

```python
!rm test.py
```

---

### 删除目录

```python
!rm -r test
```

---

## 6. Python 环境信息

### 查看 Python 解释器位置

```python
import sys

sys.executable
```

或者：

```python
!which python
```

---

### 查看 Python 版本

```python
import sys

sys.version
```

或者：

```python
!python --version
```

---

### 查看 Python 模块搜索路径

```python
import sys

sys.path
```

---

### 查看环境变量

```python
import os

os.environ
```

查看某个环境变量：

```python
import os

os.environ["PATH"]
```

---

## 7. 安装与管理 Python 包

### 安装包

```python
%pip install numpy
```

推荐在 Jupyter 中使用：

```python
%pip install numpy
```

而不是：

```python
!pip install numpy
```

因为 `%pip` 会针对当前 Notebook 使用的 Python 环境执行安装。

---

### 升级包

```python
%pip install -U numpy
```

---

### 卸载包

```python
%pip uninstall numpy
```

---

### 查看已安装包

```python
%pip list
```

---

### 查看某个包

```python
%pip show numpy
```

---

### 安装指定版本

```python
%pip install numpy==2.0.0
```

---

### 安装 requirements.txt

```python
%pip install -r requirements.txt
```

---

## 8. Conda

如果当前环境使用 Conda，可以：

### 查看 Conda 环境

```python
!conda env list
```

---

### 查看 Conda 版本

```python
!conda --version
```

---

### 安装 Conda 包

```python
%conda install numpy
```

---

### 查看 Conda 包

```python
%conda list
```

---

## 9. 多行 Magic 命令

部分 Magic 命令可以使用两个 `%`：

```python
%%time
for i in range(1000000):
    x = i ** 2
```

这里：

```text
%time
```

只作用于当前这一行。

而：

```text
%%time
```

作用于整个 Cell。

---

### `%%timeit`

对整个 Cell 进行性能测试：

```python
%%timeit

x = 0

for i in range(1000):
    x += i
```

---

### `%%writefile`

将整个 Cell 写入文件：

```python
%%writefile test.py

print("Hello")

x = 10

print(x)
```

执行后生成：

```text
test.py
```

---

### `%%capture`

捕获 Cell 的输出：

```python
%%capture

print("Hello")
print("World")
```

---

### `%%bash`

使用 Bash 执行整个 Cell：

```bash
%%bash

echo "Hello"
ls
pwd
```

---

### `%%python`

使用 Python 执行整个 Cell：

```python
%%python

x = 10
print(x)
```

---

## 10. 显示与输出

### `display()`

比 `print()` 更适合 Notebook 中显示对象：

```python
from IPython.display import display

display(x)
```

---

### 显示 HTML

```python
from IPython.display import HTML

HTML("<h1>Hello</h1>")
```

---

### 显示 Markdown

```python
from IPython.display import Markdown

display(Markdown("# Hello"))
```

---

### 显示图片

```python
from IPython.display import Image

display(Image("test.png"))
```

---

### 显示视频

```python
from IPython.display import Video

display(Video("test.mp4"))
```

---

## 11. Notebook 中的 LaTeX

Markdown Cell 中：

```markdown
$$
E = mc^2
$$
```

行内公式：

```markdown
$E=mc^2$
```

例如：

```markdown
贝尔曼方程：

$$
V^\pi(s)
=
\sum_a \pi(a|s)
\sum_{s',r}
p(s',r|s,a)
\left[
r+\gamma V^\pi(s')
\right]
$$
```

---

## 12. Notebook 中的快捷键

### Command Mode

按：

```text
Esc
```

进入 Command Mode。

---

### Edit Mode

按：

```text
Enter
```

进入 Edit Mode。

---

### 运行 Cell

```text
Shift + Enter
```

运行当前 Cell，并跳到下一个 Cell。

---

### 运行 Cell，不跳转

```text
Ctrl + Enter
```

---

### 运行并创建下一个 Cell

```text
Alt + Enter
```

---

### 添加 Cell

Command Mode 下：

```text
A
```

在上方添加 Cell。

```text
B
```

在下方添加 Cell。

---

### 删除 Cell

Command Mode 下：

```text
D
D
```

连续按两次 `D`。

---

### 修改 Cell 类型

Markdown：

```text
M
```

Code：

```text
Y
```

Raw：

```text
R
```

---

### 保存

```text
Ctrl + S
```

Mac：

```text
Cmd + S
```

---

### 中断代码执行

```text
I
I
```

连续按两次 `I`。

---

### 重启 Kernel

```text
0
0
```

连续按两次 `0`。

---

## 13. 常用 Python 辅助工具

### 查看对象类型

```python
type(x)
```

---

### 查看对象属性和方法

```python
dir(x)
```

---

### 查看对象长度

```python
len(x)
```

---

### 查看对象字符串表示

```python
str(x)
```

---

### 查看对象详细表示

```python
repr(x)
```

---

### 查看对象是否存在

```python
"x" in globals()
```

---

## 14. 性能分析

### `%time`

```python
%time function()
```

适合简单测试一次运行时间。

---

### `%timeit`

```python
%timeit function()
```

适合比较代码性能。

---

### `%prun`

分析函数内部的运行时间：

```python
%prun function()
```

---

### `%memit`

如果安装了 `memory_profiler`，可以测量内存：

```python
%load_ext memory_profiler
```

然后：

```python
%memit function()
```

---

## 15. 调试

### `%debug`

发生异常后：

```python
%debug
```

进入调试环境。

---

### `%pdb`

自动进入调试器：

```python
%pdb on
```

关闭：

```python
%pdb off
```

---

## 16. 最常用命令速查

| 命令 | 功能 |
|---|---|
| `%time` | 测量一次运行时间 |
| `%timeit` | 多次测量运行时间 |
| `%run` | 运行 Python 文件 |
| `%load` | 加载 Python 文件到 Cell |
| `%save` | 保存 Cell 为 Python 文件 |
| `%pwd` | 查看当前目录 |
| `%cd` | 切换目录 |
| `%ls` | 查看目录 |
| `%who` | 查看变量 |
| `%whos` | 查看变量详细信息 |
| `%reset` | 删除变量 |
| `%history` | 查看历史代码 |
| `%rerun` | 重新运行历史代码 |
| `%recall` | 调出历史代码 |
| `%pip` | 管理 Python 包 |
| `%conda` | 管理 Conda 包 |
| `%debug` | 调试异常 |
| `%prun` | 性能分析 |
| `?` | 查看帮助 |
| `??` | 查看详细帮助/源代码 |
| `!ls` | 执行 Shell 命令 |
| `!pwd` | 查看 Shell 当前目录 |
| `!python` | 执行 Python |
| `%%time` | 测量整个 Cell |
| `%%timeit` | 测试整个 Cell |
| `%%writefile` | 将 Cell 写入文件 |
| `%%bash` | 用 Bash 执行 Cell |
| `%%capture` | 捕获 Cell 输出 |

---

# 17. 最值得记住的几类

日常使用 Jupyter 时，最常用的是：

```text
代码执行
Shift + Enter
Ctrl + Enter
Alt + Enter

目录
%pwd
%cd
%ls

变量
%who
%whos
%reset

帮助
?
??

性能
%time
%timeit
%prun

文件
%run
%load
%save
%%writefile

Python 包
%pip install
%pip list
%pip show

Shell
!ls
!pwd
!python
!pip
```