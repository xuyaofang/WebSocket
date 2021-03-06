# Nodejs-socket.io
socket.io 是一个为实时应用提供跨平台实时通信的库

## 安装
> npm install socket.io

## 客户端
```
<script src="/socket.io/socket.io.js"></script>   
//客户端引入socket.io
const socket = io.connect('http://localhost');
//与 http://localhost 本地服务器建立连接并赋值给 socket 对象
socket.emit('say', { my: 'data' }); 
socket.on('news', function (data) { console.log(data);})
```
## 服务端
```
const app = require('express')() , 
const http = require('http').createServer(app);
const io = require('socket.io').listen(http);
//将 socket.io 绑定到服务器上，于是任何连接到该服务器的客户端都具备了实时通信功能

io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('disconnect', ()=> {
        console.log('user disconnected');
    });
    socket.on('say', function (data) { 
        console.log(data);
        socket.emit('news',data);
    });
});
```
## 通信
#### 通过 socket.io 的核心函数 emit 和 on 就实现服务器与客户端之间的双向通信
- emit ：用来触发一个事件，参数（事件名，数据，回调（一般省略，如需对方接受到信息后立即得到确认时，则需要用到回调函数））
- on ：用来监听 emit 发射的事件，参数（事件名，回调（用来接收对方发来的数据）

#### socket.io 提供了三种默认的事件（客户端和服务器都有）
- connect ：当与对方建立连接后自动触发 connect 事件
- message ：当收到对方发来的数据后触发 message 事件（通常为 socket.send() 触发）
- disconnect ：当对方关闭连接后触发 disconnect 事件 
- 此外，socket.io 还支持自定义事件

#### 服务器端接收的三种情况
- socket.emit() ：向建立该连接的客户端广播
- socket.broadcast.emit() ：向除去建立该连接的客户端的所有客户端广播
- io.sockets.emit() ：向所有客户端广播，等同于上面两个的和

## socket.io 自带的事件
Socket.IO内置了一些默认事件，使用时应避免默认事件名称
#### 服务端事件
```
io.on("connect", (socket) => {
  // connect: socket连接时触发
})
io.on("connection", (socket) => {
  // connection: socket连接成功之后触发，用于初始化
  socket.on("message", (message, callback) => {
    // message: 客户端通过socket.send传输消息触发，message为消息，callback是收到消息后执行的回调
  });
  socket.on('anything', (data) => {
    // anything: 收到任何事件时触发
  })
  socket.on("disconnect", () => {
    // disconnect: socket失去连接时触发（刷新浏览器，关闭浏览器，服务端断开，掉线等）
  });
});
```
*connect和connection事件功能相似,但是被触发的时间不同.connect先于connetion,connect是一旦有连接就被触发,而connection在连接完全建立后才被触发*

*因此直接使用connection事件即可*

#### 客户端事件：
- connecting: 正在连接
- connect: 连接成功
- message: 接收send消息
- anything: 任何事件
- disconnect: 断开连接
- connect_failed: 连接失败
- error: 错误发生，并且无法被其他事件类型所处理

- reconnecting: 正在重连
- reconnect: 重连成功
- reconnect_failed: 重连失败

##### 断开连接 ->重连成功：
* disconnect->reconnecting（可能进行多次）->connecting->reconnect->connect。
