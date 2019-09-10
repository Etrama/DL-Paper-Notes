# Notes on the paper: <br> ShakeDrop Regularization for Deep Residual Learning

# Difficulty of Comprehension: TBD

## Abstract
* The paper proposes a new regularization method called Shakedrop for Resnet and Resnet like architectures - Wide Resnet, Pyramid Net and ResNext.
### Question: What does regularization even mean beyond a technique that reduces overfitting?
### Answer:
* Anything that improves the accuracy of a NN is usually termed as regularization.
* This includes stuff like Weight Decay, Dropout, Early Stopping, L2 regularization.
* Data Augmentation is also a form of regularization, which makes sense since the AutoAugment paper used this paper for comparison.
*To my disbelief, this is all there is to regularization conceptually.

### Back to the Abstract
* Shake-Shake can only be applied to Resnext, shakedrop can be applied to resnet like architectures, so it seems to be slightly more general at this point.
* If memory serves, the AutoAugment paper that we covered earlier achieves better SOTA than Shakedrop and can be applied to all architectures. Having said that, our goal is to read more papers and be acquianted with mroe ideas in DL, so here we go again.
* ShakeDrop enables stability of training, effective regeularization often introduce instability during training, I'm guessing this means that the variance in the accuracy over a few runs is small when we use ShakeDrop.

## Introduction
* Despite the introduction of very deep CNNs inspired by Resnet-like architectures, the problem of generalization error (difference between train and test error) must still be addressed.
* "Widely-used regularization methods
include data augmentation [16], stochastic gradient descent
(SGD) [30], weight decay [18], batch normalization
(BN) [14], label smoothing [23], adversarial training [6],
mixup [31, 24, 25] and dropout [21, 26]."
* Shake-Shake interferes with the forward and backward pass during training using a random variable. Has two drawbacks:
   1. Can only be applied to Resnext
   2. We dont know WHY it works
* ShakeDrop will address the above:
   1. Can be applied to all Resnet-like architectures (Wide Resnet, Pyramid net, Resnet) - More general
   2. The paper presents an intuitive understanding of Shake-Shake
   
## Regularization methods for ResNet family
### Shake-Shake regularization: 
  * used ONLY on ResNext.
  * Basic ResNext block has a 3 branch architecture given by: <br>
  (Side Note: How to show Math equations in Github: https://stackoverflow.com/questions/11256433/how-to-show-math-equations-in-general-githubs-markdownnot-githubs-blog, decided to use https://mathpix.com/)
  * <img src="https://cdn.mathpix.com/snip/images/rifYQ8mnuwzfM9EB3KASdHhpiXbO20Bj89JrvQl4054.original.fullsize.png"><br>
      * x  - input
      * G(x) - output
      * F<sub>1</sub>(x) - output of one residual branch
      * F<sub>2</sub>(x) - output of other residual branch
      * This is called a 3 branch architecture
  * Let &alpha; and &beta; be independent random co-efficients in [0,1]. Shake-Shake is given as:
  * <img src="https://cdn.mathpix.com/snip/images/fVHP6xNT4XLg-uKcWnRcndyFRPFA0w7xn1kjgvYO8Ag.original.fullsize.png"><br>
      * train-fwd - Forward Pass
      * train-bwd - Backward Pass
  * <img src="https://cdn.mathpix.com/snip/images/-9WRQa-0GsncqcH641B8UpMggSTRe0QevrF8sV5G1vU.original.fullsize.png"><br>
  * Suggested to train 6 times longer than usual.
  * Examine F<sub>1</sub>x in the G(x) equation above:
      * In the forward pass, it is multiplied by &alpha;
      * In the backward pass, it is mulitplied by &beta;
      * The paper mentioned that if the output of a residual branch is multiplied by &alpha; then we multiply the output of the backward pass by the same co-efficent. But, that isn't the case here.
      * ShakeDrop regularization makes the gradient &beta;/&alpha; times as large as the correctly (coefficient exclusive) gradient. (1-&beta;)/(1-&alpha;) is the scaling factor of the other residual branch's gradient.
      * This disturbance prevents from the network parameters being captured in local minima, in simpler words, in the loss vs parameter graph, it helps avoid the local dips and looks for the biggest dip (global minima). WHY? We dont know yet.
       
### RandomDrop Regularization (aka Stochastic Depth and ResDrop)
* Proposed for ResNet but also applied to PyramidNet.
* Uses the following 2 branch architecture:
* <img src="https://cdn.mathpix.com/snip/images/-53KvhzkwoTmBmfX6kLaVi-pDIX2Dv9zR9E7hND-neg.original.fullsize.png"><br>
    * x  - input
    * G(x) - output
    * F(x) - output of residual branch
* RandomDrop makes drops some stochastically selected building blocks (layers). Supposed to make the network shallow in learning and avoid local dips I guess. 
* RandomDrop is a simplied version of dropout. Dropout drops network nodes, which Randomdrop drops layers.
* The l<sup>th</sup> from the input layer is given as:
* <img src="https://cdn.mathpix.com/snip/images/pY9Ep4jTCry_AmVyta0JJmkpt4UwU3biA-Ql_qHbPtQ.original.fullsize.png"><br>
* b<sub>l</sub> is a Bernoulli Random Variable.
* <img src="https://cdn.mathpix.com/snip/images/bqQnEwFA2wZjECiGfGz_tCYHPc79o8uknpnWMhApASw.original.fullsize.png"><br>
* <img src="https://cdn.mathpix.com/snip/images/YUYkUfzrLHowWxFvMOR0rLl6E56fwNMrjMbXTHc9ieU.original.fullsize.png"><br>
* Recommended to use a linear decay rule to determine p<sub>l</sub>.
* <img src="https://cdn.mathpix.com/snip/images/RPg1t87F79L7HV_XoB2erLVES9Jjh6QaTjLuLZpCgoU.original.fullsize.png"><br>
    * L - number of all building blocks
    * p<sub>L</sub> is the initial parameter, to be set to 0.5.
    
## ShakeDrop Regularization:
    
    
    
    
    
      

