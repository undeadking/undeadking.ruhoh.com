---
title:
date: '2012-10-12'
description:
categories:
tags: [git]
---

``git init``有个`--bare`参数,用它可以创建一个没有代码目录的repository,目录的内容就是一般repository中.git目录的内容

这样的repository体积较小,适合放到dropbox或者U盘上作为remote repository.下面是例子


```
#建立远程仓库目录
mkdir remote
cd remote
git init --bare

#建立工作目录1
cd ..
mkdir working1
cd working1
git init 
git remote add origin ../remote/

#添加test文件

echo Hello World! > test
git add test
git commit -m 'Initial commit'
#推送到远端
git push origin master

#建立工作目录2并从远端获取代码
cd ..
mkdir working2
cd working2
git init
git remote add origin ../remote/
git pull origin master

```
<!--#编辑文件
echo v2 >> test
git commit -am 'v2'
git push origin master

#切换到工作目录1编辑文件,产生冲突
cd ../working1
echo v3 >> test
git commit -am 'v3'
git pull origin master-->
