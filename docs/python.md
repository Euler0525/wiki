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

