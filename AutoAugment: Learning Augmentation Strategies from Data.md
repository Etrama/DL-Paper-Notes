Notes on the paper called:
Auto Augment: Learning augmentation strategies from data

# Difficulty of Comprehension: Straightforward. Paper does not feel overwhelming at any point.

# Abstract
* Like the name suggests, the paper wants to automate the search for improved data augmentation policies.

* We want better augmentation mostly because, data augmentation is akin to having more data and having more 
data is even more important that tuning the learning rate or any other hyper parameter.

* The paper comes up with a **AutoAugment** procedure which searches a search space where a policy has different
sub policies within it and one of these is randomly chosen for each mini-batch.

* A sub-policy has 2 operations and each operation is an image processing function wherein each sub-policy has some operation probabilities and magnitudes. 

* Operations include:
  * Translation
  * Rotation
  * Shearing
 
* The paper uses a search algo to find the best policy for the highest validation accuracy.

* The paper attains SOTA performance on quite a few Image recognition tasks. This paper came out on 11 Apr 2019 - Google Brain.

* The policies learned on Image Net transfers well to a few other datasets.

# Introduction
* The authors stress the importance of data augmentation as a technique to increase the "amount and diversity of data".

* Data augmentation is used to teach a model about invariances in the data domain, i.e used to the tell the network that flipping
some image horizontally need not necessarily change its classification subject to the dataset that one is working on.

* The authors cite a few papers wherein network architectures are used to hardcode these invariances.

* They argue that using data augmentation "can be easier" than hardcoding these invariances into network architectures.

# Table 1
* I wonder why the GPU hours are 0 for CIFAR-100 and Stanford Cars?
* Is it because the CIFAR-100 uses the same augmentation policies as CIFAR-10? Same for Stanford Cars which use the same polcies as Image Net?
* The authors use a NVIDIA Tesla P100, these results could possibly be recreated....

# Back to the Introduction
* The authors cite a number of papers to showcase that research focus has been towards designing better architectures.

* Image Net augmentation policies haven't changed since 2012. Wow, really? Unlike the above, it seems like DL as a community has been a little myopic about augmentation. Seems like a fertile ground for further research.

* Usually, augmentation improvements do not transfer to other datasets.

* The authors aim to automate the process of finding an effective data augmentation policy for a target dataset.

* Each policy in the implementation expresses: 
  * Several choices and possible augmentation operators.
  * Probabilities of applying these.
  * Magnitudes of applying these.

* RL is used as the search algorithm for the AutoAugment policies.

* RL guided CNNs. Hmm. Nice. Are there more papers along this vein?

* AutoAugment is effective in two different ways:
  1. Find the best augmentation policy for a particular dataset (AutoAugment-direct)
  2. Transfer these learned policies to new datasets (AutoAugment-transfer)
 
* AutoAugment-direct achieves SOTA on (Apr 11, 2019):
  * CIFAR-10
  * CIFAR-100
  * SVHN
  * reduced SVHN
  * Imagenet without additional data
  
* Using AutoAugment-transfer with the original policy trained on Image Net data, leads to improvment in test set error for the Stanford Cars and FGVC datasets. 

* The authors say: "This result suggests
that transferring data augmentation policies offers an
alternative method for standard weight transfer learning"
  * Instead of using the pre-trained Image Net weights to fine tune results on Standford Cars and FGVC, was only data augmentation used?
  
# Related Work
* Previous approaches have been manual, require expert knowledge (or a lot of trial and error I guess), and dataset specific (not transferable - so all the effort spent coming up with with data augmentations has to be replicated for every dataset).
* "For example, on MNIST,
most top-ranked models use elastic distortions, scale, translation,
and rotation"
* "On natural image
datasets, such as CIFAR-10 and ImageNet, random cropping,
image mirroring and color shifting / whitening are
more common"
* Check out some of the other papers given:
"Previous attempts at learned data augmentations include
Smart Augmentation, which proposed a network that automatically
generates augmented data by merging two or
more samples from the same class [33]. Tran et al. used a
Bayesian approach to generate data based on the distribution
learned from the training set [61]. DeVries and Taylor
used simple transformations in the learned feature space to
augment data [11].
Generative adversarial networks have also been used for
the purpose of generating additional data (e.g., [45, 41, 70,
2, 56]). The key difference between our method and generative
models is that our method generates symbolic transformation
operations, whereas generative models, such as
GANs, generate the augmented data directly. An exception
is work by Ratner et al., who used GANs to generate sequences
that describe data augmentation strategies [47]."


# AutoAugment: Searching for best Augmentation policies directly on the dataset of interest
* Defined as a Discrete search problem.
* Two components:
  * Search Algorithm
  * Search Space
* The search algorithm (controller RNN) tries a policy S (operation, batch-wise probability, magnitude)
* S is used to train the fixed network architecture giving some validation accuracy R
* R is used to update the controller

* For further learning: Why is it said that R is not differentiable? How does this justify updation by policy gradient methods?

### Search space details - refer to Fig 2 in the paper:
* A Policy consists of 5 sub-policies with each sub policy being made up of 2 operations.
* Eg: Policy X with have 5 sub policies A, B, C, D, E where A is made up of say Invert and Shear. Invert applied with a probability of 0.5 and no Magnitude (Invert doesnt need a magnitude - special case of the rotate policy I'd say). Shear applied with a probability of 0.8 and a magnitude of 7.
* A, B, C, D, and E are applied in the order defined, changing the order changes the policy.
* For every image in a mini batch, a sub policy is chosed at random to generate a transformed image to train the neural network.
* 16 different operations were considered.
* In Addition to AutoAugment the authors mention using Cutout and SamplePairing.
* Each operation comes with a default range of magnitudes, 10 discrete values for each operation, to enable searching for these using an algorithm. For example, 
* The probability of applying an operation is also discretized into 11 uniformly spaced values. Im guessing here: 0.0, 0.1, 0.2, ....., 0.9, 1.0 were the 11 values used.
* The search space for this problem is then (16 x 10 x 11)<sup>2</sup>. Explanation:
* 16 operations possible.
* 10 possible magnitudes for each. So the search space is (16 x 10)
* 11 possible probabilities for these. So the search space is (16 x 10 x 11)
* Each sub policy has 2 operations, so the search space is (16 x 10 x 11)<sup>2</sup>
* The identity operation is implicitly used, call an operation with 0 probability.
   
### Search algorithm details - refer to Fig 1 in the paper:
* RL used.
* 2 components:
 * RNN Controller
 * Proximal Policy Optimization training algorithm
* The controller makes 30 softmax predictions. 
I'm imagining this an as array of 30 values, something like: 
  1. Operation Type 1 for Subpolicy 1
  2. Magnitude 1 of above for SubPolicy 1.
  3. Probability 1 of SubPolicy 1.
  4. Operation Type 2 for Subpolicy 1
  5. Magnitude 2 of above for SubPolicy 1.
  6. Probability 2 of SubPolicy 1.
 This goes for all 5 sub policies. The subpolicy itself is determined from the bunch of 6 values like above. Operation Type is one of the 16 types defined. Magnitude is one of the 10 possible magnitude values. Probability of application is one of the 11 possible probabilities that can be applied.
  
 ### The training of controller RNN:
* RNN trained with a "reward signal"
* This reward shows how good the model is on a "child model" - just a model to determine the RNN , need not be very accurate, just have changing accuracy when the sub policies change.
* A child model is trained with augmented data generated by the 5 sub policies chosen in the above array.
* For each image in the mini batch one of the 5 sub policies is chosen randomly to augment the image.
* Child model is evaluated on validation set to measure accuracy 

### Architecture of controller RNN and training hyper-parameters:
* Refers to another paper.
* One layer LSTM RNN with 100 hidden units.
* This LSTM makes 2 x 5B softmax predictions. (B is usually 5)
* Joint prob is product of all 10B softmaxes, which is used to compute the gradient for the RNN
* Validation Network gives low prob for bad child networks and high prob for good ones.
* Used PPO - Proximal Policy Optimization with a 0.00035 learning rate.
* Is this weight decay? - Entropy penalty with weight of 0.00001.
* Baseline function is an exponential average of previous rewards with a weight of 0.95. I think this would be something like:
Reward at time t +
(0.95)<sup>1</sup> Reward at time t-1 +
(0.95)<sup>2</sup> Reward at time t-2 +
and so on.
* Weights of the controller are initialized uniformly between -0.1 and 0.1.
#### Chance for futrther research, paper openly admits that the search algorithm- PPO used may not be the best. Suggests the usage of genetic programming or even random search to improve the results in the paper.
* After this search, sub policies from 5 best policies are concatenated into a single policy with 25 sub policies, which is used to train the mdoels for each dataset.

# Experiments and Results:
Refer to the paper.

