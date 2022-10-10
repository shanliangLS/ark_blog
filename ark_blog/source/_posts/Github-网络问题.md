---
title: Github 网络问题
date: 2022-03-17 16:14:00
categories:
- 其他
tags:
- Github
---

## Github 图片不显示

参考[解决github图片不显示的问题](https://zhuanlan.zhihu.com/p/107196957)

修改hosts

C:\Windows\System32\drivers\etc\hosts

在末尾添加：

```
# GitHub Start
192.30.253.112    Build software better, together
192.30.253.119    gist.github.com
151.101.184.133    assets-cdn.github.com
151.101.184.133    raw.githubusercontent.com
151.101.184.133    gist.githubusercontent.com
151.101.184.133    cloud.githubusercontent.com
151.101.184.133    camo.githubusercontent.com
151.101.184.133    avatars0.githubusercontent.com
151.101.184.133    avatars1.githubusercontent.com
151.101.184.133    avatars2.githubusercontent.com
151.101.184.133    avatars3.githubusercontent.com
151.101.184.133    avatars4.githubusercontent.com
151.101.184.133    avatars5.githubusercontent.com
151.101.184.133    avatars6.githubusercontent.com
151.101.184.133    avatars7.githubusercontent.com
151.101.184.133    avatars8.githubusercontent.com
# GitHub End
```

2022-03-17测试有效
