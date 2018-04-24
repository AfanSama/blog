---
layout: post
title: 纯CSS导航栏下划线跟随效果
date: 2018-03-29 10:30:27
author: "afan"
catalog: true
tags:
- CSS
- html
- animation
---

# 定义需求
我们定义一下简单的规则，要求如下：

导航栏目的 li 的宽度是不固定的
当从导航的左侧 li 移向右侧 li，下划线从左往右移动。同理，当从导航的右侧 li 移向左侧 li，下划线从右往左移动。

假设 HTML 结构如下：
```html
<ul>
  <li>不可思议的CSS</li>
  <li>导航栏</li>
  <li>光标小下划线跟随</li>
  <li>PURE CSS</li>
  <li>Nav Underline</li>
  <li>Afan</li>
</ul>
```


# 神奇的 ~ 选择符

这里我们可以借助 ~ 选择符，完成这个艰难的使命，也是这个例子中，最最重要的一环。
对于当前 hover 的 li ，其对应伪元素的下划线的定位是 left: 100%，而对于 li:hover ~ li::before，它们的定位是 left: 0。

CSS 代码大致如下：
```css
ul {
    display: flex;
    position: absolute;
    width: 800px;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    list-style-type:none;
}

li {
    position: relative;
    padding: 20px;
    font-size: 20px;
    color: #000;
    line-height: 1;
    transition: 0.2s all linear;
    cursor: pointer;
}

li::before {
    content: "";
    position: absolute;
    top: 0;
    left:100%;
    width: 0;
    height: 100%;
    border-bottom: 2px solid #000;
    transition: 0.2s all linear;
}

li:hover::before {
    width: 100%;
    top: 0;
    left: 0;
    transition-delay: 0.1s;
    border-bottom-color: #000;
}

li:hover ~ li::before {
    left: 0;
}

li:active {
    background: #000;
    color: #fff;
}
```

至此，我们想要的效果就实现拉！撒花。[Demo](https://codepen.io/afansama/pen/deGBoZ)

