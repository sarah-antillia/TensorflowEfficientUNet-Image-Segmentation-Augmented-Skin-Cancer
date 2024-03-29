<h2>TensorflowEfficientUNet-Image-Segmentation-Augmented-Skin-Cancer (2024/03/20)</h2>

This is the fourth experimental Image Segmentation project for Skin-Cancer  based on
the <a href="https://github.com/sarah-antillia/Tensorflow-Image-Segmentation-API">Tensorflow-Image-Segmentation-API</a>, and
<a href="https://drive.google.com/file/d/1kb7Tc9OSDQ28h83QKstXrhxMuKgWQb89/view?usp=sharing">Skin-Cancer-ImageMask-Dataset.zip</a>

<br>
Segmentation samples.<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/segmentation_samples.png" width="720" height="auto">
<br>

We will use an online dataset augmentation strategy based on Python script <a href="./src/ImageMaskAugmentor.py">
ImageMaskAugmentor.py</a> to train aSkin-Cancer Segmentation Model.<br><br>

Please see also our first experiment <a href="https://github.com/sarah-antillia/Image-Segmentation-Skin-Lesion">Image-Segmentation-Skin-Lesion</a>
 based on a Pre-Augmented-ImageMask-Dataset of 256x256 pixel size.
<br>
The second experiment 
<a href="https://github.com/sarah-antillia/Tensorflow-Image-Segmentation-Augmented-Skin-Cancer">
Tensorflow-Image-Segmentation-Augmented-Skin-Cancer </a>
<br>
The third experiment 
<a href="https://github.com/sarah-antillia/TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer">
TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer </a>
<br>


<br>
We use the EfficientNet-B0-Based-UNet Model
<a href="./src/TensorflowEfficientUNet.py">TensorflowEfficientUNet.py</a> for this Skin-Cancer Segmentation.<br>

Our TensorflowEfficientUNet class is based on the following implementation.<br>
<a href="https://github.com/he44/EfficientNet-UNet/blob/master/efficientnet_unet/build_eunet.py">
he44:EfficientNet-UNet</a>
<br>

<h3>1. Dataset Citation</h3>
The image dataset used here has been taken from the following web site.<br>
<pre>
ISIC Challenge Datasets 2017
https://challenge.isic-archive.com/data/
</pre>

<b>Citing 2017 datasets:</b>
<pre>
Codella N, Gutman D, Celebi ME, Helba B, Marchetti MA, Dusza S, Kalloo A, Liopyris K, Mishra N, Kittler H, Halpern A.
 "Skin Lesion Analysis Toward Melanoma Detection: A Challenge at the 2017 International Symposium on Biomedical Imaging (ISBI),
  Hosted by the International Skin Imaging Collaboration (ISIC)". arXiv: 1710.05006 [cs.CV]
</pre>
<b>License: CC-0</b><br>
<br>
See also:<br>

<a href="https://paperswithcode.com/dataset/isic-2017-task-1">ISIC 2017 Task 1</a><br>
<pre>
Introduced by Codella et al. in Skin Lesion Analysis Toward Melanoma Detection: 
A Challenge at the 2017 International Symposium on Biomedical Imaging (ISBI), 
Hosted by the International Skin Imaging Collaboration (ISIC)
</pre>
<pre>
The ISIC 2017 dataset was published by the International Skin Imaging Collaboration (ISIC) as a large-scale dataset 
of dermoscopy images. The Task 1 challenge dataset for lesion segmentation contains 2,000 images for training with 
ground truth segmentations (2000 binary mask images).
</pre>
<br>
<h3>
<a id="2">
2 Skin-Cancer-ImageMask Dataset
</a>
</h3>
 If you would like to train this Skin-Cancer Segmentation model by yourself,
please download the dataset from the google drive 
<a href="https://drive.google.com/file/d/1kb7Tc9OSDQ28h83QKstXrhxMuKgWQb89/view?usp=sharing">
Skin-Cancer-ImageMask-Dataset.zip</a>
<br>

Please see also the <a href="https://github.com/sarah-antillia/ImageMask-Dataset-Skin-Cancer">ImageMask-Dataset-Skin-Cancer</a>.<br>
<br>
Please expand the downloaded ImageMaskDataset and place them under <b>./dataset</b> folder to be

<pre>
./dataset
└─Skin-Cancer
    ├─test
    │  ├─images
    │  └─masks
    ├─train
    │  ├─images
    │  └─masks
    └─valid
        ├─images
        └─masks
</pre>
 
 
<b>Skin-Cancer Dataset Statistics</b><br>
<img src ="./projects/TensorflowEfficientUNet/Skin-Cancer/Skin-Cancer_Statistics.png" width="512" height="auto"><br>
As shown above, the number of images of train and valid dataset is not necessarily large. 
<br>

<h3>
<a id="3">
3 TensorflowEfficientUNet
</a>
</h3>
This <a href="./src/TensorflowEfficientUNet.py">TensorflowEfficientUNet</a> model is slightly flexibly customizable by a configuration file.<br>
For example, <b>TensorflowEfficientUNet/Skin-Cancer</b> model can be customizable
by using <a href="./projects/TensorflowEfficientUNet/Skin-Cancer/train_eval_infer.config">train_eval_infer.config</a>
<pre>
; train_eval_infer.config
; 2024/03/20 (C) antillia.com

[model]
model          = "TensorflowEfficientUNet"
generator      = True
image_width    = 512
image_height   = 512
efficientnet   = "B0"
image_channels = 3
num_classes    = 1
activation     = "mish"
optimizer      = "Adam"
dropout_rate   = 0.02
learning_rate  = 0.0001
clipvalue      = 0.5
loss           = "bce_dice_loss"
metrics        = ["binary_accuracy"]
show_summary   = False

[dataset]
datasetclass  = "ImageMaskDataset"
resize_interpolation = "cv2.INTER_CUBIC"

[train]
save_model_file = "best_model.h5"
; To save your model as a saved_model by model.save(model_dir) method,
; please specify empty string for save_model_file as shown below, 
; because the save method never needs filename
;save_model_file = ""

dataset_splitter = True
learning_rate_reducer = True
reducer_patience      = 5
steps_per_epoch       = 200
validation_steps      = 100
epochs        = 100
batch_size    = 4
patience      = 10
metrics       = ["binary_accuracy", "val_binary_accuracy"]
model_dir     = "./models"
save_weights_only = True

eval_dir      = "./eval"
image_datapath = "../../../dataset/Skin-Cancer/train/images/"
mask_datapath  = "../../../dataset/Skin-Cancer/train/masks/"
create_backup  = False

[eval]
image_datapath = "../../../dataset/Skin-Cancer/valid/images/"
mask_datapath  = "../../../dataset/Skin-Cancer/valid/masks/"

[test]
image_datapath = "../../../dataset/Skin-Cancer/test/images"
mask_datapath  = "../../../dataset/Skin-Cancer/test/masks"

[infer] 
images_dir    = "./mini_test/images" 
output_dir    = "./mini_test_output"
merged_dir    = "./mini_test_output_merged"

[mask]
blur      = True
binarize  = False
threshold = 128

[generator]
debug        = True
augmentation = True

[augmentor]
vflip    = True
hflip    = True
rotation = True
angles   = [0, 90,180, 270]
shrinks  = [0.8]
shears   = [0.2]
transformer = True
alpah       = 1300
sigmoid     = 8

</pre>
Depending on these parameters in [augmentor] section, it will generate vflipped, hflipped, rotated, shrinked,
sheared, elastic-transformed images and masks
from the original images and masks in the folders specified by image_datapath and mask_datapath in 
[train] and [eval] sections.<br>
<pre>
[train]
image_datapath = "../../../dataset/Skin-Cancer/train/images/"
mask_datapath  = "../../../dataset/Skin-Cancer/train/masks/"
[eval]
image_datapath = "../../../dataset/Skin-Cancer/valid/images/"
mask_datapath  = "../../../dataset/Skin-Cancer/valid/masks/"
</pre>

For more detail on ImageMaskAugmentor.py, please refer to
<a href="https://github.com/sarah-antillia/Image-Segmentation-ImageMaskDataGenerator">
Image-Segmentation-ImageMaskDataGenerator.</a>.
    
<br>

<h3>
3.1 Training
</h3>
Please move to a <b>./projects/TensorflowEfficientUNet/Skin-Cancer</b> folder,<br>
and run the following bat file to train TensorflowUNet model for Skin-Cancer.<br>
<pre>
./1.train_generator.bat
</pre>
, which simply runs <a href="./src/TensorflowUNetGeneratorTrainer.py">TensorflowUNetGeneratorTrainer.py </a>
in the following way.

<pre>
python ../../../src/TensorflowUNetGeneratorTrainer.py ./train_eval_infer.config
</pre>
Train console output:<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/train_console_output_at_epoch_45.png" width="720" height="auto"><br>
Train metrics:<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/train_metrics.png" width="720" height="auto"><br>
Train losses:<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/train_losses.png" width="720" height="auto"><br>
<br>
The following debug setting is helpful whether your parameters in [augmentor] section are good or not good.
<pre>
[generator]
debug     = True
</pre>
You can check the yielded images and mask files used in the actual train-eval process in the following folders under
<b>./projects/TensorflowEfficientUNet/Skin-Cancer/</b>.<br> 
<pre>
generated_images_dir
generated_masks_dir
</pre>

Sample images in generated_images_dir<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/sample_images_in_generated_images_dir.png"
 width="1024" height="auto"><br>
Sample masks in generated_masks_dir<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/sample_masks_in_generated_masks_dir.png"
 width="1024" height="auto"><br>

<h3>
3.2 Evaluation
</h3>
Please move to a <b>./projects/TensorflowEfficientUNet/Skin-Cancer</b> folder,<br>
and run the following bat file to evaluate TensorflowUNet model for Skin-Cancer.<br>
<pre>
./2.evaluate.bat
</pre>
<pre>
python ../../../src/TensorflowUNetEvaluator.py ./train_eval_infer.config
</pre>
Evaluation console output:<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/evaluate_console_output_at_epoch_45.png" width="720" height="auto">

<pre>
Test loss    :0.2369
Test accuracy:0.9452000260353088
</pre>

As shown above, the loss is not so low, and the accuracy not so high. However, they are a little bit better than those of TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer<br>
<img src="https://github.com/sarah-antillia/TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer/blob/main/projects/TensorflowSwinUNet/Skin-Cancer/asset/evalute_console_output_at_epoch_46.png" width="720" height="auto">
<br>
<br>
Please see also our first experiment <a href="https://github.com/sarah-antillia/Image-Segmentation-Skin-Lesion">Image-Segmentation-Skin-Lesion</a>
 based on a Pre-Augmented-ImageMask-Dataset of 256x256 pixel size.

<br>
<h2>


 
<h2>
3.3 Inference
</h2>
Please move to a <b>./projects/TensorflowEfficientUNet/Skin-Cancer</b> folder<br>
,and run the following bat file to infer segmentation regions for images by the Trained-TensorflowUNet model for Skin-Cancer.<br>
<pre>
./3.infer.bat
</pre>
<pre>
python ../../../src/TensorflowUNetInferencer.py ./train_eval_infer.config
</pre>
Please note that we use <a href="./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test">mini_test</a> dataset which
contains very large images, from 3K to 6.6K in width.<br><br>

mini_test images<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/mini_test_images.png" width="1024" height="auto"><br>
mini_test mask (ground_truth)<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/mini_test_masks.png" width="1024" height="auto"><br>

<br>
Inferred test masks<br>
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/mini_test_output.png" width="1024" height="auto"><br>
<br>
Merged test images and inferred masks<br> 
<img src="./projects/TensorflowEfficientUNet/Skin-Cancer/asset/mini_test_output_merged.png" width="1024" height="auto"><br> 


Enlarged samples<br>
<table>
<tr>
<td>Input ISIC_0012223.jpg</td><td>Inferred-merged ISIC_0012223.jpg</td>
</tr>
<tr>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test/images/ISIC_0012223.jpg" width="640" height="auto"></td>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test_output_merged/ISIC_0012223.jpg"  width="640" height="auto"></td>
</tr>

<tr>
<td>Input ISIC_0012903.jpg</td><td>Inferred-merged ISIC_0012903.jpg</td>
</tr>
<tr>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test/images/ISIC_0012903.jpg" width="640" height="auto"></td>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test_output_merged/ISIC_0012903.jpg"  width="640" height="auto"></td>
</tr>

<tr>
<td>Input ISIC_0013948.jpg</td><td>Inferred-merged ISIC_0013948.jpg</td>
</tr>
<tr>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test/images/ISIC_0013948.jpg" width="640" height="auto"></td>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test_output_merged/ISIC_0013948.jpg"  width="640" height="auto"></td>
</tr>
<tr>
<td>Input ISIC_0014600.jpg</td><td>Inferred-merged ISIC_0014600.jpg</td>
</tr>
<tr>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test/images/ISIC_0014600.jpg" width="640" height="auto"></td>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test_output_merged/ISIC_0014600.jpg"  width="640" height="auto"></td>
</tr>

<tr>
<td>Input ISIC_0014693.jpg</td><td>Inferred-merged ISIC_0014693.jpg</td>
</tr>
<tr>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test/images/ISIC_0014693.jpg" width="640" height="auto"></td>
<td><img src = "./projects/TensorflowEfficientUNet/Skin-Cancer/mini_test_output_merged/ISIC_0014693.jpg"  width="640" height="auto"></td>
</tr>

</table>

<br>
<br>
<h3>
References
</h3>
<b>1. ISIC 2017 - Skin Lesion Analysis Towards Melanoma Detection</b><br>
Matt Berseth<br>
<pre>
https://arxiv.org/ftp/arxiv/papers/1703/1703.00523.pdf
</pre>

<b>2. ISIC Challenge Datasets 2017</b><br>
<pre>
https://challenge.isic-archive.com/data/
</pre>

<b>3. Skin Lesion Segmentation Using Deep Learning with Auxiliary Task</b><br>
Lina LiuORCID,Ying Y. Tsui andMrinal Mandal<br>
<pre>
https://www.mdpi.com/2313-433X/7/4/67
</pre>

<b>4. Skin Lesion Segmentation from Dermoscopic Images Using Convolutional Neural Network</b><br>
Kashan Zafar, Syed Omer Gilani, Asim Waris, Ali Ahmed, Mohsin Jamil, <br>
Muhammad Nasir Khan and Amer Sohail Kashif<br>
<pre>
https://www.mdpi.com/1424-8220/20/6/1601
</pre>

<b>5. Image-Segmentation-Skin-Lesion</b><br>
Toshiyuki Arai @antillia.com
<pre>
https://github.com/sarah-antillia/Image-Segmentation-Skin-Lesion
</pre>

<b>6. TensorflowUNet-Image-Segmentation-Augmented-Skin-Cancer</b><br>
Toshiyuki Arai @antillia.com
<pre>
https://github.com/sarah-antillia/Tensorflow-Image-Segmentation-Augmented-Skin-Cancer
</pre>

<b>7. TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer</b><br>
Toshiyuki Arai @antillia.com
<pre>
https://github.com/sarah-antillia/TensorflowSwinUNet-Image-Segmentation-Augmented-Skin-Cancer
</pre>

<b>8. ImageMask-Dataset-Skin-Cancer</b><br>
Toshiyuki Arai @antillia.com
<pre>
https://github.com/sarah-antillia/ImageMask-Dataset-Skin-Cancer
</pre>
