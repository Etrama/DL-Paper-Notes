Notes on the paper called:
Auto Augment: Learning augmentation strategies from data

# Abstract
* Like the name suggests, the paper wants to automate the search for improved data augmentation policies.

* We want better augmentation mostly because, data augmentation is akin to having more data and having more 
data is even more important that tuning the learning rate or any other hyper parameter.

* The paper comes up with a **AutoAugment** procedure which searches a search space where a policy has different
sub policies within it and one of these is randomly chosen for each mini-batch.

*
