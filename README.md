<h1>Applying techniques to visualize Deep Neural Networks</h1>

In this report, we describe 3 approaches that can be used to visualize
  how deep neural networks classify images and what area of an image is
  of most interest to them. The approaches employed here are - Saliency
  Maps, Occlusion Sensitivity and Gradient Weighted Class Activation
  Maps(Grad-CAM). The methods here achieve great accuracy, particularly
  Grad-CAM and open up a lot of interesting applications into this
  particular field.

# Introduction

In recent times, deep learning has taken over almost every industry. We
need to understand the model's decision-making process. Real world
Neural Network models have millions of parameters and extreme internal
complexity, as they use many non-linear transformations during training.
Deep neural models based on Convolutional Neural Networks (CNNs) have
enabled unprecedented breakthroughs in a variety of computer vision
tasks, from image classification, object detection, semantic
segmentation to image captioning, visual question answering and more
recently, visual dialog and embodied question answering. While these
models enable superior performance, their lack of decomposability into
individually intuitive components makes them hard to interpret.
Consequently, when today's intelligent systems fail, they often fail
spectacularly disgracefully without warning or explanation, leaving a
user staring at an incoherent output, wondering why the system did what
it did. Interpretability matters. In order to build trust in intelligent
systems and move towards their meaningful integration into our everyday
lives, it is clear that we must build 'transparent' models that have the
ability to explain why they predict what they predict.\
Knowing that deep neural networks work is not enough for scientists in
today's world. We must understand how and why exceedingly complex models
work to gain valuable insights into improving these models and surpass
human intelligence. With the deep Convolutional Networks (ConvNets) now
being the architecture of choice for large-scale image recognition, the
problem of understanding the aspects of visual appearance, captured
inside a deep model, has become particularly relevant and is the subject
of this paper. Recent work has shown that the convolutional units of
various layers of convolutional neural networks (CNNs) actually behave
as object detectors. Despite having this remarkable ability to localize
objects in the convolutional layers, this ability is lost when
fully-connected layers are used for classification. To prevent this, we
use a global average pooling layer so that the network can retain its
remarkable localization ability until the final layer.

# Literature Review

## Saliency Maps

Saliency Maps are one of the most basic techniques used to visualize
CNNs. They are a visualization technique to gain better insights into
the decision-making of a neural network. They also help in knowing what
each layer of a convolutional layer focuses on. This helps us understand
the decision making process a bit more clearly.\
Given an image $I_{0}$, a class c, and a classification ConvNet with the
class score function $S_c(I)$, we would like to rank the pixels of I0
based on their influence on the score $S_c(I_0)$. This is achieved by
computing the derivative
$$w=\left.\frac{\partial S_{c}}{\partial I}\right|_{I_{0}}$$

The magnitude of the derivative signifies which pixels have a
significant effect on the class score $S_c(I_0)$. After having computed
this derivative by back-propagating on the input image, the saliency map
is generated by rearranging the elements of the vector $w$. For multi
channel images the saliency maps are generated as
$M_{ij} = max_c|w_{h(i,j,c)}|$.

## Occlusion Sensitivity

Occlusion sensitivity is a simple technique for understanding which
parts of an image are most important for a deep network's
classification. The basic concept behind occlusion sensitivity is
simple - it calculates the importance of every patch by observing the
change of model output y when the patch is removed. The individual
results can be assembled to an attribution map. When the occluder covers
the image region that appears in the visualization, we see a strong drop
in activity in the feature map. This shows that the visualization may or
may not correspond to the image structure that stimulates that feature
map greatly.

## Grad-CAM

Gradient-weighted Class Activation Maps(Grad-CAM) is a novel approach
that uses the gradients of a particular class and produces a
localisation map highlighting the important parts of an image that were
used to make the prediction in the first place. It is usually the case
that convolutional layers hold spatial knowledge which is lost in the
fully connected layers. So we can expect the last convolutional layers
to have the best compromise between high-level semantics and detailed
spatial information. Grad-CAM uses the gradient information flowing into
the last convolutional layer of the CNN to assign importance values to
each neuron for a particular decision of interest.\
To produce the localization map
$L_{\text {Grad-CAM }}^{c} \in \mathbb{R}^{u \times v}$ for Grad-CAM we
first compute the gradient of the score $y_c$ with respect to the
feature map activations $A^{k}$ of a convolutional layer. These
gradients are global-average-pooled backward to find out the neuron
importance weights $\alpha^{c}_k$.

$$\alpha_{k}^{c}=\overbrace{\frac{1}{Z} \sum_{i} \sum_{j}}^{\text {global average pooling }} {\frac{\partial y^{c}}{\partial A_{i j}^{k}}}$$

Finally we perform a weighted combination of the activation maps and
perform a ReLU to obtain the localization maps.

$$L_{\text {Grad-CAM }}^{c}=ReLU\underbrace{\left(\sum_{k} \alpha_{k}^{c} A^{k}\right)}_{\text {linear combination }}$$

# Experiments and Results

## Saliency Maps

The dataset we use is Plant Pathology 2020 which has been downloaded
from Kaggle. The model has been used to identify the category of foliar
diseases in apple trees. This dataset contains manually captured 3651
high-quality, real-life symptom images of multiple apple foliar
diseases, with variable illumination, angles, surfaces, and noise.\
To generate the saliency maps, we use the pre-trained ResNet 18 from
PyTorch. The images are preprocessed by resizing the image and
normalizing the dataset in a way such that the mean and standard
deviation of the image is 0 and 1 respectively. We now backpropogate on
the prediction i.e. the score $y_c$ and then use the gradients to
generate the saliency map by taking the maximum absolute value across
the three color channels.

<img src = "https://github.com/mdoshi2612/WiDS-Project/blob/main/Images/Saliency%20Map.png">

We can see that the abnormal areas of the leaf are much more prominent
in the heatmap than regular parts of the leaf. This tells us that our
model is more affected by changes in the former area in making it's
decision.

## Occlusion Sensitivity

We use the ImageNet dataset which is an image dataset organized
according to the WordNet hierarchy. Each meaningful concept in WordNet,
possibly described by multiple words or word phrases, is called a
\"synonym set\" or \"synset\". There are more than 100,000 synsets in
WordNet; the majority of them are nouns (80,000+).\
We use the pre-trained VGG16 model from PyTorch to generate the
attribution map. We preprocess the images and normalize them according
to the ImageNet mean and standard deviation. We now occlude part of
images with occlusion size = 32px and occlusion stride = 14px. The model
is run on every occluded image and the heatmap updated with the value of
the score at that particular pixel. Finally we upsample the heatmap and
plot it on top of our input image to generate the attribution map.

<img src = "https://github.com/mdoshi2612/WiDS-Project/blob/main/Images/occlusion_dataset.png">

<img src = "https://github.com/mdoshi2612/WiDS-Project/blob/main/Images/Occlusion%20map.png">

We can see that the model gives priority to the area where the
instrument is present to make it's decision.

## Grad-CAM

The dataset used here is the ImageNet dataset which is the same dataset
used for the Occlusion Sensitivity approach. We then generate the
localization maps and discard the negative values to generate the
heatmap.

To perform Grad-CAM, we first preprocess the image using the inbuilt
function from Keras library. The model we use here is VGG16 from
Tensorflow. The prediction here is bullmastiff(a breed of dog) and we
use that class to apply Grad-CAM.

<img src="https://github.com/mdoshi2612/WiDS-Project/blob/main/Images/original.png">


<img src="https://github.com/mdoshi2612/WiDS-Project/blob/main/Images/Grad-CAM.png">

# Conclusion

In this report, we presented 3 techniques to understand and visualize
what part of an image is given priority by a Convolutional Neural
Network during classification. The first computes an image-specific
class saliency map, highlighting the areas of the given image,
discriminative with respect to the given class.The second approach
generated an heatmap by occluding various parts of an image and
computing the score on the resulting image. Further, we generated
Grad-CAM localizations with existing high resolution visualization
techniques.The results obtained were more than satisfactory as each
approach correctly managed to localise the most important part of an
image.
