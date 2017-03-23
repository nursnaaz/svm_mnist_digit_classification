# SVM MNIST digit classification in python using scikit-learn

Project presents well known problem of [MNIST handwritten digit classification](https://en.wikipedia.org/wiki/MNIST_database). For the puropose of this tutorial I will use [Support Vector Machine (SVM)](https://en.wikipedia.org/wiki/Support_vector_machine) algorithm with raw pixel features. Solution is written in python with use of [scikit-learn](http://scikit-learn.org/stable/) easy to use machine learning library.

![Sample MNIST digits visualization](https://plon.io/files/58cfb7171b12ce00012bd6bf)




The goal of this project is not to achieve the state of the art performance, rather to teach you 
**how to train SVM classifier on image data**. 
If you want to hit the top performance, this two resources will show you current state of the art

* [Who is the best in MNIST ?](http://rodrigob.github.io/are_we_there_yet/build/classification_datasets_results.html#4d4e495354)
* [Kaggle digit recognizer comptetition](https://www.kaggle.com/c/digit-recognizer)

Table below shows some results in comparison with other models:


| Method                                     | Accuracy | Comments     |
|--------------------------------------------|----------|--------------|
| Random forest                              | 0.937    |              |
| Simple one-layer neural network            | 0.926    |              |
| Simple 2 layer convolutional network       | 0.981    |              |
| SVM RBF                                    |          | C=5, gamma=0.05 |
| Linear SVM + Nystroem kernel approximation |          |              |
| Linear SVM + Fourier kernel approximation  |          |              |
------------------------------------------------------------------------


## Solution

In this tutorial I use two approches for SVM learning. 
First, uses classical SVM with RBF kernel. The drawback of this solution is rather 
long training on big datasets, although the accuracy with good parameters is high. 
The second, uses Linear SVM, which allows for training in O(n) time. In order to 
achieve high accuracy we use some trick. We aproximate RBF kernel in a high dimensional 
space by embedings. The teory behind is quite complicated, 
however [scikit-learn has ready to use clases for kernel approximation](http://scikit-learn.org/stable/modules/kernel_approximation.html#kernel-approximation). 
We will use:

* Nystroem kernel approximation
* Fourier kernel approximation


The code was tested with python 2.7 and python 3.5.


## How the project is organised

Project consist of three files:

* _mnist_helpers.py_ - contains some visualization functions: MNIST digits visualization and confusion matrix
* _svm_mnist_classification.py_ - script for SVM with RBF kernel classification
* _svm_mnist_embedings.py_ - script for linear SVM with embedings

### SVM with RBF kernel

The _svm_mnist_classification.py_ script downloads the MNIST database and visualize some random digits.
Next, it standarize the data (mean=0, std=1) and lauchn grid search with cross validation for finding the best parameters.

Grid search was done for params C and gamma, where C=[0.1,0.5,1,5], gamma=[0.01,0.0.05,0.1,0.5].
I have examine only 4x4 different param pairs with 3 fold cross validation so far (4x4x3=48 models), 
this procedure takes 3687.2min :) (2 days, 13:56:42.531223 exacly) on one core CPU.

Param space was generated with numpy logspace and outer matrix multiplication. 
```
C_range = np.outer(np.logspace(-1, 0, 2),np.array([1,5]))
# flatten matrix, change to 1D numpy array
C_range = C_range.flatten()

gamma_range = np.outer(np.logspace(-2, -1, 2),np.array([1,5]))
gamma_range = gamma_range.flatten()

```
Of course, you can broaden the range of parameters, but this will increase the computation time.


![SVM RBF param space](https://plon.io/files/58d3af091b12ce00012bd6e1)


Grid search is very time consuming process, so you can use my best parameters (from the range C=[], gamma=[]):
* C = 5
* gamma = 0.05

With this params:

* training time =
* accuracy: 



### Linear SVM with different embedings

The script _svm_mnist_embedings.py_ presents accuracy summury and training times for 
full RBF kernel, linear SVC, and linear SVC with two kernel aproximation 
Nystroem and Fourier.

This technique is really useful if you have big datasets. Learning SVM with RBF kernel 
could be time consuming. The time complexity of this solution is O(n^2) while specialized
linear svm (liblinar, SGD etc) has O(n). In order to be more expressive we try to aproximate
nonlinear kernel, map vectors int higher dimensional space explicity and use fast linear SVM in 
this new space.



## Further improvements
 
* Augmenting the training set with artificial samples
* Using Randomized param search


## Source code

* [Download SVM MNIST code from GitHub](https://github.com/ksirg/svm_mnist_digit_classification)
* [Run SVM MNIST project online](https://plon.io/explore/svm-mnist-handwritten-digit/USpQjoNcO8QHlmG6T)


## Useful SVM MNIST learning materials

* [MNIST handwritten digit recognition](http://brianfarris.me/static/digit_recognizer.html) - author compares a accuracy of a few machine learning classification algorithms (Random Forest, Stochastic Gradient Descent, Support Vector Machine, Nearest Neighbors)
* [Digit Recognition using OpenCV, sklearn and Python](http://hanzratech.in/2015/02/24/handwritten-digit-recognition-using-opencv-sklearn-and-python.html) - this blog post presents using HOG features and a multiclass Linear SVM.
* [Grid search for RBF SVM parameters](http://scikit-learn.org/stable/auto_examples/svm/plot_rbf_parameters.html)
* [Fast and Accurate Digit Classification- technical report](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2009/EECS-2009-159.html) - there is also download page with custom [LibLinear intersection kernel](http://ttic.uchicago.edu/~smaji/projects/digits/)
* [Random features for large-scale kernel machines](http://www.robots.ox.ac.uk/~vgg/rg/papers/randomfeatures.pdf) Rahimi, A. and Recht, B. - Advances in neural information processing 2007,
* [Efficient additive kernels via explicit feature maps](http://www.robots.ox.ac.uk/~vedaldi/assets/pubs/vedaldi11efficient.pdf) Vedaldi, A. and Zisserman, A. - Computer Vision and Pattern Recognition 2010
* [Generalized RBF feature maps for Efficient Detection](http://www.robots.ox.ac.uk/~vedaldi/assets/pubs/sreekanth10generalized.pdf) Vempati, S. and Vedaldi, A. and Zisserman, A. and Jawahar, CV - 2010

 
