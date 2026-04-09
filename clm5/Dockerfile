# escape=`
FROM ubuntu:22.04

#
# TODO: Specify image metadata following OCI format spec: https://github.com/opencontainers/image-spec/blob/main/spec.md
#
# LABEL ...


# Download basic utilities and compilers from Ubuntu apt repo
RUN DEBIAN_FRONTEND=noninteractive apt-get update `
    && apt-get install -y sudo nano git wget tree cmake python3.10 `
    && apt-get install -y gcc-10 gfortran-10 g++-10 openmpi-bin libopenmpi-dev `
    && apt-get install -y libhdf5-openmpi-dev libhdf5-openmpi-hl-fortran-100 hdf5-helpers liblapack-dev libblas-dev `
    && apt-get install -y netcdf-bin libnetcdf-dev libnetcdff-dev libpnetcdf-dev nco 

RUN apt-get install -y libxml++2.6-dev libxml2-utils cpanminus `
    && cpanm bigint && cpanm XML::LibXML

RUN ln -sf /usr/bin/python3 /usr/bin/python `
    && ln -sf /usr/bin/gcc-10 /usr/bin/gcc `
    && ln -sf /usr/bin/g++-10 /usr/bin/g++ `
    && ln -sf /usr/bin/gfortran-10 /usr/bin/gfortran

ENV CC=mpicc FC=mpifort CXX=mpicxx

# Install ESMF
RUN cd /tmp `
    && wget -c https://github.com/esmf-org/esmf/archive/ESMF_8_0_1.tar.gz `
    && tar zxf ESMF_8_0_1.tar.gz `
    && cd esmf-ESMF_8_0_1 `
    && export ESMF_DIR=$(pwd) `
    && export ESMF_PREFIX=/usr ESMF_INSTALL_MODDIR=include ESMF_INSTALL_LIBDIR=lib ESMF_INSTALL_BINDIR=bin `
    && export ESMF_F90COMPILEOPTS_ENV="-fallow-argument-mismatch" `
    && make -j8 && make install `
    && cd / && rm -rvf ESMF_8_0_1.tar.gz esmf-ESMF_8_0_1

# Set default envs
RUN cd /root `
    && echo "export CC=$CC" >> .bash_profile `
    && echo "export FC=$FC" >> .bash_profile `
    && echo "export CXX=$CXX" >> .bash_profile `
    && echo "export USER=$(whoami)" >> .bash_profile `
    && echo "export CESMDATAROOT=/home/user/CESMDATAROOT" >> .bash_profile `
    && echo "export CCSMROOT=/home/user" >> .bash_profile `
    && echo "alias ll='ls -phAlv'" >> .bash_profile

# .cime folder
RUN mkdir /home/user/.cime && cd /home/user/.cime `
    && wget https://raw.githubusercontent.com/HPSCTerrSys/containers/refs/heads/master/clm5/cime_config/config_compilers.xml `
    && wget https://raw.githubusercontent.com/HPSCTerrSys/containers/refs/heads/master/clm5/cime_config/config_machines.xml `
    && mkdir -p /home/user/CESMDATAROOT/inputdata `
    && chown -R user /home/user

USER user
WORKDIR /home/user
CMD ["/bin/bash","-l"]
