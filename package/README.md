### 构建

项目开发完成之后，执行以下命令进行构建

```
pnpm build
```

构建打包成功之后，会在根目录生成 dist 文件夹，里面就是构建打包好的文件

### 预览
发布之前可以在本地进行预览，有多种方式，这里介绍两种

* 使用项目自定的命令进行预览(推荐)
```
# 先打包在进行预览
pnpm preview
# 直接预览本地 dist 文件目录
pnpm preview:dist
```
* 本地服务器预览（通过live-server）
```
# 1.全局安装live-server
pnpm add live-server -g
# 2. 进入打包的后目录
cd ./dist
# 本地预览，默认端口8080
live-server
# 指定端口
live-server --port 9000
```

### 分析构建文件体积
如果构建文件很大，可以通过项目内置 rollup-plugin-analyzer 插件进行代码体积分析，从而优化你的代码。
```
yarn report
```
运行之后，在自动打开的页面可以看到具体的体积分布，以分析哪些依赖有问题。

### 压缩
开启 gzip 压缩 <br>
开启 gzip，并配合 nginx 的 gzip_static 功能可以大大加快页面访问速度

```
只需开启 VITE_BUILD_COMPRESS='gzip' 即可在打包的同时生成 .gz 文件

# 根据自己路径来配置更改
# 例如部署在nginx /next/路径下  则VITE_PUBLIC_PATH=/next/
VITE_PUBLIC_PATH=/
```
