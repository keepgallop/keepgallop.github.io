title: '[跟我一起读论文] Visualizing Data using t-SNE'
id: 0404-143932
tags:
  - 学习笔记
  - 翻译
  - 科研
categories:
  - 跟我一起读论文
copyright: true
date: 2018-04-04 14:39:32
---
>**ABSTRACT**: t-distributed stochastic neighbor embedding (t-SNE) 由“神经网络之父” Geoffrey Hinton在2008年提出。Fine，这已经为我们拜读原作“Visualizing Data using t-SNE”提供了充分条件……

<!-- more -->
t-distributed stochastic neighbor embedding (t-SNE) 是在SNE基础上发展而来的一种非线性降维的机器学习算法，在数据可视化方面表现良好，尤其适合现在火热的CNN的可视化。

# 1.Introduction
高纬数据可视化，在各个领域都有重要作用，用以处理拥有大幅变化纬度的数据。过去很多可视化相关技术被提出，可以参考	[de Oliveira and Levkowitz的综述](From visual data exploration to visual data mining: A survey. IEEE Transactions on Visualization and Computer Graphics, 9(3):378–394, 2003.)。这些研究很多都是简单的提供用以展示高纬数据的工具，而把对结果的解读留给人类观察者。当面对包含千百个纬度的真实世界数据集时，这个缺点会严重制约其适用性。

与这种研究不同的是，


