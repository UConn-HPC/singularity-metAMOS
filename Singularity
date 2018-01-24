# -*- mode: rpm-spec -*-

Bootstrap: docker
From: ubuntu:10.04

%labels
    Maintainer Pariksheet Nanda

%post
    # Install OS packages.
    # Download OS updates from old-releases.ubuntu.com instead of
    # archive.ubuntu.com
    sed -i "s#archive#old-releases#g" /etc/apt/sources.list
    apt-get -y update
    dev="wget curl unzip gcc g++"
    python="python-psutil cython python-matplotlib"
    pysam="python-dev zlib1g-dev"
    apt-get -y install $dev $python $pysam
    # Download metAMOS if needed.
    prefix=/usr/bin
    version=1.5rc3
    url=https://github.com/marbl/metAMOS/archive/v${version}.tar.gz
    # Don't download the tarball into the image.
    test -z $TMPDIR && TMPDIR=/tmp
    cd $TMPDIR
    tarball=$(basename $url)
    tardir=metAMOS-${version}
    [ -f $tarball ] || wget --no-check-certificate -O $tarball $url
    rm -rf $tardir
    [ -d $tardir ] || tar -xf $tarball
    cd $tardir
    SHELL=/bin/sh python INSTALL.py core <<ANSWERS
N
ANSWERS
    #python INSTALL.py iMetAMOS
