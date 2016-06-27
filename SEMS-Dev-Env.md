For machines that are set up with SEMS development environment modules (e.g. usually nfs mounted under `/project/sems/`), Trilinos has built-in support for easy automatic configuration.  Just source the script [load_sems_dev_env.sh](https://github.com/trilinos/Trilinos/blob/develop/cmake/load_sems_dev_env.sh) as:

```
$ cd <some-build-dir>/
$ source $TRILINOS_DIR/cmake/load_sems_dev_env.sh
``` 

(where `TRILNIOS_DIR` points to the base Trilinos git repo source dir, e.g. `$HOME/Trilinos`).  This loads the default compiler, MPI, CMake, Python, and several key TPLs that can be used by Trilinos like Boost, Zlib, HDF5, and Netcdf (run `module list` to see what is loaded).  Different compilers (GCC, Clang, Intel) and several versions of each can be selected.  Different versions of OpenMPI and CMake can also be selected (see modules starting with `sems-` from `module avail`).  See the default versions for each of these and more documentation in the script [load_sems_dev_env.sh](https://github.com/trilinos/Trilinos/blob/develop/cmake/load_sems_dev_env.sh) itself.  The defaults for GCC, OpenMPI, and CMake selected in the script provide the best combination of build speed, error checking, and portability checking (see [Trilinos Issue #158](https://github.com/trilinos/Trilinos/issues/158) for more background).

Once a SEMS Dev Env has been loaded, then one can configure, build, and test (for example) with:

```
$ cmake \
  -DBUILD_SHARED_LIBS=ON \
  -DCMAKE_BUILD_TYPE=DEBUG \
  -DTPL_ENABLE_MPI=ON \
  -DTrilinos_ENABLE_<PKG0>=ON \
  -DTrilinos_ENABLE_<PKG1>=ON \
  $TRILNOS_DIR
$ make -j10
$ ctest -j10
 ```

All of the TPLs supported by the loaded SEMS Dev Env will be automatically picked up and enabled (see `Final set of enabled TPLs` in the cmake STDOUT).  The same loaded SEMS Dev Env can be used for MPI or serial (non-MPI) builds and shared lib or static lib builds.  The SEMS Dev Env only needs to be changed when one wants a different compiler/version and/or MPI/version and/or CMake/version, for example, using:

```
$ source $TRILINOS_DIR/cmake/load_sems_dev_env.sh  sems-gcc/5.3.0  sems-openmpi/1.10.1
```

NOTES:
* The Trilinos CMake configure is set up to look for the env var `TRILINOS_SEMS_DEV_ENV_LOADED` that is set by the `load_sems_dev_env.sh` script.  If it finds it, then it will automatically include the file [SEMSDevEnv.cmake](https://github.com/trilinos/Trilinos/blob/develop/cmake/std/sems/SEMSDevEnv.cmake) which then reads in all of the information from the env to set the compilers, MPI implementation and the location of the various TPLs.
* If the script `load_sems_dev_env.sh` is not used, then one can load the various SEMS modules manually and then configure with `-DTrilinos_USE_SEMS_DEV_ENV=TRUE`.  The location of the compiler, MPI, and the TPLs are taken from env vars that are set by the SEMS modules.
* To unload a loaded dev env, use the [unload_sems_dev_env.sh](https://github.com/trilinos/Trilinos/blob/develop/cmake/unload_sems_dev_env.sh) script (but note in the comment in that script that someones you need to use `module purse` due to some SEMS modules not cleaning up after themselves (see [this issue](https://sems-jira.sandia.gov/browse/SEMS-1048)). 