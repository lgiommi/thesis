I am glad to inform you that now I have done, in the physics top hadronic use case, with the part of obtaining predictions from a ML model and comparing the results with a standard root mva. Let me go a little bit deeper in the work I’ve done.

I am using two root files, one composed by tt simulated data and one by real data (coming from bunch crossing, so mainly made by background). My job was to create a model that is able to predict, given a dataset, if an event is a tt signal or if it is background. This model was trained using a subsample of the two root files (the first one coming from SIM root file, that tells me how is made a signal, the second one coming from the real data root file, that tells me how is made the background). I’ve done some tests in order to obtain the model with best accuracy and I decide to use [1].
After that I have tested my model on a subsample of SIM data and real data (two subsamples made by completely different data respect to the data of the two subsamples used to train the model, and so data that the model had never seen before). I need to test the model on SIM data to obtain my efficiency on recognise the signal, and then if I test the model on real data I can compare the expected signal (on the whole real data) with the signal recognised by the model, obtaining the purity of that sample.
I can compare these values with the job done by the analysts that do a multivariate analysis inside ROOT. The mva produce for each event contained in the ROOT files an output, a continue variable (called mva), that is in the range from 0 to 1. The higher is the value of mva, the higher is the probability for an event to be a signal. So what I have done is to compare the predictions of ML with the predictions coming from the mva. And what I’ve seen is that ML has better performance (in terms of efficiency and purity). Let me show you where ML is located respect to the mva with this plot [2] (where is shown which are the efficiencies and the purities for different mva cuts). The improvement is not too big but, with a simple application of ML (a little and simple xgboost) I’ve obtained a result comparable (and little bit better) respect the multivariate analysis implemented inside ROOT.

Obviously what I have produced are preliminary results and what I have to do now is to fix better all the passages I’ve done, validate better my results in order to produce a final result, but as you can see what I’ve obtained is encouraging.
In parallel there are three main path to follow and let me know what do you think about:
1) one is to go deeper on ML techniques, in order to test different classifiers/approaches, to study performances and which are changes in the physics results (in terms of efficiency/purity);
2) move to another use case (classification task or regression task for another physics use case about muons)
3) try to move this code inside reader.py and more generally inside tfaas project (and so maybe using tensorflow for the same tt use case that I have studied), in order to implement an analysis outside ROOT (that uses ML techniques) using the CMSSW infrastructure.

For Daniele and me the last point is the more interesting, and is the original path that we had prefixed to do at the beginning of the job. What do you think?
Meanwhile if you want to inspect what I have done in the creation of the model, this [3] is the notebook. As you can see I have saved the model as a .sav file, that in another notebook I have loaded and applied to the datasets to have predictions. Therefore would be interesting to have all the passages I’ve done as a pipeline loaded inside reader.py.

[1]
XGBClassifier(learning_rate=0.05, n_estimators=200, max_depth=6)

[2]
![Efficiency VS purity](https://drive.google.com/file/d/1VRnKnSRlJDaAYdV8NQhG57RH51Dx0key/view?usp=sharing)

[3]
https://github.com/lgiommi/thesis/blob/master/Model_predictions.ipynb