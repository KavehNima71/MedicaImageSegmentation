![1](https://github.com/user-attachments/assets/0ea7c8a3-8c64-45d0-b263-6598ad488d63)
<h1 align="center">Medical Image Segmentation</h1>

## 1. Problem Statement
Medical Image Segmentation is the process of dividing medical images (like MRI, CT, or X-ray scans) into distinct regions to identify and analyze anatomical structures, abnormalities, or areas of interest at the pixel/voxel level.

**Key Types & Uses:**
- Organ/Tissue Segmentation – Isolates structures (e.g., brain, liver, tumors) for diagnosis or surgery planning.
- Lesion/Tumor Segmentation – Highlights diseased areas (e.g., cancerous tumors) for monitoring and treatment.
- Cell Segmentation – Identifies individual cells in microscopy images for research (e.g., cancer cell detection).

**Why It Matters:**
- Improves diagnostic accuracy (e.g., measuring tumor size).
- Guides radiation therapy and surgical planning.
- Enables AI-driven analysis (e.g., early disease detection).

This project aims to develop an AI-powered solution for automated segmentation of MRI images. The primary focus is on creating a deep learning model capable of accurately differentiating between three key regions: the stomach, intestines, and cancerous tumors. In current practice, physicians must manually annotate these organs in daily scans. This process is not only time-consuming but also prone to inaccuracies due to daily anatomical variations in patients and human error. The manual approach significantly prolongs treatment preparation time while potentially compromising targeting precision in radiation therapy.

![2](https://github.com/user-attachments/assets/5ef60d3b-589e-4150-93ee-c4c81ce6e502)

As shown in the figure, the tumor (pink thick line) is close to the stomach (red thick line). High doses of radiation are directed to the tumor while avoiding the stomach. The dose levels are represented by the rainbow of outlines, with higher doses represented by red and lower doses represented by green.

## 2. Related Works

- [**U-Net: Convolutional Networks for Biomedical Image Segmentation**](https://arxiv.org/abs/1505.04597)
- [**UNet++: A Nested U-Net Architecture for Medical Image Segmentation**](https://arxiv.org/abs/1807.10165)
- [**The Fully Convolutional Transformer for Medical Image Segmentation**](https://arxiv.org/abs/2206.00566)

## 3. The Proposed Method

I used the U-Net architecture for this. U-Net is an advanced convolutional neural network architecture specifically designed for medical image segmentation tasks. Named after its distinctive U-shaped structure, this model was first introduced in 2015 by researchers at the University of Freiburg for microscopic image segmentation and quickly became the gold standard in medical image processing.

![3](https://github.com/user-attachments/assets/7c4d1013-c516-42e1-b600-a46ca57779ed)


The U-Net architecture consists of two main pathways: the contracting path (encoder) and the expansive path (decoder). The contracting path processes the input image through successive convolutional and max-pooling layers, gradually reducing image dimensions while extracting increasingly complex features. Conversely, the expansive path utilizes transposed convolution operations and skip connections to simultaneously restore the original image dimensions and recover lost spatial details.

The key advantage of U-Net lies in its skip connections, which link corresponding layers in the contracting and expansive paths. This unique feature enables the model to preserve both high-level semantic features and precise spatial details simultaneously - a critical capability for medical applications requiring accurate delineation of organ boundaries and lesions.

## 4. Implementation
This section delves into the practical aspects of the project's implementation.

### 4.1. Dataset
Under this subsection, you'll find information about the dataset used for the medical image segmentation task. It includes details about the dataset source, size, composition, preprocessing, and loading applied to it. [**Dataset**](https://www.kaggle.com/competitions/uw-madison-gi-tract-image-segmentation/overview)

The dataset is MRIs of patients provided by the UW-Madison Carbone Cancer Center. The dataset contains a file named train.csv, which includes 115,488 rows. Each image has three parts: small bowel, large bowel, and stomach, which define these three regions. The annotations are provided in a csv format with the segmented areas represented as RLE-encoded masks and the images are in 16-bit grayscale PNG format. It would typically need to decode the RLE encoded masks to create pixel-wise binary masks. An empty segmentation entry represents no mask presented for the class in the MRI scan slice.

**Files:**
- **train.csv:** IDs and masks for all training objects. 
- **train.txt:** case IDs for training objects. 
- **validation.txt:** case IDs for validation objects. 
- **test.txt:** case IDs for test objects. 
- **train:** a folder of case/day folders, each containing slice images for a particular case on a given day.

Based on the provided text files containing the split information, we organized the dataset into separate training, validation and test subsets.
- **train-subset.csv:** IDs, large_bowel, small_bowel, stomach, image_paths, case_id, day, slice,	width, height and counts for training objects.
- **valid-subset.csv:** IDs, large_bowel, small_bowel, stomach, image_paths, case_id, day, slice,	width, height and counts for validation objects.
- **test-subset.csv:** IDs, large_bowel, small_bowel, stomach, image_paths, case_id, day, slice,	width, height and counts for test objects.

In the training dataset, the number of samples corresponding to each class is indicated below: Large bowel: 10,143 Small bowel: 8,190 Stomach: 6,191

**Visualization of a batch of images and target masks:**

![5](https://github.com/user-attachments/assets/187fec25-8a56-4d2b-90d1-88c2c8aa5fb9)

**red: large_bowel, green: small_bowel, blue: stomach**

Plot histogram of the number of samples per case:
![6](https://github.com/user-attachments/assets/035b00ea-fe80-49c1-87d1-b9c68b36c82e)

 Plot histogram of image sizes:
 
 ![7](https://github.com/user-attachments/assets/c8d49558-56a2-4655-9bde-41d3c650d910)

### 4.2. Model
This project utilizes the [**Segmentation Models PyTorch**](https://segmentation-modelspytorch.readthedocs.io/en/latest/) (SMP) library in combination with [**PyTorch**](https://pytorch.org/) to implement a U-Net model for medical image segmentation.

**Model:**
>in_channels = 3, # model input channels

>num_classes = 3, # model output channels

>model = smp.Unet(encoder_name='efficientnet-b1', encoder_weights='imagenet', in_channels=3, num_classes=3)

### 4.3. Configurations
**Loss Function:**

>loss_fn = smp.losses.DiceLoss(mode='multilabel')

**Metric:**

>metric = torchmetrics.Dice(average='macro', num_classes=3).to(device)

**Optimizer:**

>optimizer = torch.optim.SGD(model.parameters(), lr=0.1, momentum=0.9)

### 4.4. Train
Here, you'll find instructions and code related to the training of the segmentation model. This section covers the process of training the model on the provided dataset.

### 4.5. Evaluate

**Plot learning curves:**

![learning_curves](https://github.com/user-attachments/assets/5fd13855-2ef0-4c48-917b-145e735ee539)

**model's segmentation result**

![11](https://github.com/user-attachments/assets/a6b58fa8-fc7b-4b62-966d-e6b97aa0d91a)
![13](https://github.com/user-attachments/assets/a74db686-db9b-41ba-8a3b-29d20179cf37)
![14](https://github.com/user-attachments/assets/8588e040-aa00-44fa-9dfd-24eb143c7d8d)




