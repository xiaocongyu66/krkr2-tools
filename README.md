# krkr2-tools

KrKr2 / KAG3 离线命令行工具集，从
[`kirikiroid2-web-web`](https://github.com/fenghengzhi/kirikiroid2-web-web)
精简而来。仅保留 Linux **arm64** 命令行工具的构建路径，方便在 CI 上分发预编译
二进制，可直接在 arm64 安卓设备的 Termux/Operit 终端里跑。

## 提供的工具 (`tools/`)

| 工具 | 作用 |
|-----|------|
| `xp3` | 解包 `.xp3` 归档，可对加密 XP3 应用 `tpm/xp3filter` 脚本 |
| `xp3pack` | 重新打包目录为 `.xp3` 归档 |
| `tjsdump` | 反编译 TJS2 字节码 (`.tjs` 编译产物) 到可读源 |
| `ksdec` | 解码加密的 KAG 脚本 (`.ks` / `.tjs`) |
| `mtndump` | dump motion (`.mtn`) 文件结构 |
| `motionsim` | 离线模拟 motion 播放，输出每帧每节点的 TSV |

## 在 CI 上构建

GitHub Actions 工作流 `.github/workflows/build-tools.yml` 在每次 push / PR 时
会跑一次 **Linux arm64** 构建（`ubuntu-24.04-arm` runner）。它依赖 vcpkg 安装
`argparse / spdlog / fmt / zlib / expat` 等少量包，**不**会拉 cocos2dx /
bullet3 / opencv 等只有 GUI 端才需要的依赖。

构建产物作为 artifact 上传，名字 `krkr2-tools-linux-arm64`。

## 本地构建（arm64 Linux）

```bash
# 系统依赖（Ubuntu 24.04 arm64）
sudo apt-get install -y bison ninja-build pkg-config build-essential cmake

# vcpkg
git clone https://github.com/microsoft/vcpkg.git ~/vcpkg
~/vcpkg/bootstrap-vcpkg.sh
export VCPKG_ROOT=~/vcpkg
export VCPKG_FORCE_SYSTEM_BINARIES=1   # arm64 必须

# 配置与编译
cmake -S . -B out/linux \
  -G Ninja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake \
  -DENABLE_TESTS=OFF \
  -DBUILD_TOOLS=ON
cmake --build out/linux --target xp3 xp3pack tjsdump ksdec mtndump motionsim -j

ls out/linux/tools/*/
```

## 与上游的关系

源代码遵循上游 (`fenghengzhi/kirikiroid2-web-web`) 的 MIT 许可。本仓库只删除了
私有 `reference/` submodule，并在根目录加上了 Linux arm64 构建脚本。

如需 Web/Android/iOS 端，请直接使用上游仓库。