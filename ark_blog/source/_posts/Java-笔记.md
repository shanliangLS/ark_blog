---
title: Java 笔记
date: 2021-06-10 20:08:00
categories:
- 后端
tags:
- Java
---

## Date

### 不同格式的字符串转时间

```java
private static String[] parsePatterns = {"yyyy-MM-dd", "yyyy-MM-dd HH:mm:ss", "yyyy-MM-dd HH:mm", "yyyy年MM月dd日"};
```

```java
Data result = DateUtils.parseDate(str, parsePatterns)
```

## 异常处理

[J2EE项目异常处理](https://www.iteye.com/blog/klyuan-72170)

### checked 异常和 unChecked 异常

如果一个异常是致命的，不可恢复的。或者调用者去捕获它没有任何益处，使用unChecked异常。

如果一个异常是可以恢复的，可以被调用者正确处理的，使用checked异常。

在使用unChecked异常时，必须在在方法声明中详细的说明该方法可能会抛出的unChekced异常。由调用者自己去决定是否捕获unChecked异常