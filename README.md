# krkr2-tools

KrKr2 / KAG3 离线命令行工具集，从
[`kirikiroid2-web-web`](https://github.com/fenghengzhi/kirikiroid2-web-web)
精简而来。仅保留 Linux x64 命令行工具的构建路径，方便在 CI 上分发预编译二进制，
不附带 Web/Android/iOS/Mac 的运行时。

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

GitHub Actions 工作流 `.github/workflows/build-tools.yml` 会在每次 push / PR 时
跑一次 Linux x64 构建。它依赖 vcpkg 安装 `argparse / spdlog / fmt / zlib / expat`
等少量包，**不**会拉 cocos2dx / bullet3 / opencv 等只有 GUI 端才需要的依赖。

构建产物作为 artifact 上传，名字 `krkr2-tools-linux-x64`。

## 本地构建

```bash
# 系统依赖（Ubuntu 24.04 起即可）
sudo apt-get install -y bison ninja-build pkg-config build-essential cmake

# vcpkg
git clone https://github.com/microsoft/vcpkg.git ~/vcpkg
~/vcpkg/bootstrap-vcpkg.sh
export VCPKG_ROOT=~/vcpkg

# 配置与编译
cmake -S . -B out/linux \
  -G Ninja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake \
  -DENABLE_TESTS=OFF \
  -DBUILD_TOOLS=ON
cmake --build out/linux -j

ls out/linux/tools/*/
```

## 与上游的关系

源代码遵循上游 (`fenghengzhi/kirikiroid2-web-web`) 的 MIT 许可。本仓库只删除了
私有 `reference/` submodule，并在根目录加上了 Linux 构建脚本。

如需 Web/Android/iOS 端，请直接使用上游仓库。