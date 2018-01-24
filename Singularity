# -*- mode: rpm-spec -*-

Bootstrap: docker
From: ubuntu

%labels
    Maintainer Pariksheet Nanda

%post
    # Install OS packages.
    apt-get -y update
    dev="wget curl unzip gcc g++ make"
    python="python-psutil python-pysam python-matplotlib cython python-setuptools"
    apt-get -y install $dev $python
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
