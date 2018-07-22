---
title: SDL2函数SDL_UpdateWindowSurface与SDL_RenderPresent区别
url: 190.html
id: 190
categories:
  - Linux
date: 2016-11-11 23:36:47
tags:
---

在看别人的SDL代码时，发现他的代码中使用了SDL\_UpdateWindowSurface函数来渲染图形，而我平时用的函数都是SDL\_RenderPresent，这两个函数的声明分别如下：

  
```
int SDL_UpdateWindowSurface(SDL_Window* window)
void SDL_RenderPresent(SDL_Renderer* renderer)
```
两个函数所传入的参数不同，SDL2中引入了渲染器（SDL\_Renderer）的概念，渲染器对硬件渲染支持很好，解决了CPU渲染的低效率问题，渲染器的创建方法如下

  

  

  
```
    SDL_Renderer * SDLCALL SDL_CreateRenderer(SDL_Window *window, int index, Uint32 flags);  
```
   可以看出渲染器和窗口还是有一定关系的，渲染器引入的目的也就是本文所讨论的两个函数的区别，即为软件渲染和硬件渲染

  

  

SDL\_UpdateWindowSurface会使用CPU进行渲染，SDL\_RenderPresent会使用GPU进行渲染，即使你的设备没有显卡（这概率比较小，因为现在大多数CPU都集成显卡），它也会自动使用CPU进行软渲染（Software rendering），在SDL2中有更好的渲染方式，何必使用相对低效的方式呢  

在SDL2中，推荐使用SDL\_Texture来保存素材，然后通过SDL\_RenderCopy和SDL\_RenderCopyEx进行贴图，在主循环最后调用SDL\_RenderPresent进行渲染
