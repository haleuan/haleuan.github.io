+++
title = 'FastAPI 入门与 HTTP 协议基础'
date = 2026-06-03T20:00:00+08:00
draft = false
comments = false
ShowToc = true
categories = ['技术折腾', '后端']
tags = ['Python', 'FastAPI', 'HTTP']
+++

## Quickstart
e.g
```python
from typing import Union
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}
```
1. 创建 FastAPI 实例：
```python
app = FastAPI()
```
在这一步，创建了一个 FastAPI 应用的实例，它将用于定义和管理应用的各个组件，包括路由。`FastAPI` 是FastAPI框架的主要类。
2. 定义根路径 `/` 的路由操作
```python
@app.get("/")
def read_root():
    return {"Hello": "World"}
```
这个路由操作使用了 `@app.get("/")` 装饰器，表示当用户通过 `GET` 请求访问根路径时，将执行 `read_root` 函数。函数返回一个包含 `{"Hello": "World"}` 的字典，这个字典会被 `FastAPI` 自动转换为JSON格式并返回给用户。
3. 定义带路径参数和查询参数的路由操作
```python
@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}
```
这个路由操作使用了 `@app.get("/items/{item_id}")` 装饰器，表示当用户通过 `GET` 请求访问 `/items/{item_id}` 路径时，**将执行 `read_item` 函数**。
函数接受两个参数：
- `item_id`: 路径参数，指定为整数类型。
- `q`: 查询参数，指定为字符串类型或空（None）。
函数返回一个字典，包含传入的 item_id 和 q 参数。

## 两个核心组件
### Pydantic
负责数据校验，例如传入或者传出的数据类型是否符合要求

## HTTP协议的4大特点
HTTP是基于TCP/IP的一个上层协议，**通信双方是client(浏览器)和server**。
![](/post/fastapi-basics/Pasted_image_20240923162644.png)
### HTTP协议的特性
#### 基于请求-响应模式
协议规定，请求从client发出，server接收到请求并返回响应。也就是说，肯定是从client开始建立通信的，server在没有接收到请求之前，是不会发送响应的。
不同于socket是全双工通信，socket协议中client和server谁先给谁发都是可以的。所以对应HTTP协议的这一特点，还衍生出了一个WebSocket协议，WebSocket协议是双向的，能够让server给client推送消息。
同时，HTTP有请求也必须有响应，即使请求错误，也必须返回报错响应
#### 无状态保存
当client给server发送请求，server根据请求作出响应后，server并不对此请求作任何相关记录。但实际上在很多场景下我们是需要这种记录的，比如在某个网站上登陆过一次后，我们后续访问该网站就不需要再次登陆，这一点光凭HTTP协议是无法做到的，所以这也就需要`cookie-session`机制。

#### 短连接
1. HTTP1.0默认使用的是短连接。浏览器和服务器每进行一次HTTP操作，就建立一次连接，任务结束就中断连接。
2. HTTP/1.1起，默认使用长连接。要使用长连接，客户端和服务器的HTTP首部的Connection都要设置为keep-adive，才能支持长连接
HTTP长连接，指的是复用TCP连接。多个HTTP请求可以复用同一个TCP连接，这就节省了TCP连接建立和断开的消耗。

### HTTP协议格式
#### 请求格式
```bash
POST /api/v1/auth/password/login/?loginWay=password HTTP/1.1 # 请求首行
content-type: application/json # 请求头
user-agent: Chrome/104.0.0.0 Safari/537.36 # 请求头
# 空行
{username: "user", password: "123"} # 请求体
```
`POST`请求和`GET`请求的区别在于，发送请求时的**数据部分**放在什么的位置上：
- `GET`请求会将数据放在请求首行中的路径后面，作为一个请求参数，并没有请求体。
- `POST`请求会有一个请求体，讲数据放在请求体中
在登陆的时候需要输入密码，由于`GET`只能将数据放在路径当中，而路径是明文放在地址栏中的，所以并不安全，登陆时也就因此选用`POST`方法，虽然`POST`方法也可以看到密码，但是可以进一步进行加密处理。
#### 响应协议
```bash
HTTP/1.1 200 OK
content-type: application/json
date:  Sun, 28 Aug 2022 13:43:25 GMT
content-length: 73
# 空行
{
	"code": -1, 
	"msg": "校验错误",
	"data": {
		"global_error": ["密码错误"]
	}
}
```
