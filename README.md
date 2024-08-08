# Pintest

This is an application to
- verify processes and threads end up where you expect them to, and
- verify that pinning works as expected.


## How to build?

### Requirements

- CMake
- C++ compiler that supports C++17
- Boost
- optionally an MPI library if you want to build the MPI/hybrid versions

### Steps

1. Clone the repository and change to the directory
   ```bash
   $ git clone https://github.com/gjbex/Pintest.git
   $ cd Pintest
   ```
1. If you use a module system, load the necessary modules
   ```bash
   $ module load CMake
   $ module load Boost
   ```
1. Optionally, load the MPI library of your choice, e.g, OpenMPI
   ```bash
   $ module load OpenMPI
   ``
1. Configure the build
   ```bash
   $ cmake  -B build/  -S .  -DCMAKE_BUILD_TYPE=Release \
            -DCMAKE_INSTALL_PREFIX=install/
   ```
1. Build the application
   ```bash  
   $ cmake  --build build/ --parallel
   ```
1. Install the application
   ```bash
   $ cmake  --install build/
   ```


## How to run?

The application is installed in the `install/` directory. You can run the
application from there, or you can add the `install/bin/` directory to your
`PATH` environment variable.

```bash
$ export PATH="$PWD/install/bin:$PATH"
```

Run the serial version of the application

```bash 
$ pintest
```

Run the OpenMP version of the application with, e.g., 4 threads

```bash
$ OMP_NUM_THREADS=4 pintest_omp
```

Run the MPI version of the application with, e.g., 2 processes

```bash
$ mpirun -np 2 pintest_mpi
```

Run the hybrid version of the application with, e.g., 2 processes and 4 threads

```bash 
$ OMP_NUM_THREADS=4 mpirun -np 2 pintest_hybrid
```

The applications have two command line argumetns:
- `--duration`: the duration of the busy-wait loop in seconds
- `--cycles`: the number of cycles in the application

Run steps of the application:
- on start, it will print the runtime parameters
- each process will print the process id and the hostname of the node it is
  running on
- the application cycles for the specified number of cycles
  - each thread in each process will print the rank, the thread id, and the
    core id it runs on
  - each process runs a busy-wait loop for the specified duration
