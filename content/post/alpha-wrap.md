+++
date = '2025-09-23T13:38:43+08:00'
draft = false
title = 'Alpha Wrap'
tags = ["Alpha Wrap", "study"]
categories="study"
author="bingyi-tian"

+++

# 算法分为Refinement细化和Carving雕刻

## 检查门

如果门不可被alpha球穿过，证明这个门已经达到目标尺寸了，所以就停止收缩了，就跳过他

## 规则R1（ Refinement - Voronoi边相交 ）

计算门f的对偶边，也就是链接这个门两边的四面体，如果这个连接线与偏移曲面相交，则触发规则R1：

将从outside指向inside方向的第一个交点作为Steiner点插入到Delaunay三角剖分中。

## 规则R2（Refinement - 防止暴露输入）

若两个相邻单元的外接球中心相连组成的线段，与偏移曲面不相交，但是inside这个四面体与输入几何体相交，如果对他进行雕刻的话，就暴露了，所以自然而然的出现！规则2：把这个与输入几何体相交的四面体的外接球球心搞出来，然后往几何体上投影，这个投影就是steiner点。

## 雕刻

如果没有触发这些规则：

1. 门大于alpha球
2. inside这个四面体不与输入几何相交，即同时不满足上面的两个Rule
   1. 门的对偶边，outside四面体和inside四面体的外接球心连线不会与偏移曲面相交
   2. inside 不予输入几何相交

就执行雕刻，雕刻这个词有点装逼，其实就是把inside这个单元标记为outside，然后检查这个outside四面体，把其中新生成的三个面中，满足面的大小大于alpha的面加入门的优先级队列。



## 终止条件

首先明确一个数值Φ(f)，Φ(f)就是面f的最小Delaunay球半径？？？？还是外接圆？？？

也就是说是这个面最小空球半径

面的 **Φ(f)** 必然递减，每次插入Steiner点时，新生成的面的Φ(f)



​	

放了个国庆，要收收心了。
