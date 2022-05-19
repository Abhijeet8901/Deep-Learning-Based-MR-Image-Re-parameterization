# Deep-Learning-Based-MR-Image-Re-parameterization

[Abhijeet Narang](https://www.linkedin.com/in/abhijeet-narang-63b419175/) &nbsp;&nbsp; [Abhigyan Raj](https://www.linkedin.com/in/abhigyan-raj-023537145/) &nbsp;&nbsp; [Mihaela Pop](http://individual.utoronto.ca/mihaelapop/index.html) &nbsp;&nbsp; [Mehran Ebrahimi](https://www.linkedin.com/in/mehran-ebrahimi-a6a92711/)

This is the official implemetation of our BIOENG'22 paper **Deep Learning-Based MR Image Re-parameterization**.

Magnetic resonance (MR) image reparameterization refers to the process of generating via simulations of an MR image with a new set of MRI scanning parameters. Different parameter values generate distinct contrast between different tissues, helping identify pathologic tissue. Typically, more than one scan is required for diagnosis; however, acquiring repeated scans can be costly, time-consuming, and difficult in patients. Thus, using MR image re-parameterization to predict and estimate the contrast in these imaging scans can be an effective alternative. In this work, we propose a novel deep learning (DL) based convolutional model for MRI re-parameterization.

### Dataset
- We used MRiLab which is an MRI Simulator to generate synthetic brain scans. We use these for training and testing.
- We also use Brainweb which is a simulated brain database that contains a set of realistic MRI data volumes produced by an MRI Simulator. We use these only for testing.

## Network Architecure
Our method consists of two technical modules: 1) an image reconstruction [auto-encoder](https://ieeexplore.ieee.org/document/6302929)(for feature extraction) to extract image features of the input image; and 2) a coarse-to-fine fully convolutional network which utilizes the image features from the auto-encoder and enforces the construction of output image with desired parameters.<br />
The autoencoder comprises of two parts – an encoder that progressively downsamples the input using a series of contractive encoding layers and a decoder that reconstructs the input using a series of expansive decoding layers. Our encoder consists of 8, 3×3 convolutional layers with stride of 2 for downsampling, each of which is followed by batch normalization layer and Leaky-ReLU activation function with a slope of 0.2. Our decoder consists of 7, 3×3 transposed convolutional layers with a stride of 2 for upsampling, each followed by batch normalization and ReLU activation function. This is followed by a 3 × 3 convolutional layer with a stride of 1 and a 1 × 1 convolutional layer with a stride of 1 which acts as a pooling layer across the channels. We use tanh activation function for the last layer. After training the autoencoder, the outputs of 8 encoding layers are used as input image features for later part of our network.
<p align='center'>
     <img src="network imgs/Autoencoder.jpg" width="800" height="300" />
</p>
The param net is a coarse-to-fine model which uses a series of expansive layers to construct the output image from input image features along with the parameters. It consists of eight blocks where each block consists of a transposed convolutional layer for upsampling followed by three convolutional layers that acts as cross channel parametric pooling layers. The transposed convolutional layer has a filter size of 3 × 3 with a stride of 2 for upsampling. The size of the output of transposed convolutional filter of block i, 1 ≤ i ≤ 8 will match with the size of the output of 9 − i layer of the auto-encoder. We stacked the image features from that layer of the auto-encoder with the output of the third transposed convolutional layer. We passed this through a 3 × 3 convolutional layer with a stride of 1. We then stack the parameters with the output of this convolutional layer. This is then passed through two 3 × 3 convolutional layers with a stride of 1 which also acts as a cross channel parametric pooling layer. Each of these three convolutional layers is followed by instance normalization and Leaky ReLU activation function with the slope of 0.2. The last block is followed by two 3 × 3 convolutional layers with a stride of 1. This is followed by tanh activation function which produces the output image.
<p align='center'>
     <img src="network imgs/Param_Net.jpg" width="800" height="319" />
</p>


**You can download the trained weights from here -** <br />
[Weights for END(Autoencoder) model](https://drive.google.com/file/d/1vdS1uEPm_9HkCiL0jbchnj-h16RlWSxN/view?usp=sharing).<br />
[Weights for Default-to-Param model](https://drive.google.com/file/d/1NcmYU1XXc-FEofpzWRbVlk_Kyvc9e170/view?usp=sharing).<br />
[Weights for Param-to-Param model](https://drive.google.com/file/d/1C_5h0n2P89eHxtOILiNsNYxmK7Ibgpvw/view?usp=sharing).<br />

## MRiLab Results
### Default-to-Param Model
<p align='center'>
    <img src="Sample Results/Default-to-Param_MRiLab-Results.jpg" width="800" height="555" />
</p>

### Param-to-Param Model
<p align='center'>
    <img src="Sample Results/Param-to-Param_MRiLab-Results.jpg" width="800" height="543" />
</p>

## BrainWeb Results
### Default-to-Param Model
<p align='center'>
    <img src="Sample Results/Default-to-Param_BrainWeb-Results.jpg" width="800" height="294" />
</p>

### Param-to-Param Model
<p align='center'>
    <img src="Sample Results/Param-to-Param_BrainWeb-Results.jpg" width="800" height="287" />
</p>


