# 16S Intermediate Bioinformatics Training -  Software setup and testing

1. [Install Singularity on Ubuntu](#1.-install-singularity-on-ubuntu)
2. [Install Nextflow](#2.-install-nextflow)
3. Download the Rstudio Singularity image
4. Running RStudio on a cluster
5. Running Rstudio on a server
6. Running the DADA2 Nextflow pipeline on test data

## 1. Install Singularity on Ubuntu

Singularity can be installed system wide. The most up to date instructions are found here.

Lets install Singularity v3.1.1

```
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
```

Looks OK.

## 2. Install Nextflow
Nextflow needs to be installed in each user’s home directory (permissions assigned to the user) and be available on the users path.

Requirements: Java 1.8 or later is required. Also see Nextflow setup instructions here.

```
$ mkdir /home/user/nextflow
$ cd  /home/user/nextflow
$ curl -s https://get.nextflow.io | bash
$ echo “export PATH=$PATH:/home/user/nextflow/” >> /home/user/.bashrc
$ sudo su user
$ nextflow -v
nextflow version 19.07.0.5106
```

Nextflow version 19.07 is fine.

## 3. Download the Rstudio Singularity image
Download the Rstudio Singularity image here.

## 4. Running RStudio on a cluster
This setup is focus on running a RStudio Singularity container on a SLURM cluster. For PBS/Torque or SGE clusters the only difference would be in the way that you would submit your interactive jobs. 

Firstly one should configure ssh in such a way that it is simple to connect to a worker node once a job is running. The easiest way it to add the following to your `local ~/.ssh/config` file:

```
Host *.ilifu.ac.za
    User USERNAME
    ForwardAgent yes

Host slwrk-*
    Hostname %h
    User USERNAME
    StrictHostKeyChecking no
    ProxyCommand ssh headnode nc %h 22
```

One should substitute in your headnode, workernode and USERNAME settings in the above script.

Next is the process of starting an interactive job and launching RStudio. To begin start an interactive job – below is an example of launching a single node / 1 core job with 8Gb of ram:

```
USERNAME@slurm-login:~$ srun --nodes=1 --ntasks 1 --mem=8g --pty bash
USERNAME@slwrk-103:~$
```

Once the interactive session has begun on a specific node (in this case slwrk-103), RStudio can be launched as follows:

```
USERNAME@slwrk-103:~$ RSTUDIO_PASSWORD='Make your own secure password here' /cbio/images/bionic-R3.6.1-RStudio1.2.1335-bio.simg
```

Running rserver on port 37543

This will launch an RStudio server listening on a random free port (in this case 37543). Now one needs to port-forward from your local machine to the host machine. One connects to the appropriate node by running:

```
$ ssh slwrk-103 -L8082:localhost:37543
```

On your local machine. Specifically what this does is forward traffic on your local machine's port 8082 to the worker node's port 37543 (and it knows how to connect to `slwrk-103` by using the `.ssh/config` settings above). One may use any free local port – ssh will complain if you choose something that is not free with an error message approximating:

```
bind [127.0.0.1]:8000: Address already in use
channel_setup_fwd_listener_tcpip: cannot listen to port: 8000
```
Finally in your browser you can connect to `http://localhost:8082` and you can login with your `USERNAME` and the `RSTUDIO_PASSWORD` which you set.
