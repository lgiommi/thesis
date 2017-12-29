I discussed with Daniele. We will try to give a try to both attempts (i.e. event classification and S/B discrimination for example) and gain asap some feeling of what might be more promising, if not both. Over last few days, we spent some time on the hadronic top analysis side (that Daniele told me he briefly discussed with you few weeks ago). The description of what could be done is here:

https://docs.google.com/document/d/1xAde4A7C7aejoQO-F_8vpUzf27QMHUAUfn0f96CgAsg

(I do not think anything of this is actually private, but since it contains some details of the analysis and we did not check what has been published and what not, please let's not propagate this URL aside from this thread among us 3)

Daniele worked on setting the gdoc above up with me, by discussing the details with the analysis team - he was part of it years ago.. (many details in the gdoc still prelim, but it gives a feeling of our possible goals on that use case). 

They use private rootuples. No *AOD, all that is at the source, and they do not control the code that produces the final rootuples they use. This is true for so many PAGs. So, we started to write a small "TTreeDumper" in cpp to get data in "easier to handle" format from any ROOT TTree - which most analysis teams use anyway - and transform it into something that we should be able to handle (e.g. txt or csv for what matters, as we will probably dump everything to use pandas data frames at the end). The approach in C++, not done yet, but would look as simple as [1]. Other approaches can be tried (e.g. the TTreeReader recently available in root). Perhaps, it would be better for this use case to use python directly: we are just starting now to have a look at how to use pyroot for this, the existing documentation is relatively poor. Updates on this will come.

I will put the summary of the work - as you asked in your mail - in my github.

Ciao, Luca

`[1]
void TTreeDumper( TString samplename ) 
{ 
  TFile *inputfile  = new TFile( "flatTree_" + samplename + ".root", "READ" ); 
  TTree *evt = (TTree*)inputfile->Get( "boosted/events" ); 

  vector<bool> *mytriggerBit = 0; 
  evt->SetBranchAddress( "triggerBit", &mytriggerBit ); 

  int mynJets = 0; 
  evt->SetBranchAddress( "nJets", &mynJets ); 

  // add here more variables from your TTree of interest

  // loop 
  Long64_t nevents = evt->GetEntries(); 
  for ( Long64_t ievent = 0; ievent < nevents; ++ievent ) { 
    if ( !(ievent % 10000 ) ) std::cout << "evt counter  =  " << ievent << std::endl; 
    evt->GetEntry( ievent ); 

  // add here loops/selections as needed

  // dump out part goes here

  } 

}
`