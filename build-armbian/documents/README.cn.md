# Armbian 构建及使用方法

查看英文说明 | [View English description](README.md)

Github Actions 是 Microsoft 推出的一项服务，它提供了性能配置非常不错的虚拟服务器环境，基于它可以进行构建、测试、打包、部署项目。对于公开仓库可免费无时间限制地使用，且单次编译时间长达 6 个小时，这对于编译 Armbian 来说是够用的（我们一般在3小时左右可以完成一次编译工作）。分享只是为了交流经验，不足的地方请大家理解，请不要在网络上发起各种不好的攻击行为，也不要恶意使用 GitHub Actions。

# 目录

- [Armbian 构建及使用方法](#armbian-构建及使用方法)
- [目录](#目录)
  - [1. 注册自己的 Github 的账户](#1-注册自己的-github-的账户)
  - [2. 设置隐私变量 GITHUB\_TOKEN](#2-设置隐私变量-github_token)
  - [3. Fork 仓库并设置 GH\_TOKEN](#3-fork-仓库并设置-gh_token)
  - [4. 个性化 Armbian 固件定制文件说明](#4-个性化-armbian-固件定制文件说明)
  - [5. 编译固件](#5-编译固件)
    - [5.1 手动编译](#51-手动编译)
    - [5.2 定时编译](#52-定时编译)
    - [5.3 自定义默认固件配置](#53-自定义默认固件配置)
  - [6. 保存固件](#6-保存固件)
  - [7. 下载固件](#7-下载固件)
  - [8. 安装 Armbian 到 EMMC](#8-安装-armbian-到-emmc)
    - [8.1 Amlogic 系列安装方法](#81-amlogic-系列安装方法)
    - [8.2 Rockchip 系列安装方法](#82-rockchip-系列安装方法)
      - [8.2.1 Radxa-Rock5B 的安装方法](#821-radxa-rock5b-的安装方法)
        - [8.2.1.1 安装系统至 microSD](#8211-安装系统至-microsd)
        - [8.2.1.2 安装系统至 eMMC](#8212-安装系统至-emmc)
        - [8.2.1.3 安装系统至 NVMe](#8213-安装系统至-nvme)
      - [8.2.2 电犀牛 R66S 的安装方法](#822-电犀牛-r66s-的安装方法)
      - [8.2.3 电犀牛 R68S 的安装方法](#823-电犀牛-r68s-的安装方法)
      - [8.2.4 贝壳云的安装方法](#824-贝壳云的安装方法)
      - [8.2.5 我家云的安装方法](#825-我家云的安装方法)
  - [9. 编译 Armbian 内核](#9-编译-armbian-内核)
  - [10. 更新 Armbian 内核](#10-更新-armbian-内核)
  - [11. 安装常用软件](#11-安装常用软件)
  - [12. 常见问题](#12-常见问题)
    - [12.1 每个盒子的 dtb 和 u-boot 对应关系表](#121-每个盒子的-dtb-和-u-boot-对应关系表)
    - [12.2 LED 屏显示控制说明](#122-led-屏显示控制说明)
    - [12.3 如何恢复原安卓 TV 系统](#123-如何恢复原安卓-tv-系统)
      - [12.3.1 使用 armbian-ddbr 备份恢复](#1231-使用-armbian-ddbr-备份恢复)
      - [12.3.2 使用 Amlogic 刷机工具恢复](#1232-使用-amlogic-刷机工具恢复)
    - [12.4 设置盒子从 USB/TF/SD 中启动](#124-设置盒子从-usbtfsd-中启动)
    - [12.5 禁用红外接收器](#125-禁用红外接收器)
    - [12.6 启动引导文件的选择](#126-启动引导文件的选择)
    - [12.7 网络设置](#127-网络设置)
      - [使用 interfaces 设置网络](#使用-interfaces-设置网络)
      - [12.7.1 由 DHCP 动态分配 IP 地址](#1271-由-dhcp-动态分配-ip-地址)
      - [12.7.2 手动设置静态 IP 地址](#1272-手动设置静态-ip-地址)
      - [12.7.3 在 docker 中使用 OpenWrt 建立互通网络](#1273-在-docker-中使用-openwrt-建立互通网络)
      - [使用 NetworkManager 设置网络](#使用-networkmanager-设置网络)
      - [12.7.4 新增网络接口](#1274-新增网络接口)
      - [12.7.5 设置静态 IP 地址](#1275-设置静态-ip-地址)
      - [12.7.6 设置 DHCP 获取动态 IP 地址](#1276-设置-dhcp-获取动态-ip-地址)
      - [12.7.7 修改以太网卡 MAC 地址](#1277-修改以太网卡-mac-地址)
    - [12.8 如何添加开机启动任务](#128-如何添加开机启动任务)
    - [12.9 如何更新系统中的服务脚本](#129-如何更新系统中的服务脚本)
    - [12.10 如何制作安卓系统分区表](#1210-如何制作安卓系统分区表)
      - [12.10.1 安装 adb 工具包](#12101-安装-adb-工具包)
      - [12.10.2 查看安卓分区情况](#12102-查看安卓分区情况)
      - [12.10.3 制作安卓系统分区表](#12103-制作安卓系统分区表)
      - [12.10.4 使用安卓系统分区表](#12104-使用安卓系统分区表)
    - [12.11 如何制作 u-boot 文件](#1211-如何制作-u-boot-文件)
      - [12.11.1 提取 bootloader 和 dtb 文件](#12111-提取-bootloader-和-dtb-文件)
      - [12.11.2 制作 acs.bin 文件](#12112-制作-acsbin-文件)
      - [12.11.3 制作 u-boot 文件](#12113-制作-u-boot-文件)
    - [12.12 内存大小识别错误](#1212-内存大小识别错误)
    - [12.13 如何反编译 dtb 文件](#1213-如何反编译-dtb-文件)

## 1. 注册自己的 Github 的账户

注册自己的账户，以便进行固件个性化定制的继续操作。点击 github.com 网站右上角的 `Sign up` 按钮，根据提示注册自己的账户。

## 2. 设置隐私变量 GITHUB_TOKEN

设置 Github 隐私变量 `GITHUB_TOKEN` 。在固件编译完成后，我们需要上传固件到 Releases ，我们根据 Github 官方的要求设置这个变量，方法如下：
Personal center: Settings > Developer settings > Personal access tokens > Generate new token ( Name: GITHUB_TOKEN, Select: public_repo )。其他选项根据自己需要可以多选。提交保存，复制系统生成的加密 KEY 的值，先保存到自己电脑的记事本，下一步会用到这个值。图示如下：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/109418474-85032b00-7a03-11eb-85a2-759b0320cc2a.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/109418479-8b91a280-7a03-11eb-8383-9d970f4fffb6.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/109418483-90565680-7a03-11eb-8320-0df1174b0267.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/109418493-9815fb00-7a03-11eb-862e-deca4a976374.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/109418485-93514700-7a03-11eb-848d-36de784a4438.jpg width="300" />
</div>

## 3. Fork 仓库并设置 GH_TOKEN

现在可以 Fork 仓库了，打开仓库 https://github.com/ophub/amlogic-s9xxx-armbian ，点击右上的 Fork 按钮，复制一份仓库代码到自己的账户下，稍等几秒钟，提示 Fork 完成后，到自己的账户下访问自己仓库里的 amlogic-s9xxx-armbian 。在右上角的 `Settings` > `Secrets` > `Actions` > `New repostiory secret` ( Name: `GH_TOKEN`, Value: `填写刚才GITHUB_TOKEN的值` )，保存。并在左侧导航栏的 `Actions` > `General` > `Workflow permissions` 下选择 `Read and write permissions` 并保存。图示如下：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/109418568-0eb2f880-7a04-11eb-81c9-194e32382998.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/163203032-f044c63f-d113-4076-bf94-41f86c7dd0ce.png width="300" />
<img src=https://user-images.githubusercontent.com/68696949/109418573-15417000-7a04-11eb-97a7-93973d7479c2.jpg width="300" />
<img src=https://user-images.githubusercontent.com/68696949/167579714-fdb331f3-5198-406f-b850-13da0024b245.png width="300" />
<img src=https://user-images.githubusercontent.com/68696949/167585338-841d3b05-8d98-4d73-ba72-475aad4a95a9.png width="300" />
</div>

## 4. 个性化 Armbian 固件定制文件说明

固件编译的流程在 [.github/workflows/build-armbian.yml](../../.github/workflows/build-armbian.yml) 文件里控制，在 workflows 目录下还有其他 .yml 文件，实现其他不同的功能。编译固件时采用了 Armbian 官方的当前代码进行实时编译，相关参数可以查阅官方文档。

```yaml
- name: Compile Armbian [ ${{ env.ARMBIAN_BOARD }} ]
  id: compile
  run: |
    cd build/
    sudo chmod +x compile.sh
    sudo ./compile.sh BRANCH=${{ env.ARMBIAN_BRANCH }} RELEASE=${{ env.ARMBIAN_RELEASE }} BOARD=${{ env.ARMBIAN_BOARD }} \
                      BUILD_MINIMAL=no BUILD_DESKTOP=no HOST=armbian KERNEL_ONLY=no KERNEL_CONFIGURE=no \
                      CLEAN_LEVEL=make,debs COMPRESS_OUTPUTIMAGE=sha
    echo "status=success" >> ${GITHUB_OUTPUT}
```

## 5. 编译固件

固件编译的方式很多，可以设置定时编译，手动编译，或者设置一些特定事件来触发编译。我们先从简单的操作开始。

### 5.1 手动编译

在自己仓库的导航栏中，点击 Actions 按钮，再依次点击 Build armbian > Run workflow > Run workflow ，开始编译，等待大约 3 个小时，全部流程都结束后就完成编译了。图示如下：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/163203938-e7762b09-e6b8-4cf5-b1f1-9c67c1a29953.png width="300" />
<img src=https://user-images.githubusercontent.com/68696949/163204044-9c7a7429-47ee-4fce-b7dd-e217bebf6133.png width="300" />
<img src=https://user-images.githubusercontent.com/68696949/163204127-6b16b558-7e78-4c22-a28a-7b37b5a34fa3.png width="300" />
</div>

### 5.2 定时编译

在 [.github/workflows/build-armbian.yml](../../.github/workflows/build-armbian.yml) 文件里，使用 Cron 设置定时编译，5 个不同位置分别代表的意思为 分钟 (0 - 59) / 小时 (0 - 23) / 日期 (1 - 31) / 月份 (1 - 12) / 星期几 (0 - 6)(星期日 - 星期六)。通过修改不同位置的数值来设定时间。系统默认使用 UTC 标准时间，请根据你所在国家时区的不同进行换算。

```yaml
schedule:
  - cron: '0 17 * * *'
```

### 5.3 自定义默认固件配置

默认固件的配置信息记录在 [model_database.conf](../armbian-files/common-files/etc/model_database.conf) 文件里，将需要编译固件的 `BUILD` 值设置为 `yes`，并将对应的 `BOARD` 名称添加到 [rebuild](../../rebuild) 脚本的 `build_armbian` 数组里（数组中的 `BOARD` 名字要求唯一）。

在本地编译时通过 `-b` 参数指定，在 github.com 的 Actions 里编译时通过 `armbian_board` 参数指定。

一般情况下只需要编译具有通用性的固件即可，同家族的其他盒子可以参考配置文件信息表，通过修改 `/boot/uEnv.txt` 中的 `dtb` 值进行使用。

## 6. 保存固件

固件保存的设置也在 [.github/workflows/build-armbian.yml](../../.github/workflows/build-armbian.yml) 文件里控制。我们将编译好的固件通过脚本自动上传到 github 官方提供的 Releases 里面。

```yaml
- name: Upload Armbian Firmware to Release
  uses: ncipollo/release-action@main
  if: ${{ steps.build.outputs.status }} == 'success' && env.UPLOAD_RELEASE == 'true' && !cancelled()
  with:
    tag: Armbian_${{ env.FILE_DATE }}
    artifacts: ${{ env.FILEPATH }}/*
    allowUpdates: true
    token: ${{ secrets.GH_TOKEN }}
    body: |
      This is Armbian firmware for Amlogic s9xxx TV Boxes
      * Firmware information
      Default username: root
      Default password: 1234
```

## 7. 下载固件

从仓库首页右下角的 Release 版块进入，选择和自己盒子型号对应的固件。图示如下：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/163204798-0d98524c-73df-4876-8912-fcae2845fbba.png width="300" />
<img src=https://user-images.githubusercontent.com/68696949/163204879-4898babf-fa00-4e63-89ea-235129e2ce1d.png width="300" />
</div>

## 8. 安装 Armbian 到 EMMC

Amlogic 和 Rockchip 的安装方法不同。不同的设备具有不同的存储，有的设备使用外置 microSD 卡，有的带有 eMMC，有的支持使用 NVMe 等多种存储介质，根据设备不同，分别介绍其安装方法。首先在 [Releases](https://github.com/ophub/amlogic-s9xxx-armbian/releases) 里下载自己设备的 Armbian 系统，解压缩成 .img 格式备用。根据自己的设备，使用下面小结中不同的安装方法。

当安装完成后，将 Armbian 设备接入`路由器`，设备开机`2分钟`后，到路由器里查看设备名称为 Armbian 的 `IP`，使用 `SSH` 工具连接进行管理设置。默认用户名为 `root`，默认密码为 `1234`，默认端口为 `22`

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202972715-addcd695-970a-43d6-8a34-24a9c4bc80a2.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202972773-fc9e9ef9-69a7-4279-8329-6fad1cf2f5b9.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202972818-b72e18cd-17d1-4f9f-a0fa-b6c22eef041d.png width="600" />
</div>

### 8.1 Amlogic 系列安装方法

登录 Armbian 系统 (默认用户: root, 默认密码: 1234) → 输入命令：

```yaml
armbian-install
```

| 可选参数  | 默认值   | 选项     | 说明                |
| -------  | ------- | ------  | -----------------   |
| -m       | no      | yes/no  | 使用 Mainline u-boot |
| -a       | yes     | yes/no  | 使用 [ampart](https://github.com/7Ji/ampart) 分区表调整工具 |
| -l       | no      | yes/no  | List. 显示全部设备列表 |

举例: `armbian-install -m yes -a no`

### 8.2 Rockchip 系列安装方法

每个设备的安装方法不同，分别介绍如下。

#### 8.2.1 Radxa-Rock5B 的安装方法

Radxa-Rock5B 有 microSD/eMMC/NVMe 等多种存储介质可以选择，相应的安装方法也不同。下载 [rk3588_spl_loader_v1.08.111.bin 和 spi_image.img](../u-boot/rockchip/rock5b) 文件备用。下载 [RKDevTool](https://github.com/ophub/kernel/releases/download/tools/Radxa_rock5b_RKDevTool_Release_v2.96__DriverAssitant_v5.1.1.tar.gz) 工具及驱动备用。下载 [Rufus](https://rufus.ie/) 或者 [balenaEtcher](https://www.balena.io/etcher/) 写盘工具备用。

##### 8.2.1.1 安装系统至 microSD

使用 Rufus 或者 balenaEtcher 等工具将 Armbian 系统镜像写入 microSD 里，然后把写好固件的 microSD 插入设备即可使用。

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202972996-300f223b-f6f6-48af-86ca-bdc842e5017d.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202973216-85b1a21b-0763-4a36-8c57-84490e071fdd.png width="600" />
</div>

##### 8.2.1.2 安装系统至 eMMC

- 使用 USB 转 eMMC 读卡器安装：将 eMMC 模块与电脑连接，使用 Rufus 或者 balenaEtcher 等工具将 Armbian 系统镜像写入 eMMC 里，然后把写好固件的 eMMC 插入设备即可使用。
- 使用 Maskrom 模式安装：关闭开发板电源。按住金色按钮。将 USB-A 转 C 型电缆插入 ROCK 5B C 型端口，另一端插入 PC。松开金色按钮。检查 USB 设备提示找到一个 MASKROM 设备。右键单击列表的空白区域，然后选择加载 `rock-5b-emmc.cfg` 配置文件（配置文件和 RKDevTool 在同一个目录下）。将 `rk3588_spl_loader_v1.08.111.bin` 和 `Armbian.img` 按下图所示设置，选择写入即可。

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202954823-3d3b1509-eedc-4192-91eb-017269c7f896.png width="200" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202954901-c829d74d-c75a-4fd3-9bd0-aa3cdf2b77b4.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202954956-6646b7cf-6354-4dc8-b076-c3323cb89a43.png width="200" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202954998-c08514e2-8760-4c0f-b5f7-0d30be635aa5.png width="600" />
</div>

##### 8.2.1.3 安装系统至 NVMe

ROCK-5B 在主板上有一个 SPI 闪存，将引导加载程序安装到 SPI 闪存可以支持 SoC maskrom 模式不直接支持的其他启动介质（如 SATA、USB3 或 NVMe）。使用 NVMe 需要先写入 SPI 文件。方法如下：

关闭开发板电源。删除可启动设备，如MicroSD卡，eMMC模块等。按住金色（或某些开发板修订版上的银色）按钮。将 USB-A 转 C 型电缆插入 ROCK-5B C 型端口，另一端插入 PC。松开金色按钮。检查 USB 设备找到一个 MASKROM 设备。在列表框中右键选择加载配置，然后在资源管理文件夹中选择配置文件（配置文件和 RKDevTool 在同一个目录下），根据下图选择 `rk3588_spl_loader_v1.08.111.bin` 和 `spi_image.img` 文件，点击写入即可，如下图所示：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202954823-3d3b1509-eedc-4192-91eb-017269c7f896.png width="200" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202961420-8316c96c-2796-43ed-b5ed-2fa5bfa1ddff.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202954956-6646b7cf-6354-4dc8-b076-c3323cb89a43.png width="200" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202961447-49c0941a-e233-4b2a-b96b-b47636ce3cf2.png width="600" />
</div>

- 使用读卡器安装：将 M.2 NVMe SSD 插入 M.2 NVMe SSD 到 USB3.0 读卡器，以连接到主机。使用 Rufus 或者 balenaEtcher 等工具将 Armbian 系统镜像写入 NVMe 里，然后把写好固件的 NVMe 插入设备即可使用。
- 使用 microSD 卡安装：将 Armbian 系统镜像写入 microSD 卡，将 microSD 卡插入设备并启动，上传 `Armbian.img` 镜像文件到 microSD 卡，使用 `dd` 命令将 Armbian 镜像写入 NVMe 中，命令如下：

```Shell
dd if=armbian.img  of=/dev/nvme0n1  bs=1M status=progress
```

#### 8.2.2 电犀牛 R66S 的安装方法

使用 Rufus 或者 balenaEtcher 等工具将 Armbian 系统镜像写入 microSD 里，然后把写好固件的 microSD 插入设备即可使用。

#### 8.2.3 电犀牛 R68S 的安装方法

- 下载 [RKDevTool](https://github.com/ophub/kernel/releases/download/tools/FastRhino_r68s_RKDevTool_Release_v2.86___DriverAssitant_v5.1.1.tar.gz) 工具及驱动，解压并安装 DriverAssitant 驱动程序，打开 RKDevTool 工具备用。
- R68s 在关机状态下，先插入 USB 双公头线，然后按住 Recovery 键并插上 12V 电源，两秒之后松开 Recovery 键，刷机工具会`发现一个 LOADER 设备`。
- 在 RKDevTool 工具操作界面的空白处点右键，添加项。
- 地址是 `0x00000000`，名字是 `armbian`，路径点击右侧选择 `armbian.img` 系统文件。
- 选择添加的 armbian 一行外，取消其他行的选择，点击执行写入即可。

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202970301-d798677b-e875-4971-ac8f-ee58b2a1e686.png width="200" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202970405-cb68cb78-cd0f-43ee-b807-5e594ab73099.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202970488-5f18c574-c11f-486f-8fe8-002f3ba2f3f4.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202970577-87549acf-b98b-441f-bb31-e4fd6608108d.png width="600" />
</div>

#### 8.2.4 贝壳云的安装方法

方法转载自 [milton](https://www.cnblogs.com/milton/p/15391525.html) 的教程。刷机需要进入 Maskrom 模式。先断开所有连接，通过短接 CLK 和 GND（使用 TTL 的 GND, 或者旁边小按钮的 GND 均可）这两个触点，然后将 USB 连接到 PC 就会检测到 MASKROM 设备了。短接点位置如下：

<div style="width:100%;margin-top:40px;margin:5px;">
<img src=https://user-images.githubusercontent.com/68696949/202977817-fb12d291-47e2-47e4-88c3-e21f9ae57922.png width="600" /><br />
<img src=https://user-images.githubusercontent.com/68696949/202977900-50b4770d-8444-42a0-8478-3234043455bd.png width="600" />
</div>

打开 RKDevTool 刷机工具，右键添加项。

- 地址 `0xCCCCCCCC`, 名字 `Boot`, 路径[选择](../u-boot/rockchip/beikeyun) `rk3328_loader_v1.14.249.bin`。
- 地址 `0x00000000`, 名字 `system`, 路径选择要刷的 `Armbian.img` 固件。

点击执行写入即可。

#### 8.2.5 我家云的安装方法

方法转载自 [cc747](https://post.smzdm.com/p/a4wkdo7l/) 的教程。刷机需要进入 Maskrom 模式。使我家云处于断电状态，拔掉所有线。用 USB 双公头线，一头插入我家云的 USB2.0 接口，一头插入电脑。用回形针插进 Reset 孔，并按压住不松开。插入电源线。等待几秒钟，直到 RKDevTool 框的下方出现`发现一个LOADER设备`后才松开回形针。将 RKDevTool 切换到`高级功能`点击`进入Maskrom`按钮，提示`发现一个MASKROM设备`。右键添加项。

- 地址 `0xCCCCCCCC`, 名字 `Boot`, 路径[选择](../u-boot/rockchip/l1pro) `rk3328_loader.bin`。
- 地址 `0x00000000`, 名字 `system`, 路径选择要刷的 `Armbian.img` 固件。

点击执行写入即可。

## 9. 编译 Armbian 内核

支持在 Ubuntu20.04/22.04 或 Armbian 系统中编译内核。支持本地编译，也支持使用 GitHub Actions 云编译，具体方法详见 [内核编译说明](../../compile-kernel/README.cn.md)。

## 10. 更新 Armbian 内核

登录 Armbian 系统 → 输入命令：

```yaml
# 使用 root 用户运行 (sudo -i)
# 如果不指定其他参数，以下更新命令将更新到当前同系列内核的最新版本。
armbian-update
```

| 可选参数  | 默认值       | 选项        | 说明               |
| -------  | -------     | ------     | ----------------  |
| -k       | auto latest | [内核名称](https://github.com/ophub/kernel/tree/main/pub/stable)  | 设置更新内核名称  |
| -v       | stable      | stable/dev | 指定内核版本分支     |
| -m       | no          | yes/no     | 使用主线 u-boot     |

举例: `armbian-update -k 5.15.50 -v dev -m yes`

如果当前目录下有成套的内核文件，将使用当前目录的内核进行更新（更新需要的 4 个内核文件是 `header-xxx.tar.gz`, `boot-xxx.tar.gz`, `dtb-amlogic-xxx.tar.gz`, `modules-xxx.tar.gz`。其他内核文件不需要，如果同时存在也不影响更新，系统可以准确识别需要的内核文件）。如果当前目录没有内核文件，将从服务器查询并下载同系列的最新内核进行更新。在设备支持的可选内核里可以自由更新，如从 5.10.125 内核更新为 5.15.50 内核。

## 11. 安装常用软件

登录 Armbian 系统 → 输入命令：

```yaml
armbian-software
```

使用 `armbian-software -u` 命令可以更新本地的软件中心列表。根据用户在 [Issue](https://github.com/ophub/amlogic-s9xxx-armbian/issues) 中的需求反馈，逐步整合常用[软件](../armbian-files/common-files/usr/share/ophub/armbian-software/software-list.conf)，实现一键安装/更新/卸载等快捷操作。包括 `docker 镜像`、`桌面软件`、`应用服务` 等。详见更多[说明](armbian_software.md)。

## 12. 常见问题

在 Armbian 的使用中，一些可能遇到的常见问题汇总如下。

### 12.1 每个盒子的 dtb 和 u-boot 对应关系表

请查阅[说明](amlogic_model_database.md)

### 12.2 LED 屏显示控制说明

请查阅[说明](led_screen_display_control.md)

### 12.3 如何恢复原安卓 TV 系统

通常使用 armbian-ddbr 备份恢复，或者使用 Amlogic 刷机工具恢复原安卓 TV 系统。

#### 12.3.1 使用 armbian-ddbr 备份恢复

建议您在全新的盒子里安装 Armbian 系统前，先对当前盒子自带的原安卓 TV 系统进行备份，以便在需要恢复系统时使用。请从 `TF/SD/USB` 启动 Armbian 系统，输入 `armbian-ddbr` 命令，然后根据提示输入 `b` 进行系统备份，备份文件的存放路径为 `/ddbr/BACKUP-arm-64-emmc.img.gz` ，请下载保存。在需要恢复安卓 TV 系统时，将之前备份的文件上传至 `TF/SD/USB` 设备的相同路径下，输入 `armbian-ddbr` 命令，然后根据提示输入 `r` 进行系统恢复。

#### 12.3.2 使用 Amlogic 刷机工具恢复

- 一般情况下，重新插入电源，如果可以从 USB 中启动，只要重新安装即可，多试几次。

- 如果接入显示器后，屏幕是黑屏状态，无法从 USB 启动，就需要进行盒子的短接初始化了。先将盒子恢复到原来的安卓系统，再重新刷入 Armbian 系统。首先下载 [amlogic_usb_burning_tool](https://github.com/ophub/kernel/releases/tag/tools) 系统恢复工具并安装好。准备一条 [USB 双公头数据线](https://user-images.githubusercontent.com/68696949/159267576-74ad69a5-b6fc-489d-b1a6-0f8f8ff28634.png)，准备一个 [曲别针](https://user-images.githubusercontent.com/68696949/159267790-38cf4681-6827-4cb6-86b2-19c7f1943342.png)。

- 以 x96max+ 为例，在盒子的主板上确认 [短接点](https://user-images.githubusercontent.com/68696949/110590933-67785300-81b3-11eb-9860-986ef35dca7d.jpg) 的位置，下载盒子的 [Android TV 固件包](https://github.com/ophub/kernel/releases/tag/tools)。其他常见设备的安卓 TV 系统固件及对应的短接点示意图也可以在此[下载查看](https://github.com/ophub/kernel/releases/tag/tools)。

```
操作方法：

1. 打开刷机软件 USB Burning Tool:
   [ 文件 → 导入固件包 ]: X96Max_Plus2_20191213-1457_ATV9_davietPDA_v1.5.img
   [ 选择 ]：擦除 flash
   [ 选择 ]：擦除 bootloader
   点击 [ 开始 ] 按钮
2. 使用 [ 曲别针 ] 将盒子主板上的 [ 两个短接点进行短接连接 ]，
   并同时使用 [ USB 双公头数据线 ] 将 [ 盒子 ] 与 [ 电脑 ] 进行连接。
3. 当看到 [ 进度条开始走动 ] 后，拿走曲别针，不再短接。
4. 当看到 [ 进度条 100% ], 则刷机完成，盒子已经恢复成 Android TV 系统。
   点击 [ 停止 ] 按钮, 拔掉 [ 盒子 ] 和 [ 电脑 ] 之间的 [ USB 双公头数据线] 。
5. 如果以上某个步骤失败，就再来一次，直至成功。
   如果进度条没有走动，可以尝试插入电源。通长情况下不用电源支持供电，只 USB 双公头的供电即可满足刷机要求。
```

当完成恢复出厂设置，盒子已经恢复成 Android TV 系统，其他安装 Armbian 系统的操作，就和你之前第一次安装系统时的要求一样了，再来一遍即可。

### 12.4 设置盒子从 USB/TF/SD 中启动

- 把刷好固件的 USB/TF/SD 插入盒子。
- 开启开发者模式: 设置 → 关于本机 → 版本号 (如: X96max plus...), 在版本号上快速连击 5 次鼠标左键, 看到系统显示 `开启开发者模式` 的提示。
- 开启 USB 调试模式: 系统 → 高级选选 → 开发者选项 (设置 `开启USB调试` 为启用)。启用 `ADB` 调试。
- 安装 ADB 工具：下载 [adb](https://github.com/ophub/kernel/releases/tag/tools) 并解压，将 `adb.exe`，`AdbWinApi.dll`，`AdbWinUsbApi.dll` 三个文件拷⻉到 `c://windows/` 目录下的 `system32` 和 `syswow64` 两个文件夹内，然后打开 `cmd` 命令面板，使用 `adb --version` 命令，如果有显示就表示可以使用了。
- 进入 `cmd` 命令模式。输入 `adb connect 192.168.1.137` 命令（其中的 ip 根据你的盒子修改，可以到盒子所接入的路由器设备里查看），如果链接成功会显示 `connected to 192.168.1.137:5555`
- 输入 `adb shell reboot update` 命令，盒子将重启并从你插入的 USB/TF/SD 启动，从浏览器访问固件的 IP 地址，或者 SSH 访问即可进入固件。

### 12.5 禁用红外接收器

默认情况下启用对红外接收器的支持，但如果您将电视盒用作服务器，那么您可能希望禁用 IR 内核模块以防止错误地关闭您的盒子。 要完全禁用 IR，请添加以下行：

```yaml
blacklist meson_ir
```

至 `/etc/modprobe.d/blacklist.conf` 并重启。

### 12.6 启动引导文件的选择

- 目前已知的设备中，只有 `T95(s905x)` / `T95Z-Plus(s912)` 盒子需要使用 `/bootfs/extlinux/extlinux.conf` 文件。将固件写入 USB 后，双击打开，将固件自带的 `/boot/extlinux/extlinux.conf.bak` 文件名称中的 `.bak` 删除即可使用。当写入 eMMC 时 `armbian-install` 会自动检查，如果存在 `extlinux.conf` 文件，会自动创建。

- 其他设备只需要 `/boot/uEnv.txt` 即可启动，不要修改 `extlinux.conf.bak` 文件。

### 12.7 网络设置

#### 使用 interfaces 设置网络

网络配置文件 [/etc/network/interfaces](../armbian-files/common-files/etc/network/interfaces) 的内容如下：

```yaml
source /etc/network/interfaces.d/*

# Network is managed by Network manager
# You can choose one of the following two IP setting methods:
# Use # to disable another setting method


# 01. Enable dynamic DHCP to assign IP
auto eth0
iface eth0 inet dhcp
        hwaddress ether 12:34:56:78:9A:BC


# 02. Enable static IP settings(IP is modified according to the actual)
#auto eth0
#allow-hotplug eth0
#iface eth0 inet static
#address 192.168.1.100
#netmask 255.255.255.0
#gateway 192.168.1.6
#dns-nameservers 192.168.1.6


# 03. Docker install OpenWrt and communicate with each other
#allow-hotplug eth0
#no-auto-down eth0
#auto eth0
#iface eth0 inet manual
#
#auto macvlan
#iface macvlan inet dhcp
#        hwaddress ether 12:34:56:78:9a:bc
#        pre-up ip link add macvlan link eth0 type macvlan mode bridge
#        post-down ip link del macvlan link eth0 type macvlan mode bridge
#
#auto lo
#iface lo inet loopback
```

默认采用 DHCP 动态 分配 IP 的策略（方法1），由 Armbian 所接入的网络路由器自动分配 IP。如果想改为静态 IP，可以把设置方法 1 禁用或删除，启用方法 2 的静态 IP 设置。

#### 12.7.1 由 DHCP 动态分配 IP 地址

```yaml
source /etc/network/interfaces.d/*

auto eth0
iface eth0 inet dhcp
```

#### 12.7.2 手动设置静态 IP 地址

其中的 IP 和网关和 DNS 根据自己的网络情况修改。

```yaml
source /etc/network/interfaces.d/*

auto eth0
allow-hotplug eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 192.168.1.1
```

#### 12.7.3 在 docker 中使用 OpenWrt 建立互通网络

其中的 MAC 地址根据自己的需要修改。

```yaml
source /etc/network/interfaces.d/*

allow-hotplug eth0
no-auto-down eth0
auto eth0
iface eth0 inet manual

auto macvlan
iface macvlan inet dhcp
        pre-up ip link add macvlan link eth0 type macvlan mode bridge
        post-down ip link del macvlan link eth0 type macvlan mode bridge

auto lo
iface lo inet loopback
```

#### 使用 NetworkManager 设置网络

#### 12.7.4 新增网络接口

新增网络接口 eth0 并立即生效（默认设置 DHCP 获取动态 IP 地址），其中 MYETH 根据自己的情况修改。

```yaml
MYETH=eth0
nmcli connection add \
con-name $MYETH type ethernet ifname $MYETH \
ipv6.method disabled
nmcli connection up $MYETH
```

#### 12.7.5 设置静态 IP 地址

修改网络接口 eth0 为静态 IP 地址并立即生效，其中 MYETH、 IP、 GW、 DNS 根据自己的网络情况修改。

```yaml
MYETH=eth0
nmcli connection modify $MYETH \
ipv4.method manual \
ipv4.addresses 192.168.67.167/24 \
ipv4.gateway 192.168.67.1 \
ipv4.dns 119.29.29.29,223.5.5.5 \
ipv6.method disabled
nmcli connection up $MYETH
```

#### 12.7.6 设置 DHCP 获取动态 IP 地址

修改网络接口 eth0 为 DHCP 获取动态 IP 地址并立即生效，其中 MYETH 根据自己的情况修改。

```yaml
MYETH=eth0
nmcli connection modify $MYETH \
ipv4.method auto \
ipv6.method disabled
nmcli connection up $MYETH
```

#### 12.7.7 修改以太网卡 MAC 地址

修改网络接口 eth0 的 MAC 地址并立即生效，以解决局域网 MAC 地址冲突问题，其中 MYETH、MYMAC 根据自己的情况修改。

```yaml
MYETH=eth0
MYMAC=12:34:56:78:9A:BC
nmcli connection modify $MYETH \
ethernet.cloned-mac-address $MYMAC
nmcli connection up $MYETH
```

* 新增、设置或修改部分网络参数，以太网卡会断开现有连接，并重新连接网络。
* 以上使用 NetworkManager 设置网络，默认禁用 ipv6 ，如需使用 ipv6 设置并启用即可。
* 由于软硬件环境不同（盒子，系统，网络设备等），生效所需时间 2-15 秒左右。更长时间未生效的建议检查软硬件环境。

### 12.8 如何添加开机启动任务

系统中已经添加了自定义开机启动任务脚本文件，在 Armbian 系统中的路径是 [/etc/custom_service/start_service.sh](../armbian-files/common-files/etc/custom_service/start_service.sh) 文件，可以根据个人需求在该脚本中自定义添加相关任务。

### 12.9 如何更新系统中的服务脚本

使用 `armbian-sync` 命令可以一键将本地系统中的全部服务脚本更新到最新版本。

### 12.10 如何制作安卓系统分区表

在 12.10 - 12.11 中制作安卓系统分区表及 u-boot 的方法整理自 [unifreq](https://github.com/unifreq) 在社群中指导大家制作相关文件的教学聊天内容，制作源码在他的仓库中。

我们将 Armbian 系统写入 eMMC 系统时，需要首先确认设备的安卓系统分区表，确保将数据写入至安全区域，尽量不要破坏安卓系统分区表，以免造成系统无法启动等问题。如果写入了不安全的区域，会无法启动，或出现类似下面的错误：

<img width="800" alt="image" src="https://user-images.githubusercontent.com/68696949/187075834-4ac40263-52ae-4538-a4b1-d6f0d5b9c856.png">

以下是详细的手动操作过程，其中 `12.10.2 - 12.10.3` 中的提取工作，也可以使用一键脚本完成：[get_android_system_partition_table_information.tar.xz](https://github.com/ophub/kernel/releases/download/tools/get_android_system_partition_table_information.tar.xz)，使用方法在一键脚本文件的备注里。

#### 12.10.1 安装 adb 工具包

adb 工具包是由 Google 开发的一款安卓系统辅助工具，可以帮助用户管理安卓设备，使用它进行刷机、安装相关程序等。点此 [下载 adb](https://github.com/ophub/kernel/releases/download/tools/adb.tar.gz) 工具包，然后在 Windows 系统下，将 `adb.exe`，`AdbWinApi.dll` 和 `AdbWinUsbApi.dll` 三个文件拷⻉到 `c://windows/` 目录下的 `system32` 和 `syswow64` 两个文件夹内，在电脑`开始菜单`的`运行`中输入 `cmd` 回车，打开 `cmd` 面板，输入执行 `adb --version` 命令，如果有显示就表示可以使用了。

#### 12.10.2 查看安卓分区情况

我们将电视盒子插入网线、电源、显示器开机，正常进入安卓 TV 系统桌面后，在它的网络信息里查看其 IP 信息。为方便说明，下面以 192.168.1.111 作为安卓电视盒子的 IP 进行操作说明。在 `cmd` 面板中，依次输入下面的命令并回车执行，先看下分区情况：

```shell
adb connect 192.168.1.111
adb shell
cd /dev/block
ls -l | grep 179 | sort -t6
```

<img width="415" alt="image" src="https://user-images.githubusercontent.com/68696949/187029647-48b9ecbc-3932-47a4-b0a8-d781508e62d6.png">

#### 12.10.3 制作安卓系统分区表

依次输入一下命令，将以下几个位置的分区信息文件保存下来：

```shell
cat /proc/partitions >/data/local/partitions.txt
cat /proc/ntd >/data/local/ntd.txt
ls -l /dev/block >/data/local/block.txt
```

<img width="310" alt="image" src="https://user-images.githubusercontent.com/68696949/187029771-034f6dc0-78a4-4e9d-b50f-2fbc6f213ec0.png">

在本地 window 电脑的 C 盘根目录下创建名称为 `mybox` 的文件夹，在 `cmd` 面板中依次输入以下命令，把电视盒子里的文件下载到本地电脑：

```shell
adb pull /data/local/partitions.txt C:\mybox
adb pull /data/local/ntd.txt C:\mybox
adb pull /data/local/block.txt C:\mybox
```

打开 excel 模板 [android_partition_table_template.xlsx](android_partition_table_template.xlsx)，我们根据上面得到的三个分区信息文件把数据套进去，得到设备最终的安卓系统分区表。通过分类，确定了`混合区域`和`安全区域`。其中`混合区域`的 cache 中可以作为 Armbian 或 OpenWrt 系统的 `boot` 分区的使用，`安全区域`可以作为 `rootfs` 分区使用。

![Snip20220827_1](https://user-images.githubusercontent.com/68696949/187031866-ddc0f76a-810a-40ef-99d3-1484bd4092d6.png)

#### 12.10.4 使用安卓系统分区表

根据`混合区域`和`安全区域`的具体位置，在 [armbian-install](../armbian-files/common-files/usr/sbin/armbian-install) 中添加对应的分区信息。以我们制作的 tx3 盒子的安卓系统分区表为例。跳过 `68 MiB（BLANK1=68）` 的不安全区域；它的 cache 分区共有 1120 MiB 可以使用，但一般 `BOOT` 分区设置 `256 MiB（BOOT=256）` 已经够用了，其他容量弃用了；`混合区域`共有 1350 MiB 空间，所以 `BLANK2` 的值为 `1350-68-256=1026（BLANK2=1026）` MiB。结果如下：

```shell
# Set partition size (Unit: MiB)
elif [[ "${AMLOGIC_SOC}" == "s905x3" ]]; then
    BLANK1="68"
    BOOT="256"
    BLANK2="1026"
```

### 12.11 如何制作 u-boot 文件

u-boot 文件是引导系统正常启动的重要文件。

#### 12.11.1 提取 bootloader 和 dtb 文件

提取需要使用 HxD 软件。可以从 [官网下载链接](https://mh-nexus.de/en/downloads.php?product=HxD20) 或 [备份下载链接](https://github.com/ophub/kernel/releases/download/tools/HxDSetup.2.5.0.0.zip) 获取安装。

在 `cmd` 面板中依次执行以下命令提取相关文件，并下载到本地电脑。

```shell
# 使用 adb 工具进入盒子
adb connect 192.168.1.111
adb shell

# 导出 bootloader 命令
dd if=/dev/block/bootloader of=/data/local/bootloader.bin

# 导出 dtb 命令
cat /dev/dtb >/data/local/mybox.dtb

# 导出 gpio 命令
cat /sys/kernel/debug/gpio >/data/local/mybox_gpio.txt

# 依次把 bootloader、dtb 和 gpio 文件都下载到本地电脑C盘根目录下的 mybox 文件夹
adb pull /data/local/bootloader.bin C:\mybox
adb pull /data/local/mybox.dtb C:\mybox
adb pull /data/local/mybox_gpio.txt C:\mybox
```

#### 12.11.2 制作 acs.bin 文件

主线 u-boot 最重要的是 acs.bin，用于初始化内存的部分，原厂 u-boot 位于固件最前面的 4MB 位置。使用刚才获得的 `bootloader.bin` 文件提取 `acs.bin` 文件。

打开 HxD 软件，打开上面导出的 `bootloader.bin` 文件，`右键 - 选择范围`，起始位置 `F200`，长度 `1000`，选`十六进制`。

<img width="400" alt="image" src="https://user-images.githubusercontent.com/68696949/187056711-1b58ce71-2a7d-4e9b-a976-e5f278edaa53.png">

复制选择的结果，然后新建文件，插入式粘贴，警告忽略，另存为 acs.bin 文件。

<div style="width:100%;margin-top:40px;margin:5px;">
<img width="600" alt="image" src="https://user-images.githubusercontent.com/68696949/187056725-0a0e60af-6a21-4a6b-a2d5-f3d46b438a6a.png">
<img width="600" alt="image" src="https://user-images.githubusercontent.com/68696949/187056827-8419c738-3428-473e-9a95-ab7270170d98.png">
<img width="600" alt="image" src="https://user-images.githubusercontent.com/68696949/187056852-9f62f16a-f7f1-4c34-a2c2-78358d198f9a.png">
</div>

如果是锁了 bootloader 的话这个区域的代码是是乱码就没用了。正常的应该像上图中这样有很多 `0` ，有 `cfg` 会连续出现几次，中间会出现 `ddr` 相关的字样，这种正常代码就是可以使用的。

#### 12.11.3 制作 u-boot 文件

制作 u-boot 需要 https://github.com/unifreq/amlogic-boot-fip 和 https://github.com/unifreq/u-boot 这两个源码库，编译自己盒子的两个 u-boot 文件。

在 amlogic-boot-fip 源码里面每个机型只有 acs.bin 这个文件是不同的，其它的文件都可以通用。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/68696949/187057209-c4716384-46ef-4922-9710-8da7ae6db1e4.png">

制作 u-boot 的方法详见 https://github.com/unifreq/u-boot/tree/master/doc/board/amlogic 里的具体说明，选择自己设备的型号进行编译测试。

根据 [unifreq](https://github.com/unifreq) 的方法制作 u-boot 需要用到盒子的 acs.bin，dts 和 config 文件。其中安卓系统导出来的 dts 不能直接转换成 Armbian 的格式，需要自己编写一个对应的 dts 文件。根据自己设备具体硬件上的区别部分，比如开关、led、电源控制、tf卡、sdio wifi模块等，使用内核源码库中相似的 [dts](https://github.com/unifreq/linux-5.15.y/tree/main/arch/arm64/boot/dts/amlogic) 文件进行修改制作。

以制作 X96Max Plus 的 u-boot 为例：

```shell
~/make-uboot
    ├── amlogic-boot-fip
    │   ├── x96max-plus                                     # 自己创建目录
    │   │   ├── asc.bin                                     # 自己制作源文件
    │   │   └── other-copy-files...                         # 复制其他目录的文件
    │   │
    │   ├── other-source-directories...
    │   └── other-source-files...
    │
    └── u-boot
        ├── configs
        │   └── x96max-plus_defconfig                       # 自己制作源文件
        ├── arch
        │   └── arm
        │       └── dts
        │           ├── meson-sm1-x96-max-plus-u-boot.dtsi  # 自己制作源文件
        │           ├── meson-sm1-x96-max-plus.dts          # 自己制作源文件
        │           └── Makefile                            # 编辑
        ├── fip
        │   ├── u-boot.bin                                  # 生成文件
        │   └── u-boot.bin.sd.bin                           # 生成文件
        ├── u-boot.bin                                      # 生成文件
        │
        ├── other-source-directories...
        └── other-source-files...
```

- 下载 [amlogic-boot-fip](https://github.com/unifreq/amlogic-boot-fip) 源码。在根目录创建 [x96max-plus](https://github.com/unifreq/amlogic-boot-fip/tree/master/x96max-plus) 目录，里面的文件除了自己制作的 `asc.bin` 文件外，其他文件可以从其他目录下复制。
- 下载 [u-boot](https://github.com/unifreq/u-boot) 源码。制作对应的 [x96max-plus_defconfig](https://github.com/unifreq/u-boot/blob/master/configs/x96max-plus_defconfig) 文件放入 [configs](https://github.com/unifreq/u-boot/tree/master/configs) 目录。制作对应的 [meson-sm1-x96-max-plus-u-boot.dtsi](https://github.com/unifreq/u-boot/blob/master/arch/arm/dts/meson-sm1-x96-max-plus-u-boot.dtsi) 和 [meson-sm1-x96-max-plus.dts](https://github.com/unifreq/u-boot/blob/master/arch/arm/dts/meson-sm1-x96-max-plus.dts) 文件放入 [arch/arm/dts](https://github.com/unifreq/u-boot/tree/master/arch/arm/dts) 目录，并编辑此目录中的 [Makefile](https://github.com/unifreq/u-boot/blob/master/arch/arm/dts/Makefile) 文件，添加 `meson-sm1-x96-max-plus.dtb` 文件的索引。
- 进入 u-boot 源码目录根目录下，根据文档 https://github.com/unifreq/u-boot/blob/master/doc/board/amlogic/x96max-plus.rst 中的步骤操作。

最终生成的文件有两类：在 u-boot 根目录下的 `u-boot.bin` 文件是 `/boot` 目录下使用的不完整版 u-boot（对应仓库中的 [overload](../amlogic-u-boot/overload) 目录）；在 `fip` 目录下的 `u-boot.bin` 和 `u-boot.bin.sd.bin` 是 `/usr/lib/u-boot/` 目录下使用的完整版 u-boot 文件（对应仓库中的 [bootloader](../amlogic-u-boot/bootloader/) 目录），完整版的两个文件相差 512 字节，大的那个是填充了 512 字节的 0 在前面。

<div style="width:100%;margin-top:40px;margin:5px;">
<img width="400" alt="image" src="https://user-images.githubusercontent.com/68696949/189039426-c127631f-77ca-4fcb-9fb6-4220045d712b.png">
<img width="400" alt="image" src="https://user-images.githubusercontent.com/68696949/189029320-e43a4cc9-b4b5-4de4-92fe-b17bd29020d0.png">
</div>


💡提示：在写入 eMMC 进行测试前，请先查看 12.3 的救砖方法。务必掌握短接点位置，有原厂 .img 格式的安卓系统文件，并进行过短接刷机测试，确保救砖方法都已经掌握的情况下再进行写入测试。

### 12.12 内存大小识别错误

如果内存大小识别不正确（4G内存识别为1-2G是不正常，识别为3.7G是正常），可以尝试手动复制一份 `/boot/UBOOT_OVERLOAD` 文件（注意是`复制`一份，`不要改名`，改名后安装与更新等操作后将无法启动），在 `USB` 中使用时另存为 `/boot/u-boot.ext`，在 `eMMC` 中使用时另存为 `/boot/u-boot.emmc`。

除了想尝试解决内存的问题外，不要手动复制 u-boot 文件，添加不正确会导致无法启动以及出现各种问题。

### 12.13 如何反编译 dtb 文件

有些新设备不在目前的支持列表（或有变异体），可以通过反编译，调整相关参数进行尝试。

```shell
# 安装依赖
sudo apt install device-tree-compiler

# 1. 反编译命令（使用 dtb 文件生成 dts 源码）
dtc -I dtb -O dts -o xxxxx.dts xxxxxx.dtb

# 2. 编译命令（使用 dts 编译生成 dtb 文件）
dtc -I dts -O dtb -o xxxxxxx .dtb xxxxxxxx.dts
```
