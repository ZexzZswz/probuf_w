# 在 Windows 上通过 CMake 一键构建本地 protobuf（依赖本地 Abseil）

本说明基于你已经拥有以下本地源码与依赖：
- `C:\Users\ssq\Desktop\proto_windows\protobuf-main`
- `C:\Users\ssq\Desktop\proto_windows\abseil-cpp-master`
- `C:\Users\ssq\Desktop\proto_windows\zlib-1.3.1`（可选，当前默认关闭）

`protobuf-driver/CMakeLists.txt` 已封装了构建配置，默认产出静态库（/MD），通过 `add_subdirectory` 直接使用本地 Abseil 源码，默认关闭 `protoc`、upb 和安装导出，避免 CRT/链接冲突。如果要打开可执行生成或安装，请见下方“可选项”。

## 快速使用
```powershell
cd C:\Users\ssq\Desktop\proto_windows\protobuf-driver
cmake -B build -S .
cmake --build build --config Release
```

构建输出位置：
- 构建目录：`protobuf-driver/build`
- Abseil 库：`build/abseil-build/...`
- protobuf 库：`build/protobuf-build/...`

## 关键默认配置（可在 CMake GUI/ccmake 中修改）
- 运行时：`CMAKE_MSVC_RUNTIME_LIBRARY=MultiThreadedDLL`（全程 /MD）
- C++ 标准：`CMAKE_CXX_STANDARD=17`（Abseil 要求）
- 本地依赖路径：
  - Abseil 源码：`C:/Users/ssq/Desktop/proto_windows/abseil-cpp-master`（通过 `add_subdirectory` 直接使用）
  - protobuf 源码：`C:/Users/ssq/Desktop/proto_windows/protobuf-main`
  - zlib 安装路径：`C:/Users/ssq/Desktop/proto_windows/zlib-1.3.1/install`（当前未使用）
  - 安装前缀：`C:/Users/ssq/Desktop/proto_windows/protobuf-main/install`（当前未使用）
- protobuf 选项（封装在 driver 中的缓存变量）：
  - `protobuf_BUILD_TESTS=OFF`
  - `protobuf_BUILD_SHARED_LIBS=OFF`（静态库）
  - `protobuf_WITH_ZLIB=OFF`（当前关闭，避免缺失 zlib 安装路径错误）
  - `protobuf_LOCAL_DEPENDENCIES_ONLY=ON`
  - `protobuf_INSTALL=OFF`（当前关闭，避免 Abseil export 报错）
  - `protobuf_BUILD_PROTOC_BINARIES=OFF`（可执行默认关闭）
  - `protobuf_BUILD_LIBUPB=OFF`

## 在你自己的 C/C++ 项目中链接
- include 路径：`protobuf-main/src`（或构建后的 `build/protobuf-build/src`）
- lib 路径：`build/protobuf-build` 下的相应配置目录（如 `Release`）
- 链接库：至少 `libprotobuf.lib`（如需精简可用 `libprotobuf-lite.lib`）
- 保持 /MD 运行时以与构建结果一致。

## 可选项

### 需要 protoc/upb 可执行时
在配置阶段把下列变量改为 ON：
- `protobuf_BUILD_PROTOC_BINARIES=ON`
- 如果需要 upb 生成器，再打开 `protobuf_BUILD_LIBUPB=ON`

### 需要 zlib 压缩支持时
1. 先单独构建并安装 zlib 到 `C:/Users/ssq/Desktop/proto_windows/zlib-1.3.1/install`
2. 确认 `install/include` 和 `install/lib` 目录存在
3. 在 `CMakeLists.txt` 中将 `protobuf_WITH_ZLIB` 改为 `ON`
4. 重新执行 `cmake -B build -S .` 和 `cmake --build build --config Release`

### 需要安装 protobuf/Abseil 时
- 将 `protobuf_INSTALL` 改为 `ON`（注意：可能需要额外配置 Abseil 的 export 规则）
- 如需同时安装 Abseil，可开启 `ABSL_ENABLE_INSTALL ON`

注意：务必保持 Abseil 与 protobuf 统一使用 /MD；不要混入 /MT 目标，也不要添加 `/NODEFAULTLIB`。如遇 CRT 或缺符号冲突，可优先考虑直接使用官方预编译的 `protoc.exe` 搭配本地库。

