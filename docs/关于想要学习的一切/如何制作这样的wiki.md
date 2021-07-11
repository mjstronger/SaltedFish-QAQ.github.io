# 如何制作这样的wiki

## 关于本wiki

这个wiki，更多的是由本人单独维护。仅作为本人笔记本一般的存在，主要作用在于多端记录，发布。不管是在公司，还是家里都可以编辑，发布。

本wiki主要记录本人的学习以及生活上的某些东西，有些杂乱，亦有些感悟。

## mkdocs的使用

本wiki基于mkdocs建立，感谢bgg的指导.jpg。更加专业的教程请参照[bgg的博客](https://noodlefighter.com/posts/d1b9/)。

首先mkdocs需要一个python 3环境，关于python环境请自行安装。

安装mkdocs

```c
$ pip install mkdocs mkdocs-material
$ mkdocs --version
```

打开某文件夹，在其文件夹下建立新目录

```c
$ mkdocs new test-mkdocs
INFO    -  Creating project directory: test-mkdocs
INFO    -  Writing config file: test-mkdocs/mkdocs.yml
INFO    -  Writing initial docs: test-mkdocs/docs/index.md
[r@r-lh tmp]$ tree test-mkdocs/
test-mkdocs/
├── docs
│   └── index.md
└── mkdocs.yml

1 directory, 2 files
```

运行本地服务器测试

```c
$ cd test-mkdocs/
$ mkdocs serve
INFO    -  Building documentation...
INFO    -  Cleaning site directory
INFO    -  Documentation built in 0.05 seconds
[I 200726 14:07:14 server:296] Serving on http://127.0.0.1:8000
INFO    -  Serving on http://127.0.0.1:8000
[I 200726 14:07:14 handlers:62] Start watching changes
INFO    -  Start watching changes
```

至此可以通过访问https://127.0.0.1:8000访问本地服务器上运行的wiki了。其效果如下图所示

![img](pictures\wiki\wiki0.png)

关于如何配置wiki主题，参照如下网址：

网址暂缺

## 基于GitHub pages发布本wiki

之前按照云南廊坊王逆袭逆王爷的推荐，使用闲置家用笔记本和花生壳ddns搭建。但是。。。好鸡掰难用哦。遂转至使用github pages发布。

上传wiki项目到github个人仓库，在本仓库中创建github action脚本。每次更新后自动发布，在仓库的.github/**文件夹下。windows相关文件，参考awei的wiki，或者本人的github仓库。

https://github.com/SaltedFish-QAQ/SaltedFish-QAQ.github.io

mkdocs.yml文件中修改wiki配置，docs文件夹下是个人wiki文档。docs/CNAME删除即可。其他的基本照抄就好了

然后，PUSH！！！

## 设置GitHub pages

在wiki仓库中点击setting->pages，如下图所示

![img](pictures\wiki\wiki1.png)

source中的选项依照图中设置就好

自定义域名填入自己的域名，然后在自己的域名服务商那里设置解析规则。将自己的域名解析至github pages的默认域名。此操作不做详细赘述

## 结束了

大概就是这样了，有啥没写清楚的。就这样了你打我啊XD