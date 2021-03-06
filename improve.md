## 二阶段优化内容

### 前端优化

#### 错误拦截完善

1. window.onerror

window.onerror 可以捕获全局错误，但是只有在返回值为 **true** 时，才会阻止冒泡

2. 拦截异步错误

```js
window.addEventListener("unhandledrejection", function(e) {
  // Event新增属性
  // @prop {Promise} promise - 状态为rejected的Promise实例
  // @prop {String|Object} reason - 异常信息或rejected的内容
  // 会阻止异常继续抛出，不让Uncaught(in promise) Error产生
  e.preventDefault();
});
```

3. 拦截跨域错误

在跨域请求的 script 标签上设置 crossorigin 属性。或者将跨域资源转换为同源资源。

#### 批量上传

1. 实现消息批量

在高并发场景下，为保证前端**尽量少的请求数量**以及尽可能**实时的错误上传**，从缓存栈大小，和上传延迟时间方面进行批量上传设计。  
参考**滑动窗口协议**的实现机制，来进行动态调整。

#### 容错与容灾

1. 考虑在极端网络条件下，请求可能中途发送失败；以及边缘交互场景，用户在上传错误时关闭浏览器。

对这一场景，通过对上传信息进行缓存和重传来保障错误信息的完整收集。

可使用浏览器缓存机制，在上传错误信息时，先进行一次缓存，当上传成功后，清除缓存。如在用户再次打开浏览器时，发现缓存中存在错误信息，则进行一次重传，以保障信息被准确捕捉和存储。

### 后端优化

#### 使用 redis

通过 redis 做后端数据缓存，减少数据库读写开销。

#### 支持高并发

1. 实现多进程架构，支持高并发，解决高频率交互场景。

通过 cluster 在高并发场景保证服务可用与稳定。

#### 存储设施改进

1. 读写优化

sql 语法优化

2. 数据库选型优化

确认后决定数据库选型，社区意见，mysql 在高并发场景支持度不够好

3. 存储方式优化

在大数据场景下，存储单元的大小，严重影响数据库表大小。因此需要对存储的数据结构进行优化。

4. map 存储优化

目前使用 contenthash 命名的情况下，一定程度上缓解了 map 文件在服务端的存储压力。

但随着被监控项目的迭代，不可避免，map 文件提及会逐渐增大，需要进行定时任务来清理。

### source-map 优化

1. map 文件上传优化

需要保证不会重传已经存在的 map 文件，如此可以减少 request 数量及上传速度。

后端可以提供接口来查询，目前服务端已经存储的 map 文名，通过 contenthash 方式来进行比对，判定哪些是新增文件，需要上传。

2. 支持错误源码预览

在客观条件支持的情况下（源代码不被报漏在外），对错误源码进行解析，展示具体的错误内容。

潜在问题：若要展示错误代码内容，对于打包的 source-map 转化方式有要求，不能采用压缩率过高的压缩方式。

#### 操作方式优化

1. 实现类似 sentry 的监控系统打开方式

2. api 简化，提供更简洁的调用方式

#### 运维优化

1. 实现 CI/CD
