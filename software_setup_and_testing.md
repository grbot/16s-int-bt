# 16S Intermediate Bioinformatics Training -  Software setup and testing

1. Install Singularity on Ubuntu
2. Install Nextflow
3. Download the Rstudio Singularity image
4. Running RStudio on a cluster
5. Running Rstudio on a server
6. Running the DADA2 Nextflow pipeline on test data

## 1. Install Singularity on Ubuntu

Singularity can be installed system wide. The most up to date instructions are found here.

Lets install Singularity v3.1.1

$ sudo apt-get update && sudo apt-get install -y \
    build-essential \
    libssl-dev \
    uuid-dev \
    libgpgme11-dev \
    squashfs-tools \
    libseccomp-dev \
    pkg-config

$ export VERSION=1.12.5 OS=linux ARCH=amd64 && \
    wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz && \
    sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz && \
    rm go$VERSION.$OS-$ARCH.tar.gz

$ echo 'export GOPATH=${HOME}/go' >> ~/.bashrc && \
    echo 'export PATH=/usr/local/go/bin:${PATH}:${GOPATH}/bin' >> ~/.bashrc && \
    source ~/.bashrc

$ go get -u github.com/golang/dep/cmd/dep

$ go get -d github.com/sylabs/singularity

$ export VERSION=v3.1.1 # or another tag or branch if you like && \
    cd $GOPATH/src/github.com/sylabs/singularity && \
    git fetch && \
    git checkout $VERSION

$ ./mconfig && \
    make -C ./builddir && \
    sudo make -C ./builddir install
 
$ singularity version
3.1.1

Looks OK.
