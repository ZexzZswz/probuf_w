cmake_minimum_required(VERSION 3.16)
project(protobuf_local_build LANGUAGES C CXX)

# 运行时统一使用 /MD
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreadedDLL" CACHE STRING "MSVC runtime")

# 统一使用 C++17，满足 Abseil 要求
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 路径根据本机默认值，可按需覆盖
set(ZLIB_ROOT "C:/Users/ssq/Desktop/proto_windows/zlib-1.3.1/install" CACHE PATH "zlib install root")

# 不再通过 find_package(absl) 查找，因此不需要 absl_DIR
# set(absl_DIR "C:/Users/ssq/Desktop/proto_windows/abseil-cpp-master/install/lib/cmake/absl" CACHE PATH "absl config dir")

# Abseil 源码目录
set(absl_SOURCE_DIR "C:/Users/ssq/Desktop/proto_windows/abseil-cpp-master"
    CACHE PATH "abseil source")

set(protobuf_SOURCE_DIR "C:/Users/ssq/Desktop/proto_windows/protobuf-main" CACHE PATH "protobuf source")
set(CMAKE_INSTALL_PREFIX "C:/Users/ssq/Desktop/proto_windows/protobuf-main/install" CACHE PATH "install prefix")

set(ZLIB_INCLUDE_DIR "${ZLIB_ROOT}/include" CACHE PATH "zlib include")
set(ZLIB_LIBRARY     "${ZLIB_ROOT}/lib/zlibstatic.lib" CACHE FILEPATH "zlib lib")

# 如果希望在执行 install 时顺便安装 Abseil，可以开启
# set(ABSL_ENABLE_INSTALL ON CACHE BOOL "Install abseil when running install")

# 先把 Abseil 源码加入当前工程，这一步会生成 absl::strings 等 target
add_subdirectory(${absl_SOURCE_DIR} ${CMAKE_BINARY_DIR}/abseil-build)

# protobuf 关键选项
set(protobuf_BUILD_TESTS OFF CACHE BOOL "disable tests")
set(protobuf_BUILD_SHARED_LIBS OFF CACHE BOOL "static libs")
# 先关闭 zlib 以避免缺失安装路径导致的 ZLIB::ZLIB 错误，如需压缩支持可等 zlib 安装好后再改为 ON
set(protobuf_WITH_ZLIB OFF CACHE BOOL "use zlib")
set(protobuf_LOCAL_DEPENDENCIES_ONLY ON CACHE BOOL "no remote fetch")
# 本地使用，不安装/导出 protobuf 及 Abseil 的 CMake 包，避免 absl_* export 报错
set(protobuf_INSTALL OFF CACHE BOOL "install protobuf")
# 如需生成 protoc 或 upb，可改为 ON
set(protobuf_BUILD_PROTOC_BINARIES OFF CACHE BOOL "build protoc")
set(protobuf_BUILD_LIBUPB OFF CACHE BOOL "build upb")

# 将源码加入构建
add_subdirectory(${protobuf_SOURCE_DIR} ${CMAKE_BINARY_DIR}/protobuf-build)
