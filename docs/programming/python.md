# Python

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
