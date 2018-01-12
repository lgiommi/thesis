Hi Valentin.
I’ve spent much time in using uproot through reader.py but because of our need to use any root file (for two physics use-case I’m focusing on), I’m moved to try root_numpy. At the moment this is a cleaner way (with less lines of code) to have some readable file that we can use for a machine learning model. When this part will be in a “usable” condition, I’ll come back more into reader.py. Just to give you an overview, these are some lines of code [1]. When I’ll have a cleaner script, I’ll put into my github repository.

Anyway, about reader.py, I modified your code in order to use it also for my root files. Using the file
*/afs/cern.ch/work/b/bonacor/public/MuonTree.root*
I've change (line 348)

    exclude_branches=None, identifier=('run', 'event', 'luminosityBlock'),

into

    exclude_branches=None, identifier=('runNumber', 'eventNumber', 'luminosityBlockNumber'),

I’ve declared below (always in the constructor)

    self.selected_branches=selected_branches

Then the read_chunck function become [2] because in self.tree.iterate I want to iterate only into the branches that I’ve parsed as argument (—branches). Before here each branch would be read. In this way if I do

    ./reader_v7.py --fin=/afs/cern.ch/work/b/bonacor/LUCA/MuonTree.root
    --branch=MuonPogTree/MUONPOGTREE --branches=l1muons.pt,nVtx,orbit,genParticles.eta --verbose=1 --fout=specs.json --nevts=5

I obtain this [3] json file. Then I successfully run

> ./reader_v7.py --fin=/afs/cern.ch/work/b/bonacor/LUCA/MuonTree.root
> --branch=MuonPogTree/MUONPOGTREE --branches=l1muons.pt,nVtx,orbit,genParticles.eta --verbose=1 --specs=specs.json --nevts=1000

Then I’ve tried to use reader.py for another root file
*/afs/cern.ch/work/b/bonacor/public/small10kevts.root*
I’ve changed line 348

    exclude_branches=None, identifier=('runNo', 'evtNo', 'lumi'),

running

> ./reader_v7.py --fin=small10kevts.root --branch=events
> --branches=nJets,rho,met,yJJ,ptJJ --verbose=1 --fout=specs_top.json --nevts=5

but I have this error [4] and for the moment I don’t have success in solve this issue.



[1]

> from root_numpy import tree2array, array2root from root_numpy import
> testdata from numpy import genfromtxt, savetxt
> 
> inputfile = "MuonTree.root" rootfile = ROOT.TFile(inputfile) my_tree =
> rootfile.Get('MuonPogTree/MUONPOGTREE’)
> 
> print([b.GetName() for b in my_tree.GetListOfBranches()])
> 
> print("--- TTree2NumPy array ONLY FOR SELECTED BRANCHES") my_array =
> tree2array(my_tree,
>     branches=['dtPrimitives.phi','dtPrimitives.phiB','dtPrimitives.id_r'],
>     start=0, stop=2, step=1) print my_array print("--- Save numpy array into a csv") savetxt("output2.csv", my_array, delimiter=",")
> my_data = genfromtxt('output2.csv', delimiter=',') print my_data


[2]
    

> def read_chunk(self, nevts, set_branches=False, set_min_max=False):
>         "Reach chunk of events and determine min/max values as well as load branch values"
>         # read some portion of the data to determine branches
>         startTime = time.time()
>         identifier_list=[]
>         for id_loop in self.identifier:
>             identifier_list.append(id_loop)
>         if not self.gen:
>             self.gen = self.tree.iterate(branches=self.selected_branches+identifier_list,
> entrysteps=nevts, keycache=self.cache)
>             #self.gen = self.tree.iterate(entrysteps=nevts, keycache=self.cache)
>         self.branches = {} # start with fresh dict
>         try:
>             self.branches = self.gen.next()
>         except StopIteration:
>             self.gen = self.tree.iterate(branches=self.selected_branches+identifier_list,
> entrysteps=nevts, keycache=self.cache)
>             #self.gen = self.tree.iterate(entrysteps=nevts, keycache=self.cache)
>             self.branches = self.gen.next()

[3]

    {"maxv": {"eventNumber": 2000.0, "runNumber": 1.0, "luminosityBlockNumber": 1.0, "orbit": 0.0, "genParticles.eta": 0.89959681034088135, "nVtx": -1.0, "l1muons.pt": 200.0}, "fkeys": ["nVtx", "orbit"], "jkeys": ["genParticles.eta", "l1muons.pt"], "nans": {"eventNumber": -0.00050025012506253123, "runNumber": 0, "luminosityBlockNumber": 0, "orbit": 0, "genParticles.eta": 0.50005574910398964, "nVtx": 0, "l1muons.pt": -0.012658227848101266}, "jdim": {"genParticles.eta": 1, "l1muons.pt": 2}, "minv": {"eventNumber": 1.0, "runNumber": 1.0, "luminosityBlockNumber": 1.0, "orbit": 0.0, "genParticles.eta": -0.89979743957519531, "nVtx": -1.0, "l1muons.pt": 2.5}}


[4]

> ./reader_v7.py --fin=small10kevts.root --branch=events
> --branches=nJets,rho,met,yJJ,ptJJ --verbose=1 --fout=specs_top.json --nevts=5
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz First pass: 10000 events, 0.0168519020081 sec, shape (5,) 5
> branches: flat 0 jagged VMEM used: 6.823936 (MB) SWAP used: 0.0 (MB)
> ['nJets', 'rho', 'met', 'yJJ', 'ptJJ'] Select branches ... met nJets
> ptJJ rho yJJ ['nJets', 'rho', 'met', 'yJJ', 'ptJJ']
>  1000 entries, 1000 1000branches, 1000 MB, 1000 sec, 1000 MB/sec, 1000 kHz
>  idx=0 event=0 shape=0 proc.time=0 Traceback (most recent call last):   File "./reader_v7.py", line 954, in <module>
>     main()   File "./reader_v7.py", line 934, in main
>     parse(reader, nevts, verbose, fout, hists)   File "./reader_v7.py", line 886, in parse
>     reader.next(verbose=verbose)   File "./reader_v7.py", line 574, in next
>     return self.next_vector(verbose)   File "./reader_v7.py", line 760, in next_vector
>     arrIdx = [random.randint(0, len(self.jagged_keys())-1) for _ in range(3)]   File "/usr/lib64/python2.6/random.py", line 228, in
> randint
>     return self.randrange(a, b+1)   File "/usr/lib64/python2.6/random.py", line 204, in randrange
>     raise ValueError, "empty range for randrange() (%d,%d, %d)" % (istart, istop, width) ValueError: empty range for randrange() (0,0,
> 0)