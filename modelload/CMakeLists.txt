cmake_minimum_required(VERSION 3.20)
SET(CMAKE_C_COMPILER "C:/mingw64/bin/gcc.exe")
SET(CMAKE_CXX_COMPILER "C:/mingw64/bin/g++.exe")
project(modelload)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE "Debug")

#Set variables so all cmakelists.txt in sub dirs can access
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) # Static libraries (.lib) and import libraries (.lib) go here.
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) # Shared libraries (.so/.dylib). Mostly used on Linux/macOS.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin) # Executables and DLLs go here.

include_directories(
    ${CMAKE_SOURCE_DIR}/../external/GLFW/glfw3.4/include
    ${CMAKE_SOURCE_DIR}/../external/vulkan/1.4.328.1/include
    ${CMAKE_SOURCE_DIR}/../external/glm/0.9.9.8)

link_directories(
    ${CMAKE_SOURCE_DIR}/../external/GLFW/glfw3.4/lib
    ${CMAKE_SOURCE_DIR}/../external/vulkan/1.4.328.1/lib)

link_libraries(vulkan-1 glfw3dll)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

aux_source_directory(${PROJECT_SOURCE_DIR} SRC)  
foreach(cppfile IN LISTS SRC)  
    get_filename_component(filename ${cppfile} NAME_WE)  
    add_executable(${PROJECT_NAME} ${cppfile}) 
endforeach()  

file(COPY ${PROJECT_SOURCE_DIR}/../external/GLFW/glfw3.4/lib/glfw3.dll DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/)
file(COPY ${PROJECT_SOURCE_DIR}/../external/vulkan/1.4.328.1/Layer/VkLayer_khronos_validation.dll DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/vulkan/layers/)
file(COPY ${PROJECT_SOURCE_DIR}/../external/vulkan/1.4.328.1/Layer/VkLayer_khronos_validation.json DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/vulkan/layers/)

file(COPY ${PROJECT_SOURCE_DIR}/shaders/vert.spv DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/shaders/)
file(COPY ${PROJECT_SOURCE_DIR}/shaders/frag.spv DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/shaders/)

file(COPY ${PROJECT_SOURCE_DIR}/models/viking_room.obj DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/models/)
file(COPY ${PROJECT_SOURCE_DIR}/textures/viking_room.png DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/textures/)