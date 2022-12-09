---
title: 实现 PWA “可安装” 需要什么？
date: 2020-04-14T07:58:05+08:00
draft: false
comments: true
tags: 
- PWA
---

> 2020年2月14日发布 · 2020年2月24日更新
>
> 原文作者：Pete LePage
> 
> 原文地址：https://web.dev/install-criteria/

![](http://oss.yuguo.im/blog/202004/pwa.svg)

许多浏览器会向用户表明，当您的渐进式 Web 应用程序（PWA）满足特定条件时，可以安装该程序。示例包括地址栏中的“安装”按钮或弹出菜单中的“安装”菜单项。另外，满足条件时，许多浏览器都会触发 `beforeinstallprompt` 事件，使您可以直接从 PWA 启用安装流程。

在 Chrome 浏览器中，您的 Progressive Web App 必须满足以下条件，才能触发 `beforeinstallprompt` 事件并显示浏览器内安装提示：

- 该 web app 尚未安装
- 满足用户启发式行为(引导页)
- 通过 HTTPS 提供服务
- 包括一个 [Web App Manifest](https://web.dev/add-manifest/ "Web App Manifest")，其中包括
    - `short_name` 或 `name`
    - `icons` - 必须包含 192px 和 512px 图标
    - `start_url`
    - `display` - 必须是指定选项 `fullscreen`, `standalone` 或 `minimal-ui` 中的一个
    - 注: `prefer_related_applications` 要么不存在，要么值为 `false`
- Registers a service worker with a functional `fetch` handler

其他浏览器具有类似的安装标准，尽管可能会有细微的差异。检查各个站点以获取完整详细信息：
- [Edge](https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps#requirements "Edge")
- [Firefox](https://developer.mozilla.org/en-US/Apps/Progressive/Add_to_home_screen#How_do_you_make_an_app_A2HS-ready "Firefox")
- [Opera](https://dev.opera.com/articles/installable-web-apps/ "Opera")
- [Samsung Internet](https://hub.samsunginter.net/docs/ambient-badging/ "Samsung Internet")
- [UC Browser](https://plus.ucweb.com/docs/pwa/docs-en/zvrh56 "UC Browser")

**在 Android 手机上，如果 web app manifest 中包含 `related_applications` 和 `"prefer_related_applications": true`，则会将用户定向到 Google Play 商店，并提示您安装指定的本机应用。**

