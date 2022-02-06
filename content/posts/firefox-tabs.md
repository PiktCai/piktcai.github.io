---
title: 'Firefox 垂直标签栏实现'
date: '2021-10-05'
tags: ['技术']
---
1. 开启 `about:profiles` 页
2. 打开设置文件夹
3. 新建 `chrome` 文件夹
4. 在 `chrome` 文件夹中新建 `userChrome.css` 文件，其内容为：

```css
#TabsToolbar {
  visibility: collapse !important;
}

#sidebar-close {
  visibility: collapse;
}
```

1. 安装 `Tree Style Tab` 附加组件
2. 开启 `about:config` 页
3. 搜索选项 `toolkit.legacyUserProfileCustomizations.stylesheets` ，设置为 `true`
4. 重启 Firefox 浏览器即可查看更改后的样式

Ref: [707wk —— 《Firefox 使用竖直标签》](https://www.cnblogs.com/707wk/p/14589193.html)