---
layout: post
title: CSS自适应表格了解一下
date: 2018-03-19 17:15:52
author: "afan"
catalog: true
tags: 
- CSS
---


>插件描述：如何让table元素也能表现出自适应性？按HTML5的说法，table是一个不鼓励使用的HTML标记，但现实工作中，我们避免不了的偶尔会用到它。那么，怎样让一个传统的表格也表现出自适应性呢？

调整页面宽度，或者在不同屏幕尺寸的设备上(PC，手机)尝试浏览器本页，你会发现下面的表格呈现出自适应布局特征，能够自动的使用不同的屏幕尺寸，数据的表现不会因为屏幕大小变化而变得不合适。

今天要说的是自适应布局技术中的一个难题，就是，如何让table元素也能表现出自适应性。按HTML5的说法，table是一个不鼓励使用的HTML标记，但现实工作中，我们避免不了的偶尔会用到它。那么，怎样让一个传统的表格也表现出自适应性呢？

网上有很多种解决方案，最常见的是配合JavaScript。css-tricks里给出了一个用纯CSS实现的，但它需要将一些业务数据写在CSS里。而本文这将提到的一种方法也是用纯CSS实现表格的自适应布局，而且CSS只负责表现，不牵涉业务逻辑和数据。

假设一种需求，用一个表格来展示付款数据。下面是我们使用的表格和数据：

```html
<table>
  <thead>
    <tr>
      <th>支付</th>
      <th>日期</th>
      <th>金额</th>
      <th>周期</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-label="支付">支付 #1</td>
      <td data-label="日期">02/01/2015</td>
      <td data-label="金额">$2,311</td>
      <td data-label="周期">01/01/2015 - 01/31/2015</td>
    </tr>
    <tr>
      <td data-label="支付">支付 #2</td>
      <td data-label="日期">03/01/2015</td>
      <td data-label="金额">$3,211</td>
      <td data-label="周期">02/01/2015 - 02/28/2015</td>
    </tr>
  </tbody>
</table>
```

借助伪元素和自定义属性

我们将借助伪元素 :before 和 :after 的力量。通常， 它们用显示图标类的内容，比如一个箭头，提示，或文字图案(icon)。它的另外一个神奇的功能是元素属性值显示到HTML内容里，content: attr(data-label)，放在before/after伪元素类里。沿着这个思路，我们就能够让table在PC端表现成网格效果，而在小屏的手机端表现成流式布局。

我们暂以600px为小屏幕大屏幕的分界点，下面的CSS使用媒体查询语句，在小于600px宽的屏幕上，用CSS将td上的属性值取出来，放到内容区显示。

```css
body {
    font-family: arial;
  }

  table {
    border: 1px solid #ccc;
    width: 80%;
    margin:0;
    padding:0;
    border-collapse: collapse;
    border-spacing: 0;
    margin: 0 auto;
  }

  table tr {
    border: 1px solid #ddd;
    padding: 5px;
  }

  table th, table td {
    padding: 10px;
    text-align: center;
  }

  table th {
    text-transform: uppercase;
    font-size: 14px;
    letter-spacing: 1px;
  }

  @media screen and (max-width: 600px) {

    table {
      border: 0;
    }

    table thead {
      display: none;
    }

    table tr {
      margin-bottom: 10px;
      display: block;
      border-bottom: 2px solid #ddd;
    }

    table td {
      display: block;
      text-align: right;
      font-size: 13px;
      border-bottom: 1px dotted #ccc;
    }

    table td:last-child {
      border-bottom: 0;
    }
    /* 核心代码 */
    table td:before {
      content: attr(data-label);
      float: left;
      text-transform: uppercase;
      font-weight: bold;
    }
  }

.note{max-width: 80%; margin: 0 auto;}
```
当可视区域的宽度小于600px时，会触发自适应布局设置的条件，表格变成流式布局：你也可以分别在PC和手机上分别测试这个网页。完美的实现了自适应布局的效果。

[在线DEMO](https://codepen.io/afansama/pen/bMEPPV)