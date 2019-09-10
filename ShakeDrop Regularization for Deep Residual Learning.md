# Notes on the paper: <br> ShakeDrop Regularization for Deep Residual Learning

# Difficulty of Comprehension: TBD

## Abstract
* The paper proposes a new regularization method called Shakedrop for Resnet and Resnet like architectures - Wide Resnet, Pyramid Net and ResNext.
### Question: What does regularization even mean beyond a technique that reduces overfitting?
* Shake-Shake can only be applied to Resnext, shakedrop can be applied to resnet like architectures, so it seems to be slightly more general at this point.
* If memory serves, the AutoAugment paper that we covered earlier achieves better SOTA than Shaedrop and can be applied to all architectures. Having said that, our goal is to read more papers and be acquianted with mroe ideas in DL, so here we go again.
* ShakeDrop enables stability of training, effective regeularization often introduce instability during training, I'm guessing this means that the variance in the accuracy over a few runs is small when we use ShakeDrop.

## Introduction
