cmake_minimum_required(VERSION 3.23)
project(Examples)

add_subdirectory(Fibonacci)
add_subdirectory(HowToUseJIT)
add_subdirectory(HowToUseLLJIT)
add_subdirectory(IRTransforms)
add_subdirectory(Kaleidoscope)
add_subdirectory(ModuleMaker)
add_subdirectory(OrcV2Examples)
add_subdirectory(SpeculativeJIT)
add_subdirectory(Bye)
add_subdirectory(BrainF)

if (LLVM_ENABLE_EH AND (NOT WIN32) AND (NOT "${LLVM_NATIVE_ARCH}" STREQUAL "ARM"))
    add_subdirectory(ExceptionDemo)
endif ()

add_subdirectory(ParallelJIT)
