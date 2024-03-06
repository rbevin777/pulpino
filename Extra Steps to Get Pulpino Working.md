## Steps to get pulpino setup working
1. Download and install modelsim from here https://www.intel.com/content/www/us/en/software-kit/750666/modelsim-intel-fpgas-standard-edition-software-version-20-1-1.html download the run file into a temporary directory, make it executable using `chmod +x` and intstall it using `./ModelSimSetup-20.1.1.720-linux.run`

2. Once modelsim is installed locate the install directory where the executables are, e.g. vsim. This will likely be in `$HOME/intelFPGA/MODELSIM_VERSION/modelsim_ase/bin`

3. Install the relevant pre-requisites
```
sudo apt-get install git curl strace autoconf automake autotools-dev \
    curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev \
    gawk build-essential bison flex texinfo gperf libtool patchutils \
    bc zlib1g-dev libexpat-dev ninja-build cmake libglib2.0-dev \
    tcsh csh wget
```
4. Download and install python2. This is needed to run scripts in this repo.
```
sudo apt-get install -y python2
sudo apt-get install -y python-pip
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
python2 get-pip.py
pip install pyyaml
```

5. Clone and compile the riscv-gnu-toolchain
```
git clone https://github.com/pulp-platform/riscv-gnu-toolchain.git
cd riscv-gnu-toolchain
git submodule update --init --recursive

./configure --prefix=/opt/riscv --with-arch=rv32imfcxpulpv3 --with-abi=ilp32 --enable-multilib

make
```
If you get a permission error running `make`, try `sudo make`

6. add the riscv-gnu-toolchain to the path. Go to $HOME directory and add the following line to the `.profile` file.
```
export PATH="$PATH:$HOME/opt/riscv/bin"
```

Then you will need to restart your PC for these changes to take effect, or you can run the following command in the $HOME directory:
```
source .profile
```
and this will update the path with the new addition. 

Check that the riscv toolchain is installed by typing `riscv` and hit the tab key and you should get something like this returned:
```
riscv32-unknown-elf-addr2line      riscv32-unknown-elf-elfedit        riscv32-unknown-elf-gcc-ranlib     riscv32-unknown-elf-gprof          riscv32-unknown-elf-ranlib
riscv32-unknown-elf-ar             riscv32-unknown-elf-g++            riscv32-unknown-elf-gcov           riscv32-unknown-elf-ld             riscv32-unknown-elf-readelf
riscv32-unknown-elf-as             riscv32-unknown-elf-gcc            riscv32-unknown-elf-gcov-dump      riscv32-unknown-elf-ld.bfd         riscv32-unknown-elf-run
riscv32-unknown-elf-c++            riscv32-unknown-elf-gcc-9.2.0      riscv32-unknown-elf-gcov-tool      riscv32-unknown-elf-nm             riscv32-unknown-elf-size
riscv32-unknown-elf-c++filt        riscv32-unknown-elf-gcc-ar         riscv32-unknown-elf-gdb            riscv32-unknown-elf-objcopy        riscv32-unknown-elf-strings
riscv32-unknown-elf-cpp            riscv32-unknown-elf-gcc-nm         riscv32-unknown-elf-gdb-add-index  riscv32-unknown-elf-objdump        riscv32-unknown-elf-strip
```

If you don't get this output, make sure you rerun the `.profile` file using `source ~/.profile`

7. Clone out the pulpino project
```
git clone https://github.com/rbevin777/pulpino.git
cd pulpino
```

8. Update `ips_list.yml` as this fixes a broken issue from here: https://github.com/pulp-platform/pulpino/issues/399 
Replace this line
```
adv_dbg_if:
  commit: pulpinov1
```
with this line
```
adv_dbg_if:
  commit: c440ae8 # pulpinov1 branch was deleted, this is the commit where pulpinov1 was merged.
```

9. Run the `update-ips.py` script and `generate-scripts.py`
```
python2 update-ips.py
python2 generate-scripts.py
```

10. Every instance of `-m32` needs to be removed from the compiler flags. So use whatever tool you wish for that. I use the find/replace feature in VS Code.

11. Also replace every instance of `__riscv__` with `__riscv`. 

12. In the `cmake_configure` scripts in the sw folder, the `GCC_MARCH` variable needs updated to work with the compiler so replace `IMXpulpv2` with `rv32imfxpulpv3`

