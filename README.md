# learn_caffe
# How I end up not learning caffe

## Installation

### Method 1
Here are official instructions to install on Mac:
http://caffe.berkeleyvision.org/install_osx.html

Steps I did:



    brew install --build-from-source --with-python -vd protobuf
    brew install --build-from-source -vd boost boost-python
    brew install protobuf boost
    git clone https://github.com/BVLC/caffe.git
    cd caffe
    cp Makefile.config.example Makefile.config
    make all -j8


This failed with BLAS includes not found error (fatal error: 'cblas.h' file not found).

I installed openblas (brew install openblas) which took ages, edited Makefile.config, uncomment lines about brew BLAS, change to blas to 'open'

This failed with vecLib linking error.

### Method 2

http://hoondy.com/2015/04/03/how-to-install-caffe-on-mac-os-x-10-10-for-dummies-like-me/
http://playittodeath.ru/how-to-install-caffe-on-mac-os-x-yosemite-10-10-4/
Required Anaconda, which I didn't want to install or Intell MKL that I didn't want to pay for.

### Method 3

I decided to investigate what might be the source of the problem in Method 1 by reading about vecLib and accelerate.
I found that Accelerate framework files in /System/Library/Frameworks do not have all the includes for BLAS.
I found BLAS includes in xcode.app ... frameworks/Accelerate. I decided to mv this framework instead of one in System/Library.
This appeared to be a terrible idea. I ended up with non-functional mac - no sudo, no chrome, doesn't boot after reboot.
I found a solution here  http://unix.stackexchange.com/questions/77193/sudo-unable-to-initialize-pam-no-such-file-or-directory
which somehow didn't work out ("hard drive is not writable" or smth.) I turned off my hard drive encryption and performed few checks in
recovery mode and tried to restore original Accelerate framework. This all took me more than 2 hours and some nerves, but I recovered my mac finally.

After investigating what might be the source of the problem in Method 1, I discovered that there is a bug in Makefile
of caffe that makes it fail to find Accelerate and vecLib frameworks on Mac. It relies on 
pkgutil --pkg-info=com.apple.pkg.CLTools_Executables to find out xcode version. This command is not supposed to work
on Yosemite according to http://stackoverflow.com/questions/15371925/how-to-check-if-command-line-tools-is-installed. 
I just edited the Makefile to evaluate this command to True and recompiled caffe. After that I succeed compiling Caffe binaries.
However after I compiled python bindings, I got segfault 11 during caffe import.
This is a common issue probably related to boost, brew, system python, opencv etc.

#### I decided to just give up for a time being and go through tutorials of caffe visually and then maybe try again if I like it.


## Design and features

Caffe relies on configuration a lot. You end up configuring layers and datainputs in protobuf. There are some examples where you can generate
configuration files from python pretending that you have layer objects but then you dump this to file and execute via other means.
Python seemed to be a second grade citizen in caffe. 

All layers were written in cpp or cuda. You can write a layer in python but its not going to be fast like in Theano.
It seems that you have to implement stuff in cpp in you want something custom.

Caffe is heavily vision oriented: Caffe doesn't have RBMs implemented, 
LSTM is coming but was not done yet (https://github.com/BVLC/caffe/pull/2033).

What I did like a lot is the idea of Zoo of models - you can share a model in a standard way.

Benchmarks show that caffe is not the fastest tool in the world (https://github.com/soumith/convnet-benchmarks).
It relies on CuDNN for conv nets anyway like other tools.


## Result

As a result I was not exited at all by design or features. Full day of installation without success didn't help either.
