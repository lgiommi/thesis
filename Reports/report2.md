Second report 22/12


----------
I spent few days focussing on your (latest, master) code. Let me recap what I get, and some questions. I forked everything, so all links below are to my github - but they are hopefully self-explanatory.

---

I started checking the proto part:

https://github.com/lgiommi/TFaaS/tree/master/src/proto

I am understanding that you create tfaas.proto by hand, and I understand more or less the logic of it by studying it from the protobuffer web site [1], where I ran also the tutorial [2] to learn. Note that wherever I have to choose a language, I have chosen C++ - perhaps I should also check everything in golang but I do not feel so confident on it yet. I ran it both with --go_out and --cpp_out, and focussed on the latter. I am planning to run it also with --python_out as I see it as a viable option, so I can check how it will be in py. I am getting that in this way you create a class (e.g. Person in the tutorial, Detector and Hits mainly in your github code) that allows you to have all the methods you need afterwards, i.e. all info exchange between your client side (EDAnalyzer) and your server side (Go).

---

Then, I am checking how you use it here [3]. This [4] is the analyzer you wrote, this needs you to dump the CMS geometry like this:

> cmsRun
> /cvmfs/cms.cern.ch/slc6_amd64_gcc530/cms/cmssw/CMSSW_8_0_19/src/Fireworks/Geometry/python/dumpRecoGeometry_cfg.py
> tag=2015 out=geom2015.root

which produced an output you read back into the previous analyzer, which is far beyond a basic one from the tutorials I have checked as it prepares everthing (including c2numpy calls) for TFaaS. I noticed I had errors though when trying to run the geometry dump above [5] and this is my geometry output [6]. Next step for me is getting deeper into the analyzer.

---

Another thing I tried is to get inside the go part [7]. Go code is hard for me (yet), I uderstand the logic of the instructions to run though, but I did not manage to do so as I am forced now to run on a local resource and not lxplus where I have the cert (Daniele's at least) - that will be the next step. I am not digging further into details of the go part, so this part of your last mail: "read Go code to see how it respond back the predictions" is not done yet on my side. But I assume I can in the meantime go on and assume the server gives back predictions in some ways.

---

I’ve built also the go-server to see if everything was ok. I fixed some problems that came out and I’m stopped here because of course I have to move everything to lxplus to have the possibility to run the server using the server key and the server certificate.

---

Concerning this "what is missing is actual code which will train the model and yield actual predictions", to proceed I need input:

- what could the purpose of a first attempt be? e.g. some classification of what, a simple topology of some kind?

- as a consequence of question above, how do I get some "input" to the analyzer? can we agree a priori on a set of root files to use and a goal? (e.g. let's find on DAS some muon datasets and focus on these? would the goal here be to build a model that identify muons? or?). Perhaps this is in the analyzer in some parts I did not fully understand yet, if so can you please point me to which part this is?

- What’s the difference between
> void pixelHits( std::vector<TVector3> &pixelPoints, FWGeometry *geom, const reco::Track &t );

and 

> void SiStripClusters( std::vector<TVector3> &points, FWGeometry *geom, const reco::Track &t );

They get the geometry of the detector or they get the geometry of the hits? It seems to me the second one but if it is so, where we get the geometry of the detector? Probably it started from the line 458 of [4], right? And then in the c2numpy part we recreate as output (in “writer”) the hits.

---
[1] https://developers.google.com/protocol-buffers/
[2] https://developers.google.com/protocol-buffers/docs/cpptutorial
[3]https://github.com/lgiommi/TFaaS/tree/master/src/cpp/TFModelAnalyzer
[4]https://github.com/lgiommi/TFaaS/blob/master/src/cpp/TFModelAnalyzer/plugins/TFModelAnalyzer.cc
[5]https://drive.google.com/file/d/1eXsYvPgtG0xYUDsJ80bsvyhUkZmBpjdu/view
[6]https://drive.google.com/file/d/1Dwyp6w-EH7k9VnaXYXDMgKYo1kDkZLC0/view?usp=sharing
[7] https://github.com/lgiommi/TFaaS/tree/master/src/Go



----------
