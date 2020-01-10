***CUDA GPU-ENABLED FFMPEG***

The following installation steps assume that you are logged in as the root user.

* Start off with a clean installation of Ubuntu 18.04 LTS.  This should also work with Ubuntu 16.04, but 18.04 LTS is recommended.

* Log in as the root user.  If you've logged in as a non-root user with sudo rights, escalate by running:

```bash
sudo su
```

* Update the current system and install software-properties-common:

```bash
apt-get update
apt-get dist-upgrade -y
apt-get install software-properties-common -y
```

* Install dependencies:

```bash
add-apt-repository ppa:longsleep/golang-backports
```

Press "Y" or "Enter" as per the on-screen instructions.  Then continue:

```
apt-get update
apt-get dist-upgrade -y
apt-get install -y build-essential pkg-config autoconf gnutls-dev git curl golang-go
```

* Build and install NASM:

```bash
git clone -b nasm-2.14.02 https://repo.or.cz/nasm.git "$HOME/nasm"
cd "$HOME/nasm"
./autogen.sh
./configure
make
make install
```

NOTE: While installing NASM, expect a failure during installation of man pages / documentation.  It should be OK otherwise - It's safe to ignore these errors.

* Build and install x264:

```bash
git clone http://git.videolan.org/git/x264.git "$HOME/x264"
cd "$HOME/x264"
git checkout 545de2ffec6ae9a80738de1b2c8cf820249a2530
./configure --enable-pic --enable-static --disable-cli
make
make install-lib-static
```

* Install NV Codec Headers:

```bash
git clone --single-branch https://git.videolan.org/git/ffmpeg/nv-codec-headers.git "$HOME/nv-codec-headers"
cd "$HOME/nv-codec-headers"
git checkout 250292dd20af60edc6e0d07f1d6e489a2f8e1c44
make install
cd ..
rm -rf "$HOME/nv-codec-headers"
```

Now that we've downloaded and installed most of our dependencies, it's time to build FFMPEG.

* We will need to define the following environment:

```bash
export PATH=/usr/local/cuda/bin:$HOME/compiled/bin:$PATH
export PKG_CONFIG_PATH=$HOME/compiled/lib/pkgconfig
```

* Pull down FFMPEG source code:

```bash
git clone https://git.ffmpeg.org/ffmpeg.git "$HOME/ffmpeg"
```

* Configure FFMPEG's build system:

```bash
cd "$HOME/ffmpeg"
git checkout 3ea705767720033754e8d85566460390191ae27d
./configure --disable-static --enable-shared \
        --enable-gpl --enable-nonfree --enable-libx264 --enable-cuda --enable-cuvid \
        --enable-nvenc --enable-cuda-nvcc --enable-libnpp --enable-gnutls \
        --extra-ldflags=-L/usr/local/cuda/lib64 \
        --extra-cflags='-pg -I/usr/local/cuda/include' --disable-stripping
```

* Build and install FFMPEG:

```bash
make
make install
```

--

Continue on to [cuda-livepeer-setup.md](cuda-livepeer-setup.md)
