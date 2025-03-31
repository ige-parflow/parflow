
# Parflow Installation On Ige-clusters

```note

To use ige clusters refers to the [Documentation on github](https://ige-calcul.github.io/public-docs/docs/clusters/Ige/ige-calcul1.html)
```

# Rnvironment variables on Ige cluster 

```bash
export HDF5_DIR=/workdir/phyrev/commun/install/hdf5-1.12.0-install
export NETCDF_DIR=/workdir/phyrev/commun/install/netcdf-4.7.2-install
export SILO_ROOT=/workdir/phyrev/commun/install/silo-4.10.2-install
export HYPRE_ROOT=/workdir/phyrev/commun/install/hypre-2.1-install

export PATH=$NETCDF_DIR/bin:$PATH
```

# DEFAULT WORKING DIRECTORY

```bash
export WORKDIR=/workdir/phyrev/$USER

cd $WORKDIR
```

# par exemple installation avec une arborescence Parflow à la racine du compte perso

```bash
mkdir PFTree
cd PFTree
mkdir parflow-src
mkdir parflow-devs
```


# PATHS

```bash
#export PARFLOW_SRC=/home/$USER/PFTree/parflow-src
#export PARFLOW_DIR=/home/$USER/PFTree/parflow-devs/parflow-dev_RACINES

# ige_calcul
export PARFLOW_SRC=$WORKDIR/PFTree/parflow-src
export PARFLOW_DIR=$WORKDIR/PFTree/parflow-devs/parflow-dev_NEIGE
```

# Cloner le code avec la branche IGE_CLM_neige

```bash
git clone https://github.com/ige-parflow/parflow.git -b IGE_CLM_neige  $PARFLOW_SRC
```


# INSTALL AVEC SORTIES NEIGE


```bash
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

```
