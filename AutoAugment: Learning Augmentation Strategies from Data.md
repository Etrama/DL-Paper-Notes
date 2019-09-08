Notes on the paper called:
Auto Augment: Learning augmentation strategies from data

# Abstract
* Like the name suggests, the paper wants to automate the search for improved data augmentation policies.

* We want better augmentation mostly because, data augmentation is akin to having more data and having more 
data is even more important that tuning the learning rate or any other hyper parameter.

* The paper comes up with a **AutoAugment** procedure which searches a search space where a policy has different
sub policies within it and one of these is randomly chosen for each mini-batch.

* A sub-policy has 2 operations and each operation is an image processing function wherein each sub-policy has some operation probabilities and magnitudes. Operations include:
  * Translation
  * Rotation
  * Shearing
 
* The paper uses a search algo to find the best policy for the highest validation accuracy.

* The paper attains SOTA performance on quite a few Image recognition tasks. This paper came out on 11 Apr 2019 - Google Brain.

* The policies learned on Image Net transfers well to a few other datasets.
