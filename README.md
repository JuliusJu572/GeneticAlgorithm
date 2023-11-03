# GeneticAlgorithm
A Genetic Algorithm for solving a inventory problem
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

## 目标

$$min\ z = w_1f_1 + w_2f_2 + w_3f_3 + w_4f_4$$

$x_{wsw'}$ 仓库 $w$ 中的 $s$ 向仓库 $w'$ 发送的数量

$x_{wsw'}$ 为本模型中的决策变量，其中 $w$ 代表单个仓库，$w\in W$。 $s$ 代表单个商品，即不同商品不同尺码的单品。$w'$代表仓库的集合中的另一个仓库，$w'\in W$。总来说，$x_{wsw'}$ 用于表示商品从仓库 $w$ 发送到另一个仓库 $w'$ 的数量。

$f_1$：优先归入到重要的店铺

$$f_1 = \sum_{w \in W}\sum_{s \in S}({Q'}_{ws}* L_{ws})$$


$f_2$：SKC归入店铺后水平尽量在店铺的铺货标准上下界内

$$f_2 = \sum_{w \in W}\sum_{s \in S}\left(\left/{Q'}_{ws}- L_{ws}\right/+\left/{Q'}_{ws}- U_{ws}\right/+\left/ U_{ws}-L_{ws}\right/\right)*H_{ws}$$


$f_3$：SKU规划水平靠近店铺的期望

$$f_3 = \sum_{w \in W}\sum_{s \in S}\left/{Q'}_{ws}-T_{ws}\right/*H_{ws}$$


$f_4$：在归入店均匀增加的同时，要优先分给优先级高的店铺

$$f_4= \sum_{w \in W}\sum_{s \in S} (H_{w,s}* \left|x_{wsw'} - avg(x_{wsw'}) \right|) $$

其中 $avg$ 为商品总调入量/调入店铺数

$$avg(x_{wsw'}) = \sum_{s \in S}\frac{x_{wsw'}}{\sum_{w \in W,w=w',x_{wsw'}>0}|1|}$$

## 约束

1. 数量平衡。
	分配前后的 SKU 总数量要一致。
	$$\forall s \in S,\sum_{w \in W} Q_{ws} = \sum_{w \in W} Q'_{ws}$$
2. 调出店需调空
	如果 SKC 要求归出店要调空（信息由 skc_merge_strategy.csv 中的 out_store_clear_out 给出），需要清空归出店库存。
	
	$$\forall s \in S, \forall w \in W, \text{when} \ E_{ws} = 1, x_{wsw'} =\{0,Q_{ws}\}$$
	
3. 调入店数目上限有要求
	信息由 skc_merge_strategy.csv 中的 store_upper_limit 给出。如果该数值为 0 表示，不用考虑该约束条件；如果该数值为 大于 0 的整数 n，那么要求特定 SKC（由 skc_merge_strategy.csv 中的product_code 给出），最多可以归入到 n 家店铺中。

	$$\forall s \in S, \sum_{w \in W, w=w’,x_{wsw’}>0}|1| <= A_s$$

	$D_{a}$ 表示SKC $a$ 商品会发往不同的仓库$d$的仓库数量$D$
	
4. 在单尺码上低优先级上的门店都归完了再归出高优先级的门店。

$$\forall s \in S,\forall w_1,w_2,w' \in W,\text{when } H_{w_1s}<H_{w_2s} \text{ and } x_{w_2sw'}<Q_{w_2s},x_{w_1sw'}=0$$
