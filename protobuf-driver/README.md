# 在 Windows 上通过 CMake 一键构建本地 protobuf（依赖本地 Abseil/zlib）

本说明基于你已经拥有以下本地源码与依赖：
- `C:\Users\ssq\Desktop\proto_new\protobuf-33.2`
- `C:\Users\ssq\Desktop\proto_new\abseil-cpp-master`
- `C:\Users\ssq\Desktop\proto_new\zlib-1.3.1`

`protobuf-driver/CMakeLists.txt` 已封装了构建配置，默认产出静态库（/MD），并使用本地 zlib/Abseil，默认关闭 `protoc` 和 upb 生成器，避免 CRT/链接冲突。如果要打开可执行生成，请见下方“可选项”。

## 快速使用
```powershell
cd C:\Users\ssq\Desktop\proto_new
cmake -S protobuf-driver -B build-protobuf -G "Visual Studio 17 2022" -A x64
cmake --build build-protobuf --config Release -- /m
cmake --install build-protobuf --config Release
```

输出位置（默认）：
- 安装前缀：`C:/Users/ssq/Desktop/proto_new/protobuf-33.2/install`
- 头文件：`install/include`
- 库文件：`install/lib/libprotobuf.lib`、`install/lib/libprotobuf-lite.lib`

## 关键默认配置（可在 CMake GUI/ccmake 中修改）
- 运行时：`CMAKE_MSVC_RUNTIME_LIBRARY=MultiThreadedDLL`（全程 /MD）
- 本地依赖路径：
  - zlib：`C:/Users/ssq/Desktop/proto_new/zlib-1.3.1/install`
  - Abseil CMake 包：`C:/Users/ssq/Desktop/proto_new/abseil-cpp-master/install/lib/cmake/absl`
  - protobuf 源：`C:/Users/ssq/Desktop/proto_new/protobuf-33.2`
  - 安装前缀：`C:/Users/ssq/Desktop/proto_new/protobuf-33.2/install`
- protobuf 选项（封装在 driver 中的缓存变量）：
  - `protobuf_BUILD_TESTS=OFF`
  - `protobuf_BUILD_SHARED_LIBS=OFF`
  - `protobuf_WITH_ZLIB=ON`
  - `protobuf_LOCAL_DEPENDENCIES_ONLY=ON`
  - `protobuf_BUILD_PROTOC_BINARIES=OFF`（可执行默认关闭）
  - `protobuf_BUILD_LIBUPB=OFF`

## 在你自己的 C/C++ 项目中链接
- include 路径：`<prefix>/include`
- lib 路径：`<prefix>/lib`
- 链接库：至少 `libprotobuf.lib`（如需精简可用 `libprotobuf-lite.lib`）
- 保持 /MD 运行时以与构建结果一致。

## 可选项：需要 protoc/upb 可执行时
在配置阶段把下列变量改为 ON：
- `protobuf_BUILD_PROTOC_BINARIES=ON`
- 如果需要 upb 生成器，再打开 `protobuf_BUILD_LIBUPB=ON`

注意：务必保持 Abseil 与 protobuf 统一使用 /MD；不要混入 /MT 目标，也不要添加 `/NODEFAULTLIB`。如遇 CRT 或缺符号冲突，可优先考虑直接使用官方预编译的 `protoc.exe` 搭配本地库。***

