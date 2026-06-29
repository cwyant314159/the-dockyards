ARG VERILATOR_TAG=v5.048
ARG VERILATOR_INSTALL_DIR=/opt/verilator/${VERILATOR_TAG}

FROM debian:stable-slim AS builder

ARG VERILATOR_TAG
ARG VERILATOR_INSTALL_DIR

RUN apt-get update && apt-get install -y --no-install-recommends \
    autoconf \
    bison \
    build-essential \
    ca-certificates \
    ccache \
    flex \
    g++ \
    gdb \
    git \
    help2man \
    libfl-dev \
    libfl2 \
    libgoogle-perftools-dev \
    libjemalloc-dev \
    liblz4-dev \
    make \
    numactl \
    perl \
    perl-doc \
    python3 \
    python3-distro \
    zlib1g \
    zlib1g-dev

WORKDIR /src
RUN git clone --depth 1 --branch ${VERILATOR_TAG} https://github.com/verilator/verilator.git

WORKDIR /src/verilator
RUN autoconf
RUN ./configure --prefix=${VERILATOR_INSTALL_DIR}
RUN make -j$(nproc)
RUN make test
RUN make install

FROM debian:stable-slim AS final

ARG VERILATOR_TAG
ARG VERILATOR_INSTALL_DIR

RUN apt-get update && apt-get install -y --no-install-recommends \
    perl \
    libfl2 \
    liblz4-dev \
    libsystemc \
    libsystemc-dev \
    zlib1g \
    zlib1g-dev \
    z3 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/verilator /opt/verilator
ENV PATH="${VERILATOR_INSTALL_DIR}/bin:${PATH}"
RUN verilator --version