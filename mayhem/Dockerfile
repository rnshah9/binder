FROM ubuntu:20.04 as base

# Config
ARG BRANCH="master"
ENV CLANG_VERSION=14

# General dependencies
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
 && apt-get install -yq wget gnupg \
 && rm -rf /var/lib/apt/lists/*

# Add llvm repo
RUN wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -
RUN echo "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-${CLANG_VERSION} main" >> /etc/apt/sources.list

# Run dependencies
RUN apt-get update \
 && apt-get install -yq "clang-${CLANG_VERSION}" \
 && rm -rf /var/lib/apt/lists/*


# Build binder
FROM base as build

# Build dependencies
RUN apt-get update
RUN apt-get install -yq \
	"libclang-${CLANG_VERSION}-dev" \
	cmake \
	git

# Clone binder source
COPY . /binder

# Build
WORKDIR "/build"
RUN clang++-${CLANG_VERSION} /binder/test/diffbinder.cpp -o /diffbinder


# Install image
FROM base as install
COPY --from=build /diffbinder /diffbinder
COPY --from=build /binder/README.md /README.md
