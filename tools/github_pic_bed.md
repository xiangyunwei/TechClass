## 背景

图壳图床失效，sm.ms图床上传下载缓慢，第三方图床随时可能gg不稳定。

## Github图床

### Github 建仓库

为了不污染博客仓库提交记录，单独建立 图片存储仓库。

创建 github 仓库的方法这里不多做说明了，不懂的可以 google 一下，这是基本操作了。



## 利用PicGo上传图片到 Github (不稳定)

### 设置 PicGo

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/PicGo设置上传参数.png)

因为我win10下使用PicGo客户端总是出现莫名的上传失败现象，可能是网络环境还是啥原因，总之图片能否上传成功完全看PicGo心情。

于是差点放弃使用PicGo，为什么是差点呢？因为我后来发现打开软件过一段时间就能正常上传了，非常神奇，但是我不想每次都等他一阵才能使用，就有了下面的方法。



## 手动上传到 Github 修改链接（非常稳定）

既然 PicGo 经常上传失败，那我为什么不自己上传图片到 github 呢？就相当于 github 提交代码一样，而且还没有图片数量限制，最重要的是上传一定是成功的，只要 github 没挂。下面说明手动上传操作步骤。

### 设置  typora  图片参数

#### 设置图像根目录（可选）

> 格式 -  图像 - 设置图片根目录

图片的目录和文档的目录不在一起的时候，拖动插入图片插入文档中就会出现相对路径的符号

比如这样： `../../images/using_github_picgo_bed/设置图片转义.png` 的路径就比较难看，也不方便后面查找替换 CDN 的 URL。

设置根目录为图片所在目录之后，拖动插入图片生成的 MD 路径： `/using_github_picgo_bed/设置图片转义.png` 更加简洁了。

**所以，这里设置图片的存放路径为图像根目录。**



#### 设置图像名称中文转义（可选）

> 格式 -  图像 -  全局图像设置

这个设置主要是针对汉字的转义，设置了有更好的兼容性，不设置可读性更好。

举个栗子。不设置转义是这样的 MD 图像路径：`/using_github_picgo_bed/设置图片转义.png` 

开启转义的 MD 图像路径：`/using_github_picgo_bed/%E8%AE%BE%E7%BD%AE%E5%9B%BE%E7%89%87%E8%BD%AC%E4%B9%89.png`

我选择不开启转义，因为目前我还没发现那个 MD 解析器不能识别未转义的汉字。

![设置图片转义](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/设置图片转义.png)

### 导入图片

编写 MD 文件时可以选择拖入或者导入本地的图片，Typora 会自动转换成 MD 链接格式，这时候是本地的链接地址，本地预览够用了，发布的时候要用网络图片地址，下一步教你怎么把本地图片地址转换成网络图片地址。



### 图片后处理

1. 先一次性批量 git push 所有图片到 github 仓库。

2. 之后 Win下用 Ctrl+H 替换，Linux下用vim打开文件 `s/x/x/g` 模式匹配命令也能替换（注意要对路径符号`/` 转义为`\/`），修改本地图片链接为网络地址即可。

   

#### 本地路径替换成github原始地址

查看图片在github上的原始地址

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/复制github图片地址.png)



按原始地址格式替换 MD 中本地图片地址

![替换本地路径成github图片地址](https://github.com/lemonchann/images/raw/master/using_github_picgo_bed/替换本地路径成github图片地址.png)



#### 本地路径替换成 jsDelivr CDN加速地址

**什么是 CDN**

> CDN的全称是Content Delivery Network，即内容分发网络。CDN是构建在网络之上的内容分发网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要有内容存储和分发技术。

jsDelivr 是一个快速免费的公用 CDN ，可以加快你的资源访问速度，当然也可以加快你的图片访问速度。

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/jsDelivr首页.png)

可以用免费的  jsDelivr CDN 加速图片访问，官网地址 `https://www.jsdelivr.com/?docs=gh`， 使用非常简单方便。

使用方法：`https://cdn.jsdelivr.net/gh/你的github用户名/你的github仓库名@发布的版本号/文件路径`

**替换本地图片链接**

通过文本替换方式，把本地图片链接替换成CDN的访问链接，即可轻松实现 CDN 加速图片访问效果。

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/替换本地路径成网络路径.png)



