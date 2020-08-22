---
layout: post
title:  "从头理理激活函数"
date:   2020-08-22
excerpt: "One ReLu to rule them all?"
image: "/images/activation_functions.jpg"
---

简单来说，激活函数的作用就是为神经网络提供非线性(non-linearity)，从而提高它的拟合能力。一个多层感知机(或者简单的全连接网络)，实际上就是将一个个线性模型依次叠加起来。然而线性模型如果仍然只是通过线性的方式组合，最终得到的结果也只不过是一个线性模型而已。这时就需要非线性的激活函数来达到这个目的。

早期的神经网络主要使用sigmoidal类型的激活函数，比如```hyperbolic tangent (tanh)```以及```logistic sigmoid```。


![sigmoid vs. tanh](/images/sigmoidal_funcs.png#center) 


这种类型的激活函数存在一个问题：当隐层神经单元接近-1/0/1的饱和状态时，其梯度会消失(vanishing gradient problem)，导致模型收敛速度降低，甚至收敛到局部最小值。因此需要十分注意隐层单元的初始化。另外一个问题则是此类激活函数无法得到一个稀疏的表征(sparse repre- sentation)。

ReLu类型的激活函数(rectified linear unit)则能够避免上述问题：

\[
h^{(i)} = max(w ^ {(i)T}x, 0)
\]

然而，ReLu将所有负值都置为零，也可能会导致某个神经单元一直不会激活，其参数也就一直不会调整。如果类似的单元过多，大量零梯度也会降低收敛速度。一个简单的解决方法就是在ReLu饱和时提供一个极小的但不为零的梯度(Leaky Relu):

\[
h^{(i)} = 0.01 * w ^ {(i)T}x, w ^ {(i)T}x < 0
\]

此时我们可以得到更稳健的梯度(有可能提高收敛速度)，但也失去了绝对稀疏性(hard-zero sparsity)。至于稀疏性到底有什么重要性，这里简单提一下：稀疏的表征对输入的扰动(噪音)更不敏感，泛化能力更强。ReLu类型的激活函数能够产生更加稀疏的表征，信息在不同的神经单元里均匀分布。

Leaky ReLu使用了一个固定的0.01超参数，然而这个数字背后并没有完备的理论依据。Randomized Leaky Rectified Linear Units (RReLUs)对其进行随机赋值，而Kaiming He将其扩展为参数化的ReLu，即Parametric Rectified Linear Unit (PReLU)，使得ReLu中的负梯度(非线性)可以通过学习自我调整。其论文发现在CNN图片识别任务中，PReLu效果更好，且顶部的卷积层后面接的PReLu往往比底部参数更大，表明顶部保留了更多信息，而底部则通过更多非线性得到更抽象的表征。

PReLu之后的又一变体是exponential linear unit (ELU)，其提出者认为ReLu的非负性导致其平均激活后的数值大于零，而这些大于零的值会形成下一层网络的偏置(bias)，大量强相关性的神经元会加重这一偏置。ELU则将重心放在解决这一偏置上，使得梯度更接近于自然梯度，从而提高模型收敛速度。在此之后还有scaled exponential linear units等等，但是这些ReLu的变体虽然有着这样那样的理论依据，似乎都没有足够的说服力表面其确实优于ReLu。目前来看，激活函数仍然是ReLu一统江湖。

在对激活函数进行各种魔改之前，也许我们应该仔细想想ReLu成功的根本原因。Le Quoc利用强化学习对激活函数做了探索，并提出了Swish:

\[
f(x) = x * (1 + \exp(-x))^{-1}
\]

Swish具有光滑且非单调递增两个特点