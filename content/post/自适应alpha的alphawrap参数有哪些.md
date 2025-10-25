+++
date = '2025-10-25T16:19:06+08:00'
draft = false
title = '自适应alpha的alphawrap参数有哪些'
tags = ["",""]
categories = ""
author = "bingyi-tian"
description = ""
+++



注意很多模型有重复点，要先把重复点消除掉，可以用meshlab





首先，代码的核心创新点，alpha的特征自适应，是通过feature_coff控制的，具体实现如下（在geometry_utils.h中）：
简洁版本：
// 特征边处理 - 更高密度采样
if (是特征边) {
    mean_sizing /= (基础系数 * 特征系数);
} else {
    mean_sizing /= 基础系数;
}
原版本：
                    // 特征边处理
                    // find feature edge
                    // e_feature_ 是一个标记数组（如Eigen矩阵或二维数组），存储每条边是否为特征边
                    if (m_oracle.e_feature_(face_id * 3, 0) == 1 || m_oracle.e_feature_(face_id * 3 + 1, 0) == 1 || m_oracle.e_feature_(face_id * 3 + 2, 0) == 1) {
                        // coff_是全局缩放系数，控制整体网格密度。
						// feature_coff_是特征边的缩放系数，控制特征边的局部密度。
                        mean_sizing /= (m_oracle.coff_ * m_oracle.feature_coff_);
                    }
                    else {
                        mean_sizing /= m_oracle.coff_;
                    }

relative_alpha这个参数没有用
relative_offset就是正常的偏移曲面参数，值越大越贴近原本模型
coff = 2是缩放参数，值越大，三角面片越小
inset这个参数在feature是1的时候貌似没有有什么效果，
在feature的时候：
feature_coff决定特征保持效果明显不明显

注意一个很重要的参数，max_abs_curvature 指的是最大绝对曲率，在sizing_field.cpp中
他是对于平坦区域的三角面片进行的一个截断，因为平坦区域的deltax（最大绝对曲率）是0，算法不能取0

```c++
		//max_abs_curvature_ = mean_curevature[i];
		max_abs_curvature_ = 0.001 * mean_curevature[i];
```



尺寸钳制策略：
// 防止极端值
if (sizing < min_edge_length) sizing = min_edge_length;
else if (sizing > max_edge_length) sizing = max_edge_length;
