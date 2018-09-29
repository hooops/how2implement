# 微信的语音段传输

## 原理

* 使用 webRTC 技术获取麦克风设备和录制音频
* 使用 webSocket 技术传输音频流
* 备注：webRTC与webSocket的区别
* * WebSockets允许浏览器和Web服务器之间的全双工通信
  * WebRTC的PeerConnection允许两个浏览器之间的全双工通信

## webRTC的使用

### 获取麦克风/摄像头设备

```javascript
// 获取媒体设备
navigator.mediaDevices.getUserMedia(constraints)
.then(function(stream) {
  /* 使用这个stream stream */
})
.catch(function(err) {
  /* 处理error */
});

// constrains配置参数
var constrains = {
  // 获取麦克风
  audio: true,
  // 获取摄像头
  video: {
    width: { min: 1024, ideal: 1280, max: 1920 },
    height: { min: 776, ideal: 720, max: 1080 },
    // 优先前置摄像头
    facingMode: "user",
    // 强制后置摄像头
    facingMode: { exact: "environment" },
    // 帧率
    frameRate: { ideal: 10, max: 15 }
  }
}
```

### 适配旧的浏览器

```javascript
// 老的浏览器可能根本没有实现 mediaDevices，所以我们可以先设置一个空的对象
if (navigator.mediaDevices === undefined) {
  navigator.mediaDevices = {};
}

// 一些浏览器部分支持 mediaDevices。我们不能直接给对象设置 getUserMedia 
// 因为这样可能会覆盖已有的属性。这里我们只会在没有getUserMedia属性的时候添加它。
if (navigator.mediaDevices.getUserMedia === undefined) {
  navigator.mediaDevices.getUserMedia = function(constraints) {

    // 首先，如果有getUserMedia的话，就获得它
    var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

    // 一些浏览器根本没实现它 - 那么就返回一个error到promise的reject来保持一个统一的接口
    if (!getUserMedia) {
      return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
    }

    // 否则，为老的navigator.getUserMedia方法包裹一个Promise
    return new Promise(function(resolve, reject) {
      getUserMedia.call(navigator, constraints, resolve, reject);
    });
  }
}

navigator.mediaDevices.getUserMedia({ audio: true, video: true })
.then(function(stream) {
  var video = document.querySelector('video');
  // 旧的浏览器可能没有srcObject
  if ("srcObject" in video) {
    video.srcObject = stream;
  } else {
    // 防止再新的浏览器里使用它，应为它已经不再支持了
    video.src = window.URL.createObjectURL(stream);
  }
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) {
  console.log(err.name + ": " + err.message);
});
```

## webSocket的使用

```javascript
// 创建websocket连接
const socket = new WebSocket('ws://localhost:8080');

// 连接建立的时候触发
socket.addEventListener('open', function (event) {
    socket.send('Hello Server!');
});

// 接受到消息的时候触发
socket.addEventListener('message', function (event) {
    console.log('Message from server', event.data);
});

// websocket对象的属性
binaryType // 一个字符串表示被传输二进制的内容的类型。取值应当是"blob"或者"arraybuffer"。
onclose    // 连接关闭的时候触发
onerror    // 连接出错的时候触发
onmessage  // 连接收到消息的时候触发
onopen     // 连接建立的时候触发
readyState 	
```

## Github地址

* [https://github.com/ns2250225/audioRecord](https://github.com/ns2250225/audioRecord)

## 效果展示

![](../.gitbook/assets/1%20%281%29.png)

