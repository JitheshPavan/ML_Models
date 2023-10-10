# Image Classification Using Machine Learning
The collection of codes written for Image classification. The language used is Python, and the library Pytorch. Thanks to YouTube tutorials by Daniel Bourke. The repo consists of two main models.
## TinyVGG.
![1 3ZkXJ-nIajuY3iX27w12aw](https://github.com/JitheshPavan/ML_Models/assets/140151086/87003856-20c5-46b6-aafb-058134e7fb2e)
The model first extracts local features using Convolutional NN. The output of each CNN is generally half of the previous former one. This output is then mapped to categories using a feed-forward neural network.
I trained this network in a supervised manner using CIFAR-10 and CIFAR-100 datasets. 
#Lenet5
![1lvvWF48t7cyRWqct13eU0w](https://github.com/JitheshPavan/ML_Models/assets/140151086/91d8b2a8-4cc0-4466-8730-a274d65795e7)
It's a much simpler networker.Requires images to be 32*32. 
