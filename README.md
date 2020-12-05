# For Proxmox

下载编译后的rootfs.tar.gz文件至promox的容器模板后，ssh进入promox并切换为ssh
```
pct create 201 local:vztmpl/openwrt-x86-64-generic-rootfs.tar.gz --rootfs local:1024 --ostype unmanaged --hostname lede --arch amd64 --cores 4 --memory 2048 --swap 2048
pct enter 201
passwd
```
修改密码后可web登录

## 挂载共享目录
```
pct set 100 -mp0 /home/pve/Downloads/op,mp=/op,quota=0,replicate=0,ro=0,
```
出于安全性考虑，禁止含有文件链接。
| 参数| 含义|
|---- |----|
|quota=<boolean>| 在容器内启用用户空间配额（对基于zfs子卷的存储卷无效)|
|replicate=<boolean> (default = 1) |  	卷是否被可以被调度任务复制|
|ro=<boolean>  |                       	用于标识只读挂载点 |
| shared=<boolean> (default = 0)   |   	用于标识当前存储卷挂载点对所有节点可见 |


参考: proxmox 6.2 文档 11.3.4挂载点

## 其他

若提示UDP转发未开，则
```
echo 'xt_TPROXY' > /etc/modules-load.d/tproxy.conf
```

如需官方原版，也可直接根据以下项目编译
https://github.com/mikma/lxd-openwrt

## 旁路由的设置
OPENWRT默认物理网口绑定到LAN区域（也就是桥接），而LAN区域默认拥有路由远程管理权限。但在单臂路由时，由于一个物理网口同时要收发WAN区域流量和LAN区域流量，所以物理网口（eth0）需要取消桥接LAN。在eth0取消桥接LAN后，由于LAN区域已经不再有实际物理网口绑定，原虚拟网卡LAN的地址自动赋值给物理网口eth0，所以并不妨碍我们远程管理路由。但硬路由LAN桥接的eth0网卡有四个子接口（eth0.1-eth0.4），在取消桥接LAN后系统无法给4个子网卡自动设置IP，网卡没有设置IP，所以我们就不能远程管理路由了

ref: https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=4031598

# Actions-OpenWrt

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=LICENSE)](https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE)
![GitHub Stars](https://img.shields.io/github/stars/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Stars&logo=github)
![GitHub Forks](https://img.shields.io/github/forks/P3TERX/Actions-OpenWrt.svg?style=flat-square&label=Forks&logo=github)

Build OpenWrt using GitHub Actions

[Read the details in my blog (in Chinese) | 中文教程](https://p3terx.com/archives/build-openwrt-with-github-actions.html)

## Usage

- Click the [Use this template](https://github.com/P3TERX/Actions-OpenWrt/generate) button to create a new repository.
- Generate `.config` files using [Lean's OpenWrt](https://github.com/coolsnowwolf/lede) source code. ( You can change it through environment variables in the workflow file. )
- Push `.config` file to the GitHub repository.
- Select `Build OpenWrt` on the Actions page.
- Click the `Run workflow` button.
- When the build is complete, click the `Artifacts` button in the upper right corner of the Actions page to download the binaries.

## Tips

- It may take a long time to create a `.config` file and build the OpenWrt firmware. Thus, before create repository to build your own firmware, you may check out if others have already built it which meet your needs by simply [search `Actions-Openwrt` in GitHub](https://github.com/search?q=Actions-openwrt).
- Add some meta info of your built firmware (such as firmware architecture and installed packages) to your repository introduction, this will save others' time.

## License

[MIT](https://github.com/P3TERX/Actions-OpenWrt/blob/main/LICENSE) © P3TERX
