+++
title = 'FastAPI Basics and the HTTP Protocol'
date = "2025-05-08T20:00:00+08:00"
draft = false
comments = false
ShowToc = true
categories = ['Tech Notes', 'Backend']
tags = ['Python', 'FastAPI', 'HTTP']
+++

## Quickstart
For example:
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

1. Create a FastAPI instance:
```python
app = FastAPI()
```
This creates an instance of a FastAPI application, which is used to define and manage the application's components, including routes. `FastAPI` is the framework's main class.

2. Define a route operation for the root path `/`:
```python
@app.get("/")
def read_root():
    return {"Hello": "World"}
```
The `@app.get("/")` decorator means that `read_root` runs when a user sends a `GET` request to the root path. The function returns a dictionary containing `{"Hello": "World"}`, which FastAPI automatically converts to JSON and returns to the user.

3. Define a route operation with a path parameter and a query parameter:
```python
@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}
```
The `@app.get("/items/{item_id}")` decorator means that **the `read_item` function runs** when a user sends a `GET` request to `/items/{item_id}`.
The function accepts two parameters:
- `item_id`: a path parameter specified as an integer.
- `q`: a query parameter specified as a string or empty (`None`).
The function returns a dictionary containing the supplied `item_id` and `q` values.

## Two core components
### Pydantic
Responsible for data validation, such as checking whether incoming or outgoing data has the expected types.

## Four characteristics of the HTTP protocol
HTTP is an application-layer protocol based on TCP/IP. **The two communicating parties are the client (browser) and the server.**
![](/post/fastapi-basics/Pasted_image_20240923162644.png)

### HTTP characteristics
#### Request-response model
The protocol specifies that a request is sent by the client, and the server receives it and returns a response. Communication is initiated by the client; the server does not send a response before receiving a request.
Unlike sockets, which support full-duplex communication and allow either the client or server to send first, HTTP is request-response. The WebSocket protocol was developed to support bidirectional communication and let a server push messages to a client.
An HTTP request must have a response, even when the request is erroneous; an error response must still be returned.

#### Statelessness
After the client sends a request and the server responds, the server does not retain a record related to that request. In many situations, however, we need such a record. For example, after logging in to a website once, we may want to visit it later without logging in again. HTTP alone cannot provide this, so a `cookie-session` mechanism is needed.

#### Short-lived connections
1. HTTP 1.0 uses short-lived connections by default. The browser establishes a connection for each HTTP operation and closes it when the operation is done.
2. Since HTTP/1.1, persistent connections are used by default. To use one, the `Connection` header on both the client and server must be set to `keep-alive`.
An HTTP persistent connection reuses a TCP connection for multiple HTTP requests, reducing the overhead of establishing and closing TCP connections.

### HTTP message format
#### Request format
```bash
POST /api/v1/auth/password/login/?loginWay=password HTTP/1.1 # request line
content-type: application/json # request header
user-agent: Chrome/104.0.0.0 Safari/537.36 # request header
# blank line
{username: "user", password: "123"} # request body
```
The difference between `POST` and `GET` is where the **data portion** of the request is placed:
- A `GET` request places data after the path in the request line as query parameters and has no request body.
- A `POST` request has a request body and places the data there.
A login requires a password. Since `GET` puts data in the path, which is visible in the address bar, it is not secure. For this reason, login uses `POST`; although the password can still be seen, it can be further encrypted.

#### Response format
```bash
HTTP/1.1 200 OK
content-type: application/json
date:  Sun, 28 Aug 2022 13:43:25 GMT
content-length: 73
# blank line
{
	"code": -1,
	"msg": "Validation error",
	"data": {
		"global_error": ["Incorrect password"]
	}
}
```
