cmake_minimum_required(VERSION 3.20)
SET(CMAKE_C_COMPILER "C:/mingw64/bin/gcc.exe")
SET(CMAKE_CXX_COMPILER "C:/mingw64/bin/g++.exe")
project(VulkanSingleExample)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE "Debug")

#Set variables so all cmakelists.txt in sub dirs can access
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) #this is required in Windows, all lib goes here
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) #this is not required in windows
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin) #all exe dll/exe goes here

include_directories(
    #$ENV{VULKAN_SDK}/Include
    $ENV{INCLUDE}        #Put this in environment user variable INCLUDE: C:\VulkanSDK\GLFW\include;C:\VulkanSDK\GLM
    ${CMAKE_SOURCE_DIR}/../external/GLFW/glfw3.4/include
    ${CMAKE_SOURCE_DIR}/../external/vulkan/1.4.328.1/include)

link_directories(
    #$ENV{VULKAN_SDK}/Lib
    $ENV{LIB}
    ${CMAKE_SOURCE_DIR}/../external/GLFW/glfw3.4/lib
    ${CMAKE_SOURCE_DIR}/../external/vulkan/1.4.328.1/lib)          #Put this in environment user variable LIB: C:\VulkanSDK\GLFW\lib-static-ucrt  

#to verify environment varaibles (reboot machine after change if needed):
#echo %VULKAN_SDK%
#echo %INCLUDE%
#echo %LIB%

link_libraries(vulkan-1 glfw3dll)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

#add_executable(vulkanBasicExample ${PROJECT_SOURCE_DIR}/vulkanBasicExample.cpp)
aux_source_directory(${PROJECT_SOURCE_DIR} SRC)  
foreach(cppfile IN LISTS SRC)  
    get_filename_component(filename ${cppfile} NAME_WE)  
    add_executable(${filename} ${cppfile})  
endforeach()  


file(COPY ${PROJECT_SOURCE_DIR}/../external/GLFW/glfw3.4/lib/glfw3.dll DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/)
file(COPY ${PROJECT_SOURCE_DIR}/../external/vulkan/1.4.328.1/Layer/VkLayer_khronos_validation.dll DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/vulkan/layers/)
file(COPY ${PROJECT_SOURCE_DIR}/../external/vulkan/1.4.328.1/Layer/VkLayer_khronos_validation.json DESTINATION ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/vulkan/layers/)




#test link to a specific exe
# add_executable(simpleTriangle samples/simpleTriangle.cpp)
# target_link_libraries(simpleTriangle wxj-vulkan-framework vulkan-1 glfw3dll)

#test work code   
# aux_source_directory(${PROJECT_SOURCE_DIR}/framework SRC) 
# add_executable(testfw samples/simpleTriangle.cpp ${SRC})
# target_link_libraries(testfw vulkan-1 glfw3dll)

#test work code
# aux_source_directory(${PROJECT_SOURCE_DIR}/framework SRC)
# set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR})
# add_library(fw STATIC ${SRC})
# add_executable(testfw samples/simpleTriangle.cpp)
# target_link_libraries(testfw fw vulkan-1 glfw3dll)

