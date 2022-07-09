FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04

ENV PYTHON_VERSION=3.10.5
ENV GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D
ENV PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV DEBIAN_FRONTEND=noninteractive

# Update GPG key
RUN distro=$(. /usr/lib/os-release\ echo $ID$VERSION_ID | tr -d ".") \
 && arch=$(/usr/bin/arch) \
 && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/$distro/$arch/3bf863cc.pub \
 && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/$distro/$arch/7fa2af80.pub


RUN apt-get update
RUN apt-get install -y --no-install-recommends mercurial openssh-client subversion procps
RUN apt-get update && apt-get install -y --no-install-recommends autoconf automake bzip2 dpkg-dev file g++ gcc imagemagick libbz2-dev libc6-dev libcurl4-openssl-dev libdb-dev libevent-dev libffi-dev libgdbm-dev libglib2.0-dev libgmp-dev libjpeg-dev libkrb5-dev liblzma-dev libmagickcore-dev libmagickwand-dev libmaxminddb-dev libncurses5-dev libncursesw5-dev libpng-dev libpq-dev libreadline-dev libsqlite3-dev libssl-dev libtool libwebp-dev libxml2-dev libxslt-dev libyaml-dev make patch unzip xz-utils zlib1g-dev
RUN apt-get install -y --no-install-recommends libbluetooth-dev tk-dev uuid-dev git wget

# install python
RUN wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
&& wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
&& GNUPGHOME="$(mktemp -d)" && export GNUPGHOME \
&& gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$GPG_KEY" && gpg --batch --verify python.tar.xz.asc python.tar.xz \
&& command -v gpgconf > /dev/null && gpgconf --kill all || : \
# && rm -rf "$GNUPGHOME" python.tar.xz.asc \
&& mkdir -p /usr/src/python && tar --extract --directory /usr/src/python --strip-components=1 --file python.tar.xz\
&& rm python.tar.xz && cd /usr/src/python\
&& gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
&& ./configure --build="$gnuArch" --enable-loadable-sqlite-extensions --enable-optimizations --enable-option-checking=fatal --enable-shared --with-lto --with-system-expat --without-ensurepip \
&& nproc="$(nproc)"\
&& make -j "$nproc" \
&& make install\
&& bin="$(readlink -ve /usr/local/bin/python3)"\
&& dir="$(dirname "$bin")" \
&& mkdir -p "/usr/share/gdb/auto-load/$dir"\
&& cp -vL Tools/gdb/libpython.py "/usr/share/gdb/auto-load/$bin-gdb.py"\
&& cd / \
&& rm -rf /usr/src/python \
&& find /usr/local -depth \( \( -type d -a \( -name test -o -name tests -o -name idle_test \) \) -o \( -type f -a \( -name '*.pyc' -o -name '*.pyo' -o -name 'libpython*.a' \) \) \) -exec rm -rf '{}' + \
&& ldconfig && python3 --version

ENV PYTHON_PIP_VERSION=22.0.4
ENV PYTHON_SETUPTOOLS_VERSION=58.1.0
ENV PYTHON_GET_PIP_URL=https://github.com/pypa/get-pip/raw/6ce3639da143c5d79b44f94b04080abf2531fd6e/public/get-pip.py
ENV PYTHON_GET_PIP_SHA256=ba3ab8267d91fd41c58dbce08f76db99f747f716d85ce1865813842bb035524d

# install pip
RUN wget -O get-pip.py "$PYTHON_GET_PIP_URL"\
&& echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum -c -\
&& export PYTHONDONTWRITEBYTECODE=1\
&& python3 get-pip.py --disable-pip-version-check --no-cache-dir --no-compile "pip==$PYTHON_PIP_VERSION" "setuptools==$PYTHON_SETUPTOOLS_VERSION" \
&& rm -f get-pip.py && pip --version

# コマンド指定
CMD ["bin/bash"]

# you may make PATH to cudnn "export PATH="/usr/local/cuda/bin:$PATH" in bash
