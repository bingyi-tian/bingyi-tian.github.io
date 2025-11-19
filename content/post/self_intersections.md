+++
date = '2025-11-18T18:52:32+08:00'
draft = false
title = 'Self_intersections'
tags = ["",""]
categories = ""
author = "bingyi-tian"
description = ""
+++

remove_self_intersections 和 autorefine_and_remove_self_intersections 是 CGAL 中用于处理多边形网格自相交问题的两个不同函数。虽然它们都有修复自相交网格的功能，但它们的具体操作和应用场景有所不同。

remove_self_intersections： 检测并移除多边形网格中的自相交部分，再填补孔洞，得到的网格是分离的。

autorefine_and_remove_self_intersections：不仅检测并移除自相交，还会自动进行网格细化，以提高网格质量，得到的网格是连接在一起的。

1.自相交检测

bool intersecting = CGAL::Polygon_mesh_processing::does_self_intersect(mesh);
AI写代码
cpp
运行
检测时间测试 
文件大小	时间（秒）
25M	0.034
50M	0.05
100M	0.109
200M	0.2
400M	 0.408
1G	0.952
2.删除自相交

CGAL::Polygon_mesh_processing::experimental::remove_self_intersections(mesh);
AI写代码
cpp
运行
 效果测试：
![image-20251118185315124](E:\Users\Administrator\Desktop\blog\bingyi-tian.github.io\content\post\image\image-20251118185315124.png)

- 时间测试 

| 文件大小 | 时间（秒） |
| -------- | ---------- |
| 25M      | 1.838      |
| 50M      | 4.115      |
| 100M     | 10.787     |
| 200M     | 28.593     |
| 400M     | 68.749     |
| 1G       | 223.132    |



3.删除自相交和网格重新细化

CGAL::Polygon_mesh_processing::experimental::autorefine_and_remove_self_intersections(mesh);

-  效果测试：

![image-20251118185401340](E:\Users\Administrator\Desktop\blog\bingyi-tian.github.io\content\post\image\image-20251118185401340.png)

时间测试 ：
提示“exception Non-handled triple intersection of input triangles”，未能得到修复时间

测试电脑：
处理器    12th Gen Intel(R) Core(TM) i7-12700   2.10 GHz
RAM    32.0 GB





remove_self_intersections 和 autorefine_and_remove_self_intersections这两个接口在CGAL里面为什么没有

找到了,PMP后面加上experimental命名空间就行
