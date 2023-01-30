find_package(LLVM REQUIRED CONFIG)
project(HowToUseLLJIT)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})
list(APPEND CMAKE_MODULE_PATH "${LLVM_CMAKE_DIR}")
include(AddLLVM)

set(LLVM_LINK_COMPONENTS
  Core
  OrcJIT
  Support
  nativecodegen
  )

add_llvm_example(HowToUseLLJIT
  HowToUseLLJIT.cpp
  )
