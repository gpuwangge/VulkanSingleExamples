# Build
Setup CMake  
项目已经内部集成Vulkan/GLFW，不需要系统级设定()  
一些项目可能需要GLM  
Go to the directory of a single vulkan example  
```
mkdir build  
cd build  
cmake -G "MinGW Makefiles" ..   
make  
```

# Run
To use validation layer, in VS Code Terminal(Power Shell), before you execute the binary, run the following:  
```
$env:VK_LAYER_PATH="vulkan\layers"
```
To confirm the environment is set, run:  
```
$env:VK_LAYER_PATH
```

如果不想运行前设置VK_LAYER_PATH，可以用以下方法任选其一：
- 系统变量中设置VK_LAYER_PATH
- 安装Vulkan SDK
- 源代码自动寻找"vulkan\layers"这个位置，详细见LuminError的instance.cpp (SetupVulkanLayerPath)


# Build/Run for Linux
在Vulkan网站下载Linux版本的安装包(.tar.xz)，用tar -xvf解压缩。  
使用里面的include/, lib/。注意的是有些文件名跟windows的不一样，比如windows icd loader叫vulkan-1.lib, linux的就叫libvulkan.so。  
libvulkan.so实际上它只是一个符号链接（symlink）。最终运行时加载的是 libvulkan.so.1 因此 Linux 其实也有"1"，只是隐藏在 .so.1 中，而不是名字里。  

另外glslc也有linux版本，在bin/文件夹里。这里注意不管在windows还是linux下编译出来的.spv都是一样的，可以通用。  

Set layers location in Linux
```
VK_LAYER_PATH=/vulkan/layers ./your_program
```


# Example Explain
## simpletriangle
source: hello triangle example from https://vulkan-tutorial.com/  
https://github.com/Overv/VulkanTutorial/blob/main/code/15_hello_triangle.cpp  

## particlecompute
source: compute example from https://vulkan-tutorial.com/  
https://github.com/Overv/VulkanTutorial/blob/main/code/31_compute_shader.cpp  

## imagecompute
Show a purple rect in the window using only compute shader  

## depth
Depth test. One textured square on top of another textured square.  

## modelload
Model load using tiny_obj_loader.  

## textureload
Texture load using stb_image.

## headless
Based on simpletriangle, but remove glfw. Dump .ppm instead of present it on screen.  
to view .ppm, use GIMP: https://www.gimp.org/downloads/  
注意：这是一个headless 示例。  

Headless 示例就是：不创建任何窗口、不依赖操作系统窗口系统，只把渲染结果写到内存/文件 的 Vulkan 示例程序。  
- 没有 glfwCreateWindow / HWND / X11 / wayland 等  
- 不需要 VkSurfaceKHR，不需要 VkSwapchainKHR，也不需要 vkAcquireNextImageKHR / vkQueuePresentKHR。  
- 渲染目标是自己创建的 VkImage（通常作为 framebuffer 的 color attachment）。  
- 渲染完成后，把图像从 GPU 显存拷贝到 host-visible 的 buffer，然后：  
写入文件（如 PPM / PNG）  
或者直接交给后续 CPU 处理（图像处理、AI、测试等）  

典型用途：
- 在没有显示器的服务器 / 容器 / CI 里跑渲染或计算（所谓“无头模式” / headless mode）。
- 离线渲染、批量生成测试图、自动化测试。
- GPU 计算任务（compute shader）不需要任何显示输出，只关心计算结果。
- 做驱动 / 引擎的 headless 测试目标，模拟各种呈现条件。

跟普通教程示例的区别
- 普通教程（比如 Vulkan Tutorial）会教你：
创建 window surface  
创建 swapchain  
每帧：acquire image → render → present  
这是为了“看到画面”。  

- Headless 示例则：
不创建 surface / swapchain  
自己创建一张 offscreen image 当 render target  
只渲染一次（或有限次数）  
把结果拷回 CPU，保存成文件  
整个流程完全不需要窗口系统。  

对比源代码  
- 原版 hello triangle： 渲染到 swapchain image -> present 到屏幕。  
- headless hello triangle： 渲染到自建 image -> copy 到 readback buffer -> 存成 ppm  


# 额外说明
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

