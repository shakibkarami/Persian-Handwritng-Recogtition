# Persian handwriting recognition using CNN
This project was part of final project of the course <a href="https://wp.kntu.ac.ir/nasihatkon/teaching/cvug/s2020/">Foundations of Computer Vision Course</a> instructed by Dr. Behrooz Nasihatkon.
It contains scripts for data pre-processing and exctraction, annotation, training, and prediction and uses computer vision and machine learning techniques. 

# Data collection
The original dataset is taken from the one that is collected by students in the course, which can be found <a href='https://wp.kntu.ac.ir/nasihatkon/teaching/cvug/s2020/assets/files/project/Persian-digits-and-letter-raw.zip'>in the official website of the course</a>. However, in this repository, some modifications are applied to the dataset and some images that were not collected according to the instructions were removed. We recommend to use this dataset can be found in `data/01_raw.zip`. It should be unzip and put data in `data/01_raw` directory.

The dataset contains a set of images, taken from forms that can be found in `assets/dataset_form_a5.pdf`. Each form contains 4 Aruco markers and a number of cells which should be filled with persian handwritten numbers and letters. These forms have 2 types:
Type 'a': number '0', '1', the first part of the persian alphabet, '2', and '3'.
Type 'b': number '4', '5', the second part of the persian alphabet, '6', '7, '8', and '9'. 

In case of a need to collect a new dataset, or add more data to the current dataset, the following instructions must be followed:
1. Form should be printed, prefrably in A5 size.
2. The orientation of the arucos should be the same as a sample shown in `assets/dataset_sample1.jpg`.
3. Each row is dedicated to either a number or a letter. 
4. Each row should be filled with the determined order of the specific type of form. It should be either the same as `assets/dataset_sample1.jpg` (type a) or `assets/dataset_sample2.jpg` (type b).
5. Image should be taken in format 'jpg'. While the orientation of the arucos should be fixed, the form it self can be in custome distance and orientation.
6. The image should be stored in the `data/01_raw` directory with the correct subdirectory that corresponds to the type of form.
7. (pre_labeling step): Each form will be separated manually in `data/02_splitted/a` or `data/02_splitted/b` based on their type. 
   
# Data pre-processing
The objective of the data pre-processing is to first, extract each cell from each form, and store each cell in a folder that represents the class of that cell (0 to 9 for the numbers and 10 to 42 for the letters). This is the labeling process. Also, shuffling the labeled dataset and split it to train, val, and test set is done afterwards.

Since in the original dataset, the images are stored numrically altogether, it is not easy to store the extracted cells into the correct subfolder that would represent the label/class of that cell with only computer vision tools. Therefore, the manual separation of the forms based on their type (a or b) is needed. The separated dataset can be found `data/02_splitted.zip`. It should be unzip and data should be stored in `/data/02_splitted` directory.

The labeling process can be done by the `src/data_preprocessing.py` script which operate as follows (The labeled data can be found in `data/03_labeled.zip` in case one would like to skip executing the `src/data_preprocessing.py` script.):
1. Makes the neccessary directories in the `data/03_labeled` directory. These directories will later represent the class of each data.
2. Reads image
3. Detects the Aruco markers, and drops the ones that their markers cannot be detected
4. Applies perspective transformation on the image so that the orientation of the form will be fixed for all images
5. Resize the images to a determined size in order to have the same size for all the images
6. Extracts each cell, resizes them to a determined size, and store them to their correspondent folder in the `data/03_labeled` directory(labeling process)

The last step would be to shuffle the dataset and create the train, val, and test set based on the ratio. The output can be found in `data/04_final.zip`.

For running the preprocessing follow these steps:
1. Put the splitted dataset into the proper directory (default is the data folder)
2. Add the relative path of the splitted, labeled, and final into the config file, which is in `config/config.yaml`.
3. If the config file is different than `config/config.py` pass its path in the `src/data_preprocessimg.py` script.
4. Execute the script in the project directory:
```bash
cd Persian-Handwriting-Recognition/
python3 src/data_preprocessing.py
```

# Model training
The model training procedure is done with the `src/train_model.py` script. After the data preprocessing step, the dataset is ready be used for training. First step is to create data_generators for train, val, and test data and do a bit of data augmentation on the data. Then, the model is built and compiled. The training step is done afterwards where the model is trained in 50 epochs (can be modified in config file). 
The trained model is stored in `models/trained_model.h5`. Then, the evaluation is done and the accuracy and loss on the test model is calculated. If one wants to skip the training, the model can be found in `models/train_model.zip`.

Here are the summary of the train/validation/test processes:

Train: loss = 0.3356 | accuracy = 0.8877 
Validation: loss = 0.1868 | accuracy = 0.9371
Test: loss = 0.1630 | test accuracy = 0.9445

# Inferance and prediction
After the training procedure, the model is ready to be used for prediction. The `src/prediction.py` script does the prediction for images that are extracted from test forms. 

A test form sample can be seen in `assets/test.jpg`. First the cells should be extracted from it (quite same procedure as `src/data_preprocessing.py`) with `src/extract_data_from_test_form.py`. The test forms should be put in `data/test_forms/forms` and the script will save the restuls in `data/test_forms/extracted` under a folder that represents the name of the form. 

The objective would be to predict the student ID, first name, last name and the degree of the student based on the given form. 
