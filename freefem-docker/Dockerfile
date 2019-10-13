FROM ubuntu AS builder

MAINTAINER Simon Garnotel "simon.garnotel@gmail.com"

# Install dependencies
RUN apt-get update && apt-get install -y \
	autoconf \
	automake \
	autotools-dev \
	bison \
	cmake \
	coinor-libipopt-dev \
	cpp \
	flex \
	g++ \
	gcc \
	gdb \
	gfortran \
	ghostscript \
	git \
	m4 \
	make \
	libopenblas-dev \
	liblapack-dev \
	libhdf5-dev \
	libgsl-dev \
	libscotch-dev \
	libfftw3-dev \
	libarpack2-dev \
	libsuitesparse-dev \
	libmumps-seq-dev \
	libnlopt-dev \
	libgmm++-dev \
	libtet1.5-dev \
	libopenmpi-dev \
	patch \
	pkg-config \
	python \
	unzip \
	valgrind \
	wget \
	&& rm -rf /var/lib/apt/lists/*

# Copy ff++ repository
RUN git clone https://github.com/FreeFem/FreeFem-sources.git /root/FreeFem++-source

# Compile FreeFem++
RUN cd /root/FreeFem++-source \
	&& autoreconf -i \
	&& ./configure --enable-download --enable-optim --prefix=/usr/freefem/ \
	&& ./3rdparty/getall -a \
	&& cd 3rdparty/ff-petsc \
	&& make petsc-slepc \
	&& cd - \
	&& ./reconfigure \
	&& make -j 4 \
	&& make install

FROM ubuntu 

MAINTAINER Simon Garnotel "simon.garnotel@gmail.com"

# Install dependencies
RUN apt-get update && apt-get install -y \
	coinor-libipopt-dev \
	libopenblas-dev \
	liblapack-dev \
	libhdf5-dev \
	libgsl-dev \
	libscotch-dev \
	libfftw3-dev \
	libarpack2-dev \
	libsuitesparse-dev \
	libmumps-seq-dev \
	libnlopt-dev \
	libgmm++-dev \
	libtet1.5-dev \
	libopenmpi-dev \
	&& rm -rf /var/lib/apt/lists/*

# Copy ff repository
RUN mkdir /usr/freefem
COPY --from=builder  /usr/freefem /usr/freefem

# Add FreeFem++ to bashrc
RUN touch /root/.bash_aliases \
	&& echo "alias bamg='/usr/freefem/bin/bamg'" > /root/.bash_aliases \
	&& echo "alias cvmsh2='/usr/freefem/bin/cvmsh2'" > /root/.bash_aliases \
	&& echo "alias ff-c++='/usr/freefem/bin/ff-c++'" > /root/.bash_aliases \
	&& echo "alias ff-get-dep='/usr/freefem/bin/ff-get-dep'" > /root/.bash_aliases \
	&& echo "alias ffmaster='/usr/freefem/bin/ffmaster'" > /root/.bash_aliases \
	&& echo "alias ff-mpirun='/usr/freefem/bin/ff-mpirun --allow-run-as-root'" >> /root/.bash_aliases \
	&& echo "alias ff-pkg-download='/usr/freefem/bin/ff-pkg-download'" >> /root/.bash_aliases \
	&& echo "alias FreeFem++='/usr/freefem/bin/FreeFem++'" >> /root/.bash_aliases \
	&& echo "alias FreeFem++-mpi='/usr/freefem/bin/FreeFem++-mpi'" >> /root/.bash_aliases \
	&& echo "alias FreeFem++-nw='/usr/freefem/bin/FreeFem++-nw'" >> /root/.bash_aliases

ENV GLOB *.edp
CMD ["bash", "-c", "ls /data/$GLOB | xargs -I {} /usr/freefem/bin/FreeFem++ {}"]
