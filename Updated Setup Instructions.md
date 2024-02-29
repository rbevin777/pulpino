## Steps to get pulpino setup working
1. Download and install modelsim from here https://www.intel.com/content/www/us/en/software-kit/750666/modelsim-intel-fpgas-standard-edition-software-version-20-1-1.html download the run file into a temporary directory, make it executable using `chmod u+x` and intstall it using `./ModelSimSetup-20.1.1.720-linux.run`

2. Once modelsim is installed locate the install directory where the executables are, e.g. vsim. This will likely be in `$HOME/intelFPGA/MODELSIM_VERSION/modelsim_ase/bin`

3. Install the relevant pre-requisites
```
sudo apt-get install git curl strace autoconf automake autotools-dev \
    curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev \
    gawk build-essential bison flex texinfo gperf libtool patchutils \
    bc zlib1g-dev libexpat-dev ninja-build cmake libglib2.0-dev \
    tcsh csh wget
```

4. Download and install gcc-5 and g++ using the following commands. Solution from https://askubuntu.com/questions/1109933/how-can-i-build-and-install-gcc-5-4-0-on-ubuntu-18-04
```
mkdir ~/Downloads/gcc-5.4-deb && cd ~/Downloads/gcc-5.4-deb

# Taken from the build at https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/gcc-5-base_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/libasan2_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/libmpx0_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/libcilkrts5_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/libubsan0_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/libgcc-5-dev_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/cpp-5_5.4.0-6ubuntu1~16.04.12_amd64.deb
wget https://launchpad.net/~ci-train-ppa-service/+archive/ubuntu/3807-deletedppa/+build/17785138/+files/gcc-5_5.4.0-6ubuntu1~16.04.12_amd64.deb

# These two are taken from DannyDahan's solution. A newer version from launchpad may have worked aswell
wget http://cz.archive.ubuntu.com/ubuntu/pool/main/i/isl/libisl15_0.16.1-1_amd64.deb
wget http://cz.archive.ubuntu.com/ubuntu/pool/main/m/mpfr4/libmpfr4_3.1.4-1_amd64.deb


# g++
wget http://launchpadlibrarian.net/375474836/libstdc++6_5.4.0-6ubuntu1~16.04.10_amd64.deb
wget http://launchpadlibrarian.net/375474834/libstdc++-5-dev_5.4.0-6ubuntu1~16.04.10_amd64.deb
wget http://launchpadlibrarian.net/375474751/g++-5_5.4.0-6ubuntu1~16.04.10_amd64.deb


# gcc
sudo dpkg -i gcc-5-base_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libcilkrts5_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libubsan0_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libmpx0_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libisl15_0.16.1-1_amd64.deb
sudo dpkg -i libmpfr4_3.1.4-1_amd64.deb
sudo dpkg -i cpp-5_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libasan2_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i libgcc-5-dev_5.4.0-6ubuntu1~16.04.12_amd64.deb
sudo dpkg -i gcc-5_5.4.0-6ubuntu1~16.04.12_amd64.deb

# g++
sudo dpkg -i libstdc++6_5.4.0-6ubuntu1~16.04.10_amd64.deb
sudo dpkg -i libstdc++-5-dev_5.4.0-6ubuntu1~16.04.10_amd64.deb
sudo dpkg -i g++-5_5.4.0-6ubuntu1~16.04.10_amd64.deb
```

5. Update symlinks to remove your installed version of GCC and use GCC-5.4
```
sudo rm /usr/bin/gcc
sudo rm /usr/bin/g++
sudo ln -s /usr/bin/gcc-5 /usr/bin/gcc
sudo ln -s /usr/bin/g++-5 /usr/bin/g++
```

6. Download and install python2
```
sudo apt-get install -y python2
sudo apt-get install -y python-pip
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
python2 get-pip.py
pip2 install pyyaml
```

7. Clone and compile ri5cy-gnu-toolchain
```
git clone https://github.com/pulp-platform/ri5cy_gnu_toolchain.git
cd ri5cy_gnu_toolchain
make
```

8. add the ri5cy-gnu-toolchain to the path. Go to $HOME directory and add the following line to the `.profile` file. Make sure to update the path to the path where you cloned ri5cy_gnu_toolchain.
```
export PATH="$PATH:$HOME/PATH_TO_DIR/ri5cy_gnu_toolchain/install/bin"
```

Then you will need to restart your PC for these changes to take effect, or you can run the following command in the $HOME directory:
```
source .profile
```
and this will update the path with the new addition.

9. Clone out the pulpino project, and switch to the branch `fix-adv_dbg_if-commit-issue` as this branch has a fix for this broken issue: https://github.com/pulp-platform/pulpino/issues/399 
```
git clone https://github.com/rbevin777/pulpino.git
cd pulpino
git checkout fix-adv_dbg_if-commit-issue
```

10. Run the `update-ips.py` script and `generate-scripts.py`
```
python2 update-ips.py
python2 generate-scripts.py
```

