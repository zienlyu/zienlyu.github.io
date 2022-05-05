---
title:  "Spoken Digit-pair Recognition"
date:   2021-11-15 20:13:45
categories: [Projects]
tags: [Projects]
---

In this supervised learning problem, we are given the feature which is an audio file of two people speaking different digits at the same time. The labels indicate the digits they speak. Allowed digits are {1, 2, 3, 4} and there are six possible labels: {(1,2),(1,3),(1,4),(2,3),(2,4),(3,4)}. The training set consists of 90000 examples and the test set consists of 24750 examples.

**1 data processing**

At first, we used the function wavfile.read() to transform the wav file into structured data. Then we used np.fft.fft() to apply Fast Fourier transform to the data so that we can extract the useful information from the audio file -- the discrete Fourier transform of the sequence. The result is a complex number so we used np.real() and np.imag() to extract the real and imaginary part and deleted the symmetrical data. Finally, by using np.concatenate() to combine them into X_train whose shape is 90000*6002.

As to labels, at first we defined a dictionary so that the six possible labels can be transformed to 0-5. We used the array y_train to denote the labels, the shape of which is 90000.

In order to evaluate the performance of the classifiers, we divided the data into a training set and validation set by 7:3 and used X_trn, y_trn, X_val, y_val to denote the training set and validation set.

About data processing and feature extraction, we made some changes which would be introduced in part 3, like spectrogram and multilabel.

**2 traditional classification methods**

**2.1 Naive Bayes**

Firstly, we tried the Naive Bayes model. We used X_trn and y_trn to fit a Gaussian Naive Bayes model, which turned out to give an accuracy score of 0.72744 on the validation samples.

**2.2 Decision Tree**

After that, we implemented the decision tree classification on the training set. We used a tree with a maximum depth of 10 and the default criterion “gini”. This model gives us a similar accuracy of 0.73396 on the validation samples.

**2.3 PCA feature extraction**

When trying other classifiers, the training time is too long if we use all the 6002 features. So we decided to use the PCA method to extract 500 principal components from X_train and denote the training set with 300 features as X_PCA.

**2.4 SVM classification**

By fitting X_PCA and y_trn into a SVM model with a linear kernel, we obtained a really high accuracy of 0.99996 when predicting labels using X_val.

**2.5 Logistic regression**

Then we fit X_PCA and y_trn into a logistic regression, and it turns out that we have obtained an accuracy of 0.8581 on the validation set.

**3. Deep learning methods**

**3.1 FFT + NN model + single-label**

At first, we used the same data as before, which was the same as the X_trn, y_trn, X_val and y_val we mentioned above. Then we fit X_trn and y_trn into a two-layer neural network. The nn model consisted of a hidden layer with 1000 neurons and a Relu function as an activation function. We chose nn.CrossEntropyLoss() as the loss function and Adam as the optimizer with a default learning rate. The batch size was set at 32. When the accuracy on validation set stops increasing, we stopped the epochs. After 24 epochs, the accuracy on the validation set was 99.9926%. However, after submitting the result, the accuracy on the test data was only 0.90909.

**3.2 FFT + NN model + multi-label**

After observing the data, we found out that there was not a “43” label in our training data. Then the original classification method cannot properly predict the “43” label. Therefore, we turned this problem into a multi-label question. We used another method to read the labels so that “21” indicated that the sample had two labels “2” and “1” . Using a one-hot vector to denote it, it becomes (1,1,0,0). We changed the loss function into nn.BCEWithLogitsLoss(). After 30 epochs, the accuracy on the validation set could achieve 99.9815%. The accuracy on the test set improved to 0.92589.

**3.3 Spectrogram + CNN model + multi-label**

After searching for some knowledge about audio processing, we found out that maybe spectrograms could give us more information about the audios. So we imported the librosa package and used the amplitudes as the features.

Also, we fit the data into a convolutional neural network. We added a convolutional layer with 1 in-channels, 8 out-channels, 2 kernels and 2 paddings. After 10 epochs, the accuracy on the validation set could be decreased to 99.9704%. After submitting the result, the accuracy on the test data improved again, to 0.95410. This shows that my efforts really paid off. And this was my final result on kaggle.
