# GeneticAlgorithm
A Genetic Algorithm for solving an inventory problem

## 目的

将SKU从滞销店铺调入销路好的店铺，或者调入优先级高的店铺。

## 补充

- 分配优先级是定位在SKC和店铺上的。一个SKC对不同店铺优先级不同，一个店铺对多个SKC的优先级不同。
- SKC一款商品，SKU一个单品（SKU+尺码）
- 优先级：数值越小，优先级越高
- 归出店的定义：调入后水平<=库存水平

## 过程

优化前，所有店铺提供自己的要求调入SKU数量，之后整体考虑。

## 问题

将所有门店中的该 SKC 下的各个 size 的 SKU 重新再店铺之间进行分配，并考虑如下商业目标和约束。
给定 SKC ID，将所有门店中的该 SKC 下的各个 size 的 SKU 重新再店铺之间进行分配，并考虑如下商业目标和约束。
