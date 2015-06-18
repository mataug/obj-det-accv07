# Introduction #

This is source code for the ACCV 2007 paper, "Object Detection Combining Recognition and Segmentation"


# How to run the code #

## 1. Obtain the source code ##

You can obtain source code via subversion or directly go to Downloads page and download the zip file.

Let's denote the root folder of our source code as $SOURCE\_CODE$.


## 2. Download image data and pb edge detector ##
Our test images can be download from here: http://www.cis.upenn.edu/~jshi/ped_html/

One of our edge detection relies on pb edge detector. You can download it from our Downloads page [segbench.tar.gz](http://obj-det-accv07.googlecode.com/files/segbench.tar.gz) or from here: http://www.eecs.berkeley.edu/Research/Projects/CS/vision/grouping/segbench/

Unzip image files to $SOURCE\_CODE$/data/images/.

Unzip segbench.tar.gz to $SOURCE\_CODE$/.

## 3. Add project source folder to Matlab searching path list ##

The 'startup.m' script is meant to add whole source directories (including sub-directories) to Matlab path list.

On Linux, if you start Matlab after you change working directory to $SOURCE\_CODE$, the 'startup.m' script will be automatically invoked. Otherwise, run 'startup.m' when current directory points to $SOURCE\_CODE$ in Matlab.

On Windows, 'startup.m' needs to be manually invoked.

## 4. Build mex files ##

Go to $SOURCE\_CODE$/mex, and run the following script line to generate all native files.

```
>>mex_compile
```

## 4. Run demos ##

demo\_train.m

demo\_one\_image.m

demo\_multi\_image.m