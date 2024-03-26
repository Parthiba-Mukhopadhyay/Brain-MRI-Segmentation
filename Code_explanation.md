---

# Brain MRI Segmentation

## Dataset

**Dataset Used:** [Kaggle LGG MRI Segmentation (3M)](https://www.kaggle.com/mateuszbuda/lgg-mri-segmentation)

### About the Dataset
- The dataset contains brain MRI images along with manual FLAIR (Fluid-attenuated Inversion Recovery) abnormality segmentation masks.
- It provides pixel-level classification of abnormal regions within the brain MRI images.
- Organized in subsets, each containing exactly 5 cases or 5 patients' data, with a total of 22 subsets.
- Includes both tumor and normal brain MRI images.
- Tumor images may contain various types such as Glioma, Metastatic adenocarcinoma, Metastatic bronchogenic carcinoma, Meningioma, and Sarcoma.

## Libraries Used

- **NPP (Neural Pre Processing):** End-to-end weakly supervised learning approach for converting raw head MRI images to intensity-normalized, skull-stripped brains in a standard coordinate space.
- **Nilearn:** Enables approachable and versatile analyses of brain volumes, supporting GLM-based analysis and leveraging scikit-learn for multivariate statistics.
- **Nibabel:** Python library providing read and write access to common medical and neuroimaging file formats.
- **TorchIO:** Open-source Python library for efficient loading, preprocessing, augmentation, and patch-based sampling of 3D medical images in deep learning, following the design of PyTorch.
- **Einops:** Python library used to manipulate tensors (multidimensional arrays) in ML models.
- **Surfa:** Collection of Python utilities for medical image analysis and mesh-based surface processing.
- **PyTorch Lightning:** Lightweight PyTorch wrapper for high-performance AI research, aiming to scale models, not the boilerplate.
- **Skull Stripping:** Open-source library, part of PyTorch Lightning, for preparing skull stripping methods on T1 sequence head MRI images.
- **Argparse:** Module for creating programs in a command-line environment.
- **Itertools:** Module implementing iterator building blocks inspired by constructs from APL, Haskell, and SML.
- **Shutil, Glob:** Libraries for file handling and manipulation.
- **Seaborn, Matplotlib:** Libraries for visualization and graphical representation.
- **Tqdm:** Library for animated loading and processing sections.
- **TensorFlow, Scikit-image, NumPy, Pandas:** Predefined libraries for image processing and data manipulation.

---
---

```python
import os

dirs = []
masks = []
images = []

for dirname, _, filenames in os.walk(DataPath):
    for filename in filenames:
        if 'mask' in filename:
            dirs.append(dirname.replace(DataPath, ''))
            masks.append(filename)
            images.append(filename.replace('_mask', ''))
```
The above code iterates through the directory specified by `DataPath` using `os.walk()`, which generates the file names in a directory tree by walking either top-down or bottom-up. For each file, it checks if the filename contains the substring 'mask'. If it does, it appends the directory name (with `DataPath` removed), the mask filename, and the corresponding image filename (with '_mask' removed) to separate lists. This process effectively identifies image and mask files within the specified directory structure and organizes their names into separate lists.

```python
timage_generator = imagegen.flow_from_dataframe(dataframe=train, 
                                                x_col="image-path", 
                                                batch_size=BATCH_SIZE, 
                                                seed=42, 
                                                class_mode=None,
                                                target_size=(ImgHeight,ImgWidth),
                                                color_mode='rgb')
```
In this code block, a Keras `ImageDataGenerator` is initialized to generate batches of training images using the data specified in the dataframe `train`. The `flow_from_dataframe` method is used to generate batches of images from the dataframe. Parameters such as `x_col` specify the column in the dataframe containing the image file paths, `batch_size` sets the size of each batch, `seed` sets the random seed for reproducibility, `class_mode=None` indicates that the generator will not assign any labels to the images, `target_size` specifies the dimensions to which the images will be resized, and `color_mode='rgb'` specifies that the images will be loaded in RGB color mode.

```python
def data_iterator(image_gen, mask_gen):
    for img, mask in zip(image_gen, mask_gen):
        yield img, mask
```
The `data_iterator` function takes two generators (`image_gen` and `mask_gen`) as input and yields batches of images and masks as tuples `(img, mask)` by iterating over them using `zip`.

```python
callbacks = [
    EarlyStopping(patience=10, verbose=1),
    ReduceLROnPlateau(factor=0.1, patience=5, min_lr=1e-5, verbose=1),
    ModelCheckpoint('model-brain-mri.h5', verbose=1, save_best_only=True, save_weights_only=True)
]
```
These callback functions are used during the training of a neural network model:
- `EarlyStopping`: Stops training if a monitored metric has stopped improving for a specified number of epochs (patience).
- `ReduceLROnPlateau`: Reduces the learning rate when a monitored metric has stopped improving. It reduces the learning rate by a factor if no improvement is seen for a number of epochs defined by the patience parameter.
- `ModelCheckpoint`: Saves the model's weights to a file whenever the validation loss has improved. The `save_best_only=True` argument ensures that only the weights of the best-performing model are saved.
