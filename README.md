# Face detection and face recognition using deep learning 

Being a data scientist I am attracted by the possibilities of machine learning and especially deep learning. Recently I dived into the object and face recognition world of Python. I learned a lot and although  many tutorials are available I sometimes found it difficult to get a complete view on the things I needed. So I decided to share a selection of my notebooks on Github to help others who are interested in this topic. This repository is not a complete tutorial. I will give some clarification below and added comments to my notebooks. But for more background I refer to the resources mentioned in the text and elsewhere.  

Face recognition on normal photographs/snapshots is done in two steps:
- Detecting faces on pictures and cropping them to the right size
- Face recognition using a classification algorithm
For both steps (deep) neural networks can be used.

## Detecting faces on pictures

The Opencv package is very usefull for the first step (face detection). For some years the so called haarcascades provide an easy and fast way to detect images. The haarcascade is an XML file that can be downloaded from opencv and saved in a folder on your environment so it can be read by Python. There are more versions of these cascades, for instance eye cascade. I tried the eyecascade additional to haarcascade but found the results disappointing.  With the latest version of Opencv a new algorithm has been released that consist of a serialized deep learning model. This model can be downloaded (2 files) I spend some time comparing both the haarcascade and this deep learning on a set of pictures. My conclusions are:
	- Haarcascade finds many faces in the front and background but (dependent of the settings) makes many errors.
	- Deeplearning finds less faces and makes few errors. But it has some trouble finding small faces in the background.

Probably the limitations of Opencv's deeplearning are caused by the size of the input image (300x300 pixels) while haarcascade is using the entire size (but uses no colors). So my idea was to make use of the best of both worlds and use both models. First haarcascase is used to generate possible faces by using settings that generate many faces but lead to many errors. The candidates are cropped and these cropped images are fed to the deep learning net. So deep learning receives candidate faces of a large size and with this input can better confirm if the candidates are real faces. This two steps face detection leads to better results: more faces detected and less errors. In the notebook cv2-compare this is demonstrated. I made an additional notebook cv2_readbatch that crawls over local or network folders to find images and uses this two steps face detection to find faces. 

## Face recognition

Having experience with Python, the Keras package and Tensorflow I have tried the VGG16 deep learning model for object detection in the past. The VGG model was developed some years ago and won the image classification task in 2014. Although new models were developed it still performs very well and can be used for free. The face recognition version of the model has the same architecture but is trained on 2622 identities and multiple images. The accuracy of the model is above 95%. Resources for Python and Keras can be found here https://github.com/rcmalli/keras-vggface. The model recently has been retrained using RESNET50 or SENET50. I haven't tried these until so far as the genuine model performed very well.

Training a deep learning model is an extensive task that requires much resources. But fortunately the VGGFace model is available in a pre-trained version.  By applying the practice of transfer learning (explained very well here: https://machinelearningmastery.com/transfer-learning-for-deep-learning/) you can use the model for your own dataset. Training and prediction is possible in this way on moderate hardware (it even predicted well on a i3/4GB laptop).

VGGFace requires pictures of a particular format (224x224 pixels and 3 colors). Moreover the faces to be recognized must be pretty close. So one or more faces on a picture from a certain distance is not usable. Software is needed to detect faces on images and then the faces need to be cropped to the right size. For this task I selected OpenCV. https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_objdetect/py_face_detection/py_face_detection.html#face-detection 
This package contains functionality that recognizes faces. In this way multiple faces  of different sizes can be detected on a single picture and used by the model. More about Opencv and its face detection capabilities with deep learning in a separate chapter. 

Face recognition is a typical classification task. Before predictions can be made a labelled training set is needed. I trained the model using the 'Faces in the wild' dataset. http://vis-www.cs.umass.edu/lfw/ I selected 17 different identities with 10 or more pictures, preprocessed the pictures and trained and tested the model. When transfer learning is applied  the model must be adapted. This can be done by removing the so called 'top' of the model. I did some experiments replacing 1 and 3 layers and discovered that just replacing the last layer and adding a classification layer with 17 classes performed very well (95-100%) within 2-4 epochs. Training 3 replaced layers required considerable more  epochs and took much more time. With this experience I selected pictures of my own collection (initially with a limited number of classes), preprocessed them and trained the model with comparable results. I stored the optimized model using HDF5 and json. The described steps are part of the notebook 'VGGface classification lfw data'. 

The final step was building a small pipeline ('predict_picturebook') that reads pictures in different folders, preprocesses the pictures, classifies  the pictures with the stored model and returns an overview of the results. In this way unknown faces are read, preprocessed and input for the restored model. Unfortunately the model doesn't contain an unknown category, so these faces are classified against known classes. To solve this issue I decided to use the probability of the predictions. With known faces the probability is usually 0.95 or higher. Unknown faces lead to probabilities that are spread across different labels and are usually below 0.5. So I use this threshold to determine the unknown faces. Unknown faces can be collected and when enough faces are there they can be labelled and the model can be retrained. Doing this in multiple iterations the most common faces can be detected and labelled and the model will be more robust.

#### Use case photograph/snapshot collection

Face recognition can be useful for various use cases in for instance security, robotization or domotica. One of my motivations to dive into this subject was my struggle with my photo collection. With the current availability of photo devices, whether they are DSLR's or smart phones the number of images taken is increasing. I am rather lazy in sorting the pictures. From time to time I drop pictures of myself and my family on the NAS, that evolved to an unstructured collection of hundreds of gigabites. I can sort a bit on time and directory names, but when I want to find pictures of a specific person I don't have any instruments. With the notebooks that I developed I can crawl this directory structure and generate a list of found faces that I can use to search for pictures 

#### Future plans
I got some ideas for future plans:
- I found that Opencv can be used to detect faces in video. I even found that it can be used on a RPI. I have done some interesting things using a RPI in the past: making a crossover filter and digital sound processor, probably worth another Github repository :-)  So it would be interesting to see if I can get Opencv to work on it. 
- VGG is also trained on object detection. Probably I can use this model to add objects (including dogs and cats) to my photograph/snapshot detection to make an even more complete 'content detection' algorithm. 

While I made these notebooks I read various websites and github repositories and reused snippets of code from different sources. I can't reconstruct what I have used from what sources so I finalize with a general 'Thank you so much!' for so many valuable material on the internet that helped me to make this repository. If you have any questions or remarks don't hesitate to contact me. 
