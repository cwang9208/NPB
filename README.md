# NAS Parallel Benchmarks

## NAS Parallel Benchmarks overview
The NAS Parallel Benchmarks (NPB) are a small set of programs designed to help evaluate the performance of parallel supercomputers.

### Benchmark Specifications
The original eight benchmarks specified in NPB 1 mimic the computation and data movement in CFD applications:
- five kernels
 - IS - Integer Sort, random memory access
 - EP - Embarrassingly Parallel
 - CG - Conjugate Gradient, irregular memory access and communication
 - MG - Multi-Grid on a sequence of meshes, long- and short-distance communication, memory intensive
 - FT - discrete 3D fast Fourier Transform, all-to-all communication
- three pseudo applications
 - BT - Block Tri-diagonal solver
 - SP - Scalar Penta-diagonal solver
 - LU - Lower-Upper Gauss-Seidel solver

### Benchmark Classes
- Class S: small for quick test purposes
- Class W: workstation size (a 90's workstation; now likely too small)
- Classes A, B, C: standard test problems; ~4X size increase going from one class to the next
- Classes D, E, F: large test problems; ~16X size increase from each of the previous classes

### Pre-requisites
```
sudo apt-get install mpich2
```

## MPI version
MPI: Spawn processes across physically-different nodes in a cluster environment
### compilation

Before compilation, one needs to check the configuration file 'make.def' in the config directory and modify the file if necessary. If it does not (yet) exist, copy 'make.def.template' or one of the sample files in the NAS.samples subdirectory to 'make.def' and edit the content for site- and machine-specific data.
```
#---------------------------------------------------------------------------
# This is the fortran compiler used for MPI programs
#---------------------------------------------------------------------------
MPIF77 = mpif77

#---------------------------------------------------------------------------
# These macros are passed to the linker to help link with MPI correctly
#---------------------------------------------------------------------------
FMPI_LIB  = -L/usr/local/lib -lmpi

#---------------------------------------------------------------------------
# These macros are passed to the compiler to help find 'mpif.h'
#---------------------------------------------------------------------------
FMPI_INC = -I/usr/local/include

#---------------------------------------------------------------------------
# This is the C compiler used for MPI programs
#---------------------------------------------------------------------------
MPICC = mpicc
# This links MPI C programs; usually the same as ${MPICC}
CLINK	= $(MPICC)

#---------------------------------------------------------------------------
# These macros are passed to the linker to help link with MPI correctly
#---------------------------------------------------------------------------
CMPI_LIB  = -L/usr/local/lib -lmpi

#---------------------------------------------------------------------------
# These macros are passed to the compiler to help find 'mpi.h'
#---------------------------------------------------------------------------
CMPI_INC = -I/usr/local/include
```

To build a whole suite, you can type "make suite".
Make will build all the benchmarks specified in file "config/suite.def".
```
# config/suite.def
# This file is used to build several benchmarks with a single command. 
# Typing "make suite" in the main directory will build all the benchmarks
# specified in this file. 
# Each line of this file contains a benchmark name, class, and number
# of nodes. The name is one of "cg", "is", "ep", mg", "ft", "sp", "bt", 
# "lu", and "dt". 
# The class is one of "S", "W", "A", "B", "C", "D", and "E"
# (except that no classes C, D and E for DT, and no class E for IS).
# The number of nodes must be a legal number for a particular
# benchmark. The utility which parses this file is primitive, so
# formatting is inflexible. Separate name/class/number by tabs. 
# Comments start with "#" as the first character on a line. 
# No blank lines. 
# The following example builds 1 processor sample sizes of all benchmarks. 
ft	S	1
mg	S	1
sp	S	1
lu	S	1
bt	S	1
is	S	1
ep	S	1
cg	S	1
dt	S	1
```
### Execution
Here is an example of running bt, class A with 4 MPI processes and 2 OpenMP threads per process (in csh):
```
setenv OMP_NUM_THREADS 2
mpirun -np 4 -hosts 10.22.1.2,10.22.1.3,10.22.1.4 bin/bt.A.4
```
## OpenMp version
OpenMp: A pthreads alternative, used to create multi-threaded shared-memory programs

### Compilation

### Execution
The executable is named \<benchmark-name\>.\<class\>.x and is placed in the bin subdirectory (or in the directory BINDIR specified in make.def, if you've defined it).  Folllowing is an example of running a benchmark in csh:
```
setenv OMP_NUM_THREADS 4
bin/bt.A.x > BT.A_out.4
```
It runs BT Class A problem on 4 threads and the output is stored in BT.A_out.4.
