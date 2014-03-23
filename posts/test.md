---
title: 两行实现快速排序
date: '2012-09-21'
description:
categories:
tags: [python]

type: draft
---


````
def qsort(li):
	if (li == []):return []
	else :return qsort([s for s in li[1:] if s < li[0]]) + [li[0]] + qsort([b for b in li[1:] if b >= li[0]])

li = [4,2,1,5,0,-1]
print qsort(li)

````