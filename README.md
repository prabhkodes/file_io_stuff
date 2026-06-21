# HPC File Systems & Parallel I/O

![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=black)
![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![OpenMPI](https://img.shields.io/badge/OpenMPI-364d6e?style=flat-square&logoColor=white)
![OpenMP](https://img.shields.io/badge/OpenMP-006DB8?style=flat-square&logoColor=white)
![HDF5](https://img.shields.io/badge/HDF5-0096D6?style=flat-square&logoColor=white)

Three projects covering parallel I/O patterns and filesystem performance relevant to HPC workloads.

## Projects

### jacobi_io_parallel
2D heat diffusion solved with the Jacobi method, distributed row-wise across MPI ranks with OpenMP threading per rank. Every `print_interval` steps, all ranks write their local tile into a single shared HDF5 file using collective MPI-IO. Per-phase timings (halo exchange, stencil, HDF5 write) are gathered across ranks and saved to `statistics.txt`. Includes a gnuplot script to animate the converging solution.

```bash
make run              # mpirun -n 4, OMP_NUM_THREADS=2
make run NP=8 OMP=4
```

### h5_stuff
Two focused programs showing the HDF5 parallel write pattern from scratch:
- `test_hdf5.cpp` — each rank writes a local array into a shared 1D dataset via hyperslab selection and collective `H5Dwrite`
- `convert_IO.c` — converts an ASCII Jacobi output file to HDF5; each rank reads its portion and writes rows collectively into a 2D `/temperature` dataset

```bash
make run_cpp    # mpirun -n 4 ./test_hdf5.x
make run_c      # mpirun -n 4 ./convert_IO.x
```

### compare_fs_hpc
`fio` benchmarks comparing ext4, XFS, and Btrfs on sequential and random I/O. Run on an AWS EC2 instance with a dedicated EBS volume reformatted for each filesystem. Raw output (IOPS, bandwidth, latency percentiles) is in the `.txt` files.

## Dependencies

- OpenMPI
- HDF5 with MPI support: `brew install hdf5-mpi` or `module load hdf5` on a cluster
- libomp
- `fio` (compare_fs_hpc)
