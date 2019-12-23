cmake_minimum_required(VERSION 3.5)
project(DobbyDrill)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_C_STANDARD 11)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} ${CMAKE_CXX_FLAGS}")

add_definitions(-DPLUGIN_DOBBY_DRILL)

if(DOBBY_SOURCE_DIR)
  message(STATUS "[*] DOBBY_SOURCE_DIR : ${DOBBY_SOURCE_DIR}")
else()
  message(FATAL_ERROR "[!] DobbyDrill: no DOBBY_SOURCE_DIR specificed!")
endif()

message(STATUS "[*] DobbyDrill for iOS-ARM64 MachO file hook")

include(${DOBBY_SOURCE_DIR}/cmake/Util.cmake)
include(${DOBBY_SOURCE_DIR}/cmake/Macros.cmake)

set(Dobby.SOURCE_FILE_LIST
    ${DOBBY_SOURCE_DIR}/source/core/arch/Cpu.cc
    ${DOBBY_SOURCE_DIR}/source/core/arch/CpuRegister.cc
    ${DOBBY_SOURCE_DIR}/source/core/arch/CpuFeature.cc

    ${DOBBY_SOURCE_DIR}/source/core/modules/assembler/assembler.cc
    ${DOBBY_SOURCE_DIR}/source/core/modules/assembler/assembler-arm64.cc
    ${DOBBY_SOURCE_DIR}/source/core/modules/codegen/codegen-arm64.cc

    ${DOBBY_SOURCE_DIR}/source/ExecMemory/CodeBuffer/CodeBufferBase.cc
    ${DOBBY_SOURCE_DIR}/source/ExecMemory/CodeBuffer/code-buffer-arm64.cc
    ${DOBBY_SOURCE_DIR}/source/ExecMemory/AssemblerCodeBuffer.cc
    ${DOBBY_SOURCE_DIR}/source/ExecMemory/AssemblyCode.cc
    ${DOBBY_SOURCE_DIR}/source/ExecMemory/ExecutableMemoryArena.cc
    ${DOBBY_SOURCE_DIR}/source/ExecMemory/PageAllocator.cc

    # InterceptRouting
    ${DOBBY_SOURCE_DIR}/source/InterceptRouting/InterceptRouting.cpp

    #InterceptRoutingPlugin
    ${DOBBY_SOURCE_DIR}/source/InterceptRoutingPlugin/FunctionInlineReplace/function-inline-replace.cc
    ${DOBBY_SOURCE_DIR}/source/InterceptRoutingPlugin/FunctionInlineReplace/FunctionInlineReplaceExport.cc

    # srcxx
    ${DOBBY_SOURCE_DIR}/source/dobby.cpp
    ${DOBBY_SOURCE_DIR}/source/interceptor.cpp
)

# add logging library
set(logging.SOURCE_FILE_LIST
    ${DOBBY_SOURCE_DIR}/external/logging/logging.c
    ${DOBBY_SOURCE_DIR}/external/logging/cxxlogging.cc
  )

# add stdcxx library
set(stdcxx.SOURCE_FILE_LIST
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteCollection.cc
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteIterator.cc
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteMemOpt.cc
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteMutableArray.cc
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteMutableBuffer.cc
    ${DOBBY_SOURCE_DIR}/external/stdcxx/LiteObject.cc
  )

set(DobbyDrill.SOURCE_FILE_LIST
    ./source/ExecMemory/clear-cache-tool-static.cc
    ./source/ExecMemory/code-patch-tool-static.cc
    ./source/InterceptRoutingTrampoline/arm64/trampoline-arm64.cc
    ./source/PlatformInterface/Common/platform-static.cc
    ./source/InstructionRelocation/arm64/ARM64InstructionRelocation.cc
)
set(MachOManipulator.SOURCE_FILE_LIST
    MachOManipulator/MachOManipulator.cc
)

set(RuntimeSupport.SOURCE_FILE_LIST
    RuntimeSupport/FunctionInlineReplaceExport.cc
    RuntimeSupport/StubRebase.cc
)

include_directories(
    .
    ${DOBBY_SOURCE_DIR}/include
    ${DOBBY_SOURCE_DIR}/source
    ${DOBBY_SOURCE_DIR}/source/UserMode
    ${DOBBY_SOURCE_DIR}/external/logging
    ${DOBBY_SOURCE_DIR}/external/stdcxx
)

add_executable(DobbyDrill
    main.cc
    ${Dobby.SOURCE_FILE_LIST}
    ${DobbyDrill.SOURCE_FILE_LIST}
    ${MachOManipulator.SOURCE_FILE_LIST}
    ${logging.SOURCE_FILE_LIST}
    ${stdcxx.SOURCE_FILE_LIST}
)