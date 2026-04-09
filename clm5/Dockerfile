# escape=`
FROM centos:8.2.2004

# Image metadata for documentation purposes. Has no effect on docker build behavior.
LABEL `
    org.label-schema.schema-version="1.0" `
    org.label-schema.name="CLM5.0 Development Environment" `
    org.label-schema.version="0.4" `
    org.label-schema.vendor="IBG-3" `
    org.label-schema.license="GPLv2" `
    org.label-schema.build-date="20201111" `
    clm.version.major="5" `
    clm.env.compiler="GCC" `
    clm.env.compiler.version="8.3.1" `
    clm.env.mpi="OpenMPI" `
    clm.env.mpi.version="4.0.2-2" `
    clm.env.cmake.version="3.18" `
    clm.env.pnetcdf.version="1.12.1" `
    clm.env.netcdf-c.version="4.7.0" `
    clm.env.netcdf-fortran.version="4.5.2-3" `
    clm.env.esmf.version="8.0.1"

# Import cime config files to image
WORKDIR /tmp/.cime
COPY cime_config/* /tmp/.cime/

# Install required packages from base repo
RUN dnf update -y `
    && dnf group install -y "Development Tools" `
    && dnf install -y glibc-langpack-en libcurl-devel man-db man-pages passwd sudo vim wget which tree `
                      perl-App-cpanminus python38 python38-pip `
                      epel-release `
    && dnf config-manager --set-enabled PowerTools `
    `
# Install required packages from EPEL and SCL repos
    && dnf install -y hdf5-devel lapack-devel ncl nco netcdf-devel netcdf-fortran netcdf-fortran-devel openssl-devel openmpi openmpi-devel `
    && dnf clean all `
    `
# misc
    && ln -s /usr/bin/python3 /usr/bin/python `
    && cpanm bigint && cpanm XML::LibXML

# Required envs for building libraries
ENV ESMF_DIR=/tmp/bld/esmf-ESMF_8_0_1
ENV ESMF_INSTALL_PREFIX=/usr/local
ENV ESMF_INSTALL_MODDIR=include
ENV ESMF_INSTALL_LIBDIR=lib
ENV ESMF_INSTALL_BINDIR=bin
ENV PATH="/usr/lib64/openmpi/bin:${PATH}"

# Install CMake
RUN mkdir -vp /tmp/bld `
    && cd /tmp/bld `
    && wget -c https://github.com/Kitware/CMake/releases/download/v3.18.4/cmake-3.18.4.tar.gz `
    && tar zxf cmake-3.18.4.tar.gz && cd cmake-3.18.4 `
    && ./bootstrap && make && make install `
    `
# PnetCDF
    && cd /tmp/bld `
    && wget -c https://github.com/Parallel-NetCDF/PnetCDF/archive/checkpoint.1.12.1.tar.gz `
    && tar zxf checkpoint.1.12.1.tar.gz `
    && cd PnetCDF-checkpoint.1.12.1 `
    && autoreconf -i `
    && ./configure --prefix=/usr/local && make -j && make install `
    `
# Build and install ESMF libraries
    && cd /tmp/bld `
    && wget -c https://github.com/esmf-org/esmf/archive/ESMF_8_0_1.tar.gz `
    && tar zxf ESMF_8_0_1.tar.gz `
    && cd esmf-ESMF_8_0_1 `
    && make -j && make install `
    `
# Cleanup
    && cd /tmp && rm -rf /tmp/bld

# Add user account
WORKDIR /home

RUN groupadd -g 1555 clm5docker `
    && useradd clmuser -g clm5docker`
    && echo clm5 | passwd --stdin clmuser `
    && usermod -aG wheel clmuser `
    && rm /run/nologin `
    `
# Set compiler and library paths for CLM5
    && echo "" >> /home/clmuser/.bash_profile `
    && echo "# Set compiler and library paths for CLM5" >> /home/clmuser/.bash_profile `
    && echo "export PATH=/usr/lib64/openmpi/bin:$PATH" >> /home/clmuser/.bash_profile >> /home/clmuser/.bash_profile `
    && echo "source /etc/profile.d/ncarg.sh" >> /home/clmuser/.bash_profile `
    `
# Set CLM5 environment variables
    && echo "" >> /home/clmuser/.bash_profile `
    && echo "# CLM5 environment variables" >> /home/clmuser/.bash_profile `
    && echo "" >> /home/clmuser/.bash_profile `
    && echo "export PROJECT=/p/project" >> /home/clmuser/.bash_profile `
    && echo "export SCRATCH=/p/scratch" >> /home/clmuser/.bash_profile `
    && echo "export BUDGET_ACCOUNTS=slts" >> /home/clmuser/.bash_profile `
    && echo "export CESMDATAROOT=/p/scratch/CESMDATAROOT" >> /home/clmuser/.bash_profile `
    && echo "export CSMDATA=\$CESMDATAROOT/inputdata" >> /home/clmuser/.bash_profile `
    `
# Copy cime config files to home directory
    && mv /tmp/.cime /home/clmuser/.cime

USER clmuser
WORKDIR /home/clmuser
CMD ["/bin/bash","-l"]
