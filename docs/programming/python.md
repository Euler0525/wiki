# Python

## Jupyter

使用 `%lsmagic` 可以 **列出所有可用的魔法命令**。使用 `%magic` 可以查看详细的帮助文档。

| 类别               | 魔法命令                  | 类型       | 使用方法与示例                                               | 使用效果                                                     |
| :----------------- | :------------------------ | :--------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **系统与文件操作** | `%run`                    | 行魔法     | `%run ./scripts/data_preprocess.py`                          | **运行外部 Python 脚本**，其定义的函数、变量等会载入当前 Notebook 环境，便于模块化开发。 |
|                    | `%ls` / `%dir`            | 行魔法     | `%ls ../data/` 或 `%dir ../data/`                            | **列出指定目录** 下的文件和文件夹（`%ls` 为 Unix 风格，`%dir` 为 Windows 风格）。 |
|                    | `%pwd`                    | 行魔法     | `%pwd`                                                       | **打印当前工作目录** 的绝对路径。                             |
| **代码执行与调试** | `%time` / `%timeit`       | 行魔法     | `%time sum(range(1000000))`<br>`%timeit sum(range(1000000))` | **测量代码执行时间**。`%time` 测单次，`%timeit` 自动多次测量取平均，更准确，适合微小代码片段。 |
|                    | `%%time` / `%%timeit`     | 单元格魔法 | 在单元格首行写 `%%time` 或 `%%timeit`                           | **测量整个单元格代码** 的执行时间。                           |
|                    | `%debug`                  | 行魔法     | 在代码报错后，于新单元格输入 `%debug`                         | **启动交互式调试器（pdb）**，进入发生异常的环境，可逐行检查变量，是事后调试的神器。 |
|                    | `%%writefile`             | 单元格魔法 | `%%writefile hello.py`<br>`print("Hello World")`             | **将单元格内容写入指定文件**，常用于快速创建脚本或保存代码片段。 |
| **环境与包管理**   | `%env`                    | 行魔法     | `%env` 或 `%env PATH`                                        | **不带参数时列出所有环境变量**；带参数（如 `PATH`）则获取或设置（`%env VAR=value`）特定环境变量。 |
|                    | `%pip` / `%conda`         | 行魔法     | `%pip install numpy` 或 `%conda install numpy`               | **在 Notebook 内部直接安装 Python 包**，无需退出切换到命令行，非常方便。 |
| **可视化与展示**   | `%matplotlib inline`      | 行魔法     | `%matplotlib inline`                                         | **设置 Matplotlib 图表在单元格内直接显示**，而非弹出窗口，是数据可视化的标配命令。 |
|                    | `%config`                 | 行魔法     | `%config InlineBackend.figure_format = 'svg'`                | **动态配置后端行为**，例如将内嵌图表格式从 PNG 改为更清晰的 SVG。 |
| **其他实用工具**   | `%history`                | 行魔法     | `%history -n 10-20`                                          | **打印历史命令**。可用参数限定范围（如最近 10-20 条代码），方便回顾或导出代码。 |
|                    | `%who` / `%whos`          | 行魔法     | `%who` 或 `%whos`                                            | **列出当前交互命名空间中所有变量**。`%who` 只列名称，`%whos` 额外显示类型、值等详细信息。 |
|                    | `%%html` / `%%javascript` | 单元格魔法 | `%%html`<br>`<h1 style='color:blue'>标题</h1>`               | **将单元格内容直接解析为 HTML 或 JavaScript 代码并执行**，用于嵌入富媒体或交互组件。 |
|                    | `%%latex`                 | 单元格魔法 | `%%latex`<br>`$E = mc^2$`                                    | **将单元格内容渲染为 LaTeX 公式**，用于编写数学表达式或报告。  |

## Plot

> [Visualize-ML/Book1_Python-For-Beginners/Book1_Ch11](https://github.com/Visualize-ML/Book1_Python-For-Beginners/tree/main/Book1_Ch11_Python_Codes)

- `plotly.express.plot()`

```python
import numpy as np
import pandas as pd
import plotly.express as px

x = np.linspace(0, 2 * np.pi, 100)
y_sin = np.sin(x)
y_cos = np.cos(x)

fig = px.line(x=x, y=[y_sin, y_cos], labels={'y': 'f(x)', 'x': 'x'})
fig.data[0].name = 'Sine'
fig.data[1].name = 'Cosine'
fig.show()

df = pd.DataFrame({'x': x, 'Sine': y_sin, 'Cosine': y_cos})
fig = px.line(df, x='x', y=['Sine', 'Cosine'], labels={'value': 'f(x)', 'X': 'x'})
fig.show()
```

## Socket Programming

- `server`

```python
import socket
import threading


def serverSocket(host, port):
    socket_server = socket.socket()
    socket_server.bind((host, port))
    socket_server.listen(5)
    print(f"Listening...")
    num = 0
    while True:
        num += 1
        conn, addr = socket_server.accept()
        print(f"{num}: Received a message from client {addr}")
        client_handler = threading.Thread(
            target=handleClient, args=(conn, addr, num))
        client_handler.start()


def handleClient(conn, addr, num):
    while True:
        data_from_client: str = conn.recv(1024).decode("UTF-8")
        print(f"{num}: The message from {addr} is {data_from_client}.")
        msg = input(f"{num}: Enter the message replied to client {addr}: ")
        if msg == 'exit':
            break
        conn.send(msg.encode("UTF-8"))
    conn.close()


if __name__ == '__main__':
    server_host = input("Please enter server IP: ")
    server_port = int(input("Please enter server port: "))
    serverSocket(server_host, server_port)

```

- `client`

```python
import socket


def create_client(host, port):
    socket_client = socket.socket()
    socket_client.connect((host, port))
    while True:
        msg = input(f"Enter the message sent to server ({host}:{port}): ")
        if msg == "exit":
            break
        socket_client.send(msg.encode("UTF-8"))
        response = socket_client.recv(1024).decode("UTF-8")
        print(f"The response from server ({host}:{port}) is {response}")
    socket_client.close()


if __name__ == '__main__':
    server_host = input("Please enter server IP: ")
    server_port = int(input("Please enter server port: "))
    create_client(server_host, server_port)

```

## List

> Only the outermost for-expression is evaluated immediately, the other expressions are deferred until the generator is run.  ——PEP 289

```python
old_list = [1, 2, 3]
new_list = (i for i in old_list if i in old_list)
old_list = [0, 1, 2]
print(list(new_list))

# old_list1 = [1, 2, 3]
# old_list2 = [0, 1, 2]
# new_list = (i for i in old_list1 if i in old_list2)
# print(list(new_list))

# old_list = [1, 2, 3]
#
# def __gen(a):
#     for i in a:
#         if i in old_list:
#             yield i
#
# g = __gen(iter(old_list))
# old_list = [0, 1, 2]
# print(list(g))
```

---

## Not Bug

- Normal Use

```python
config = {"KEY": "admin"}

class User(object):
    def __init__(self, name):
        self.name = name


if __name__ == "__main__":
    user = User("Euler0525")
    s = "Hello {}!".format(user.name)
    print(s)

# Hello Euler0525!
```

- Malicious Use

```python
config = {"KEY": "admin"}

class User(object):
    def __init__(self, name):
        self.name = name


if __name__ == "__main__":
    user = User("Euler0525")
    s = "Hello {}!".format(user.__class__.__init__.__globals__)
    print(s)

# Hello {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7fdd2d6a10f0>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': '/root/tmp/main.py', '__cached__': None, 'config': {'KEY': 'admin'}, 'User': <class '__main__.User'>, 'user': <__main__.User object at 0x7fdd2d6db5b0>}!
```

---

### Serialization And Deserialization

- Normal Use

```python
import pickle

data = ["aa", "bb", "cc"]
p = pickle.dumps(data)
print(p)
# b'\x80\x04\x95\x14\x00\x00\x00\x00\x00\x00\x00]\x94(\x8c\x02aa\x94\x8c\x02bb\x94\x8c\x02cc\x94e.'

d = pickle.loads(p)
print(d)
# ['aa', 'bb', 'cc']
```

- Malicious Use

The `__reduce__` method in the Python class (the return value is a tuple) is automatically called when deserialization occurs.

```python
class A(object):
    def __reduce__(self):
        return (os.system,("whoami",))

a = A()
p = pickle.dumps(a)
pickle.loads(p)
# root
```

---

```python
# Default argument value is mutable
class Player(object):
    def __init__(self, name: str, assets=None):
        if assets is None:
            assets = []
        self.name = name
        self.assets = assets


# class Player(object):
#     def __init__(self, name: str, assets=[]):
#         self.name = name
#         self.assets = assets
```

---

- 列表扩展

```python
l = []
for i in range(5):
    l.append(i + 1)
    print(l)
    print(l.__sizeof__())
    print("********************")

l1 = [1, 2, 3, 4]
print(l.__sizeof__())


[1]
72
********************
[1, 2]
72
********************
[1, 2, 3]
72
********************
[1, 2, 3, 4]
72
********************
[1, 2, 3, 4, 5]
104
********************
104
```

---

- 子句的执行顺序

```python
arr = [1, 8, 15]
g = (x for x in arr if arr.count(x) > 0)
arr = [2, 8, 22]
print(list(g))  # [8]
```

在生成表达式中，`in` 在声明时执行，条件子句在运行时执行。

```python
arr_1 = [1, 2, 3, 4]
g1 = (x for x in arr_1)
arr_1 = [1, 2, 3, 4, 5]
arr_2 = [1, 2, 3, 4]
g2 = (x for x in arr_2)
arr_2[:] = [1, 2, 3, 4, 5]
print(list(g1))  # [1, 2, 3, 4]
print(list(g2))  # [1, 2, 3, 4, 5]
```

1. `arr_1` 被绑定到新对象 `[1, 2, 3, 4, 5]`，但 `in` 在声明时执行，所以仍然引用旧对象；
2. `arr_2` 用切片将旧对象原地更新为 `[1, 2, 3, 4, 5]`，因此 `g2` 与 `arr_2` 引用同一个对象；
