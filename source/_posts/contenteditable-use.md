---
title: 🥥🔧我是如何用contenteditable手撸一个简易的图文编辑器的
date: 2023-07-30 14:29:15
tags: [js, webdev]
categories: 
- [js, webdev]
---
先说下背景，客户那边需要一个简易的输入框，里面能**填充文字和粘贴图片**，一开始我想到的实现方案当然是去找到一个开源的组件，然后与后端定义好规则，简单了事。但是由于公司这个老旧项目是用*angularjs*写的，好不容易找到的组件又不符合公司可信的要求，花了很多时间翻github以后，我决定自己写一个。

客户的要求非常简单，只用**文字**+**图片**的，那么可以用contenteditable轻松实现。🚪[MDN传送门](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/contenteditable)

### 🚶初步实现
```html
    <!--只需要在标签上添加 contenteditable="true"-->
    <div contenteditable="true" id='custom_editor'
    style='width:400px;height:400px;border:1px solid grey'
    >text here</div>
```
现在我们能在这个div里填充文字，但是当粘贴图片时，出现了问题。

当我使用微信截图等工具直接截图粘贴的时候，图片是能正常显示的，但是当我右键复制本地图片再粘贴的时候，图片无法显示，因为路径出现了问题。我们查看复制进去裂掉后的图片路径，
发现是**图片的系统路径**

而截图粘贴的图片是**base64**。
![base64img](/img/contenteditable-use/right_img.jpg)

### 🤔在粘贴图片时做一些操作
为了解决本地图片复制的路径问题，我们需要在粘贴的时候做一些操作

```js
    const target = document.getElemetById('custom_editor')
    target.addEventListener('paste' => {
        const items = e.clipboardData.items
            for (let i = 0; i < items.length; i++) {
            // is file
            if (items[i].kind === 'file') {
                const file = items[i].getAsFile()
                const reader = new FileReader()
                reader.onload = (e) => {
                    const img = new Image()
                    // base64
                    img.src = e.target.result
                    target.appendChild(img)
                }
                reader.readAsDataURL(file)
            }
        }
    })
```

### 🏃最后处理下光标的问题
上面的代码都是在target里直接append，这样就用户排版体验极差，我们接下来要做的就是在光标的位置处粘贴图片，就能完美解决这个问题啦😊

```js
    const target = document.getElemetById('custom_editor')
    target.addEventListener('paste' => {
        const items = e.clipboardData.items
            for (let i = 0; i < items.length; i++) {
            // is file
            if (items[i].kind === 'file') {
                const file = items[i].getAsFile()
                const reader = new FileReader()
                reader.onload = (e) => {
                    const img = new Image()
                    img.src = e.target.result
                    // insert img
                    const selection = window.getSelection()
                    const range = selection.getRangeAt(0)
                    // 清除光标选中的内容
                    range.deleteContents()
                    //  插入元素
                    range.insertNode(img)
                }
                reader.readAsDataURL(file)
            }
        }
    })
```