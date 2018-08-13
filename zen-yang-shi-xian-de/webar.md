# WebAR

## 原理

* **调用AR.js和A-frame库来实现识别Marker图像的位置，并在位置上绘制图形或动画**

## 实现代码

```javascript
<!doctype HTML>
<html>
<!-- include A-Frame obviously -->
<script src="/static/js/aframe.min.js"></script>
<!-- include ar.js for A-Frame -->
<script src="/static/js/aframe-ar.js"></script>
<!-- include A-Frame-Extras -->
<script src="/static/js/aframe-extras.min.js"></script>

<body style='margin : 0px; overflow: hidden;'>
  <a-scene embedded arjs>
    <!-- 加载 gltf 模型 和 自定义的标记 Marker 图像 -->
    <a-marker preset='custom' type='pattern' url='/static/marker/pattern-marker.patt'>
      <a-entity gltf-model="/static/gltf/scene.gltf" scale="1.5 1.5 1.5" rotation="-160 0 0" position="0 0.5 0">
      </a-entity>
    </a-marker>
    
    <!-- 添加摄像头 -->
    <a-entity camera></a-entity>
  </a-scene>
</body>
</html>
```

## 备注

* 寻找GLTF模型的地方：[https://sketchfab.com/feed\#](https://sketchfab.com/feed#)

## Github地址

* [https://github.com/ns2250225/ns2250225.github.io](https://github.com/ns2250225/ns2250225.github.io)

## 效果展示

![](../.gitbook/assets/marker.png)

![](../.gitbook/assets/screenshot.png)

