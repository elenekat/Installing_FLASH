# Installing FLASH on MacOS

There is 4 steps: 
1. install dependencies for FLASH
2. download and untar code
3. change paths for dependencies
4. might need to fight with errors when compiling

# 1. Install dependencies

## 🚀 Some easy dependencies we need: 
* C compiler
* Fortran compiler
* Python (you can get it from python.org if you don't have it already)
* utilities like "make" and "git"

---

Open terminal and copy these commands there. We will need:
### ⭐ xcode

```bash 
xcode-select --install
```

This installs the Xcode Command Line Tools package. We need it to get C compilers, "make" utility and git. 

---

### ⭐ Homebrew
We need package manager to install some other dependencies for FLASH. Recommended one is Homebrew (go to https://brew.sh/ for all information). 

To install it type:

```bash 
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

and follow directions in terminal: 
* you will need to enter your admin password. It will tell you what will be installed and ask you to click enter.

* next step is to add brew in your path. It will tell you which two commands to run, they will look like: 

   ```bash 
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
   ```
   ```bash 
   eval "$(/opt/homebrew/bin/brew shellenv)"
   ```

note: to check if you have installed brew (or something else like python) you can type in terminal:
```bash
which brew
```
this will tell you path of it as well
or to just check which version you have:
```bash
brew --version
```

---

### ⭐ SVN
```bash 
brew install svn
```
---

### ⭐ gfortran
go to https://github.com/fxcoudert/gfortran-for-macOS/releases, and download and install appropriate dmg for your computer. 

---


<br>

## 🚀 more tricky dependencies we need: 
* MPI
* HDF5
* HYPRE

---
### ⭐ openMPI

```bash 
curl https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.4.tar.gz \
    --output openmpi-4.1.4.tar.gz 
```

```bash 
tar -xvf openmpi-4.1.4.tar.gz
```

```bash 
cd openmpi-4.1.4
```

```bash 
./configure --prefix=/usr/local/flash-deps/openMPI lt_prog_compiler_wl_FC='-Wl,'
```

```bash 
make -j all
```

```bash 
sudo make install
```

note: using sudo will ask you for your admin password.

<br>
cd out of openMPI directory

```bash 
cd ..
```

note: this is version 4.1.4, if you want newer version, you can download it from: https://www.open-mpi.org/ 

---
### ⭐ hdf5
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

cd out of hdf5 directory

```bash 
cd ..
```
---
### ⭐ hypre

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
note: If you wish, you can also update hdf5 and hypre to newer version. 

That should be it. cd out of hypre directory:

```bash 
cd ..
```
You should see there 3 directories with the source for openMPI, hdf5 & hypre which can be removed if you wish. 

Check if you have all three of them in this directory: 
```/usr/local/flash-deps```

<br>

# 2. Get FLASH Code

download the tar file and untar it (change name for different versions):

```bash 
tar zvf FLASH4.8.tar
```

<br> 

# 3. Changing paths for dependencies
We need to change paths to the dependencies in Makefile.h file. 

<br>

Makefile.h is in ```FLASH4.8/sites/Prototypes/Darwin``` directory.

in this file paths for dependencies should look like this:

```fortran
#HDF4_PATH =
HDF5_PATH = /usr/local/flash-deps/hdf5
#LIB_HDF5 = ${HDF5_PATH}/lib

ZLIB_PATH  =

PAPI_PATH  =
PAPI_FLAGS =

LIB_NCMPI = /usr/local/flash-deps/openMPI
MPE_PATH   =
MPI_PATH = /usr/local/flash-deps/openMPI

HYPRE_PATH = /usr/local/flash-deps/hypre
```

and also need to change this lines:

```fortran
FCOMP   = $(MPI_PATH)/bin/mpif90
CCOMP   = $(MPI_PATH)/bin/mpicc
CPPCOMP = $(MPI_PATH)/bin/mpiCC
LINK    = $(MPI_PATH)/bin/mpif90
```

Try running some test problem now. 

# 4. Fighting Errors

There might be some errors when trying to compile. 

One of errors I had: ```gr_hypreF90CAdapters.c:9:10: fatal error: '_hypre_parcsr_ls.h' file not found```
<br>
To fix it add this to the Makefile.h:
```fortran
LIB_LAPACK = -llapack
```
Makefile.h should look like this:
``` fortran
LIB_LAPACK = -llapack
      
LIB_OPT   =
LIB_DEBUG =
LIB_TEST  =
      
#LIB_HDF5  = -L${HDF5_PATH}/lib -lhdf5 /usr/lib64/libz.a
LIB_HDF5  = -L${HDF5_PATH}/lib -lhdf5 -DH5_USE_16_API
      
LIB_PAPI  =
LIB_MATH  =
      
LIB_MPI   = 
#LIB_NCMPI = -L $(NCMPI_PATH)/lib -lpnetcdf
LIB_MPE   =
      
LIB_HYPRE = -L${HYPRE_PATH}/lib -lHYPRE
```

     
also check that flags look like this: 
```fortran
FFLAGS_HYPRE = -I${HYPRE_PATH}/include
CFLAGS_HYPRE = -I${HYPRE_PATH}/include
```
Some other errors I had were fixed after using:

```bash
make clean
```
For example, error I had:
```Error: Cannot open module file ‘cond_interface.mod’```
which was fixed after using make clean.

Some errors are also answered in [FLASH-USERS] emails.
Error I had:
```Error: Reading module 'iso_c_binding.mod' at line 1 column 1: Unexpected EOF```
to fix it in Makefile.h comment out line “iso_c_binding.mod".
