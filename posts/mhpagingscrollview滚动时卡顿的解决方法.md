---
title:
date: '2012-09-26'
description:
categories:
tags: [iOS]

type: 
---



由于`scrollViewDidScroll`在视图滚动的时候会不断重复运行,里面的`scrollViewDidScroll`会重复执行多次,在View的内容多的时候会出现卡顿,让它只执行一次会提高界面的流畅度

http://github.com/hollance/MHPagingScrollView 中 Paging.xcodeproj工程的patch如下,应用之后除了第0页之外的滚动都只会输出一次

```
diff --git a/Sources/AppViewController.m b/Sources/AppViewController.m
index 6da53e9..9c74d02 100644
--- a/Sources/AppViewController.m
+++ b/Sources/AppViewController.m
@@ -70,8 +70,12 @@
 
 - (void)scrollViewDidScroll:(UIScrollView *)theScrollView
 {
-	pageControl.currentPage = [pagingScrollView indexOfSelectedPage];
-	[pagingScrollView scrollViewDidScroll];
+    NSInteger index = [pagingScrollView indexOfSelectedPage] - 1;
+    if (pageControl.currentPage != index) {
+        pageControl.currentPage = index;
+        NSLog(@"currentPage:%d",[pagingScrollView indexOfSelectedPage]);
+        [pagingScrollView scrollViewDidScroll];
+    }
 }
 
 - (void)scrollViewDidEndDecelerating:(UIScrollView *)theScrollView
```