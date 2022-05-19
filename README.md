# Deep-Learning-Based-MR-Image-Re-parameterization

[Abhijeet Narang](https://www.linkedin.com/in/abhijeet-narang-63b419175/) &nbsp;&nbsp; [Abhigyan Raj](https://www.linkedin.com/in/abhigyan-raj-023537145/) &nbsp;&nbsp; [Mihaela Pop](http://individual.utoronto.ca/mihaelapop/index.html) &nbsp;&nbsp; [Mehran Ebrahimi](https://www.linkedin.com/in/mehran-ebrahimi-a6a92711/)

This is the official implemetation of our BIOENG'22 paper **Deep Learning-Based MR Image Re-parameterization**.

Magnetic resonance (MR) image reparameterization refers to the process of generating via simulations of an MR image with a new set of MRI scanning parameters. Different parameter values generate distinct contrast between different tissues, helping identify pathologic tissue. Typically, more than one scan is required for diagnosis; however, acquiring repeated scans can be costly, time-consuming, and difficult in patients. Thus, using MR image re-parameterization to predict and estimate the contrast in these imaging scans can be an effective alternative. In this work, we propose a novel deep learning (DL) based convolutional model for MRI re-parameterization. Based on our preliminary results, DL-based techniques hold the potential to learn the non-linearities that govern the re-parameterization.

### Dataset
- We used MRiLab which is an MRI Simulator to generate synthetic brain scans. We use these for training and testing.
- We also use Brainweb which is a simulated brain database that contains a set of realistic MRI data volumes produced by an MRI Simulator. We use these only for testing.

## Network Architecure
Our method consists of two technical modules: 1) an image reconstruction [auto-encoder](https://ieeexplore.ieee.org/document/6302929)(for feature extraction) to extract image features of the input image; and 2) a coarse-to-fine fully convolutional network which utilizes the image features from the auto-encoder and enforces the construction of output image with desired parameters.
The autoencoder comprises of two parts – an encoder that progressively downsamples the input using a series of contractive encoding layers and a decoder that reconstructs the input using a series of expansive decoding layers. Our encoder consists of 8, 3×3 convolutional layers with stride of 2 for downsampling, each of which is followed by batch normalization layer and Leaky-ReLU activation function with a slope of 0.2. Our decoder consists of 7, 3×3 transposed convolutional layers with a stride of 2 for upsampling, each followed by batch normalization and ReLU activation function. This is followed by a 3 × 3 convolutional layer with a stride of 1 and a 1 × 1 convolutional layer with a stride of 1 which acts as a pooling layer across the channels. We use tanh activation function for the last layer. After training the autoencoder, the outputs of 8 encoding layers are used as input image features for later part of our network.
<p align='center'>
     <img src="network imgs/Autoencoder.jpg" width="871" height="327" />
</p>
The param net is a coarse-to-fine model which uses a series of expansive layers to construct the output image from input image features along with the parameters. It consists of eight blocks where each block consists of a transposed convolutional layer for upsampling followed by three convolutional layers that acts as cross channel parametric pooling layers. The transposed convolutional layer has a filter size of 3 × 3 with a stride of 2 for upsampling. The size of the output of transposed convolutional filter of block i, 1 ≤ i ≤ 8 will match with the size of the output of 9 − i layer of the auto-encoder. We stacked the image features from that layer of the auto-encoder with the output of the third transposed convolutional layer. We passed this through a 3 × 3 convolutional layer with a stride of 1. We then stack the parameters with the output of this convolutional layer. This is then passed through two 3 × 3 convolutional layers with a stride of 1 which also acts as a cross channel parametric pooling layer. Each of these three convolutional layers is followed by instance normalization and Leaky ReLU activation function with the slope of 0.2. The last block is followed by two 3 × 3 convolutional layers with a stride of 1. This is followed by tanh activation function which produces the output image.
<p align='center'>
     <img src="network imgs/Param_Net.jpg" width="1061" height="423" />
</p>


You can download the trained weights from here - <br />
[Weights for END(Autoencoder) model](https://drive.google.com/file/d/1vdS1uEPm_9HkCiL0jbchnj-h16RlWSxN/view?usp=sharing).<br />
[Weights for Default-to-Param model](https://drive.google.com/file/d/1NcmYU1XXc-FEofpzWRbVlk_Kyvc9e170/view?usp=sharing).<br />
[Weights for Param-to-Param model](https://drive.google.com/file/d/1C_5h0n2P89eHxtOILiNsNYxmK7Ibgpvw/view?usp=sharing).<br />

## MRiLab Results
### Default-to-Param Model
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;**Input** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; **Required** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; **Generated**&emsp;&emsp; &emsp;&emsp;&emsp;&emsp;&emsp; **Absolute Difference**
<p align='center'>
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=1.2, TE=0.038/Input, TR=4.5, TE=0.05, Slice =14.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=1.2, TE=0.038/Required, TR=1.2, TE=0.038, Slice=14.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=1.2, TE=0.038/Generated, TR=1.2, TE=0.038, Slice=14.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=1.2, TE=0.038/Difference, min=0.00015258789, max=91.92021, avg=8.629864.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**TE=50ms, TR=4.5s** &emsp;&emsp;&emsp;&emsp;&ensp; **TE=38ms, TR=1.2s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=38ms, TR=1.2s** &emsp;&emsp;&emsp;&emsp;&emsp; **Avg. Difference=8.63**

<p align='center'>
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=5.2, TE=0.398/Input, TR=4.5, TE=0.05, Slice =7.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=5.2, TE=0.398/Required, TR=5.2, TE=0.398, Slice=7.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=5.2, TE=0.398/Generated, TR=5.2, TE=0.398, Slice=7.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/MriLab Test set/TR=4.5, TE=0.05 to TR=5.2, TE=0.398/Difference, min=7.56979e-06, max=85.252975, avg=4.819974.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**TE=50ms, TR=4.5s** &emsp;&emsp;&emsp;&emsp; **TE=398ms, TR=5.2s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=398ms, TR=5.2s** &emsp;&emsp;&emsp;&emsp;&ensp; **Avg. Difference=4.82**

### Param-to-Param Model
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;**Input** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; **Required** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; **Generated**&emsp;&emsp; &emsp;&emsp;&emsp;&emsp;&emsp; **Absolute Difference**
<p align='center'>
    <img src="Sample Results/Param to Param/MriLab Test set/TR=2.2, TE=0.096 to TR=2.0, TE=0.949/Input, TR=2.2, TE=0.096, Slice=4.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=2.2, TE=0.096 to TR=2.0, TE=0.949/Required, TR=2.0, TE=0.949, Slice=4.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=2.2, TE=0.096 to TR=2.0, TE=0.949/Generated, TR=2.0, TE=0.949, Slice=4.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=2.2, TE=0.096 to TR=2.0, TE=0.949/Difference, min=0.0, max=124.75277, avg=7.9379506.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**TE=96ms, TR=2.2s** &emsp;&emsp;&emsp;&emsp; **TE=949ms, TR=2.0s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=949ms, TR=2.0s** &emsp;&emsp;&emsp;&emsp;&ensp; **Avg. Difference=7.94**

<p align='center'>
    <img src="Sample Results/Param to Param/MriLab Test set/TR=7.2, TE=0.163 to TR=5.3, TE=0.094/Input, TR=7.2, TE=0.163, Slice=17.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=7.2, TE=0.163 to TR=5.3, TE=0.094/Required, TR=5.3, TE=0.094, Slice=17.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=7.2, TE=0.163 to TR=5.3, TE=0.094/Generated, TR=5.3, TE=0.094, Slice=17.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/MriLab Test set/TR=7.2, TE=0.163 to TR=5.3, TE=0.094/Difference, min=0.00035715103, max=80.61673, avg=7.3353896.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;**TE=163ms, TR=7.2s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=94ms, TR=5.3s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=94ms, TR=5.3s** &emsp;&emsp;&emsp;&emsp;&emsp; **Avg. Difference=7.34**

## BrainWeb Results
### Default-to-Param Model
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;**Input** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; **Required** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; **Generated**&emsp;&emsp; &emsp;&emsp;&emsp;&emsp;&emsp; **Absolute Difference**
<p align='center'>
    <img src="Sample Results/Default to Param/Brainweb Test set/TR=4.5, TE=0.05 to TR=6.0, TE=0.075/Input, TR=4.5, TE=0.05.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/Brainweb Test set/TR=4.5, TE=0.05 to TR=6.0, TE=0.075/Required, TR=6.0, TE=0.075.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/Brainweb Test set/TR=4.5, TE=0.05 to TR=6.0, TE=0.075/Generated, TR=6.0, TE=0.075.png" width="220" height="240" />
    <img src="Sample Results/Default to Param/Brainweb Test set/TR=4.5, TE=0.05 to TR=6.0, TE=0.075/Difference, min=0.0012011528, max=146.38287, avg=23.265463.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**TE=50ms, TR=4.5s** &emsp;&emsp;&emsp;&emsp;&ensp; **TE=75ms, TR=6.0s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=75ms, TR=6.0s** &emsp;&emsp;&emsp;&emsp;&emsp; **Avg. Difference=23.26**

### Param-to-Param Model
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;**Input** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; **Required** &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; **Generated**&emsp;&emsp; &emsp;&emsp;&emsp;&emsp;&emsp; **Absolute Difference**
<p align='center'>
    <img src="Sample Results/Param to Param/Brainweb Test set/TR=6.0, TE=0.075 to TR=3.4, TE=0.04/Input, TR=6.0, TE=0.075.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/Brainweb Test set/TR=6.0, TE=0.075 to TR=3.4, TE=0.04/Required, TR=3.4, TE=0.04.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/Brainweb Test set/TR=6.0, TE=0.075 to TR=3.4, TE=0.04/Generated, TR=3.4, T=0.04.png" width="220" height="240" />
    <img src="Sample Results/Param to Param/Brainweb Test set/TR=6.0, TE=0.075 to TR=3.4, TE=0.04/Difference, min=0.00045681, max=136.90112, avg=20.537035.png" width="220" height="240" />
</p>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**TE=75ms, TR=6.0s** &emsp;&emsp;&emsp;&emsp;&ensp; **TE=40ms, TR=3.4s** &emsp;&emsp;&emsp;&emsp;&emsp; **TE=40ms, TR=3.4s** &emsp;&emsp;&emsp;&emsp;&emsp; **Avg. Difference=20.54**






