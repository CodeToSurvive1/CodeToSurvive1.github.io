---
layout: "post"
title: "如何设置没有reducer"
date: "2016-10-24 19:05"
tags: [大数据,hadoop]
category: "hadoop"
---


#### 两种设置方法

1.直接在job中设置

job.setNumReduceTasks(0);

2.通过configuration设置

conf.set("mapred.reduce.tasks","0");
