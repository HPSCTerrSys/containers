# escape=`
FROM ubuntu:24.04

#
# TODO: Specify image metadata following OCI format spec: https://github.com/opencontainers/image-spec/blob/main/spec.md
#
# LABEL ...


# Download basic utilities and compilers from Ubuntu apt repo
RUN DEBIAN_FRONTEND=noninteractive apt-get update `
    && apt-get install -y sudo nano git wget tree cmake python3.12 `
    && apt-get install -y gfortran g++ openmpi-bin libopenmpi-dev `
    && apt-get install -y libxml++2.6-dev libhdf5-openmpi-dev libhdf5-openmpi-hl-fortran-100t64 hdf5-helpers liblapack-dev libblas-dev


ENV VER_NETCDF_C="4.9.2"
ENV VER_NETCDF_F90="4.6.1"
ENV VER_PNETCDF="1.14.0"
ENV CC=mpicc FC=mpifort CXX=mpicxx
ENV TMP=/tmp/bld
ENV INSTALL_PREFIX=/usr/local
ENV MAKEFLAGS=-j8

# Build prep
RUN mkdir -vp $TMP

# Install netCDF
RUN cd $TMP `
    && wget -c https://github.com/Unidata/netcdf-c/archive/refs/tags/v${VER_NETCDF_C}.tar.gz `
    && tar zxf v${VER_NETCDF_C}.tar.gz `
    && cd netcdf-c-${VER_NETCDF_C} `
    && CPPFLAGS=-I/usr/include/hdf5/openmpi LDFLAGS=-L/usr/lib/x86_64-linux-gnu/hdf5/openmpi ./configure --prefix=${INSTALL_PREFIX} `
    && make install

# Install netCDF Fortran
RUN cd $TMP `
    && wget -c https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v${VER_NETCDF_F90}.tar.gz `
    && tar zxf v${VER_NETCDF_F90}.tar.gz `
    && cd netcdf-fortran-${VER_NETCDF_F90} `
    && ./configure --prefix=${INSTALL_PREFIX} `
    && make install

# Install PnetCDF
RUN cd $TMP `
    && wget -c https://github.com/Parallel-NetCDF/PnetCDF/archive/checkpoint.${VER_PNETCDF}.tar.gz `
    && tar zxf checkpoint.${VER_PNETCDF}.tar.gz `
    && cd PnetCDF-checkpoint.${VER_PNETCDF} `
    && autoreconf -i `
    && ./configure --prefix=${INSTALL_PREFIX} `
    && make install

# Delete build artifacts
RUN cd / && rm -rvf $TMP

# Add user account
RUN groupadd hpscterrsys `
    && useradd user -g hpscterrsys -m `
    && usermod -aG hpscterrsys user `
    && adduser user sudo `
    `
# Set default envs
    && echo "export CC=$CC" >> /home/user/.bash_profile `
    && echo "export FC=$FC" >> /home/user/.bash_profile `
    && echo "export CXX=$CXX" >> /home/user/.bash_profile `
    && echo "alias ll='ls -phAlv'" >> /home/user/.bash_profile

USER user
WORKDIR /home/user
CMD ["/bin/bash","-l"]
