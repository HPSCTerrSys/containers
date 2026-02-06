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
apt-get install -y sudo nano git wget tree cmake python3.12 pip
apt-get install -y gfortran g++ openmpi-bin libopenmpi-dev
apt-get install -y libxml++2.6-dev libhdf5-openmpi-dev libhdf5-openmpi-hl-fortran-100t64 hdf5-helpers
apt-get install -y liblapack-dev libblas-dev
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

RUN <<EOF
# Add user account
groupadd hpscterrsys
useradd user -g hpscterrsys -m
usermod -aG hpscterrsys user
adduser user sudo
printf "clm\nclm" | passwd user
EOF

USER user
WORKDIR /home/user
RUN <<EOF
# Preconfigure default eCLM environment
echo "export CC=$CC FC=$FC CXX=$CXX" >> .bash_profile
echo "export LD_LIBRARY_PATH=/usr/local/lib" >> .bash_profile
echo "export eCLM=$HOME/eCLM" >> .bash_profile
echo "export PATH=$PATH:$HOME/.local/bin" >> .bash_profile
echo "alias ll='ls -phAlv'" >> .bash_profile
cat <<eof >> .bash_profile
function bld_eclm() {
  if [[ ! -d \$eCLM ]]; then
    git clone https://github.com/HPSCTerrSys/eCLM.git \$eCLM
  fi
  if git -C \$eCLM status >/tmp/eclm_status 2>&1; then
    if [[ \$1 == "-f" ]]; then rm -rf bld; fi
    if [[ ! -f bld/CMakeCache.txt ]]; then
      cmake -S \$eCLM/src -B bld
    fi
    cmake --build bld --parallel 8
    cmake --install bld --prefix=\$HOME/.local
    pip install --user --break-system-packages \$eCLM/namelist_generator
  else
    echo "\$eCLM is not a valid eCLM repository."
    echo "  Failing command:  git -C \$eCLM status"
    echo "    Error message:  \$(cat /tmp/eclm_status)"
    exit 1
  fi
}
eof
mkdir -vp .local/bin share
EOF

CMD ["/bin/bash","-l"]
