
This script can be found on the jupyter lab @Openreprolab

/mnt/data-summer-shared/Softs/Parflow/install_openreprolab.sh



#!/bin/bash

# Create environment with mamba

1. Step by step
# mamba create -n parflow python=3.10
# mamba activate parflow
# Install missing libraries/compilers
# mamba install cmake m4 zlib  gcc  gfortran gxx  openmpi
# Install pftools
# pip install pftools[all]


2. OR create the env from yaml file (/mnt/data-summer-shared/Softs/Parflow/)

# mamba env create -n myparflow  -f  parflow.yaml 




# NO NEED HERE  hypre hdf5  netcdf4 netcdf-fortran


# Openreprolab  jupyterlab install 
export INSTALL_DIR=/mnt/data-summer-shared/Softs/Parflow/common
export HDF5_DIR=$INSTALL_DIR/hdf5-1.12.0-install
export NETCDF_DIR=$INSTALL_DIR/netcdf-4.7.2-install
export SILO_ROOT=$INSTALL_DIR/silo-4.10.2-install
export HYPRE_ROOT=$INSTALL_DIR/hypre-2.1-install

export PATH=$NETCDF_DIR/bin:$PATH

# DEFAULT WORKING DIRECTORY
export WORKDIR=/home/jovyan/Parflow

cd $WORKDIR

# par exemple installation avec une arborescence Parflow à la racine du compte perso 

mkdir PFTree 
cd PFTree
mkdir parflow-src
mkdir parflow-devs

# machine perso
#export PARFLOW_SRC=/home/$USER/PFTree/parflow-src
#export PARFLOW_DIR=/home/$USER/PFTree/parflow-devs/parflow-dev_RACINES

# ige_calcul
export PARFLOW_SRC=$WORKDIR/PFTree/parflow-src
export PARFLOW_DIR=$WORKDIR/PFTree/parflow-devs/parflow-dev_NEIGE


# Cloner le ocde avec la branche IGE_CLM_neige
git clone https://github.com/ige-parflow/parflow.git -b IGE_CLM_neige  $PARFLOW_SRC

########## INSTALL AVEC SORTIES NEIGE 

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

