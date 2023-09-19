---
title: Fixing memory leaks in web applications👻
date: 2023-09-19 11:08:05
tags: [js, webdev, browser]
categories: 
- [js, webdev, browser]
---

最近发现了一篇讲前端内存泄漏的文章，i got something。

## spa made it

-*One of these problems is memory leaks. A poorly-coded SPA can easily eat up megabytes or even gigabytes of memory, continuing to gobble up more and more resources, even as it’s sitting innocently in a background tab. At this point, the page might start to slow to a crawl, or the browser may just terminate the tab and you’ll see Chrome’s familiar “Aw, snap!” page.*

spa天生的劣势便是如此，开发者很容易就忘记释放内存。server-rendered可能也会造成内存泄漏，但由于页面切换，浏览器就会释放掉之前的内存。

## 内存泄漏情况
根据我的经验，最常见的内存泄漏源是如下 API：
1. *addEventListener*。这是最常见的一种，只需要 *removeEventListener* 一下，
2. *setTimeout/ setInterval*。如果创建一个循环计时器（例如每 30 秒运行一次），那么需要使用clearTimeout或 来清理它clearInterval。（setTimeout如果使用类似的方式可能会泄漏setInterval- 即在回调setTimeout内部安排一个新的setTimeout。）
3. *IntersectionObserver/ResizeObserver/MutationObserver* 等等。这些新的API非常方便，但也很可能泄漏。如果在组件内部创建一个组件，并将其附加到全局可用的元素，那么需要调用来disconnect()清理它们。（请注意，被gc的 DOM 节点也会对其listeners和observers进行gc。因此，通常情况下，您只需要担心全局元素，例如 body，document， header, footer等）
4. *Promises/ Observables/ EventEmitters*等。 (如果没有resolved or rejected，Promise就会被泄漏，在这种情况下 .then() 里的callback都会被泄漏)。
5. 全局对象存储。对于像Redux这样的东西，状态是全局的，所以如果你不小心，你可以不断地向它追加内存，它永远不会被清理。
6. 无限 DOM 增长。如果你在没有虚拟化的情况下实现无限滚动列表，那么DOM节点的数量将会无限制地增长。

## 识别内存泄漏
在 Chrome DevTools 中，我们选择的主要工具是“内存”选项卡中的“堆快照”工具。Chrome 中还有其他内存工具，但我认为它们对于识别泄漏没有太大帮助。

点击“拍摄快照”按钮时，您就捕获了该网页上特定 JavaScript VM 中的所有活动对象。这包括 引用的对象window、回调引用的对象setInterval等。将其视为时间上的freezen time，代表该网页使用的所有内存。

下一步是重现一些认为可能泄漏的场景 - 例如，打开和关闭模式对话框。一旦对话框关闭，您会期望内存返回到之前的级别。因此，您拍摄另一个快照，然后将其与前一个快照进行比较。

但是，存在一些限制：
1. 即使你点击了“收集垃圾”这个小按钮，你也可能需要为 Chrome 拍摄几个连续的快照才能真正清理掉未引用的内存。根据我的经验，三个就足够了。（检查每个快照的总内存大小 - 它最终应该稳定。）
2. 如果您有 Web Worker、Service Worker、iframe、共享 Worker 等，那么该内存将不会在堆快照中表示，因为它位于另一个 JavaScript VM 中。如果愿意，可以捕获此内存，但请确保知道正在测量哪一个。
3. 有时快照器会卡住或崩溃。在这种情况下，只需关闭浏览器选项卡并重新开始即可。
   
当然完成了上述步骤，你可能会发现，你产生了非常多的泄漏，但是这些很多不是真的泄漏，他们只是被缓存起来稍后会释放掉，那么如何在这些大量的泄漏里找到真的泄漏呢

## 找到真的泄漏
作者说需要重复上述的步骤7次，来查看哪些内容是被泄漏了7次的。
技巧是选择按泄漏的数量排序而不是总内存排序，因为7是个素数，这样你试了7次以后，就能非常直观的找到符合条件的object了。


## 堆快照工具的限制
1. 如果保存并重新加载快照文件，那么你将丢失对象分配位置的所有文件引用。例如，你不会看到事件侦听器的闭包来自哪个文件的第几行。由于这确实是关键信息，因此保存和发送堆快照文件几乎没有用处。
2. 如果涉及到WeakMap，那么 Chrome 就会向您显示这些引用，即使它们并不重要——一旦其他引用被清理干净，这些对象就会被取消分配。所以它们只是噪音。
3. Chrome 根据对象的原型对对象进行分类。因此，使用实际的类/函数越多，使用匿名对象越少，就越容易看到到底泄漏了什么。

## 实际开发应用中
事实上以我目前开发时遇到的内存泄漏的问题来说，事件监听器，threejs，rxjs的observe都是非常容易造成泄漏的，开发中要避免类似的问题，我们会在组件内定义一个effcts数组，将所有的监听器放在effects里面，然后等到页面销毁前，主动遍历effects执行取消订阅，这个方法目前还不错，推荐给大家



