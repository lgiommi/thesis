This week, after we have solved some little problems with reader.py and above all after I have understand better how it works, I went deep into my physics problem of which I already mentioned to you in the previous mails. I try to expose you better which is the problem and what we want to do. First of all I have used reader.py to read root files and create csvs in order to work freely locally using Jupyter Notebooks (but I know that when I want I can do the same thing inside reader.py). It seemed to me an easy way to path. So I use reader.py on
`/afs/cern.ch/work/a/acastro/public/forDaniele/flatTTbst2017tris/flatTree_TT_TuneCUETP8M2T4_13TeV-powheg-pythia8.root` (that is the root file with simulated data) and I create `/afs/cern.ch/work/b/bonacor/public/simulatoReduced.csv` csv with the columns that I want. Then I run reader.py on `/afs/cern.ch/work/a/acastro/public/forDaniele/flatTTbst2017tris/flatTree_JetHT.root` (that contains real data an so mainly background data) and I produced `/afs/cern.ch/work/b/bonacor/public/reale3.5.csv` . Here I decided to use only 3.5 million against the 24 million that are inside the root file otherwise the dimension was too much big (with 3.5 million the csv is already 1.4 GB) and because otherwise the maximum dimension of the jagged arrays of the data would become 6 and not 5 as the simulated dataset. So this needs to increase the dimension of the arrays also for the simulated data.
Now I have to show you what is our physical aim and how we have to build the model (here there is the full description of the elements inside the root file https://docs.google.com/document/d/1xAde4A7C7aejoQO-F_8vpUzf27QMHUAUfn0f96CgAsg/edit).

> 1) Apply the “preselection” on the simulated file in order to create
> the signal
> - trigger multijet where one is btagged, that is "triggerBit[2] ==1"
> - at least fat-jets, that is "nJets >=2"
> - threshold on Pt, that is "jetPt[0]>400 && jetPt[1]>400"
> - no leptons, that is "nLeptons == 0”
> 
> 2) Apply the “preselection bis” on the root file with real data in
> order to create the background dataset
> - “triggerBit[4] ==1” that is with no btagged jets
> - at least fat-jets, that is "nJets >=2"
> - threshold on Pt, that is "jetPt[0]>400 && jetPt[1]>400"
> - no leptons, that is "nLeptons == 0”
> - nBjets==0 && category==0

After that I merged the two set of data where I add an additional target column, that contains one for data from group 1 and zero for data from a group 2. You can find here a notebook in which I have done these things https://github.com/lgiommi/thesis/blob/master/Pre_ML.ipynb. After that I started to do some ML. I directly used LogisticRegression using all the columns but I obtained a 100% accuracy. So I wrote [1] and I’ve seen that there were three columns of triggerBit that are fully correlated with data that were signal or background. So after I have deleted them I wrote [2] and I have obtained [3]. Now I want to use other classifier as xgboost, adaboost and compare better the performances.
After that I have to apply the model to the data that pass the preselection and with category==2, using the simulation dataset to obtain signal, using real data to obtain background. Then I have to compute S/B and S/sqrt(B) and to compare the results with S and B obtained when I do cuts on mva variable that is an output from TMVA.



[1]

    from sklearn.feature_selection import RFE
    from sklearn.linear_model import LogisticRegression
    #Feature Extraction with RFE
    model = LogisticRegression()
    rfe = RFE(model, 3)
    fit = rfe.fit(X, Y)
    print("Num Features: %d" % fit.n_features_)
    print("Selected Features: %s" % fit.support_)
    print("Feature Ranking: %s" % fit.ranking_)

[2]

    from matplotlib import pyplot
    from sklearn.model_selection import KFold
    from sklearn.model_selection import cross_val_score
    from sklearn.linear_model import LogisticRegression
    from sklearn.tree import DecisionTreeClassifier
    from sklearn.neighbors import KNeighborsClassifier
    from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
    from sklearn.naive_bayes import GaussianNB
    from sklearn.svm import SVC
    # Compare Algorithms
    # prepare models
    models = []
    models.append(( 'LR' , LogisticRegression()))
    models.append(( 'LDA' , LinearDiscriminantAnalysis()))
    #models.append(( 'KNN' , KNeighborsClassifier()))
    models.append(( 'CART' , DecisionTreeClassifier()))
    models.append(( 'NB' , GaussianNB()))
    #models.append(( 'SVM' , SVC()))
    
    # evaluate each model in turn
    results = []
    names = []
    scoring = 'accuracy'
    for name, model in models:
      kfold = KFold(n_splits=10, random_state=7)
      cv_results = cross_val_score(model, X, Y, cv=kfold, scoring=scoring)
      results.append(cv_results)
      names.append(name)
      msg = "%s: %f (%f)" % (name, cv_results.mean(), cv_results.std())
      print(msg)
    # boxplot algorithm comparison
    fig = pyplot.figure()
    fig.suptitle('Algorithm Comparison')
    ax = fig.add_subplot(111)
    pyplot.boxplot(results)
    ax.set_xticklabels(names)
    pyplot.show()

[3]

    LR: 0.905559 (0.001851)
    LDA: 0.897600 (0.000813)
    CART: 0.881419 (0.001656)
    NB: 0.834356 (0.002249)