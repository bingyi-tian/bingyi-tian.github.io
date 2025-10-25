+++
date = '2025-10-25T15:45:03+08:00'
draft = false
title = 'Run_alpha_wrap3'
tags = ["",""]
categories = ""
author = "bingyi-tian"
description = ""
+++





```
git clone https://github.com/CGAL/cgal.git


PS D:\VisualStudio\Source\cgal\cgal\cgal\Alpha_wrap_3\examples\Alpha_wrap_3> ls


    目录: D:\VisualStudio\Source\cgal\cgal\cgal\Alpha_wrap_3\examples\Alpha_wrap_3


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/10/25     15:28            738 CMakeLists.txt
-a----        2025/10/25     15:28           4967 mixed_inputs_wrap.cpp
-a----        2025/10/25     15:28            748 output_helper.h
-a----        2025/10/25     15:28           6346 pause_and_resume_wrapping.cpp
-a----        2025/10/25     15:28           2283 point_set_wrap.cpp
-a----        2025/10/25     15:28           5370 successive_wraps.cpp
-a----        2025/10/25     15:28           2386 triangle_mesh_wrap.cpp
-a----        2025/10/25     15:28           2415 triangle_soup_wrap.cpp
-a----        2025/10/25     15:28           6545 volumetric_wrap.cpp
-a----        2025/10/25     15:28           3093 wrap_from_cavity.cpp

mkdir -p build/debug

cmake -DCMAKE_BUILD_TYPE=Debug -DCGAL_DIR="D:/VisualStudio/Source/cgal/cgal/cgal" ../..

```



