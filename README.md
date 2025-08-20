# Installing FLASH on MacOS

We need to install dependencies. Open terminal and copy these commands there.  

# Some easy dependencies we need: 
* C compiler
* Fortran compiler
* Python (you can get it from python.org if you don't have it already)
* "make" utility
   
## step-by-step commands to get everything:
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

follow directions in terminal: 
* you will need to enter your admin password and then it will tell you what it is going to install and ask you to click enter.

* next step is to add brew in your path. It will tell you which two commands to run, they will look like: 

   ```bash 
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
   ```
   ```bash 
   eval "$(/opt/homebrew/bin/brew shellenv)"
   ```

This should be it. note: to check if you have it installed (or something else like python) you can type in terminal:
```bash
which brew
```
this will tell you path of it as well
or if you just want to check which version you have:
```bash
brew --version
```

---

### SVN
```bash 
brew install svn
```
---

### gfortran
go to https://github.com/fxcoudert/gfortran-for-macOS/releases, and download and install appropriate dmg for your computer. 

---

# more tricky dependencies that we need to install: 
* MPI
* HDF5
* HYPRE

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

---
### hdf5

cd out of openMPI directory

```bash 
cd ..
```

```bash 
git clone https://github.com/HDFGroup/hdf5.git
```

```bash 
cd hdf5
```

```bash 
git checkout hdf5-1_13_1
```

```bash 
CC=/usr/local/flash-deps/openMPI/bin/mpicc FC=/usr/local/flash-deps/openMPI/bin/mpif90 ./configure --enable-parallel --enable-fortran --prefix=/usr/local/flash-deps/hdf5
```

```bash 
make -j all
```

```bash 
sudo make install
```
---
### hypre

cd out of hdf5 directory

```bash 
cd ..
```

```bash 
git clone https://github.com/hypre-space/hypre.git
```

```bash 
cd hypre
```

```bash 
git checkout v2.24.0
```

```bash 
cd src
```

```bash 
CC=/usr/local/flash-deps/openMPI/bin/mpicc ./configure --prefix=/usr/local/flash-deps/hypre
```

```bash 
make -j
```

```bash 
sudo make install
```

That should be it. cd out of hypre directory:

```bash 
cd ..
```
You should see there 3 directories with the source for openMPI, hdf5 & hypre which can be removed if you wish. 

Check if you have all three of them in this directory: ```bash /usr/local/flash-deps```






















