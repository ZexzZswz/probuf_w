cmake_minimum_required(VERSION 3.16)
project(protobuf_local_build LANGUAGES C CXX)

# 运行时统一使用 /MD
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreadedDLL" CACHE STRING "MSVC runtime")

# 路径根据本机默认值，可按需覆盖
set(ZLIB_ROOT "C:/Users/ssq/Desktop/proto_new/zlib-1.3.1/install" CACHE PATH "zlib install root")
set(absl_DIR "C:/Users/ssq/Desktop/proto_new/abseil-cpp-master/install/lib/cmake/absl" CACHE PATH "absl config dir")
set(protobuf_SOURCE_DIR "C:/Users/ssq/Desktop/proto_new/protobuf-33.2" CACHE PATH "protobuf source")
set(CMAKE_INSTALL_PREFIX "C:/Users/ssq/Desktop/proto_new/protobuf-33.2/install" CACHE PATH "install prefix")

set(ZLIB_INCLUDE_DIR "${ZLIB_ROOT}/include" CACHE PATH "zlib include")
set(ZLIB_LIBRARY     "${ZLIB_ROOT}/lib/zlibstatic.lib" CACHE FILEPATH "zlib lib")

# protobuf 关键选项
set(protobuf_BUILD_TESTS OFF CACHE BOOL "disable tests")
set(protobuf_BUILD_SHARED_LIBS OFF CACHE BOOL "static libs")
set(protobuf_WITH_ZLIB ON CACHE BOOL "use zlib")
set(protobuf_LOCAL_DEPENDENCIES_ONLY ON CACHE BOOL "no remote fetch")
# 如需生成 protoc 或 upb，可改为 ON
set(protobuf_BUILD_PROTOC_BINARIES OFF CACHE BOOL "build protoc")
set(protobuf_BUILD_LIBUPB OFF CACHE BOOL "build upb")

# 将源码加入构建
add_subdirectory(${protobuf_SOURCE_DIR} ${CMAKE_BINARY_DIR}/protobuf-build)

