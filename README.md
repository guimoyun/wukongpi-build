# wukongpi_build

基于 **Armbian build framework 26.11.0-trunk**（2026-09-30 快照）裁剪的 WuKong Pi 专用构建仓库，仅保留全志 H3/H2+（sun8i）相关内容。

- 板型：WuKong Pi（Allwinner H2+，sun8i 家族）
- u-boot：v2026.07（板级补丁：`patch/u-boot/v2026.07-sunxi/board_wukongpi/`）
- 内核：legacy 6.12 / current 6.18 / edge 7.2（dts 已移植到各版本补丁目录）

### Basic requirements

- x86_64 or aarch64 machine with at least 2GB of memory and ~35GB of disk space for a virtual machine, container or bare metal installation
- Ubuntu Jammy 22.04.x amd64 or aarch64 for native building
- Superuser rights (configured sudo or root access).

### Simply start with the build script

```bash
apt-get -y install git
git clone https://github.com/Timfu2019/wukongpi_build.git
cd wukongpi_build
./compile.sh
```

非交互构建示例（Debian Bookworm、server 镜像、current 内核）：

```bash
./compile.sh build BOARD=wukongpi BRANCH=current BUILD_DESKTOP=no BUILD_MINIMAL=no \
  RELEASE=bookworm KERNEL_CONFIGURE=no
```

### 目录说明（裁剪后）

| 路径 | 内容 |
|---|---|
| `config/boards/wukongpi.conf` | 板型定义 |
| `config/sources/families/sun8i.conf` | H3/H2+ 家族配置 |
| `config/kernel/linux-sunxi-*.config` | 32 位 sunxi 内核配置（legacy/current/edge） |
| `patch/kernel/archive/sunxi-6.12` | legacy 内核补丁（含 wukongpi dts 补丁） |
| `patch/kernel/archive/sunxi-6.18/dt_32` | current 内核 dts 直放目录 |
| `patch/kernel/archive/sunxi-7.2/dt_32` | edge 内核 dts 直放目录 |
| `patch/u-boot/v2026.07-sunxi/board_wukongpi` | u-boot 板级补丁（defconfig/dts/Makefile） |

原始 Armbian 框架文档见 <https://docs.armbian.com/Developer-Guide_Build-Preparation/>。
