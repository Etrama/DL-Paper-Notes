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
<img src="https://cdn.mathpix.com/snip/images/7-zHnbTRX3xvITcjs_E7jGUvGc5uLhtH9dOFiuBL1qY.original.fullsize.png"><br>
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
<img src="https://cdn.mathpix.com/snip/images/No8c0ciyigGDIx065G9i5uSor-nAhzGRAgaLRvXzBEQ.original.fullsize.png"><br>
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
<img src="https://cdn.mathpix.com/snip/images/9td58DVkAsnvf2jpKDzdeqiDF4qfuwNqpmHEgLEc_H0.original.fullsize.png"><br>  
Shakedrop is given as:
<img src="https://cdn.mathpix.com/snip/images/h2cxtd3eJytJ-SJIsiUy96FnP7qFBZiYlMi-veF3AXc.original.fullsize.png"><br>    
      * b<sub>l</sub> is a Bernoulli random variable, where &nbsp; <img src="https://cdn.mathpix.com/snip/images/IyCS_I3D-45g3GZ93j2toq4LdheBmV4Ys8fKc0gpXQs.original.fullsize.png"><br>
      * &alpha; and &beta; are independent random variables.
      * &alpha; and &beta; mostly belong to the range:
            1.&nbsp;<img src="https://cdn.mathpix.com/snip/images/rK92IWRFW9rKPuMbN_1WRBkftUgu38PAhXDLE8KJdoc.original.fullsize.png"><br>
            2.&nbsp;<img src="https://cdn.mathpix.com/snip/images/oW3DKk7lW-l94yh5lxfJn4mRAW9zg60WDIziEB6Gqog.original.fullsize.png"><br>
      * In the training phase b<sub>l</sub> controls the behavior of Shake-Drop.
      * Two cases:
            1. b<sub>l</sub> = 0. Shake-Drop reduces into Resnet. <img src="https://cdn.mathpix.com/snip/images/OK90Bx62NWwNivpdajfn4FBL2mG9C6LLoJt_jIdx7WQ.original.fullsize.png"><br>
            2. b<sub>l</sub> = 1. Shake-Drop's Resnet form is shaken by &alpha; and &beta;. <img src="https://cdn.mathpix.com/snip/images/3Qg3sbxY9XA4brTrD2I6QnOxZVbFJW_lzMXzedM2qIQ.original.fullsize.png"><br>
            
### What in god's name is a Bernoulli random variable?
* To be very honest, not at all complicated.
* Refer the first few lines of: <a href= "https://web.stanford.edu/class/archive/cs/cs109/cs109.1178/lectureHandouts/070-bernoulli-binomial.pdf">this.</a>
* It's simply a variable, which can either have the value 0 or 1.
* A Switch Flick, a coin toss, a single digit binary state.

## Interpretation of Shake-Shake regularization:
* Tries to give an intuitive explanation of Shake-Shake ever heretofore attempted.
<img src="https://cdn.mathpix.com/snip/images/7-zHnbTRX3xvITcjs_E7jGUvGc5uLhtH9dOFiuBL1qY.original.fullsize.png"><br>  
* As seen above, Shake-Shake sways the forward pass by a factor of &alpha;.
* Some other paper (DeVries and Taylor) demonstrated that "interpolation of data in the feature space can synthesize reasonable augmented data." - meaning this is a form of data augmentation. I guess we could see it like that:
   1. Imagine you had an input which goes through vanilla Resnet absolutely unmodified. The input is - x.
   2. But then we had another resnet wherein we multiply the forward pass by some factor &alpha;. We COULD see this new input as a new data point which is not in the original dataset. 
      * For 1 above - The input for the resnet was - x
      * For 2 above - The input for this new resnet is - &alpha; * x.
      * We can interpret &alpha; * x as some data point, since x is also a data point. 
      * This &alpha; * x datapoint was not present in the original data.
      * We can interpret this as a challenge to the network. Making it harder to converge.
      * Similarly, consider &beta; on the backward pass. This factor also makes it harder to converge.
      * I'm guessing here, but, given we have enough data, these challenges called &alpha; and &beta; presented to the network make it generalize better since it is difficult to achieve convergence.
      * This guess is supported by the fact that it takes 6 times as long to achieve the improved results. 

### Random Musing: Can we try make this faster? I think we should compare the time taken for this with the time taken for Auto-Augment, since Auto-Augment already achieves better results.

### Random Musing: Do all augmentation appraoches increase the time that it takes to train a network?

## Single-branch Shake Regularization:
* Since Shake-Shake relies on &alpha; and 1 - &alpha; during the forward pass (same dependence on &beta; during the backward pass), it absolutely needs 2 branches, so that we can scale one of them by &alpha; and one of them by 1 - &alpha;.
### Random musing: Has anyone tried a 3 branch architecture with the co-efficients of the branches summing up to one? Ouch. ResNext which is used in Shake-Shake IS a 3 branch architecture. Serves me right for only reading the Resnet paper. <br>(Spoiler slert) Since we know the single branch shake story has a bad ending - one critic said it failed to deliver. <br>It seems like perhaps more branches scaled with the co-efficients summing up to one might not be bad.<br> Maybe we could try making one where [&alpha; + (1- &alpha;) + (1- &alpha;)<sup>2</sup>] as the scaling factors.<br> We'll need to analyse the range of &alpha; and &beta; to maybe come up with a floor and ceiling for &alpha; and &beta; values.
* The paper quoted above (Devries et al) also showed that noise addition in the feature space generates reasonably augmented data.
* Ok, I guess noise addition too shifts the data points up and down a little making it harder to learn. Similar to scaling it by a constant, but instead of multiplying we just add noise to the data. 
* Coming back to Single Branch Shake: 
* <img src="https://cdn.mathpix.com/snip/images/lPsqyP5p0Mu4q1FDjP8oBuwGp5DLwaNfLephcTSrUcg.original.fullsize.png"><br>
* Like the spoiler said, Single Branch Shake does not work well in practice. 

### Why doesn't Single Branch shake work well despite Shake-Shake working so well on multiple branches? Why can't I think of anything other than the number of branches?

### Random Musing: To be honest, I'm a bit puzzled here. The authors mention applying Single Branch Shake on a 110-layer PyramidNet with some range of &alpha; and &beta; AFTER applying Shake-Shake.

### Random Musing: Does this mean that at the risk of having more generalized error, we can train our network faster? Maybe link it to One Shot Learning? I think we should read a One-Shot Learning Paper next.

## Stabilization of training:
* The authors also talk about the extra branch.
1. Having &alpha and &beta; scale the forward and backward pass scales the gradient by &beta;/&alpha; or (1- &beta;)/(1-&alpha;) times the usual gradient.
2. This means as alpha gets close to 0 or 1, the gradient gets close to infinity / a very very large number - an exploding gradient.
3. The two branches work as a failsafe because when &alpha; is close to 0 or 1 the gradient on the other branch is still manageable.
4. Since, single branch shake does not have this fail-safe that is probably why it doesn't do well.
5. There's a note that says that &beta;/&alpha; or (1- &beta;)/(1-&alpha;) do well if they are on the same side of 0.5, that is &alpha; and &beta; are 0.5 and 0.7 because the scaling is less than the case when say &alpha; and &beta; are 0.1 and 0.9 respectively. Less likely to explode a gradient with the former.
* The authors suggest using RandomDrop to balance out the effects of regularization and pertubation (explosion of gradient).
* They further suggest that learning is correctly promoted the original resnet network / base network of choice is selected and the Random Drop is only selected when there could be an exploding gradient. I think the idea is that we just drop the layer which caused the gradient to explode.

## Relationship with existing regularization methods:
* The authors compare ShakeDrop vs RandomDrop and Dropout.
   1. ShakeDrop does not explicitly generate new data. THe scaling factors (&alpha; and &beta;) scale the input so it seems like the data point is different.
   2. ShakeDrop doesn't update network parameters based on noisy gradients.
* Data Augmentation and Adversarial Training generate new data explicitly. 
   1. Data Augmentation does so on say image data by rotation, flipping, varying the brightness or contrast to make a slightly or vastly different copy of the original image.
   2. To be honest, I dont know much about Adversarial training. Adversarial Training, all I know is we generate some samples to try and fool the network.
* The authors summarize their results in this table: &nbsp; &nbsp; <img src="https://cdn.mathpix.com/snip/images/MUf3-bezBY6aE45BGjQeNA4LI7faveIqlsi1XJzRy0A.original.fullsize.png"> <br>

I think the conceptual part of the paper ends here, I jsut want to rush through the experiments now, because these will apply more when I start coding these papers. Let's move on to a different paper.
 

      
      
     

    
    
      

