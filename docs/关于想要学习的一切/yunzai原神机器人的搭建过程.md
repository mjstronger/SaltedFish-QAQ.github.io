# 云崽bot使用教程
我在网上冲浪的时候看到了一个有趣的开源项目直接拿来耍一耍。  
**[gitee项目页面在这里](https://gitee.com/yoimiya-kokomi/Yunzai-Bot)**  

## 项目介绍
云崽v3.0，原神qq群机器人，可以通过米游社接口，查询原神游戏数据信息，快速生成图片返回。肥肠的方便和快捷！！唯一缺点就是有时候老死机。

## 项目搭建
* 克隆项目
  ```shell
  git clone --depth=1 -b main https://gitee.com/yoimiya-kokomi/Yunzai-Bot.git
  ```
  ```shell
  cd Yunzai-Bot #进入Yunzai目录
  ```
* 安装pnpm，已安装的可以跳过
  ```shell
  npm install pnpm -g
  ```
* 安装依赖
  ```shell
  pnpm install -p
  ```
* 运行
  ```shell
  node app
  ```
## 遇见的问题
pnpm版本和node版本不搭配。node版本过低需要升级node版本。
* 首先查看node版本：
  ```shell
  node -v
  ```
* 下载n用于更新node版本
  ```shell
  sudo npm install n -g
  ```
* 然后通过n工具下载nodejs的最稳定的版本
  ```shell
  sudo n stable
  ```
* 然后查看一下node的版本如果仍然没有改变就使用命令
  ```shell
  hash -r 
  ```
## 结语
基本上yunzai跑起来之后设置软件会进行引导，根据引导进行设置即可完成yunzai机器人的全部搭建。