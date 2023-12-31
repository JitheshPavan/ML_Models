# Image Classification Using Machine Learning
The collection of codes written for Image classification. The language used is Python, and the library Pytorch. Thanks to YouTube tutorials by Daniel Bourke. The repo consists of two main models.
## TinyVGG.
![1 3ZkXJ-nIajuY3iX27w12aw](https://github.com/JitheshPavan/ML_Models/assets/140151086/87003856-20c5-46b6-aafb-058134e7fb2e)
VGG Network is composed of a series of interspersed convolutional and max pooling layers, where the spatial size of the representation gradually decreases, but the number of channels increases. These are followed by three fully connected layers. The VGG network also includes dropout layers to overcome overfitting. It also discourages joint probability distribution and makes individual representation meaningful.
I trained this network in a supervised manner using CIFAR-10 and CIFAR-100 datasets. 
</br>
## Lenet5
![1lvvWF48t7cyRWqct13eU0w](https://github.com/JitheshPavan/ML_Models/assets/140151086/91d8b2a8-4cc0-4466-8730-a274d65795e7)
</br>
It's a much simpler networker. Requires images to be 32*32. 
## AlexNet
 First major convolutional network to perform well on image classification. It consists of eight hidden layers with ReLU activation functions, of which the first five are convolutional, and the rest are fully connected.
## Classification of Golgi Dispersal
Written for the paper “Quantification of Golgi Dispersal and Classification Using Machine Learning Models” Micron Journal (Impact Factor: 2.4)
