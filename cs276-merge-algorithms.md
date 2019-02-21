---
title: cs276 merge algorithms
date: 2018-11-10 22:30:25
tags: Search Engine
---


对于倒排记录表的merge算法，进行了一些简单的实现。目前实现了Intersect OR ANDNOT 和含位置信息的(k邻居内)的合并算法。
分别描述一下关键步骤:


## Intersect

docID相同时加入，否则将docID较小的一方前进一下

## OR

docID相同时加入，否则加入较小的一方，然后让它前进

## ANDNOT

docID相同时不能加入，因为这时不满足NOT的语义，前者小时，则说明后者无此id，可加入前者。后者小时，由于前置无此id，不能加入后者，只需要前进

## PositionalWithinK

先找到docID相同的倒排记录表，然后外层遍历前者，并维护一个由后者元素构成的窗口，这个窗口会包含与当前前者元素距离满足条件的后者元素。

总的来说就这么多啦，下次开始写带压缩和不带压缩的索引。Have fun!
