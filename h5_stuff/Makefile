CC   = mpicc
CXX  = mpicxx
CFLAGS   = -O2 -Wall
CXXFLAGS = -std=c++17 -O2 -Wall

# ── HDF5 (MPI-enabled) ───────────────────────────────────────────────────────
ifdef HDF5_INCLUDE
  H5INC = $(HDF5_INCLUDE)
  H5LIB = $(HDF5_LIB)
else
  H5INC = $(shell brew --prefix hdf5-mpi)/include
  H5LIB = $(shell brew --prefix hdf5-mpi)/lib
endif

INCLUDES = -I$(H5INC)
LDFLAGS  = -L$(H5LIB) -Wl,-rpath,$(H5LIB) -lhdf5

NP ?= 4

.PHONY: all clean run_cpp run_c

all: test_hdf5.x convert_IO.x

test_hdf5.x: test_hdf5.cpp
	$(CXX) $(CXXFLAGS) $(INCLUDES) $< $(LDFLAGS) -o $@

convert_IO.x: convert_IO.c
	$(CC) $(CFLAGS) $(INCLUDES) $< $(LDFLAGS) -o $@

run_cpp: test_hdf5.x
	mpirun -n $(NP) ./test_hdf5.x

run_c: convert_IO.x
	mpirun -n $(NP) ./convert_IO.x

clean:
	rm -f test_hdf5.x convert_IO.x *.h5
