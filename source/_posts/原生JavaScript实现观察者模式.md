---
layout: post
title: 原生JavaScript实现观察者模式
date: 2018-04-23 15:15:42
author: "afan"
catalog: true
tags:
- js
- JavaScript
---
```python
print("hello world!")
```
> 观察者模式是软件设计模式的一种。在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实时事件处理系统。

说的简单些，就是在数据发生改变时，对应的处理函数自动执行。维基的定义中涉及到了主动发出通知，按照这种方式，在angularJS中的事件广播更是中规中矩，但是其缺点是代码的可维护性较差。那么如果不进行主动通知，而是在进行对象属性值设置时，调用相关的处理函数，也可达到同等效果。如下见详细代码。

# ES5下的实现
在ES5中主要是通过`Object.defineProperty`方法定义对象属性的设置和获取，并在进行设置时执行相关的处理函数，代码示例如下：

```javascript
// 创建对象
var targetObj = {
    age: 1
}
// 定义值改变时的处理函数
function observer(oldVal, newVal) {
    // 其他处理逻辑...
    console.info('name属性的值从 '+ oldVal +' 改变为 ' + newVal);
}

// 定义name属性及其set和get方法
Object.defineProperty(targetObj, 'name', {
    enumerable: true,
    configurable: true,
    get: function() {
        return name;
    },
    set: function(val) {
        //调用处理函数
        observer(name, val)
        name = val;
    }
});

targetObj.name = 'Martin';
targetObj.name = 'Lucas';
console.info('targetObj:', targetObj)
```
输出结果为

```javascript
name属性的值从 Martin 改变为 Lucas
targetObj: {age: 1, name: 'Lucas'}
```
# ES6的实现
## 使用set方法实现
```js
class TargetObj {
    constructor(age, name) {
        this.name = name;
        this.age = age;
    }
    set name(val) {
        observer(name, val);
        name = val;
    }
}

let targetObj = new TargetObj(1, 'Martin');

// 定义值改变时的处理函数
function observer(oldVal, newVal) {
    // 其他处理逻辑...
    console.info('name属性的值从 '+ oldVal +' 改变为 ' + newVal);
}
targetObj.name = 'Lucas';
console.info(targetObj)
```
输出结果为
```js
name属性的值从 Martin 改变为 Lucas
targetObj: {age: 1, name: 'Lucas'}
```
# 使用Reflect和Proxy实现
在ES6中新增的Proxy Api用处很多，结合Reflect Api可以方便的实现很多强大的逻辑，详细介绍可以参见[《ECMAScript 6 入门》—— 阮一峰](http://es6.ruanyifeng.com/#docs/reflect) 中的介绍。实现代码如下：

```js
class TargetObj {
    constructor(age, name) {
        this.name = name;
        this.age = age;
    }
}

let targetObj = new TargetObj(1, 'Martin');

let observerProxy = new Proxy(targetObj, {
    set(target, property, value, reciever) {
        if (property === 'name') {
            observer(target[property], value);
        }

        Reflect.set(target, property, value, reciever);
    }
});
// 定义值改变时的处理函数
function observer(oldVal, newVal) {
    // 其他处理逻辑...
    console.info(`name属性的值从 ${oldVal} 改变为 ${newVal}`);
}

observerProxy.name = 'Lucas';
console.info(targetObj);
```

输出结果为

```js
name属性的值从 Martin 改变为 Lucas
targetObj: {age: 1, name: 'Lucas'}
```

#总结
1. ES6中很多的新增功能，都有效的提升了JS本身的合理性和高效性
2. 很多基础的方法，概念，虽然在做业务实现时使用较少，但是去了解其理念，对编码思想的影响是很大的
3. 观察者模式可以很好的完成数据间的交互行为，虽然已经经常在用，但是具体了解后对整体的结构和原理有了更深层次的理解
4. Proxy真的很好，很强大


<!-- 转载于:https://blog.csdn.net/lm278858445/article/details/78287492 -->