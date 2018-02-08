In this week I focused into the elaboration of the written part of the thesis and I finished the first two chapters (about physics at LHC and CMS computing model).
About the project, thank you first of all about your suggestion. I’ve tried your code to read the events, running

    reader.py --fin=/opt/cms/data/nano-RelValTTBar.root --verbose=1 --fout=specs.json --nevts=5

I’ve tried to access your input file using
/afs/cern.ch/user/v/valya/public/nano-RelValTTBar.root
but seems that something is wrong [1]. It’s strange because iterate has to keep the argument entrysteps.
Anyway I continue to read your code.

[1]

    ./reader.py --fin=/afs/cern.ch/user/v/valya/public/nano-RelValTTBar.root --verbose=1 --fout=specs.json --nevts=5

    Traceback (most recent call last):
      File "./reader.py", line 472, in <module>
        main()
      File "./reader.py", line 465, in main
        nan=nan, chunk_size=chunk_size, specs=specs, verbose=verbose)
      File "./reader.py", line 178, in __init__
        self.init()
      File "./reader.py", line 242, in init
        self.read_chunk(nevts, set_branches=set_branches, set_min_max=set_min_max)
      File "./reader.py", line 198, in read_chunk
        self.branches = self.tree.iterate(entrysteps=nevts, keycache=self.cache).next()
    TypeError: iterate() got an unexpected keyword argument 'entrysteps'