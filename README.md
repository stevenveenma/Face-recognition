# Face-recognition
Face detection and face recognition using deep learning

Face recognition on normal pictures is done in two steps:
1. Detecting faces on pictures and cropping them to the right size
2. Face recognition using a classification algorithm
For both steps (deep) neural networks can be applied. 

Using Python the Opencv package is very usefull for the first step (face detection). For some years the so called haarcascades provide an easy and fast way to detect images. The haarcascade is an XML file that can be downloaded from opencv and saved in a folder on your environment so it can be read by Python. With the latest version of Opencv a new algorithm has been released that consist of a serialized deep learning model. This model can be downloaded (2 files) and applied from Python as well. I spend some time comparing both models for a set of pictures. My conclusions are:
- Haarcascade finds many faces in the front and background but (dependent of the settings) makes many errors.
- Deeplearning finds less faces but makes no errors. It has some trouble finding small faces in the background.

Perhaps the limitations of deeplearning are caused by the size of the input image (300x300 pixels) while haarcascade is using the entire size (but uses no colors). So my idea is to make use of the best of both worlds and use both models. First haarcascase is used to generate possible faces by using settings that generate many faces but lead to many errors. The candidates are cropped and these cropped images are fed to the deep learning net. So deep learning receives candidate faces of a large size and with this input can better confirm if the candidates are real faces. This two step face detection leads to better results: more faces detected and less errors. In the notebook cv2-compare this is demonstrated.
