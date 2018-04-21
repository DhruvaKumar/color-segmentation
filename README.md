# Object detection via color segmentation

This project was completed as a part of ESE650 Learning in Robotics, University of Pennsylvania in the spring of 2015. The goal of the project was to detect red barrels in an image.

Object detection of the red barrel is achieved using color segmentation. The pixels of training images containing red barrels are clustered into discrete color classes using a combination of an unsupervised learning scheme (k-means) and manual masking. Once these discrete color classes are obtained, Gaussian Mixture Models are used to model the probability distribution for each class, specifically the ones pertaining to the red barrel. For a new test image, the trained GMM model returns a mask for the red barrel pixels. Utilizing the prior shape of the barrel, shape heuristics were created to filter out false positives.

![alt text](./results/2.3.png)

---

### Description of files

`trainLabel.m`: Takes input images and red barrel masks, performs k-means and returns discrete clusters of 4 color classes

`trainModel.m`: Takes in discrete color classes, converts them to the `LAB` space and fits a GMM model to each class

`gmm.m`: GMM algorithm that takes in features for each class, number of centroids and returns the mean and covariance of each cluster

`myAlgorithm.m`: Takes in an image, GMM model and returns the bounding box, centroid and depth of the red barrel

`main.m`: main script to call `myAlgorithm` on a directory of images

---

### Usage

Run `main.m`

Specifiy the directory which contains the test images. (Replace ‘data/…’ with the directory name, say ‘test/…’)

---

### Color segmentation

#### Training: labeling color classes

Four color classes was chosen:

1. red (barrel)
2. grey (floor, ceiling, wall, everything else)
3. yellow (some walls, some weird structures)
4. other shades of red (everything else that looks like red apart from the barrel: jacket, seat, floor, brick red wall)

The red barrel pixel class was manually segmented out from the training images. The rest were segmented out using k-means on the `a` and `b` channels of the `LAB` color space. The code is in `trainLabel.m`


#### Training: GMM model 

Once the color classes were defined, a probability distribution for each color class was modeled using GMMs.

On observation of the data, the `a` and `b` channels of `LAB` and `Cb` and `Cr` channels of `YCbCr` turned out to be good candidates to cluster the data into different classes.
The following plots show the data points of each class plotted in these color spaces. The 3 cross hairs shown represent the centroid that the GMMs learned for each color class.

![alt text](./plots/lab_gmm_mean.png "lab")

![alt text](./plots/ycbcr_gmm_mean.png "ycbcr")


Since plotting all the data points was computationally cumbersome, 7000 random samples were plotted. On varying the samples, the data points for these classes stayed almost constant. Hence, using a small sampled set to represent the distribution turned out to be a good approximation.

The `a` and `b` channels of the `LAB` space was chosen to model the probability distribution of red barrel pixels since its data points were more exclusive and did a better job of filtering out false positives.

The code for training each color class with GMMs is in `trainModel.m` and `gmm.m`

#### Testing

Once the GMM is trained to model the probability distribution of the red barrel pixels, for a new test image, a mask is created for the red barrel pixels which are highly likely to occur.

Shape heuristics are then used to segment out the red barrel outputting the centroid, bounding box and depth.
Metrics used for shape detection - A weighted average of the following normalized parameters:
- Area
- Extent (number of red pixels in bounding box / total number of pixels)
- The norm of the difference of the ratio of minor axis length to major axis length to the ratio of the actual barrel dimensions. 

The code for detecting red barrels from a trained GMM model is in `main.m` and `myAlgorithm.m`

---

### Results

![alt text](./results/1.8.png)
![alt text](./results/1.9.png)
![alt text](./results/2.5.png)
![alt text](./results/3.6.png)
![alt text](./results/3.8.png)
![alt text](./results/4.5.png)
![alt text](./results/5.6.png)
![alt text](./results/5.9.png)
![alt text](./results/6.1.png)
![alt text](./results/6.5.png)
![alt text](./results/10.2.png)
![alt text](./results/10.7.png)
![alt text](./results/10.8.png)
![alt text](./results/10.9.png)

### References

- Dr. Dan Lee's lecture notes