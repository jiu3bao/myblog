---
title: Fuild + waline 给你的博客增加评论功能
date: 2023-07-30 14:29:15
tags: [fuild, hexo, waline]
categories: 
- [Blog, fuild]
---

### step0
#### why waline？
- 最重要的当然是支持vercel
- 简单啊

### step1 申请LeanCloud
申请LeanCloud账号并创建应用，访问 https://console.leancloud.app/

**注意⚠️选国际版** ~~（你有备案域名的话当我没说）~~
![lean header](/img/fuild-waline/lean-head.jpg)

创建应用
![create app](/img/fuild-waline/create-application.jpg)

点击创建好的应用，进入详情页
注意下面的3个key是我们后面需要用到的 _tips:不要轻易泄漏你的key_
![keys](/img/fuild-waline/keys.jpg)

### step2 vercel！启动！
注册登录vercel什么的就不说了
点击[传送门](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fwalinejs%2Fwaline%2Ftree%2Fmain%2Fexample)，输 项目名称并点击 *Create* 继续

等待创建成功后，进入 *dashboard*,点击上方的*settings*进入设置页，如下：
![env_var_setting](/img/fuild-waline/env_var.jpg)
设置三个环境变量**LEAN_ID**, **LEAN_KEY** 和 **LEAN_MASTER_KEY**，值分别是**step1**里面**APP ID**, **APP KEY**, **Master Key**

配置完成后点击顶部的 Deployments 点击右侧的 Redeploy 按钮进行重新部署

部署成功后，你能在顶部的**project**里看到域名
![project](/img/fuild-waline/proj.jpg)
点击并访问，你能看到你部署的waline的页面了。

### step3 注册自己的waline
在你刚刚访问的地址后面，输入/ui/registe，首个注册的人会成为管理员。
管理员登陆后，即可看到评论管理界面, 可以修改、标记或删除评论，用户也通过评论框注册账号，登陆后会跳转到自己的档案页。

### step4 在fuild里配置waline
找到项目里的_config.fluid.yml文件，找到comments配置，修改如下
``` yml 
    # 评论插件
    # Comment plugin
    comments:
        enable: true
        # 指定的插件，需要同时设置对应插件的必要参数
        # The specified plugin needs to set the necessary parameters at the same time
        # Options: utterances | disqus | gitalk | valine | waline | changyan | livere | remark42 | twikoo | cusdis | giscus | discuss
        type: waline
```
再找到waline配置，修改serverURL为刚刚vercel上的domain
``` yml
    # Waline
    # 从 Valine 衍生而来，额外增加了服务端和多种功能
    # Derived from Valine, with self-hosted service and new features
    # See: https://waline.js.org/
    waline:
    serverURL: 在这里配置你的waline域名
    path: window.location.pathname
```
 最后重启你的hexo，结束🎉