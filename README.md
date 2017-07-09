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

Multi-zone versions of NPB (NPB-MZ) are designed to exploit multiple levels of parallelism in applications and to test the effectiveness of multi-level and hybrid parallelization paradigms and tools.

Benchmarks for unstructured computation, parallel I/O, and data movement
- BT-IO - test of different parallel I/O techniques

### Documents on benchmark specifications:
The BTIO benchmark tool is the responsible to evaluate the storage performance. The BTIO version of the benchmark uses the same computational method, but with the addition that results must be written to disk at every fifth time step. There are different versions of BTIO, which are described as below:
- BTIO-full-mpiio: uses MPI-IO file operations with *collective buffering*, which means that data blocks are potentially re-ordered previously to being written to disk, resulting in coarser write granularity.
- BTIO-simple-mpiio: Also uses MPI-IO operations, but no data re-ordering is performed, resulting in a high number of seeks when storing information on the file system.
- BT-epio: In this version each node writes in a separate file. This test gives the optimal writes performance that can be obtained, because the file isn't shared by all the processes, so there is no lock restrictions.

### Benchmark Classes
- Class S: small for quick test purposes
- Class W: workstation size (a 90's workstation; now likely too small)
- Classes A, B, C: standard test problems; ~4X size increase going from one class to the next
- Classes D, E, F: large test problems; ~16X size increase from each of the previous classes

### Pre-requisites
```
sudo apt-get install libopenmpi-dev openmpi-bin gfortran
```

## MPI version
MPI: Spawn processes across physically-different nodes in a cluster environment
### compilation

Before compilation, one needs to check the configuration file 'make.def' in the config directory and modify the file if necessary. If it does not (yet) exist, copy 'make.def.template' or one of the sample files in the NAS.samples subdirectory to 'make.def' and edit the content for site- and machine-specific data.

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
