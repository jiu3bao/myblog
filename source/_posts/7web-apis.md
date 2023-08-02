---
title: 7个JavaScript Web API To Build Ur Website 💥🔧
date: 2023-07-30 14:29:15
tags: [js, webdev]
categories: 
- [js, webdev]
---
随着技术的快速变化，开发人员正在获得令人难以置信的新工具和API。但我们发现，在**100 多个 API**中，只有**5%**被开发者积极使用。

让我们来看看一些有用的Web API，它们可以帮助您将网站起飞！🚀🪐

## 🔧1.Screen Capture API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/Screen_Capture_API/Using_Screen_Capture)
顾名思义，Screen Capture API 允许您捕获屏幕内容，从而使构建截屏的过程变得轻而易举。

您需要一个**视频**元素来显示捕获的屏幕。开始**按钮**将开始**屏幕捕获**。

```html
    <video id="preview" autoplay>
        Your browser doesn't support HTML5.
    </video>
    <button id="start" class="btn">Start</button>
```
```js
    const previewElem = document.getElementById("preview");
    const startBtn = document.getElementById("start");

    async function startRecording() {
        previewElem.srcObject = 
        await navigatormediaDevices.getDisplayMedia({
            video: true,
            audio: true,
        });
    }

    startBtn.addEventListener("click", startRecording);
```


## 🔧2. Web Share API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/share)
**Web Share API**允许您将网页中的**文本**、**链接**甚至**文件**共享给设备上安装的其他应用程序。
```js
    async function shareHandler() {
        navigator.share({
            title: "your share txt",
            text: "Check out my website",
            url: "https://ur website",
        });
    }
```
**tips:**要使用**Web Share API**，您需要用户的**交互**。例如，点击或touch事件。


## 🔧3.Intersection Observer API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver)
**Intersection Observer API** 允许您**检测元素何时进入或离开视口**。这对于实现**无限滚动**非常有用。

```js
    const intersectionObserver = new IntersectionObserver((entries) => {
        // 如果 intersectionRatio 为 0，则目标在视野外，
        // 我们不需要做任何事情。
        if (entries[0].intersectionRatio <= 0) return;

        loadItems(10);
        console.log("Loaded new items");
    });
    // 开始监听
    intersectionObserver.observe(document.querySelector(".scrollerFooter"));
```
<iframe height="300" style="width: 100%;" scrolling="no" title="Untitled" src="https://codepen.io/Xixi-Xi/embed/yLQZarb?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/Xixi-Xi/pen/yLQZarb">
  Untitled</a> by Xixi Xi (<a href="https://codepen.io/Xixi-Xi">@Xixi-Xi</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>


## 🔧4. Clipboard API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard)
**Clipboard API**允许您在剪贴板中读取和写入数据。这对于实现复制到剪贴板功能非常有用。
```js
    async function copyHandler() {
        const text = "https://tapajyoti-bose.vercel.app/";
        navigator.clipboard.writeText(text);
    }
```
**tips:** 仅当页面已在屏幕上可见时，您才能使用wakeLock 。否则会抛出错误。


## 🔧5. Screen Wake Lock API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/WakeLock)
有没有想过YouTube如何防止屏幕在播放视频时关闭？就是因为**Screen Wake Lock API**。
```js
    let wakeLock = null;

    async function lockHandler() {
        wakeLock = await navigator.wakeLock.request("screen");
    }

    async function releaseHandler() {
        await wakeLock.release();
        wakeLock = null;
    }
```


## 🔧6. Screen Orientation API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/Screen/orientation)
**Screen Orientation API**允许您检查屏幕的当前方向，甚至将其锁定到特定方向。

```js
    async function lockHandler() {
        await screen.orientation.lock("portrait");
    }

    function releaseHandler() {
        screen.orientation.unlock();
    }

    function getOrientation() {
        return screen.orientation.type;
    }
```


## 🔧7. Fullscreen API 🚪[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/requestFullscreen)
允许您全屏显示一个元素或整个页面。
```js
    async function enterFullscreen() {
        await document.documentElement.requestFullscreen();
    }

    async function exitFullscreen() {
        await document.exitFullscreen();
    }
```
**tips:** 同样需要用户交互