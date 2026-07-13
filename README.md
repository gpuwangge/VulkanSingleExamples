# Build Instruction
Setup CMake/Vulkan/GLFW/GLM  
(一些项目已经内部集成Vulkan/GLFW，不需要系统级设定)
Go to the directory of a single vulkan example  
```
mkdir build  
cd build  
cmake -G "MinGW Makefiles" ..   
make  
```

# 说明
vulkanBasicExample这个例子源代码来自15号官方教程:(获取源代码的时间是07/12/2026)  
https://github.com/Overv/VulkanTutorial/blob/main/code/15_hello_triangle.cpp  
我做的修改只是修改了glfw的获取路径和shader的获取路径，vulkan版本用的是1.4.328.1。运行时validation layer会出现如下错误。  
```
validation layer: vkQueueSubmit(): pSubmits[0].pSignalSemaphores[0] (VkSemaphore 0x270000000027) is being signaled by VkQueue 0x1b4cb7fde90, but it may still be in use by VkSwapchainKHR 0xd000000000d.
Most recently acquired image indices: [0], 1.
(Brackets mark the last use of VkSemaphore 0x270000000027 in a presentation operation.)
Swapchain image 0 was presented but was not re-acquired, so VkSemaphore 0x270000000027 may still be in use and cannot be safely reused with image index 1.
Vulkan insight: See https://docs.vulkan.org/guide/latest/swapchain_semaphore_reuse.html for details on swapchain semaphore reuse. Examples of possible approaches:
   a) Use a separate semaphore per swapchain image. Index these semaphores using the index of the acquired image.
   b) Consider the VK_KHR_swapchain_maintenance1 extension. It allows using a VkFence with the presentation operation.
The Vulkan spec states: Each binary semaphore element of the pSignalSemaphores member of any element of pSubmits must be unsignaled when the semaphore signal operation it defines is executed on the device (https://vulkan.lunarg.com/doc/view/1.4.328.1/windows/antora/spec/latest/chapters/cmdbuffers.html#VUID-vkQueueSubmit-pSignalSemaphores-00067)
validation layer: vkQueueSubmit(): pSubmits[0].pSignalSemaphores[0] (VkSemaphore 0x270000000027) is being signaled by VkQueue 0x1b4cb7fde90, but it may still be in use by VkSwapchainKHR 0xd000000000d.
Most recently acquired image indices: 0, [1], 2.
(Brackets mark the last use of VkSemaphore 0x270000000027 in a presentation operation.)
Swapchain image 1 was presented but was not re-acquired, so VkSemaphore 0x270000000027 may still be in use and cannot be safely reused with image index 2.
Vulkan insight: See https://docs.vulkan.org/guide/latest/swapchain_semaphore_reuse.html for details on swapchain semaphore reuse. Examples of possible approaches:
   a) Use a separate semaphore per swapchain image. Index these semaphores using the index of the acquired image.
   b) Consider the VK_KHR_swapchain_maintenance1 extension. It allows using a VkFence with the presentation operation.
The Vulkan spec states: Each binary semaphore element of the pSignalSemaphores member of any element of pSubmits must be unsignaled when the semaphore signal operation it defines is executed on the device (https://vulkan.lunarg.com/doc/view/1.4.328.1/windows/antora/spec/latest/chapters/cmdbuffers.html#VUID-vkQueueSubmit-pSignalSemaphores-00067)
```
这是教程更新未跟上api的缘故，不是因为本人的问题。  
但是即使出现了这个错误，gtx5080运行的时候屏幕上仍然可以正确显示三角形，因此不是必须维修的。  
原理估计是因为即使源代码不符合api标准，但是这个例子太简单了，gpu计算太快了所以不会暴露问题。  

