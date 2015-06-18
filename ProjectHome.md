# Videos #

Two useful videos can be found at:

  * [ACCV2007 Slides](http://v.youku.com/v_show/id_XNTE0ODg5MDI4.html)

  * [Voting Process](http://v.youku.com/v_show/id_XNTE0ODU1MDg4.html)

# About This Project #

This project is targeting for the source code for the ACCV 2007 paper "Object Detection Combining Recognition and Segmentation".

You can get the source code using a subversion client or just download a zip file. Go to "[Downloads](http://code.google.com/p/obj-det-accv07/downloads/list)" or "[Source](http://code.google.com/p/obj-det-accv07/source/checkout)" tabs for the links.

A good slides can be viewed from here: [ACCV07Slides](https://www.dropbox.com/s/uyr69br8ehp0lwt/obj_det_accv2007_slides.pdf).

# How to Use The Code #

Please read wiki:[HowToRun](HowToRun.md) in order to set up and run the source code. Those who want to contribute or know more about this source code, please read wiki:[BehindTheSourceCode](BehindTheSourceCode.md).

## 1. Training ##

<font color='green'>%load default parameters</font>
```
para_sc = set_parameter;
```

<font color='green'>% override default model height, other parameters can be overridden as well</font>

```
para_sc.model_height    = 150;
```

<font color='green'>% load training images and annotation masks from files</font>

```
anno_data   = load_anno_data(img_dir,mask_dir);
```

<font color='green'> % generate codebook and save to a file</font>

```
codebook    = load_codebook(anno_data,para_sc);
save(codebook_file,'codebook');
```


## 2. Detection ##

<font color='green'> % load codebook from a file</font>

```
load('data/codebook/cb_test_new.mat');
```

<font color='green'> % read an image from a file</font>

```
img         = imread(img_file);
```

<font color='green'> % override parameters</font>

```
ratio       = 1/1.2;
para        = set_parameter(codebook,ratio);
```

<font color='green'> % compute edge map</font>

```
I_edge  = compute_edge_pyramid(img, para{1}.detector,...
        para{3}.min_height, para{2}.ratio);
```

<font color='green'> % detection function</font>

```
[hypo_list,score_list, bbox_list] = sc_detector(img,codebook, I_edge , para, verbose);
```

<font color='green'> % display detection result</font>

```
display_hypo_rect(img, [], hypo_list, score_list, bbox_list);
```