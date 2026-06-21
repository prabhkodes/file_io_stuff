CXX      = mpicxx
CXXFLAGS = -std=c++20 -O3 -Wall -Wextra

# ── HDF5 (MPI-enabled) ──────────────────────────────────────────────────────
# On a cluster:  module load hdf5   then HDF5_INCLUDE / HDF5_LIB are set.
# On macOS:      brew install hdf5-mpi
ifdef HDF5_INCLUDE
  H5INC = $(HDF5_INCLUDE)
  H5LIB = $(HDF5_LIB)
else
  H5INC = $(shell brew --prefix hdf5-mpi)/include
  H5LIB = $(shell brew --prefix hdf5-mpi)/lib
endif

# ── OpenMP ───────────────────────────────────────────────────────────────────
ifdef SLURM_JOB_ID          # on cluster, gcc provides -fopenmp natively
  OMP_FLAGS = -fopenmp
else                         # macOS needs Homebrew libomp
  LIBOMP    = $(shell brew --prefix libomp)
  OMP_FLAGS = -Xpreprocessor -fopenmp -I$(LIBOMP)/include
  OMP_LIBS  = -L$(LIBOMP)/lib -lomp
endif

INCLUDES = -I./include -I$(H5INC)
LDFLAGS  = -L$(H5LIB) -Wl,-rpath,$(H5LIB) -lhdf5 $(OMP_LIBS)

SRC    = src/main.cpp
TARGET = main.x

NP  ?= 4
OMP ?= 2

.PHONY: all clean run

all: $(TARGET)

$(TARGET): $(SRC)
	$(CXX) $(CXXFLAGS) $(OMP_FLAGS) $(INCLUDES) $< $(LDFLAGS) -o $@

run: $(TARGET)
	mkdir -p files
	OMP_NUM_THREADS=$(OMP) mpirun -n $(NP) ./$(TARGET) jacobian.in

clean:
	rm -f $(TARGET) statistics.txt
	rm -rf files/
