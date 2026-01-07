---
title: COMP3056 Computer Vision 1 -- Image Regions and Patches
tags: [COMP3056 Computer Vision]
categories: COMP3056 Computer Vision
cover: images/CV/Chapter1/cover.png
excerpt: An image is essentially a matrix of intensity values. To describe an image, we introduce the concept of segments, patches, features, etc,. To further extract the information of an image, we introduce the use of Histogram of Oriented Gradients (HoG). Note that this section focus more on traditional CV techniques.
date: 
---

## Table of Contents

- [Segments and Patches](#segments-and-patches)
  - [Usage of Segments and Patches](#usage-of-segments-and-patches)
- [Features and Feature Vector](#features-and-feature-vector)
  - [Classification of Features](#classification-of-features)
  - [Image Derivative (只需要理解sobel filter)](#image-derivative-%E5%8F%AA%E9%9C%80%E8%A6%81%E7%90%86%E8%A7%A3sobel-filter)
  - [<font color="#FF0000">\[IMPORTANT\]</font> Histogram of Oriented Gradients (HoG)](#font-colorff0000%5Cimportant%5Cfont-histogram-of-oriented-gradients-hog)
    - [The steps of HoG](#the-steps-of-hog)

# Segments and Patches
**Segments** is irregular, or rectangular <br />**Patches** always rectangular

## Usage of Segments and Patches
1. Track objects
2. use different views to reconstruct 3D
3. detect object classes and recognize specific objects using pre-stored model

# Features and Feature Vector
**Feature** is a piece of information about the content of an image<br />**Feature vector** refers to a vector that contains the concatenation of multiple features in an image.

## Classification of Features

1. Color Features
2. Texture Features
    1. Local Binary Patterns (LBP)
        1. For each center pixel $p_c$, create an 8-bit number $\{b_1, b_2, b_3, b_4,b_5, b_6, b_7, b_8\}$, for $i \in [1, 8]$, the local binary pattern will be: {% raw%}$$b_i = \left\{\begin{array}{rcl}0 &  & (p_i \leq p_c)\\1 &  & (p_i \gt p_c)\end{array}\right.$${% endraw %}![Local Binary Pattern](/images/CV/Chapter1/LBP.png)
 
    2. LBP Feature Vector
        1. Divide the patch into cells e.g. 16 x 16 pixels per cell
        2. Compute local path description number of each pixel
        3. Histogram these numbers over each cell
            1. 将上面的八位二进制转成十进制
            2. 所以ppt上说256-d dimension
        4. Normalize each histogram (optional)
        5. Concatenate histograms to make the feature vector![LBP feature vector](/images/CV/Chapter1/LBP_feature_vector.png)

3. Shape Features
   1. focus on image gradient measures

## Image Derivative (只需要理解sobel filter)
Definition of Sobel Filter:
![Sobel Filter](/images/CV/Chapter1/sobel_filter.png)
The calculation process for Sobel Filter:
![Sobel Filter Convolution on X axis](/images/CV/Chapter1/sobel_filter_calculation_x.png)
![Sobel Filter Convolution on Y axis](/images/CV/Chapter1/sobel_filter_calculation_y.png)

## <font color="#FF0000">\[IMPORTANT\]</font> Histogram of Oriented Gradients (HoG)
### The steps of HoG
1. Divide the patch into small cells![Divide](/images/CV/Chapter1/HOG_1.png)
2. Define slightly larger blocks, covering several cells![Find larger blocks](/images/CV/Chapter1/HOG_2.png)
3. Compute gradient magnitude and orientation at each pixel![Compute Gradient](/images/CV/Chapter1/HOG_3.png)
4. Compute a local **weighted** histogram of gradient orientations for each cell, weighting by some function of magnitude.
  1. The histogram is a vector of 9 bins corresponding to angles 0, 20, 40, 60 … 160
   2. A bin is selected based on the **direction(角度)**, and the vote (the value that goes into the bin) is selected based on the **magnitude**
      1. 先根据角度算每个格子的占比，下图中左红色圈代表角度为10度，介于0-20度之间，10 = 0 \* 0.5 + 20 \* 0.5，知道这个占比之后按照占比将右边圈代表梯度大小，则0 bin里是4 \* 0.5 = 2, 2 bin里是4 \* 0.5 = 2.![Create histogram of gradient](/images/CV/Chapter1/HOG_4.png)
      2. 遇到边界的（角度在160-180且需要分配的），第二个分配到0 bin里。下图左绿色圈代表角度为165度，165 = 160 * 0.75 + 180 * 0.25，则160 bin里是85 * 0.75 = 63.75, 0 bin（本来应该是180bin但没这个bin换成0bin）为85 * 0.25 = 21.25。![Special cases for 160-180 degrees](/images/CV/Chapter1/HOG_5.png)
5. Concatenate histogram entries to form a HoG vector for each block<br />P.S. 上图中一个block里有四个cell，一个cell生成9\*1的histogram如下图，那么四个cell的histogram直接横向拼接接为36\*1的element vector![Concatenate histogram to form a vector](/images/CV/Chapter1/HOG_6.png)
6. Normalize vector values by dividing by some function of vector length （单个元素除以全部元素平方和）



