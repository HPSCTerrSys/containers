# escape=`
FROM ubuntu:24.04

#
# TODO: Specify image metadata following OCI format spec: https://github.com/opencontainers/image-spec/blob/main/spec.md
#
# LABEL ...


# Download compiler toolchain and libraries
RUN <<EOF
export DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get install -y sudo nano vim git wget tree cmake python3.12 pip nco
apt-get install -y gfortran g++ openmpi-bin libopenmpi-dev liblapack-dev libblas-dev
apt-get install -y libxml++2.6-dev libhdf5-openmpi-dev libhdf5-openmpi-hl-fortran-100t64 hdf5-helpers
EOF

# Build prep
ENV VER_NETCDF_C="4.9.2"
ENV VER_NETCDF_F90="4.6.1"
ENV VER_PNETCDF="1.14.0"
ENV CC=mpicc FC=mpifort CXX=mpicxx
ENV TMP=/tmp/bld
ENV INSTALL_PREFIX=/usr/local
ENV MAKEFLAGS=-j8

RUN <<EOF
# Install netCDF
mkdir -p $TMP && cd $TMP
wget -c https://github.com/Unidata/netcdf-c/archive/refs/tags/v${VER_NETCDF_C}.tar.gz
tar zxf v${VER_NETCDF_C}.tar.gz
cd netcdf-c-${VER_NETCDF_C}
CPPFLAGS=-I/usr/include/hdf5/openmpi LDFLAGS=-L/usr/lib/x86_64-linux-gnu/hdf5/openmpi ./configure --prefix=${INSTALL_PREFIX}
make install
EOF

RUN <<EOF
# Install netCDF Fortran
cd $TMP
wget -c https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v${VER_NETCDF_F90}.tar.gz
tar zxf v${VER_NETCDF_F90}.tar.gz
cd netcdf-fortran-${VER_NETCDF_F90}
./configure --prefix=${INSTALL_PREFIX}
make install
EOF

RUN <<EOF
# Install PnetCDF
cd $TMP
wget -c https://github.com/Parallel-NetCDF/PnetCDF/archive/checkpoint.${VER_PNETCDF}.tar.gz
tar zxf checkpoint.${VER_PNETCDF}.tar.gz
cd PnetCDF-checkpoint.${VER_PNETCDF}
autoreconf -i
./configure --prefix=${INSTALL_PREFIX}
make install
cd / && rm -rvf $TMP
EOF

USER root
WORKDIR /root
RUN <<EOF
echo "export CC=mpicc FC=mpif90 CXX=mpicxx"                    >> .bash_profile
echo "export OMPI_ALLOW_RUN_AS_ROOT=1"                         >> .bash_profile
echo "export OMPI_ALLOW_RUN_AS_ROOT_CONFIRM=1"                 >> .bash_profile
echo "export PIP_BREAK_SYSTEM_PACKAGES=1"                      >> .bash_profile
echo "export CMAKE_INSTALL_PREFIX=/root/.local"                >> .bash_profile
echo "export LD_LIBRARY_PATH=/usr/local/lib:/root/.local/lib"  >> .bash_profile
echo "export PATH=${PATH}:/root/.local/bin"                    >> .bash_profile
echo "export SW=/root/.local"                                  >> .bash_profile
mkdir -vp .local/{bin,lib}
EOF
CMD ["/bin/bash","-l"]
