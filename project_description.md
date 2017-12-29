The proto file [1] is just the description of data exchange format we're going to use between server and CMSSW. After we compile it we will have all the methods that allow us to do info exchange between the client side (EDAnalyzer) and the Go-server side. The tfass.proto describe how data will be (un)packed. The different outputs (c++[2], py[3], go[4]) are auto-generated classes to (de)serialize the data.

In the configuration script of the analyzer [5] we pass the geometry file of the detector (line 31), we provide input ROOT files (line 23) and the Go server url (line 32). Particularly in the analyzer [6] through the pixelHits function we extract pixel detector hits from given track using given geometry while through SiStripClusters function we extract silicon detector hits.

In the go script [7] the function [8] receives an HTTP POST request from a client (in our case it would be CMSSW process). Then hits are unpacked, predictions are created and finally they are sent back to client.

 

[1] https://github.com/lgiommi/TFaaS/blob/master/src/proto/tfaas.proto

[2] https://github.com/lgiommi/TFaaS/blob/master/src/cpp/TFModelAnalyzer/plugins/tfaas.pb.cc

[3] https://drive.google.com/file/d/11W4iS4XiA49olwHGcJT-ba4I8mdKye3X/view?usp=sharing

[4] https://github.com/lgiommi/TFaaS/blob/master/src/Go/tfaaspb/tfaas.pb.go

[5] https://github.com/vkuznet/TFaaS/blob/master/src/cpp/TFModelAnalyzer/python/ConfFile_cfg.py

[6] https://github.com/vkuznet/TFaaS/blob/master/src/cpp/TFModelAnalyzer/plugins/TFModelAnalyzer.cc

[7] https://github.com/lgiommi/TFaaS/blob/master/src/Go/tfaas.go
[8] https://github.com/lgiommi/TFaaS/blob/63f3e278d2a3f907acab0ff5165ddb80083b19a7/src/Go/tfaas.go#L214