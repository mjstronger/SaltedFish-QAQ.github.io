# 在windows上用qemu安装aarch64虚拟机
  

!!! note "“好记性不如烂笔头。” —— 张溥"
## 前言
最近公司需要使用aarch64环境编包，但是苦于暂时没有aarch64服务器，只能找个虚拟机代替，便想到这种方式来搓一个aarch64虚拟机。  

## 准备工作
* 在qemu官网上直接下载[qemu](https://qemu.weilnetz.de/w64/)
* 下载qemu uefi[固件文件](https://releases.linaro.org/components/kernel/uefi-linaro/latest/release/qemu64/QEMU_EFI.fd)

## 安装QEMU

* 打开安装程序一路Next，改个路径就行。
* 然后我们的qemu就安装完成了
 

## 配置环境变量
在控制面板中搜索环境变量，添加qemu的安装路径为环境变量  

![img](pictures\wiki\wiki2.png)

## 创建虚拟磁盘
qemu安装完成后，我们在安装路径下打开命令窗口输入命令：  
```shell
# qemu-img create -f qcow2 uos.img 80G
```  
## 启动虚拟机
使用命令直接启动虚拟机：  
```shell
# qemu-system-aarch64 -m 4000 -cpu cortex-a72 -smp 4,cores=4,threads=1,sockets=1 -M virt -bios D:\tools\qemu\QEMU_EFI.fd -net nic,model=pcnet -device nec-usb-xhci -device usb-kbd -device usb-mouse -device VGA -drive if=none,file=D:\tools\qemu\CentOS-8.3.2011-aarch64-dvd1.iso,id=cdrom,media=cdrom -device virtio-scsi-device -device scsi-cd,drive=cdrom -drive if=none,file=D:\tools\qemu\uos.img,id=hd0 -device virtio-blk-device,drive=hd0
```

其中的参数详解：

>* -m 4000 表示分配给虚拟机的内存最大4000MB
>* -cpu cortex-a72 指定CPU类型，还可以选择cortex-a53、cortex-a57等
>* -smp 4,cores=4,threads=1,sockets=1 指定虚拟机最大使用的CPU核心数等
>* -M virt 指定虚拟机类型为virt，具体支持的类型可以使用 qemu-system-aarch64 -M help 查看
>* -bios D:\tools\qemu\QEMU_EFI.fd  指定UEFI固件文件
>* -net nic,model=pcnet 启用网络功能
>* -device nec-usb-xhci -device usb-kbd -device usb-mouse  启用USB鼠标等设备
>* -device VGA 启用VGA视图，对于图形化的Linux这条很重要！
>* -drive if=none,file=D:\tools\qemu\CentOS-8.3.2011-aarch64-dvd1.iso,id=cdrom,media=cdrom 指定光驱使用镜像文件
>* -device virtio-scsi-device -device scsi-cd,drive=cdrom 指定光驱硬件类型
>* -drive if=none,file=D:\tools\qemu\uos.img  指定硬盘镜像文件  



然后就可以看到虚拟机启动：
![img](pictures\wiki\wiki3.png)


## 结语
这样一个aarch64的虚拟机就捣鼓完成了，如有不完善的地方后续还会更新