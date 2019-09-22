# Notes on the paper: <br> A Closer Look at Memorization in Deep Learning

# Difficulty of Comprehension: TO BE DETERMINED.

# Abstract
* I found out about this paper, here: https://www.youtube.com/watch?v=pFWiauHOFpY&t=719s. I recommend watching this video, it is really good.
* Models with more parameters than inputs should memorize their inputs and not generalize well. (Any sufficiently big model actually) This is not observed in practice, because most famous models do generalize well. WHY?
* Couple of ways to think about DNNs (Deep Neural Networks):
  1. Incorporate good genetic priors which is why they do well in the physial world.
  2. Universal Function Approximators - more expressive with more depth.
* However, DNNs could still be memorizing their inputs. I believe this is because:
  1. Genetic evolution does not guarantee an elimination of memorization - In some cases memorization might help - Memorizing the location of a water source.
  2. If the function is only real valued for a very limited set of inputs, then the model might be more efficient by memorizing the output for certain inputs rather than doing the computation for every input - a function which is infinity for a lot of its inputs - maybe tan(90) or something like that.<br>
  
* <b>Effective Capacity</b>:
<br><img src="https://cdn.mathpix.com/snip/images/uJW4rcqdVsaEy7fJtz37oGRg-1XI_uySxCLd8vFVq4s.original.fullsize.png"><br>
 
  * EC(A) = Effective Capacity of some input A.
  * The input A = Learning Algorithm - defined by both the model and the training procedure - Train Resnet for 1000 epochs using SGD with a learning rate of 0.01
  * h - conditonal output, provided there exists D such that this h belongs to <i>A(D)</i>.
  * So the effective capacity is dependent on the architecture (Resnet) , the optimization method (Adam/SGD/AdaGrad), the number of epochs that a network is trained for (10/100/1000....), the learning rate (0.1/0.01/0.001) and also differs for every Dataset, since the optimal hyper-parameters will be differenet for every dataset for a particular architecture.
### Do we also include other parameters involved in the training whie defining A? Factors such as the batch size, the train and test transofrms, etc? Perhaps the GPU on which we train the network?

* Zhang et al(2017) have demonstrated that DNNs can EVEN fit pure noise without needing significantly more time.
* This paper tries to explore the extent to which DNNs memorize their inputs by fitting models on random data.
* Memorization - contrast between DNN behvior on real data vs noise data.
## Main takeaways:
* DNNs do not just memorize real data.
* DNNs are content aware - they learn simple patterns before memorization.
* Regularization technqiues can affect memorization in DNNs based on the techinque used, the models still learn well on real data.

### How do the models distinguish between real data and noise without explicitly being told to do so?
### To what extent are these models content aware? How is the performance on simple and complicated patterns?

## Experiment Details are best read in the paper itself.

### Some of the labels or the entire data set are replaced with noise.

## Qualitative differences of DNNs trained on Random vs Real Data


  
  
