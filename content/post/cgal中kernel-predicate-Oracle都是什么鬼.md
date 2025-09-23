+++
date = '2025-09-23T14:44:18+08:00'
draft = false
title = 'Cgal中kernel Predicate Oracle都是什么鬼'
tags = ["cgal","stady"]
categories = "cgal"
author = "bingyi-tian"
description = "学习一下这几个都是什么"

+++

## Kernel

Kernel 是一个几何基础工具箱，包含：

- 基本类型：Point,Vector,Triangle
- 数值类型：double,exact rationals
- 基本操作：距离计算，向量计算

```c++
// 例子：使用内核
using Kernel = CGAL::Exact_predicates_inexact_constructions_kernel;
Kernel::Point_3 p(1.0,2.0,3.0); // 三维点
```

## Predicate

Predicate是进行几何关系判断的函数。

- 仅仅用来判断，不计算新几何体。
- 返回离散结果（是否，正负零）

```c++
// 判断三点朝向
Orientation orientation(Point p,Point q,Point r);

// 判断点与球的位置关系
Sign side_of_oriented_sphere(Point p,Point q,Point r,Point s,Point test);

// 比较平方距离
Comparison_result compare_squared_distance(Point p,Point q,FT d2);
```

## Oracle

Oracle是回答几何查询的抽象接口。

- 回答复杂的几何问题
- 封装复杂算法逻辑
- 提供统一访问接口

典型Oracle:

- 点是否在多面体内？
- 两个几何体是否相交？
- 找到最近邻点

## 🔗 三者的工作流程

### 示例：Delaunay三角剖分

```
内核提供Point类型 → 谓词判断点位置关系 → Oracle决定如何插入新点
```

```cpp
// 1. 内核定义数据类型
Kernel::Point_3 new_point = ...;

// 2. 谓词进行几何判断  
if (side_of_oriented_sphere(a, b, c, d, new_point) == POSITIVE) {
    // 3. Oracle根据判断结果做出决策
    oracle.insert_point(new_point);
}
```

## 📊 区别对比表

| 概念         | 角色       | 输出     | 关键特点         |
| ------------ | ---------- | -------- | ---------------- |
| **内核**     | 基础提供者 | 数据类型 | 提供计算基础设施 |
| **几何谓词** | 精确判断者 | 离散结果 | 必须100%准确     |
| **Oracle**   | 智能决策者 | 查询结果 | 封装复杂逻辑     |
