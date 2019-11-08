# deepmutation
original idea: https://arxiv.org/pdf/1805.05206.pdf

This is a project I selected as my course project for Analysis of Software Artifacts in Fall 2019 at the University of Virginia. As explained in the above paper, the goal is insert mutants into the model and the data in order to evaluate the quality of the testing data in deep neural networks. Below is my proposal for the project, which largely overlaps with the original paper, DeepMutation: Mutation Testing of Deep Learning Systems.

The workflow of deep learning software development roughly respects the following procedure:
obtain a suitable dataset, preprocess, split it into training and testing data sets, develop a model, train it on the training data, evaluate the performance of the model on the test data before deployment. Since the test data is the main source to evaluate the model, its quality is of critical importance. Therefore, in this project, I ask and will try to answer the following question: “How can we evaluate the quality of the test dataset in deep learning”?

To answer this question, I use mutation testing and generally follow the procedure of Ma et al:
	Train a DNN model on the original training data. Specifically, I will use a convolutional neural network as my model Moriginal and the MNIST data set.
	Test the neural network on the test data. Mark the test data points that are correctly classified by the model. Call it Dcorrect.
	Insert a mutant. Example of mutants:
	removing, repeating, mislabeling the training data
	removing, adding hidden layers in DNN
	initializing the neuron weights with pre-trained weights
	negating and removing the activation function
	For each mutant mk, I will re-evaluate the mk-mutated DNN on Dcorrect. If any of Dcorrect is misclassified, mk survives and is evaluated. Otherwise, it is killed (no effect). The evaluation method of mk is provided below.

After testing out the results on the MNIST data set, I will try mutation testing on more complex data sets, such as CIFAR10 or MS-COCO. In addition, I will discover new, effective mutants in addition to the ones mentioned in step 3. For example, I believe that swapping the order of hidden layers will be a very useful mutant. For example, if the original two hidden layers contained x and y neurons respectively, I would change the order to y and x. Often, DL engineers design networks based on previous experiences on similar data sets or empirically on how different layer ordering performs on training/validation data sets. Being able to draw a conclusion on layer ordering based on the results from mutation testing presents another way for DL engineers to design more effective DNNs.

It is safe to believe that the test data points that get misclassified after mutants are inserted are of lower quality than those unaffected by mutation. The term quality in the context of classification refers to how far a data point lies from the model’s decision boundary. The farther it is, the more certain the model is about to which class it belongs to—therefore, the higher its quality.

Suppose that the number of different classes is c; each data point belongs to one of {class1, class2, … classc}. I evaluate the mutation score for mutant mk as follows:

MutationScore(D_correct,m^k) = ∑_(i=1)^c▒(count_misclassify(D_correct,m^k,〖class〗_i))/(〖|D〗_correct  ∈〖class〗_i |)

where count_misclassify(Dcorrect, mk, classi) is the number of test data points in Dcorrect that belong to classi  that mk causes the model to misclassify. I divide this value by the number of test data points in Dcorrect that belong to classi (〖|D〗_correct  ∈〖class〗_i |). This way of calculating the mutation score handles the case where two data sets whose sizes are largely different and also differentiate the mutant that causes only a few of D_correct  ∈〖class〗_i to be misclassified from the one that causes the majority of them to be.

It is highly likely, that most, if not every, mutant will result in different mutation scores. One way to interpret this is how detrimentally a mutant affects the model’s performance: the higher the score, the more detrimental its effect. This project will matter to DL developers who build DNNs to decide on the quality of the test data, provide them a way to evaluate data preprocessing methods, gain confidence on their model’s performance on the test data, as well as those who gather the data itself.
