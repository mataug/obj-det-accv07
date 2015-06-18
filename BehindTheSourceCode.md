# Object Definition #

How an object is visually defined leads to how it is percepted. If objects were defined by colors, then simple color matching will suffice. Unfortunately, in real world, object recognition is much more complicated.

In this work, an object is defined as a set of visual words which have loose mutual geometrical relationship. This definition is a simplified version of Pictorial Structure (see [Ref 1](#1.md)), in a sense that we use a tree structure of depth 1.

We will go through an apple detection example and explain the intuition on the go.

# Object Model #

First of all, an apple is viewed as several patches loosely stitched together. As in Figure 1:

![http://farm9.staticflickr.com/8367/8452725237_c11293eff1_z.jpg](http://farm9.staticflickr.com/8367/8452725237_c11293eff1_z.jpg)

Figure 1. An apple is a composition of multiple parts that agree on the same body center.

In practice, it is difficult to cut an object model into appropriate pieces, so we use point features as an indirect representation of object parts. Feature points are sampled in a grid style. Figure 2 shows we sample 9 points over an apple model image.

![http://farm9.staticflickr.com/8229/8452725225_23e017bc72_z.jpg](http://farm9.staticflickr.com/8229/8452725225_23e017bc72_z.jpg)

Figure 2. Sample grid points for Shape Context (See [Ref 2](#2.md)) feature extraction (only shown for points 1,6,7). We also extract a local mask for each point, as shown in the right figure.

**In our accv2007 paper, we used shape context as point feature. Actually, in this voting framework, any robust feature can be used.**

2 additional bullets I want to address here.

  * We make use of object model mask to help alleviating feature distortion from background clutter.
  * We use radial/angular blur to tolerate local deformation.

![http://farm9.staticflickr.com/8366/8453817270_718c65c5a2_b.jpg](http://farm9.staticflickr.com/8366/8453817270_718c65c5a2_b.jpg)

Figure 3. Angular blur. Due to hard-decision binning method during shape context feature extraction, similar shapes like (a) and (b) might produce totally different features. Their features are illustrated as top-left and bottom-left in (d). We say they are different because they will have a large distance in feature space if we adopt distance function such as Chi-Square . Using a blurring boundaries for binning method like in (c), these two feature will become much closer in feature space ( see top-right, bottom-right in (d)).

We call the feature set extracted from model images a **codebook**. A codebook entry consists of three elements: 1) A feature vector (Shape Context); 2) A local model mask; 3) A voting vector from feature point to body center. We visualize them in Figure 4, taking M<sub>7</sub> as an example.

![http://farm9.staticflickr.com/8374/8452725133_17ebc7965c_z.jpg](http://farm9.staticflickr.com/8374/8452725133_17ebc7965c_z.jpg)

Figure 4. A codebook entry.

# Detection Process #

Given a test image, we computer shape context on sampled grid points as well.

![http://farm9.staticflickr.com/8516/8452725099_cd0e719709_z.jpg](http://farm9.staticflickr.com/8516/8452725099_cd0e719709_z.jpg)

Figure 5. Sample features on a test image. Only those of {T<sub>2</sub>,T<sub>11</sub>,T<sub>18</sub>,T<sub>31</sub>,T<sub>40</sub>,T<sub>79</sub>,T<sub>88</sub>} are shown.


Then each feature points on test image matches against codebook to find its best match. Notice the corresponding mask function is applied before matching to a codebook entry. Suppose matching scores are normalized to [0,1]. Now we have a matching table in ideal case might look like:

| **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | |**T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | | **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | | **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | | **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | | **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** | | **T<sub>i</sub>**|	**M<sub>j</sub>**| **S<sub>ij</sub>** |
|:-----------------|:-----------------|:-------------------|:|:----------------|:-----------------|:-------------------|:|:-----------------|:-----------------|:-------------------|:|:-----------------|:-----------------|:-------------------|:|:-----------------|:-----------------|:-------------------|:|:-----------------|:-----------------|:-------------------|:|:-----------------|:-----------------|:-------------------|
|T<sub>1</sub>     |M<sub>1</sub>     |0.1                 | |T<sub>10</sub>   |M<sub>5</sub>     |0.3                 | |T<sub>18</sub>    |M<sub>1</sub>     |0.7                 |  |T<sub>29</sub>    |M<sub>4</sub>     |0.9                 | |T<sub>40</sub>    |M<sub>7</sub>     |0.8                 | |T<sub>78</sub>    |M<sub>2</sub>     |0.0                 | |...               |...               |...                 |
|T<sub>2</sub>     |M<sub>3</sub>     |0.2                 | |T<sub>11</sub>   |M<sub>3</sub>     |0.1                 | |T<sub>19</sub>    |M<sub>2</sub>     |0.8                 | |T<sub>30</sub>    |M<sub>5</sub>     |0.8                 | |T<sub>41</sub>    |M<sub>8</sub>     |0.7                 | |T<sub>79</sub>    |M<sub>6</sub>     |0.0                 | |...               |...               |...                 |
|...               |...               |...                 | |...              |...               |...                 | |T<sub>20</sub>    |M<sub>3</sub>     |0.8                 | |T<sub>31</sub>    |	M<sub>6</sub>    |0.9                 | |T<sub>42</sub>    |M<sub>9</sub>     |0.8                 | |...               |...               |...                 | |T<sub>87</sub>    |M<sub>4</sub>     |0.0                 |
|...               |...               |...                 | |...              |...               |...                 | |...               |...               |...                 | |...               |...               |...                 | |...               |...               |...                 | |...               |...               |...                 | |T<sub>88</sub>    |M<sub>2</sub>0.0  |

where S<sub>ij</sub> is the score of T<sub>i</sub> matching to M<sub>j</sub>.

Clearly, we can prune those matches with score below some threshold, yielding a much smaller matching table. More, each matching pair delivers additional information about where a possible object center could be at, in other words, each matching pair would vote for an object center with a score S<sub>ij</sub>, as illustrated in Figure 6.

![http://farm9.staticflickr.com/8089/8453817068_d5aae33dde_z.jpg](http://farm9.staticflickr.com/8089/8453817068_d5aae33dde_z.jpg)

Figure 6. A matching pair predicts(votes) for a possible object center in test image.

So, by accumulating all the votes from all those matching pairs, we might get a voting map in ideal case like this:

![http://farm9.staticflickr.com/8525/8452724977_6ae21da82e.jpg](http://farm9.staticflickr.com/8525/8452724977_6ae21da82e.jpg)

Figure 7. An ideal voting map. The bigger radius a yellow disc has, the more likely it could be an object center.

Those positions that have score above some threshold are selected as winners (hypotheses). After tracing back the voting procedure, who votes whom are revealed, and an object's mask is predicted by stitching together matched codebook entries' masks. See Figure 8.

![http://farm9.staticflickr.com/8387/8452724925_0619b9c219_b.jpg](http://farm9.staticflickr.com/8387/8452724925_0619b9c219_b.jpg)

Figure 8. Voters helps to predict a rough object mask around a hypotheses center.

# Multi-scale detection #

We search hypotheses and do maximum suppression across scales.

![http://farm9.staticflickr.com/8090/8452724821_2ed2f76e14_b.jpg](http://farm9.staticflickr.com/8090/8452724821_2ed2f76e14_b.jpg)

Please check a fullsize image at: [fullsize image](http://farm9.staticflickr.com/8369/8452724743_d2305fcf90_k.jpg)

# References #

## 1 ##
Pedro F. Felzenszwalb and Daniel P. Huttenlocher, "Pictorial Structures for Object Recognition," International Journal of Computer Vision[J](J.md), vol. 61, pp. 55-79, 2005.

## 2 ##
http://www.eecs.berkeley.edu/Research/Projects/CS/vision/shape/sc_digits.html]