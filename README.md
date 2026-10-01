# wukongpi_build

[![WuKong Pi Build](https://github.com/guimoyun/wukongpi-build/actions/workflows/wukongpi-build.yml/badge.svg)](https://github.com/guimoyun/wukongpi-build/actions/workflows/wukongpi-build.yml)

基于 **Armbian build framework 26.11.0-trunk**（2026-09-30 快照）裁剪的 WuKong Pi 专用构建仓库，仅保留全志 H3/H2+（sun8i）相关内容。

| 项目 | 值 |
|---|---|
| 仓库版本 | **v1.0.0** |
| 构建产物版本（`VERSION`） | `26.11.0-trunk-wukongpi.1` |
| Armbian 基础版本 | 26.11.0-trunk（upstream `7cbc28173`） |
| 板型 | WuKong Pi（Allwinner H2+，sun8i 家族） |
| u-boot | v2026.07（板级补丁 `patch/u-boot/v2026.07-sunxi/board_wukongpi/`） |
| 内核 | legacy 6.12 / current 6.18 / edge 7.2（dts 已移植并实测编译通过） |

### 自动化构建

推送到 `main` 或打 `v*` 标签会自动构建并上传 deb；打标签时自动发布到 Releases：

```bash
git tag v1.0.1 && git push origin v1.0.1     # 触发构建 + Release
```

产出：`linux-u-boot-wukongpi-current`、`linux-{image,dtb,headers,libc-dev}-{legacy,current,edge}-sunxi` 的 armhf deb 包。

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

### 网络受限环境（GitHub 不可达时）

框架所有源码/工具下载均支持镜像参数（已在受限网络实测通过完整构建）：

```bash
# git 克隆走 ghfast.top 镜像（一次性配置）
git config --global url."https://ghfast.top/https://github.com/".insteadOf "https://github.com/"

# 构建参数：u-boot 走 denx，ORAS 缓存走南京大学镜像
EXPERT=yes GHCR_MIRROR=nju UBOOT_MIRROR=denx \
  ./compile.sh build BOARD=wukongpi BRANCH=current BUILD_DESKTOP=no \
  RELEASE=bookworm KERNEL_CONFIGURE=no
```

说明：内核源码默认走 git.kernel.org（直连即可）；GitHub releases 的工具下载（oras/shellcheck/bat）首次需经镜像预置到 `cache/tools/`。

### 单独编译 u-boot / 内核（调试用）

```bash
./compile.sh artifact WHAT=uboot BOARD=wukongpi BRANCH=current   # 仅 u-boot
./compile.sh artifact WHAT=kernel BOARD=wukongpi BRANCH=legacy   # 仅内核（可换 current/edge）
```

### 目录说明（裁剪后）

| 路径 | 内容 |
|---|---|
| `config/boards/wukongpi.conf` | 板型定义 |
| `config/sources/families/sun8i.conf` | H3/H2+ 家族配置 |
| `config/kernel/linux-sunxi-*.config` | 32 位 sunxi 内核配置（legacy/current/edge） |
| `patch/kernel/archive/sunxi-6.12/dt_32` | legacy 内核 dts 直放目录（已启用 arm32 自动 Makefile 注册） |
| `patch/kernel/archive/sunxi-6.18/dt_32` | current 内核 dts 直放目录 |
| `patch/kernel/archive/sunxi-7.2/dt_32` | edge 内核 dts 直放目录 |
| `patch/u-boot/v2026.07-sunxi/board_wukongpi` | u-boot 板级补丁（defconfig/dts/Makefile） |

原始 Armbian 框架文档见 <https://docs.armbian.com/Developer-Guide_Build-Preparation/>。
