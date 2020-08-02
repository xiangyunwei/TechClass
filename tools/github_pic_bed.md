---
typora-root-url: ..\..\images
---

## 背景

图壳图床失效，sm.ms图床上传下载缓慢，第三方图床随时可能gg不稳定。

## Github图床

### Github 建仓库

为了不污染博客仓库提交记录，单独简历PicGo 图片仓库。

创建github 仓库的方法这里不多做说明了，不懂的可以 google 一下，这是基本操作了。



## 利用PicGo上传图片到 Github (不稳定)

### 设置 PicGo

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/PicGo设置上传参数.png)



## 手动上传到 Github 修改链接（非常稳定）

### 设置  typora  图片参数

- 设置图像根目录

> 格式 -  图像 - 设置图片根目录

图片的目录和文档的目录不在一起的时候，拖动插入图片插入文档中就会出现相对路径的符号

比如这样： `../../images/using_github_picgo_bed/设置图片转义.png` 的路径就比较难看，也不方便后面查找替换 CDN 的 URL。

设置根目录为图片所在目录之后，拖动插入图片生成的 MD 路径： `/using_github_picgo_bed/设置图片转义.png` 更加简洁了。

**所以，这里设置图片的存放路径为图像根目录。**



- 设置图像名称中文转义（可选）

  > 格式 -  图像 -  全局图像设置

设置了有更好的兼容性，不设置可读性更好。

举个栗子。不设置转义是这样的 MD 图像路径：`/using_github_picgo_bed/设置图片转义.png` 

开启转义的 MD 图像路径：`/using_github_picgo_bed/%E8%AE%BE%E7%BD%AE%E5%9B%BE%E7%89%87%E8%BD%AC%E4%B9%89.png`

![设置图片转义](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/设置图片转义.png)

### 图片后处理

因为我win10下使用PicGo客户端总是出现莫名的上传失败现象，可能是网络环境还是啥原因，总之图片能否上传成功完全看PicGo心情，于是差点放弃了使用，为什么是差点呢？因为我后来发现打开软件过一段时间就能正常上传了，非常神奇，但是我不想每次都等他一阵才能使用，就有了下面的方法。

1. 先一次性批量 git push 所有图片到 github 仓库。

2. 之后 Win下用 Ctrl+H 替换，Linux下用vim打开文件 `s/x/x/g` 模式匹配命令也能替换（注意要对路径符号`/` 转义为`\/`），修改本地图片链接为网络地址即可。

   

#### 本地路径替换成 jsDelivr CDN加速地址

![](https://cdn.jsdelivr.net/gh/lemonchann/images/using_github_picgo_bed/替换本地路径成网络路径.png)



#### 本地路径替换成github原始地址

![替换本地路径成github图片地址](https://github.com/lemonchann/images/raw/master/using_github_picgo_bed/替换本地路径成github图片地址.png)