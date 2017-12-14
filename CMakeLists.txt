cmake_minimum_required(VERSION 3.2.0)

# Default policy is from 3.4.0
cmake_policy(VERSION 3.4.0)

# Set project metadata
set(release-name "imagej-launcher")
set(release-version "5.0.0")
project("${release-name}"
  VERSION "${release-version}"
  LANGUAGES C)

set(VERSION "5.0.0-SNAPSHOT")
set(BUILD "62ee8b0e2ead74ec212978fa5c368a6c96c2e765")

# Add JNI include directory
find_package(JNI REQUIRED)
if (JNI_FOUND)
    message (STATUS "JNI_INCLUDE_DIRS=${JNI_INCLUDE_DIRS}")
    message (STATUS "JNI_LIBRARIES=${JNI_LIBRARIES}")
endif()
include_directories(${JNI_INCLUDE_DIRS})

# Add projects include directory
include_directories("src/main/include")

# Define macros used in the ImageJ.c#usage()
add_definitions(-DLAUNCHER_VERSION="${VERSION}")
add_definitions(-DBUILD_NUMBER="${BUILD}")

# Define sources files
set(SOURCE_FOLDER "src/main/c")
set(SOURCE_FILES ${SOURCE_FOLDER}/ImageJ.c ${SOURCE_FOLDER}/common.c ${SOURCE_FOLDER}/config.c ${SOURCE_FOLDER}/exe-ico.c ${SOURCE_FOLDER}/file-funcs.c ${SOURCE_FOLDER}/java.c ${SOURCE_FOLDER}/platform.c ${SOURCE_FOLDER}/splash.c ${SOURCE_FOLDER}/string-funcs.c ${SOURCE_FOLDER}/xalloc.c)

# Set compiler flags and compile
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fno-stack-protector -m64")
add_executable (imagej-launcher ${SOURCE_FILES})

# Set linker flags and compile
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -m64")
target_link_libraries(imagej-launcher PUBLIC dl PUBLIC pthread)

# Install the binary
install (TARGETS imagej-launcher DESTINATION bin)