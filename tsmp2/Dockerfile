# escape=`
FROM hpscterrsys/eclm:0.2

#
# TODO: Specify image metadata following OCI format spec: https://github.com/opencontainers/image-spec/blob/main/spec.md
#
# LABEL ...


# Download compiler toolchain and libraries
RUN <<EOF
export DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get install -y tcl-dev tk-dev liblzma-dev
EOF

# Build prep
ENV VER_ECCODES="2.46.0"
ENV VER_HYPRE="3.1.0"
ENV CC=mpicc FC=mpifort CXX=mpicxx
ENV TMP=/tmp/bld
ENV INSTALL_PREFIX=/usr/local
ENV MAKEFLAGS=-j8
ENV MPI_HOME=/usr/lib/x86_64-linux-gnu/openmpi
ENV LD_LIBRARY_PATH=/usr/local/lib

RUN <<EOF
# Install Hypre
mkdir -p $TMP && cd $TMP
wget https://github.com/hypre-space/hypre/archive/v${VER_HYPRE}.tar.gz
tar xf v${VER_HYPRE}.tar.gz
cd hypre-${VER_HYPRE}
cmake -S src -B bld                               \
      -DCMAKE_INSTALL_PREFIX="${INSTALL_PREFIX}"  \
      -DMPI_INCLUDE_DIR="${MPI_HOME}/include"     \
      -DHYPRE_ENABLE_OPENMP="ON"
cmake --build bld
cmake --install bld
EOF

RUN <<EOF
# Install ecCodes
wget https://github.com/ecmwf/eccodes/archive/refs/tags/${VER_ECCODES}.tar.gz
tar xf ${VER_ECCODES}.tar.gz
cd eccodes-${VER_ECCODES}
cmake -S . -B bld -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX}/eccodes-${VER_ECCODES}
cmake --build bld --parallel 4
cmake --install bld
cd / && rm -rvf $TMP
EOF

USER root
WORKDIR /root
CMD ["/bin/bash","-l"]
