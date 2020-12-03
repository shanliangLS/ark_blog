---
title: Node.js 编译时内存溢出
date: 2020-10-23 11:17:34
categories:
- BUG修复
tags:
- Node.js
- Vuepress
---

FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory

<!-- more -->

## 编译 Vuepress 内存溢出

- [x] 已解决

在服务器上编译一个 Vuepress 项目时报错。之前都没出事，自从加了一堆 pdf 使得项目变得很大后就有问题了

在自己主机上怎么跑怎么编译都不会报错，放到服务器上老编译失败，可能是主机 64G 内存无所畏惧？

{% codeblock lang:bash mark:10 %}
wait Rendering static HTML...
<--- Last few GCs --->
[34421:0x3ae1f20]   238228 ms: Scavenge 2031.6 (2059.1) -> 2027.7 (2076.9) MB, 8.0 / 0.0 ms  (average mu = 0.286, current mu = 0.319) allocation failure 
<--- JS stacktrace --->
==== JS stack trace =========================================
    0: ExitFrame [pc: 0x13cf019]
Security context: 0x1ba0e4d008d1 <JSObject>
    1: match [0x1ba0e4d0cb21](this=0x1bed81673269 <String[36]: function Boolean() { [native code] }>,0x1bed81673289 <JSRegExp <String[#18]: ^\s*function (\w+)>>)
    2: assertProp(aka assertProp) [0x81163b025b9] [/home/gitlab-runner/builds/dQQYXgCs/0/infra/hello/node_modules/vue/dist/vue.runtime.common.dev.js:~1669] [pc=0x2d9c37a147d2](this=0x129b621404b1 <undefi...
FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
 1: 0xa093f0 node::Abort() [node]
 2: 0xa097fc node::OnFatalError(char const*, char const*) [node]
 3: 0xb842ae v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [node]
 4: 0xb84629 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [node]
 5: 0xd30fe5  [node]
 6: 0xd31676 v8::internal::Heap::RecomputeLimits(v8::internal::GarbageCollector) [node]
 7: 0xd3def5 v8::internal::Heap::PerformGarbageCollection(v8::internal::GarbageCollector, v8::GCCallbackFlags) [node]
 8: 0xd3eda5 v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [node]
 9: 0xd4044f v8::internal::Heap::HandleGCRequest() [node]
10: 0xceef35 v8::internal::StackGuard::HandleInterrupts() [node]
11: 0x1049bf6 v8::internal::Runtime_StackGuard(int, unsigned long*, v8::internal::Isolate*) [node]
12: 0x13cf019  [node]
/home/gitlab-runner/builds/dQQYXgCs/0/infra/hello/node_modules/.bin/vuepress: line 14: 34421 Aborted                 node "$basedir/../vuepress/cli.js" "$@"
npm ERR! code ELIFECYCLE
npm ERR! errno 134
npm ERR! hello@1.0.0 docs:build: `vuepress build docs`
npm ERR! Exit status 134
npm ERR! 
npm ERR! Failed at the hello@1.0.0 docs:build script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
npm ERR! A complete log of this run can be found in:
npm ERR!     /home/gitlab-runner/.npm/_logs/2020-10-19T00_37_45_861Z-debug.log
ERROR: Job failed: exit status 1
{% endcodeblock %}

或

{% codeblock lang:bash mark:12 %}
wait Rendering static HTML...
<--- Last few GCs --->
[7124:0x4537f20]   248604 ms: Scavenge 2035.8 (2060.2) -> 2030.6 (2060.9) MB, 5.5 / 0.0 ms  (average mu = 0.201, current mu = 0.099) allocation failure 
[7124:0x4537f20]   249430 ms: Mark-sweep 2037.1 (2060.9) -> 2032.2 (2074.7) MB, 765.0 / 0.0 ms  (average mu = 0.196, current mu = 0.190) allocation failure scavenge might not succeed
<--- JS stacktrace --->
==== JS stack trace =========================================
    0: ExitFrame [pc: 0x13cf019]
Security context: 0x2cb4145808d1 <JSObject>
    1: resolveItem(aka resolveItem) [0x16f037ec6699] [1.server-bundle.js:~1745] [pc=0x29b9042c90cf](this=0x2b4191e004b1 <undefined>,0x29c6f54fbe91 <String[#17]\: 72-\x4f7f\x7528cordova\x63d2\x4ef6api>,0x37e1826f1cc1 <JSArray[535]>,0x29c6f54f32b1 <String[#38]: /doc/web/ionic/learning_ionic_chinese/>)
    2: arguments adaptor frame: 4->3
    3: resolveItem(aka reso...
FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
 1: 0xa093f0 node::Abort() [node]
 2: 0xa097fc node::OnFatalError(char const*, char const*) [node]
 3: 0xb842ae v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [node]
 4: 0xb84629 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [node]
 5: 0xd30fe5  [node]
 6: 0xd31676 v8::internal::Heap::RecomputeLimits(v8::internal::GarbageCollector) [node]
 7: 0xd3def5 v8::internal::Heap::PerformGarbageCollection(v8::internal::GarbageCollector, v8::GCCallbackFlags) [node]
 8: 0xd3eda5 v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [node]
 9: 0xd4044f v8::internal::Heap::HandleGCRequest() [node]
10: 0xceef35 v8::internal::StackGuard::HandleInterrupts() [node]
11: 0x1049bf6 v8::internal::Runtime_StackGuard(int, unsigned long*, v8::internal::Isolate*) [node]
12: 0x13cf019  [node]
/home/gitlab-runner/builds/dQQYXgCs/0/infra/hello/node_modules/.bin/vuepress: line 14:  7124 Aborted                 node "$basedir/../vuepress/cli.js" "$@"
npm ERR! code ELIFECYCLE
npm ERR! errno 134
npm ERR! hello@1.0.0 docs:build: `vuepress build docs`
npm ERR! Exit status 134
npm ERR! 
npm ERR! Failed at the hello@1.0.0 docs:build script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
npm ERR! A complete log of this run can be found in:
npm ERR!     /home/gitlab-runner/.npm/_logs/2020-10-09T08_06_30_758Z-debug.log
ERROR: Job failed: exit status 1
{% endcodeblock %}

主要错误

```bash
FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
```

原因

node 基于 V8 引擎，限制了内存使用大小。在 V8 下，64 位系统可以操纵 1.4GB 的内存，32 位可以操纵 0.7GB 内存。如果前端项目非常庞大，webpack 编译时会占用很多的系统资源，如果超出了 V8 对 node 默认的内存限制大小就会出现如上的错误

### 方案一：修改 `NODE_OPTIONS`

在服务器上运行

```bash
export NODE_OPTIONS=--max_old_space_size=8192
```

第一次出错时的解决方案，运行该命令后 `docs:build` 编译成功

一周后新增文档时再次出现报错，再加大 `--max_old_space_size=12288` 已无效

```bash
242847 ms: Mark-sweep 2041.0 (2058.8) -> 2036.4 (2083.3) MB, 790.0 / 0.0 ms  (average mu = 0.094, current mu = 0.036) allocation failure scavenge might not succeed
```

垃圾回收 `Mark-sweep` 的范围始终是 2G 左右，`--max_old_space_size=12288` 设置的 12G 没有显示

### 方案二：使用 `increase-memory-limit` （由于某些原因我这不太好弄就没弄）

安装 `increase-memory-limit`

```bash
npm install -save-dev increase-memory-limit
```

安装 `cross-env`

```bash
npm install -save-dev cross-env
```

在 `package.json` 中添加

```json
"scripts": {
    "fix-memory-limit": "cross-env LIMIT=8192 increase-memory-limit"
}
```

编译前运行

```bash
npm run fix-memory-limit
```

### 方案三：在 `docs:build` 中添加 `--max_old_space_size` 参数

Vuepress 的 `package.json` 中 `docs:build` 被封装

```json
"scripts": {
  "docs:build": "vuepress build docs"
},
```

直接在其中添加 `--max_old_space_size=8192` 会报找不到该参数的错，需要修改为如下格式

```json
"scripts": {
  "docs:build": "node --max_old_space_size=8192 xxx/xxx build"
},
```

此处直接修改 `node_modules/.bin/vuepress` ，在 `node  "$basedir/../vuepress/cli.js" "$@"` 中间增加 `--max_old_space_size=8192`

```bash
#!/bin/sh
basedir=$(dirname "$(echo "$0" | sed -e 's,\\,/,g')")

case `uname` in
    *CYGWIN*) basedir=`cygpath -w "$basedir"`;;
esac

if [ -x "$basedir/node" ]; then
  "$basedir/node"  "$basedir/../vuepress/cli.js" "$@"
  ret=$?
else
  node  --max_old_space_size=8192 "$basedir/../vuepress/cli.js" "$@"
  ret=$?
fi
exit $ret
```

终于编译成功不再报内存溢出错误了