I’ve tried to restart with your original root file (/afs/cern.ch/user/v/valya/public/nano-RelValTTBar.root) and your original reader11.py and I have this [1] (and why the selected branches don’t match with the branches that I parse as argument?) and if I don’t select any branch I don’t have errors, as we had some days ago (also you). If I put a print in read_chunk in line 487 (I read “event” branch) I can see that for each step I have 1000 elements and (if I delete —branches option) I don’t have any error. So it seems that everything is ok. But if I create a new file made by 18000 events (I duplicate the events of the /afs/cern.ch/user/v/valya/public/nano-RelValTTBar.root root file) that you can find here /afs/cern.ch/work/b/bonacor/public/double.root and I run the same (that is [2]) I can see that in the last two steps in the event branch there is no data and I have index out of bounds error [3]. So it seems that something is wrong in uproot, maybe some problem of branch reading or because the memory that we are using is finished.

Therefore I decided to write a simple code as shown in the page that you linked me, that is [4] using again double.root and if you open the csv you can see that in the last two iteration, the iterator isn’t filled. So something is wrong in iterator.
Another thing, how can I access each single element of the array (each entry for the event branch)? I’ve seen that iterator is a generator but I don’t have understood how to manipulate it. In my script I obtain 18 blocks containing an array but I can’t access to each one as a list or a dictionary.

So, after have seen these problems, is it better to write an email to Jim Pivarski? I can use my last script as an example that can show the error. I thought to write this, tell me if it can be right:

“Hi, I’m using uproot to read some root files and I have some problem when I use uproot.iterate function. In fact if I run this [4], the last two block of the iterator are empty and the filling stops in the 16th iteration (so it seems that there are less than 16000 events). This is not correct because the root file contain 18000 events for branch “event”.”

Do you want to add something? Do you want that you contact directly Jim?

Anyway, with Daniele we are ready to do some ML with the muon root file (/afs/cern.ch/work/b/bonacor/public/MuonTree.root), without reading the events with uproot for the moment (and so reader.py), waiting to solve this problem. Then also we are going to start the classification use-case in the next days.



[1]

    [bonacor@lxplus023 prova]$ python reader11.py --fin=/afs/cern.ch/user/v/valya/public/nano-RelValTTBar.root --branch=Events --branches=nElectron,Electron_dxy,Electron_eta,Electron_mass,Electron_phi,Electron_pt --verbose=1 --fout=Valentin.json --nevts=5
    # 1000 entries, 684 branches, 4.36264228821 MB, 0.783760070801 sec, 5.56629822153 MB/sec, 11.4831060363 kHz
    # 1000 entries, 684 branches, 4.33418655396 MB, 0.24884390831 sec, 17.4172901535 MB/sec, 36.1672506316 kHz
    # 1000 entries, 684 branches, 4.33791255951 MB, 0.251308917999 sec, 17.2612758594 MB/sec, 35.8124975096 kHz
    # 1000 entries, 684 branches, 4.30505752563 MB, 0.324187994003 sec, 13.2795094367 MB/sec, 27.7616696685 kHz
    # 1000 entries, 684 branches, 4.34808540344 MB, 0.264184951782 sec, 16.458490062 MB/sec, 34.0670425749 kHz
    # 1000 entries, 684 branches, 4.33607959747 MB, 0.262027978897 sec, 16.5481549555 MB/sec, 34.347477082 kHz
    # 1000 entries, 684 branches, 4.32481479645 MB, 0.285330057144 sec, 15.1572352374 MB/sec, 31.5424182439 kHz
    # 1000 entries, 684 branches, 4.33872890472 MB, 0.278485059738 sec, 15.5797546511 MB/sec, 32.3177121547 kHz
    # 1000 entries, 684 branches, 4.34572601318 MB, 0.261590957642 sec, 16.6126767239 MB/sec, 34.404858949 kHz
    First pass: 9000 events, 32.0141448975 sec, shape (2650,) 560 branches: flat 121 jagged
    VMEM used: 800.886784 (MB) SWAP used: 3.969024 (MB)
    Select branches ...
    Electron_dxy
    Electron_eta
    Electron_mass
    Electron_phi
    Electron_pt
    Electron_pterr
    nElectron
    # 1000 entries, 10 branches, 0.088996887207 MB, 0.0503659248352 sec, 1.76700591716 MB/sec, 178.69224142 kHz
    Traceback (most recent call last):
      File "reader11.py", line 1004, in <module>
        main()
      File "reader11.py", line 984, in main
        parse(reader, nevts, verbose, fout, hists)
      File "reader11.py", line 928, in parse
        reader.next(verbose=verbose)
      File "reader11.py", line 595, in next
        return self.next_vector(verbose)
      File "reader11.py", line 771, in next_vector
        idx = pos+jdx
    UnboundLocalError: local variable 'pos' referenced before assignment

[2]

    python reader11.py --fin=double.root --branch=Events --verbose=1 --fout=Valentin.json --nevts=5

[3]

    # 1000 entries, 684 branches, 3.68633270264 MB, 0.244194984436 sec, 15.095857563 MB/sec, 73.7115876543 kHz
    []
    # 1000 entries, 684 branches, 3.23728370667 MB, 0.193404912949 sec, 16.7383736913 MB/sec, 93.0689904697 kHz
    []
    # 1000 entries, 684 branches, 2.065574646 MB, 0.151855945587 sec, 13.6021980437 MB/sec, 118.533389854 kHz
    First pass: 18000 events, 57.1144351959 sec, shape (2650,) 560 branches: flat 121 jagged
    VMEM used: 1040.494592 (MB) SWAP used: -0.151552 (MB)
    [ 502  502  503  503  504  504  501  501  508  508  505  505  507  507  506
      506  509  509  510  510  513  513  512  512  514  514  515  515  518  518
      516  516  519  519  517  517  511  511  521  521  520  520  523  523  522
      522  525  525  524  524  527  527  526  526  528  528  529  529  531  531
      530  530  532  532  533  533  534  534  535  535  536  536  538  538  541
      541  537  537  539  539  540  540  543  543  547  547  542  542  544  544
      546  546  545  545  549  549  548  548  550  550 6701 6701 6702 6702 6703
     6703 6704 6704 6705 6705 6706 6706 6708 6708 6707 6707 6712 6712 6710 6710
     6713 6713 6709 6709 6717 6717 6714 6714 6711 6711 6715 6715 6718 6718 6720
     6720 6716 6716 6719 6719 6721 6721 6724 6724 6723 6723 6722 6722 6727 6727
     6725 6725 6726 6726 6728 6728 6732 6732 6730 6730 6731 6731 6729 6729 6733
     6733 6734 6734 6738 6738 6736 6736 6735 6735 6739 6739 6737 6737 6740 6740
     6741 6741 6744 6744 6742 6742 6743 6743 6745 6745 6746 6746 6748 6748 6750
     6750 6747 6747 6749 6749 7201 7201 7203 7203 7204 7204 7205 7205 7206 7206
     7202 7202 7209 7209 7208 7208 7211 7211 7207 7207 7213 7213 7210 7210 7214
     7214 7212 7212 7215 7215 7216 7216 7217 7217 7222 7222 7219 7219 7221 7221
     7218 7218 7224 7224 7223 7223 7220 7220 7226 7226 7225 7225 7229 7229 7227
     7227 7231 7231 7228 7228 7232 7232 7230 7230 7234 7234 7235 7235 7233 7233
     7236 7236 7240 7240 7237 7237 7238 7238 7239 7239 7241 7241 7242 7242 7243
     7243 7244 7244 7246 7246 7245 7245 7247 7247 7250 7250 7249 7249 7248 7248
     8504 8504 8502 8502 8501 8501 8503 8503 8507 8507 8505 8505 8506 8506 8508
     8508 8510 8510 8509 8509 8511 8511 8512 8512 8513 8513 8516 8516 8514 8514
     8517 8517 8520 8520 8515 8515 8519 8519 8521 8521 8518 8518 8522 8522 8525
     8525 8523 8523 8526 8526 8528 8528 8524 8524 8530 8530 8533 8533 8532 8532
     8527 8527 8529 8529 8531 8531 8535 8535 8534 8534 8537 8537 8539 8539 8536
     8536 8540 8540 8538 8538 8541 8541 8542 8542 8543 8543 8544 8544 8545 8545
     8546 8546 8547 8547 8549 8549 8550 8550 8548 8548 1603 1603 1602 1602 1604
     1604 1601 1601 1606 1606 1605 1605 1608 1608 1609 1609 1610 1610 1611 1611
     1607 1607 1614 1614 1613 1613 1612 1612 1616 1616 1617 1617 1615 1615 1619
     1619 1621 1621 1618 1618 1620 1620 1625 1625 1623 1623 1624 1624 1622 1622
     1629 1629 1627 1627 1626 1626 1631 1631 1630 1630 1628 1628 1632 1632 1633
     1633 1634 1634 1635 1635 1636 1636 1638 1638 1637 1637 1640 1640 1641 1641
     1639 1639 1644 1644 1643 1643 1642 1642 1646 1646 1645 1645 1648 1648 1647
     1647 1649 1649 1650 1650 2001 2001 2005 2005 2004 2004 2002 2002 2006 2006
     2003 2003 2009 2009 2008 2008 2010 2010 2011 2011 2014 2014 2012 2012 2013
     2013 2007 2007 2015 2015 2016 2016 2019 2019 2020 2020 2017 2017 2018 2018
     2021 2021 2023 2023 2022 2022 2026 2026 2024 2024 2025 2025 2027 2027 2029
     2029 2028 2028 2031 2031 2032 2032 2030 2030 2034 2034 2035 2035 2036 2036
     2033 2033 2038 2038 2041 2041 2039 2039 2037 2037 2044 2044 2040 2040 2042
     2042 2043 2043 2049 2049 2046 2046 2048 2048 2045 2045 2050 2050 2047 2047
     2051 2051 2054 2054 2053 2053 2052 2052 2056 2056 2057 2057 2055 2055 2061
     2061 2058 2058 2060 2060 2063 2063 2062 2062 2059 2059 2067 2067 2064 2064
     2066 2066 2065 2065 2068 2068 2071 2071 2072 2072 2069 2069 2070 2070 2075
     2075 2073 2073 2074 2074 2079 2079 2076 2076 2077 2077 2081 2081 2086 2086
     2078 2078 2080 2080 2082 2082 2084 2084 2083 2083 2090 2090 2089 2089 2085
     2085 2087 2087 2088 2088 2091 2091 2092 2092 2095 2095 2096 2096 2093 2093
     2097 2097 2094 2094 2100 2100 2098 2098 2099 2099 2252 2252 2251 2251 2253
     2253 2254 2254 2256 2256 2260 2260 2255 2255 2258 2258 2257 2257 2263 2263
     2259 2259 2262 2262 2266 2266 2264 2264 2261 2261 2267 2267 2265 2265 2270
     2270 2269 2269 2268 2268 2271 2271 2273 2273 2272 2272 2274 2274 2275 2275
     2276 2276 2277 2277 2279 2279 2280 2280 2284 2284 2282 2282 2278 2278 2281
     2281 2283 2283 2287 2287 2286 2286 2291 2291 2285 2285 2289 2289 2288 2288
     2290 2290 2293 2293 2294 2294 2292 2292 2296 2296 2295 2295 2298 2298 2300
     2300 2299 2299 2297 2297 2304 2304 2303 2303 2301 2301 2302 2302 2309 2309
     2305 2305 2308 2308 2307 2307 2311 2311 2306 2306 2312 2312 2310 2310 2313
     2313 2315 2315 2316 2316 2314 2314 2317 2317 2320 2320 2318 2318 2323 2323
     2321 2321 2319 2319 2327 2327 2324 2324 2322 2322 2325 2325 2330 2330 2326
     2326 2331 2331 2328 2328 2329 2329 2332 2332 2334 2334 2335 2335 2338 2338
     2333 2333 2336 2336 2339 2339 2337 2337 2341 2341 2342 2342 2344 2344 2340
     2340 2346 2346 2343 2343 2347 2347 2348 2348 2345 2345 2350 2350 2349 2349
     7803 7803 7801 7801 7802 7802 7804 7804 7805 7805 7808 7808 7806 7806 7807
     7807 7809 7809 7812 7812 7810 7810 7811 7811 7813 7813 7815 7815 7816 7816
     7819 7819 7814 7814 7818 7818 7821 7821 7823 7823 7817 7817 7822 7822 7820
     7820 7824 7824 7826 7826 7827 7827 7825 7825 7830 7830 7829 7829 7832 7832
     7831 7831 7828 7828 7834 7834 7836 7836 7833 7833 7837 7837 7838 7838 7840
     7840 7835 7835 7841 7841 7842 7842 7844 7844 7843 7843 7839 7839 7845 7845
     7847 7847 7846 7846 7848 7848 7849 7849 7850 7850]
    # 1000 entries, 684 branches, 3.87832641602 MB, 0.348171949387 sec, 11.1391122198 MB/sec, 51.6985932718 kHz
    Traceback (most recent call last):
      File "reader11.py", line 1004, in <module>
        main()
      File "reader11.py", line 984, in main
        parse(reader, nevts, verbose, fout, hists)
      File "reader11.py", line 928, in parse
        reader.next(verbose=verbose)
      File "reader11.py", line 595, in next
        return self.next_vector(verbose)
      File "reader11.py", line 749, in next_vector
        rec[key] = self.fetch_data(key)[self.chunk_idx]
    IndexError: index out of bounds

[4]

    from math import *
    import numpy
    import uproot
    
    outputfile = open(“example.csv", "w")
    iterator = uproot.iterate(
        "/afs/cern.ch/work/b/bonacor/public/double.root", "Events", 1000,
         branches=["event"], outputtype=dict)
    for block in iterator:
        outputfile.write("%s \n" %block)
    outputfile.close()

