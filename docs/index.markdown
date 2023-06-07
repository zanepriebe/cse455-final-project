---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: A Deep Dive Into Artistic Style Transfer Between Images
---
### *Emi Yoshikawa, Joanie Foley, and Zane Priebe*


- ### Colab Notebook: [Here](https://colab.research.google.com/drive/1LfMc6inwgxg4DnglQibusmg7rieuZhN3?usp=sharing)

- ### Video: [Here](https://drive.google.com/file/d/1DCSHgVNwJm0cZbMgcOIM4eQkr1tVPEvC/view?usp=sharing)


## Problem Description
Art has always been and always will be a core pillar within the human experience. As important art is, it can take years of practice and dedication to reach a desired outcome or vision. For example, imagine you want an oil-style painted family portrait. This would either take years to master oil and realistic figure painting or be a hefty divet out of the bank account. What if you could simply take a family photo, choose the desired style of painting, and then transform that image into one that looks like a painted family portrait? This problem, along with many others like accessibility to graphic design are some of the problems or scenarios we are looking to solve with our project: a deep dive into artistic style transfer between images.

## Previous work
To actually execute the transformation and produce the output image, we will be following an approach based off of the Neural-Style algorithm developed by Leon A. Garys, Alexander S. Ecker, and Matthias Bethade from Cornell University. Read more on that [here](https://arxiv.org/abs/1508.06576). Their technique utilizes neural representations to break apart and then recombine style and content of arbitrary images, thus creating a neural algorithm. Specifically, we use a pre-trained neural network –  a 19 layer VGG network (VGG19). We only need to use the convolution neural network component from the vgg19 model, so we only import vgg19.features. We chose this model because VGG19 is commonly used for style transfer due to its deep architecture which can capture both low-level and high-level features. It is widely known to effectively preserve content while transferring style so we thought it was the obvious choice. Also, its layer responses provide valuable information for calculating Gram matrices, which we use to calculate style loss so it made a lot of sense for our approach. In addition, we utilize the Adam optimizer. We chose the Adam optimizer as it has an adaptive learning rate (adapts the learning rate for each parameter) and is very attuned to noise and sparse gradients. We were deciding between Adam and L-BFGS but went with Adam as it converges faster and was more efficient.

## Our Approach
The implementation utilizes the VGG19 model for neural style transfer. The original and style images are loaded, and a generated image is created. Content and style losses are calculated based on feature differences between the generated image, original image, and style image. The Adam optimizer is used to update the generated image's pixel values through a specified number of epochs. The learning rate, number of epochs, and loss weights can be adjusted to achieve the desired stylization effect… We have chosen the following parameters as they produced us with the best balance of clarity of images, detail, and efficiency:  The progress is monitored by periodically displaying the total loss, and then finally displaying the final output image.

## Datasets
It’s important to note that one thing different about our project than existing solutions or past homeworks we have done in class is that we won’t be training a network on a dataset. Instead, we want to train the input image in order to minimize the content and style losses. Therefore, we don’t actually have any datasets to reference – we are referencing the content and style data from the images instead.

## Results
First, we selected this image as our *content image*, or image that we want to stylize:

![Starry Night](assets/image2.jpg "Content Image"){:width="400px"}

Next, we selected this image as our *style image*, or image that we want to base the content off of:

![Dog](assets/image8.jpg "Style Image"){:width="400px"}

Combinging these images, we got:

![Starry Night Dog](assets/image5.png "Combined Image"){:width="400px"}


Thus, we successfully created an image of a dog (specifically Emi’s dog Murphy :D) in the style of Van Gogh, as desired.

This is just a single example of our results. You could use any *style image* and *content image* to create a new image reproduced in a new artistic style.

## More Examples

### Emi in Picasso Style

Style image:

![Emi](assets/image3.jpg "Content Image"){:width="300px"}

Content image:

![Picasso](assets/image6.jpg "Style Image"){:width="300px"}

Output image:

![Emi Picasso](assets/image9.png "Combined Image"){:width="300px"}


### Baby Yoda in Edvard Munch (artist of The Scream) style

Style Image:

![Baby Yoda](assets/image7.jpg "Content Image"){:width="300px"}

Content Image:

![Edvard Munch](assets/image4.jpg "Style Image"){:width="300px"}

Output Image:

![Baby Yoda Edvard](assets/image1.png "Combined Image"){:width="300px"}


## Discussion

### Problems Encountered
- Setting up Pytorch was difficult at first, as it was an interface none of us had used before and we were trying to use it through Github / VScode… This was too complicated for us to get set-up so we just switched to using a CoLab notebook.
- We sometimes got red splotches in some locations on the image. This took awhile to troubleshoot as the rest of the image looked really good there were just a few red splotches on the output image. Eventually we realized that the network was maybe optimizing the output image with values that exceed the 0 to 1 tensor range. Thus we added in a clamp to check the input image after every round of optimization.
- Our initial attempts were quite slow when processing the images, taking upwards of 30 minutes.  To solve this, we switched to using the GPU rather than the CPU so that processing could be done in parallel.  Our solution now runs in approximately 3-4 minutes, which is still a while but a large improvement
It was quite difficult to find the right values for the learning rate, loss weights, ratio between weights, and number of epochs. Much experimentation and tweaking the values and comparing outputs and runtime had to be done to finally come to the values we settled on.

### Additional Steps
- The most obvious thing we would like to do to extend working on this project would be to apply it to more images and see how different artistic styles can be applied to different images.
- It would also be interesting to see what would happen if one were to switch the roles of the images we used while working on this project. For example, treat the stylized image as the content image and vice versa to see the resulting images.
- Although our program runs at an alright time, we would like it to run even faster. For example, considering that there are AI sites that can produce images in under a minute, that would be the end goal.
- It would be interesting to evolve this into not having to submit images but rather just being able to have the user input something like “a dog in van gogh style”.

### How our approach differs from others
- Like we stated above, one way our approach is different in that it does not train on any data sets, and rather uses two specific images as the “content” and “style”.  This simplified the approach that we needed to take as there is inherently less data to process. We also did not have to build or choose a specific data set which removed a layer of complexity and allowed us to focus on really developing our network, loss functions, and efficiency. However, it is important to note there is a tradeoff here, meaning that we only have one specific image to base the style off of, rather than a whole set.  Perhaps if we were to train on a set of images for the style, the resulting image could be more thoroughly resemble the desired style.
- Another way our approach differs from others is that we use the Adam optimizer over the L-BFGS optimizer. We were actually originally using the L-BFGS optimizer as this is a common optimizer used in neural style transfer and has really good fine-tuning properties. However, we transitioned and eventually settled on using the Adam optimizer as we found it more efficient, easier to implement/understand, easier to find a learning rate for, and no memory usage problems.
- In addition, we played around for awhile with our parameters (alpha, learning rate, etc.) to find the perfect parameters that gave us quality outputs but didn’t take ages. For example, we originally had epochs set to 7000 which was just way too many and took super long. We eventually settled on 1000 as this was enough epochs to get good results but didn’t take too long. We also settled on an alpha:beta ratio of 1:100 as we want to priortize conserving the content and just infusing it with the style. Ratios like 1:10 made the image too abstract.
- Last, we tweaked our implementation to prioritize GPU but use CPU if needed. Unlike other approaches, if we have to use CPU we make the image size smaller so there is less processing that has to happen and we can still acheieve the same efficiency.
