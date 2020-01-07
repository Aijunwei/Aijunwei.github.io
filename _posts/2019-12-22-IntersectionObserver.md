---
layout: post
title: Intersection Observer
---

## Intersection Observer是啥
引用MDN的原话，“Intersection Observer API提供了一种异步观察目标元素与祖先元素或顶级文档viewport的交集中的变化的方法。” 感觉发现了宝藏，有了这个API，我们检测某一个元素什么时候进入视口岂不是so easy了。

## Intersection Observer有啥用？
1. 实现图片懒加载
2. 无限加载滚动到页面底部后加载更多

## Just show me the code

```
var target = document.getElementById('target');//监听目标
var observer = new IntersectionObserver((entries, observer) => {
    //do something
    //target 进入或者移除视口时调用
    console.log(entries);
});

observer.observe(target);
```
这是一个简单的🌰，我们new了一个IntersectionObserver对象，只传了一个回调函数作为参数，实际这里省略了第二个配置项参数（options对象），然后使用observe方法监听target。当target进入视口（viewport）或者离开时，会触发传入的回调函数。注意这是一种高效的方式，不会占用过多主线程。当然如果回调中需要执行复杂计算，则可以使用Window.requestIdleCallback() ，避免产生性能问题。

## Intersection observer options
之前提到的IntersectionObserver构造函数还支持第二个配置参数，用于配置observer的环境，有以下3个字断
### root
用于指定root元素，必须是目标元素的父级元素，如果未指定或者为null时，则root为浏览器视口（viewport）。前面那个demo就是使用了默认的viewport作为root，用于检测目标元素target何时进入viewport或者离开viewport，root可以理解为一个参照物，用来检测目标元素在这个参照物内的可见性。

### rootMargin
见名思义，这不就是root的margin么？对，就是root元素的外边距，类似css的margin属性，设置方式也同css的margin一样，例如"10px"(四边的边距都是10px),"10px 20px"（上下10px，左右20px）,"10px 20px 30px"（上10px，左右20px，下30px）, "10px 20px 30px 40px"（上，右，下左）。嗯～好像讲了半天，都没明白rootMargin有啥用。我们来看MDN原话：“该属性值是用作root元素和target发生交集时候的计算交集的区域范围，使用该属性可以控制root元素每一边的收缩或者扩张。默认值为0。”

### thresholds
触发注册回调的阈值，可以是一个数值或者一个数值数组(数值表示target元素和root相交区域面积占target元素面积的比例)，当target和root元素的相交比例到达设定比例时会触发注册的回调函数。值为0表示，只要target出现在root中就触发回调，值为1表示target完全出现或者消失触发回调。如果指定的是数组，需要按升序排列，例如[0,0.5,1]。
未完成待续...

[<img src="{{ site.baseurl }}/images/404.jpg" alt="Constructocat by https://github.com/jasoncostello" style="width: 400px;"/>]({{ site.baseurl }}/)
