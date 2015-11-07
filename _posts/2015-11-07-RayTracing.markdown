---
author: Guoquan Zhao
comments: true
date: 2015-11-07 19:21:41+00:00
layout: post
title: 计算机图形学中的Ray Tracing，Ray Casting，Path Tracing 的区别
categories:
- Computer Graphics
---

# 计算机图形学中的Ray Tracing，Ray Casting，Path Tracing 的区别

* 本回答已发布在[http://www.zhihu.com/question/29863225/answer/71091733](http://www.zhihu.com/question/29863225/answer/71091733)

Ray Tracing是一种对真实光照场景的粗略模拟。Ray Tracing认为我们能分辨出某个物体的颜色的原因是因为光源发出的光线照到物体表面，吸收一部分后反射到人眼中。要想模拟这种情况，我们可以从眼睛反向追踪这束光线。如下图所示[1]
![](/img/ray-trace1.png)

一个不严谨的比喻：P前面的矩形就是我们的视网膜。为了决定他们的颜色，对于每个像素点/视网膜感光细胞，我们从P发射一条射线打到矩形上，这束光线之后会进入这个场景，与某个物体相交。我们就可以根据这个点的物理性质、光源的属性以及光线反射、折射模型决定这个像素点的最终颜色。Phong是一种比较常见的反射模型。这个模型考虑了环境光(ambient),漫反射(diffuse)，以及镜面反射(Specular)。如下图所示[2]。V向量是Ray Tracing发射的反方向。L与N的夹角大小决定了漫反射在相机处的剩余光照强度（我们这里假设漫反射各向同性，最终亮度取决于Normal向量与L的夹角。夹角越小，相机处认为越亮）。同理，当V与R夹角很小时，我们能够看到镜面反射。由于L*V是两者的cos值，随夹角增大的降低速率过慢，我们将其取α次方。ka,kd,ks均为常数。

![](/img/ray-trace2.png)

当然，这种模型非常粗糙。但是的确会比通常的方法更精确一些。可以引入光的折射来充实这个模型。在遇到镜面时，递归的追踪反射光，这样，Ray Tracing就可以很好的处理带有透明液体的场景。

![](/img/ray-trace3.png)
Ray tracing (graphics)

Ray Casting 在早期与Ray Tracing并无不同，但目前我们倾向于将其分开。主要区别是，Ray Casting并不会递归的追踪光线，而Ray Tracing可能会根据不同的反射 、折射模型递归的追踪光线。[3]。可以理解为最最基础的Ray Tracing。

Path Tracing使用了蒙特卡洛算法，与Ray Tracing不同的是，追踪光线的时候并没有使用那条可以确定的、无偏差的反射光线，而是根据BRDF（bidirectional reflectance distribution function
）随机的追踪。BRDF是表面材质的函数，对于每个法向量与光源的夹角、法向量与观察者的夹角有不同的取值。
![](/img/ray-trace4.png)

这显著的增大了计算量，然而这通常情况下会使渲染出来的结果更为真实。


参考资料：
[1][notes/Lighting08.pdf](https://cs.anu.edu.au/courses/comp4610/notes/Lighting08.pdf)
[2][notes/Raytracing10.pdf](https://cs.anu.edu.au/courses/comp4610/notes/Raytracing10.pdf)
[3][Ray casting](https://en.wikipedia.org/wiki/Ray_casting)