# http/2 request limit

迁移到http/2后

* 是否还需要合并文件？
* 原先合并的文件是否要做拆分？

**http1.x限制**： 浏览器请求限制（基本上同域名6个）

**优化策略**： 合并文件

既然http/2支持多路复用，是否代表可以无限制的发送请求？

![network request](https://raw.githubusercontent.com/feifeipan/HTTP2/master/screenshot/manyrequest2.png)

可以发现在请求到277.jpg\_.webp的时候，浏览器停止发送请求了。等待一定延迟之后才发送278.jpg\_.webp。我们通过chrome://net-internals查看了一下session包的情况

![session detail](https://raw.githubusercontent.com/feifeipan/HTTP2/master/screenshot/manyrequest1.png)

可以发现有http2\_session\_stalled\_max\_stream是128，这意味发送的窗口有限制大小，一旦超过就关闭窗口的，等待释放之后才会发送。

**另一个问题**

比如我们合并a.js,b.js,c.js之后，会导致a必须要等到a+b+c全部下载完毕之后才可以执行。如果利用http/2的多路复用特性做了拆分，就可以使a提早执行。

**建议**

1. 不要无逻辑盲目的合并文件，哪些文件需要合并要做一下业务逻辑的梳理。
2. 如果JS权重高（例如渲染页面、重要逻辑处理）并且无依赖，那么可以拆分出来，优先下载。
3. 如果JS之间有依赖（例如require），那么仍然保持合并。






