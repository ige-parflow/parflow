This script can be found on the jupyter lab @Openreprolab

/mnt/data-summer-shared/Softs/Parflow/install_openreprolab.sh





# Create environment with mamba

Two ways to install the environment:

1. Step by step

```bash
 mamba create -n parflow python=3.10
 mamba activate parflow
# Install missing libraries/compilers
 mamba install cmake m4 zlib  gcc  gfortran gxx  openmpi
# Install pftools
 pip install pftools[all]
```

2. Or (recommended)  create the env from  the given yaml file (/mnt/data-summer-shared/Softs/Parflow/parflow.yaml)

```bash
mamba env create -n myparflow  -f  parflow.yaml 
```


Once the mamba environment created, you can copy and execute the file install_openreprolab.sh

```bash
cp /mnt/data-summer-shared/Softs/Parflow/install_openreprolab.sh .
bash install_openreprolab.sh
```

Make sure to change the **WORKDIR** to adapt to your desired path installation 

You are now done, everything should be installed in your **WORKDIR**

In order to work with a jupyter notebook, create the corresponding kernel

```bash
 mamba  install ipykernel
 python -m ipykernel install --name parflow --user --display-name "Parflow Env"
```

# Running parflow

Make sure to set the environment variable before your run

```bash
export PARFLOW_DIR=/home/jovyan/Parflow/PFTree/parflow-devs/parflow-dev_NEIGE

export LD_LIBRARY_PATH=/mnt/data-summer-shared/Softs/Parflow/common/hdf5-1.12.0-install/lib:/mnt/data-summer-shared/Softs/Parflow/common/netcdf-4.7.2-install/lib:/mnt/data-summer-shared/Softs/Parflow/common/hypre-2.1-install/lib:/mnt/data-summer-shared/Softs/Parflow/common/silo-4.10.2-install/lib:$LD_LIBRARY_PATH
```

If you have an error related to numpy:

```warning
AttributeError: module 'numpy' has no attribute '__version__'
```

You can downgrade the version of numpy 


```bash
mamba install 'numpy<2'
```


# Parflow installation steps

# Openreprolab parflow dependencies (already installed)

export INSTALL_DIR=/mnt/data-summer-shared/Softs/Parflow/common

export HDF5_DIR=$INSTALL_DIR/hdf5-1.12.0-install

export NETCDF_DIR=$INSTALL_DIR/netcdf-4.7.2-install

export SILO_ROOT=$INSTALL_DIR/silo-4.10.2-install

export HYPRE_ROOT=$INSTALL_DIR/hypre-2.1-install

export PATH=$NETCDF_DIR/bin:$PATH

# DEFAULT WORKING DIRECTORY
export WORKDIR=/home/jovyan/Parflow

cd $WORKDIR

mkdir PFTree 

cd PFTree

mkdir parflow-src

mkdir parflow-devs

# Installation path 
export PARFLOW_SRC=$WORKDIR/PFTree/parflow-src

export PARFLOW_DIR=$WORKDIR/PFTree/parflow-devs/parflow-dev_NEIGE


# Clone the code from  IGE_CLM_neige branch
git clone https://github.com/ige-parflow/parflow.git $PARFLOW_SRC

cd $PARFLOW_SRC
rm -rf build; mkdir build
cd build


cmake .. \
-DCMAKE_INSTALL_PREFIX=$PARFLOW_DIR \
-DPARFLOW_AMPS_LAYER=mpi1 \
-DPARFLOW_ENABLE_HYPRE=ON \
-DPARFLOW_ENABLE_NETCDF=ON \
-DPARFLOW_ENABLE_SILO:BOOL=ON \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_Fortran_COMPILER=mpif90 \
-DCMAKE_C_COMPILER=mpicc \
-DCMAKE_C_FLAGS="-O3" \
-DCMAKE_CXX_COMPILER=mpic++ \
-DCMAKE_CXX_FLAGS="-O3" \
-DCMAKE_Fortran_LINKER=mpif90 \
-DCMAKE_Fortran_FLAGS="-O3 -fbacktrace" \
-DNETCDF_INCLUDE_DIR=$NETCDF_DIR/include \
-DNETCDF_LIBRARY=$NETCDF_DIR/lib/libnetcdf.so \
-DPARFLOW_HAVE_CLM=ON

make -j 8
make install
