# FaceAuthenticator

Authors: Azucena Morales, Joaquin Menendez, Julia Oblasova

A facial recognition pipeline that employs a Flask App running on Google Cloud Run to upload and preprocess videos of people faces. The processed data is uploaded to AWS Sagemaker where a SVM model is trained and deployed. The user could interact with this ML model using the Flask app or sending a POST request.
You can see a quick demostration here.

Keywords: GCR, Flask, Container, SageMaker, Pytorch, Scikit-Learn

Authenticator App is a cloud-based application that recognizes team members and assigns appropriate rights of access (or identifies an unauthorized attempt and declines the access).

The app process is described below:

1). A team lead uploads videos of each team member along with the names and access rights: for example, Julia Oblasova – data science manager. All videos are upload to the Flask App.

2). The app splits each video into single frames using python library CV. Each n frame is saved.

For each frame, face detection is done using a Multi-Task Cascaded Convolutional Neural Network (MTCNN). If the model detects a face, the cropped and scaled image with the face will be used to train the model.
4). The collection of cropped images with faces is converted to embeddings using a VGG-16 net model that has been pretrained in faces. Steps 2). 3). and 4) are completed in GCR.

5). The result of preprocessing step is a pickle object with a dictionary of image embeddings and labels.

6). Using Sagemaker an SVM model is trained using the using the pickle object that is allocated in an S3 bucket.

7). After the training process is completed, Sagemaker deploys the model to an ednpoint.

8). For the testing step, a user submits a photo. The photo is preprocessed with steps 3) and 4) in GCR. Then, the embedding is passed as a binary file in numpy format through an endpoint to the Sagemaker SVM model. Finally, the model predicts who is in the photo. If the probability of recognition is 90% or above, a user on a photo will be granted the access rights.

Note: a user may connect to SageMaker directly through the console via a POST request.
