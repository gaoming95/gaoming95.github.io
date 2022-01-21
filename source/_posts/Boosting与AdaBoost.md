---
title: Boosting与AdaBoost
subtitle: Boosting与AdaBoost
date: 2019-09-06
author: 高明
tags:
	- 机器学习
---



# Boosting与AdaBoost

提升学习是一种机器学习技术，可以用于回归和分类问题。它每一步产生弱预测模型 (如决策树)，并加权累加到总模型中。如果每一步的弱预测模型的生成都是依据损失函数的梯度方式的，那么称为梯度提升(Gradient Boosting)。

提升技术的意义： 如果一个问题存在弱预测模型，那么可以通过Boosting技术得到一个强预测模型。

常见的模型有： Adaboost、Gradient Boosting(GBT/GBDT/GBRT)、XGBoost、LightGBM。

![原 始 训 练 集  （ M 个 样 本 ）  多 个 ' ' 分 类 器  最 终 。  根 据 效 果  修 改 后 训 练  集 1 （ M 个 样 本 ）  弱 学 习 器 2  根 据 效 果  修 改 后 训 练  集 2 （ M 个 样 本 ）  弱 学 习 器 3  根 据 效 果  修 改 后 训 练  集 3 （ M 个 样 本 ）  弱 学 习 器 4  根 据 效 果  修 改 后 训 练  集 4 （ M 个 样 本 ）  弱 学 习 器 5 ](file:///C:/Users/gaoming/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

参考文献

[Boosting提升学习](https://www.jianshu.com/p/c9e44a2120f7)

