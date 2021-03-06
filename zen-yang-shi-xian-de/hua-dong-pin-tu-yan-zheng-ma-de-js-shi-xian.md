# 滑动拼图验证码的JS实现

## 原理

* 后端随机生成拼图滑块和拼图缺口的X坐标，Y坐标，传给前端显示
* 前端拖动拼图滑块，把滑块最终停留的X坐标传给后端
* 后端判断，拼图滑块的X坐标与拼图缺口的X坐标的差值是否小于阈值

## 代码实现

### HTML

```text
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>滑动验证</title>
    <link rel="stylesheet" href="css/index.css">
</head>

<body>
    <div class="box">
        <div class="imgBox">
            <!-- 拼图滑块 -->
            <div class="verify"></div>
            <!-- 拼图缺口 -->
            <div class="verified"></div>
        </div>
        <div class="handle">
            <span class="swiper"></span>
            <span class="text">拖动滑块</span>
        </div>
        <div class="refreshBox">
            <span class="refresh">点击切换图片</span>
        </div>
    </div>
    <script src='js/jquery-3.1.1.min.js'></script>
    <script src='js/index.js'></script>
</body>

</html>
```

### JS

```javascript
var box = $('.box'),
  imgBox = $('.imgBox'),
  handle = $('.handle'),
  swiper = $('.swiper'),
  text = $('.text'),
  verify = $('.verify'),
  verified = $('.verified'),
  refresh = $('.refresh')

// 图片集合
var imgs = []
for (let i = 1; i < 17; i++) {
  imgs.push('' + i + i)
}

$(function() {
  // 随机添加背景图
  refreshImg()

  refresh.click(function() {
    e = event || window.event
    e.stopPropagation()

    refreshImg()
    start()
  })

  refresh.mousedown(function() {
    $(this).addClass('click')
  })
  refresh.mouseup(function() {
    $(this).removeClass('click')
  })

  window.onload = start()
})

function start() {
  var verImg = document.getElementsByClassName('verImg')[0]

  if (verImg) {
    verImg.onload = function() {
      // 获取图片高度
      var imgH = this.clientHeight
      // 随机生成坐标（图片框固定宽度为300px，高度不定）
      var verX = 150 * (1 + Math.random()) - 38,
        verY = imgH / 4 + Math.random() * imgH / 2

      // 用户移动滑块函数
      fnDown(verX, verY)
    }
  }
}

function fnDown(verX, verY) {
  swiper.mousedown(function() {
    e = event || window.event
    e.stopPropagation()

    // 30为模块宽度
    verify.css({
      display: 'block',
      top: `${verY}px`,
      'background-position': `-${verX}px -${verY}px`
    })
    verified.css({ display: 'block', left: `${verX}px`, top: `${verY}px` })
    // 获取鼠标到按钮的距离
    var disX = e.clientX - $(this).offset().left,
      disY = e.clientY - $(this).offset().top
    text.css('opacity', '0')

    // 防止重复绑定触发多次
    box.unbind('mousemove')
    box.unbind('mouseup')

    // 移动
    box.bind('mousemove', function() {
      e = event || window.event
      fnMove(e, disX, disY)
    })

    // 释放
    box.bind('mouseup', function() {
      var stopL = verify.offset().left - 28
      // 误差在2px以内则算做成功
      if (Math.abs(stopL - verX) > 2) {
        alert('验证失败')
      } else {
        alert('验证成功')
      }
      // 解除绑定，并将滑动模块归位
      box.unbind('mousemove')
      swiper.css('left', '0px')
      verify.css('left', '10px')
      text.css('opacity', '1')
      box.unbind('mouseup')
    })
  })
}

function fnMove(e, posX, posY) {
  // 这里的e是以鼠标为参考
  var l = e.clientX - posX - $(handle).offset().left,
    winW = $(handle).width() + 29
  // 限制拖动范围只能在handle中
  if (l < 0) {
    l = 0
  } else if (l > winW) {
    l = winW
  }

  swiper.css('left', `${l}px`)
  verify.css('left', `${l + 10}px`)
}

function refreshImg() {
  // 随机生成下标
  var index = Math.round(Math.random() * 15)
  var imgH = 0

  verify.hide()
  verified.hide()

  var verImg = $('.verImg')
  if (verImg.length) {
    verImg.attr('src', `imgs/${imgs[index]}.jpg`)
  } else {
    imgBox.prepend(`<img class='verImg' src="imgs/${imgs[index]}.jpg" />`)
  }
  verify.css('background-image', `url('imgs/${imgs[index]}.jpg')`)
}
```

### CSS

```css
.box {
  width: 300px;
  padding: 20px;
  background-color: #fff;
  box-shadow: 2px 2px 8px 0 rgba(0, 0, 0, 0.4);
}

.imgBox {
  position: relative;
  width: 300px;
  overflow: hidden;
  box-shadow: 0 0 8px 0 rgba(0, 0, 0, 0.4);
}

.imgBox img {
  width: 100%;
}

.imgBox div {
  display: none;
}

.handle {
  display: flex;
  align-items: center;
  position: relative;
  height: 30px;
  border-radius: 20px;
  margin: 20px 0;
  padding: 4px 0 4px 70px;
  box-shadow: 0 0 20px 0 rgba(0, 0, 0, 0.2) inset;
  background: #f5f5f5;
  user-select: none;
}

.text {
  opacity: 1;
  transition: opacity 0.5s ease-in-out;
  color: #aaa;
}

.swiper {
  position: absolute;
  top: -10px;
  left: 0px;
  width: 58px;
  height: 58px;
  border-radius: 50%;
  background-color: pink;
  box-shadow: 2px 2px 6px 0 rgba(0, 0, 0, 0.2);
}

.verify {
  position: absolute;
  left: 10px;
  width: 38px;
  height: 38px;
  border-radius: 5px;
  background-repeat: no-repeat;
  background-attachment: scroll;
  background-size: 300px;
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4), 0 0 10px 0 rgba(90, 90, 90, 0.4);
  z-index: 10;
}

.verified {
  position: absolute;
  width: 38px;
  height: 38px;
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4) inset;
}

.refreshBox {
  border-top: 1px solid #ccc;
  padding: 15px 0 0 5px;
}

.refresh {
  color: #fff;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  padding: 8px 20px;
  border-radius: 20px;
  background-color: #555;
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4);
  transition: all 0.5s ease-in-out;
}
.refresh.click {
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4) inset;
}
```

## Github地址

* [https://github.com/himushroom/verify-slide](https://github.com/himushroom/verify-slide)

## 效果展示

![](../.gitbook/assets/demo.gif)

