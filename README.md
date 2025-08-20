# Installing FLASH on MacOS

We need to install dependencies. Open terminal and copy these commands there.  

## Some easy dependencies we need: 
* C compiler
* "make" utility
* Fortran compiler
* Python (you can get it from python.org if you don't have it already)
   
---

### xcode

```bash 
xcode-select --install
```

This installs the Xcode Command Line Tools package, not full xcode. We need this to get C compilers, "make" utility and git. 

---

### Homebrew
We need package manager to install tricky dependencies for FLASH. Recommended one is Homebrew (go to https://brew.sh/ for all information). 

To install it type:

```bash 
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

follow directions in terminal: you will need to enter your admin password and then it will tell you what it is going to install and ask you to click enter.

next step is to add brew in your path. It will tell you which two commands to run it will be two commands like this: 

```bash 
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
```
```bash 
eval "$(/opt/homebrew/bin/brew shellenv)"
```

This should be it. To check if we have it installed and up to date:

```bash 
brew update
```

### SVN
```bash 
brew install svn
```
---

### gfortran
go to https://github.com/fxcoudert/gfortran-for-macOS/releases, and download and install appropriate dmg for your computer. 

---

## more tricky dependencies that we need to install: 
* MPI
* HDF5
* HYPRE

we are installig them in ```/usr/local``` directory.
---
### openMPI

```bash 
brew curl https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.4.tar.gz \
    --output openmpi-4.1.4.tar.gz 
```

```bash 
brew tar -xvf openmpi-4.1.4.tar.gz
```

```bash 
brew cd openmpi-4.1.4
```

```bash 
brew ./configure --prefix=/usr/local/flash-deps/openMPI lt_prog_compiler_wl_FC='-Wl,'
```

```bash 
brew make -j all
```

```bash 
sudo make install
```

this is version 4.1.4, if you for some reason need newer version, you can download it from: https://www.open-mpi.org/ 













# note: to check if you have something you can type in terminal:
```bash
which python
```
or
```bash
python --version
```






