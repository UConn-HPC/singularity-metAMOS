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
    dev="wget"
    apt-get -y update
    apt-get -y install $dev
    # Download metAMOS if needed.
    prefix=/usr/bin
    version=1.5rc2
    url=ftp://ftp.cbcb.umd.edu/pub/data/treangen/MA_fb_v${version}_linux.tar.gz
    # Don't download the tarball into the image.
    test -z $TMPDIR && TMPDIR=/tmp
    DBDIR=$TMPDIR/DB
    TESTDIR=$TMPDIR/Test
    cd $TMPDIR
    tarball=$(basename $url)
    tardir=metAMOS_v${version}_binary
    # Binary tarball is 1.3 GB!
    test -f $prefix/runPipeline || test -d $tardir || test -f $tarball || wget -O $tarball $url
    test -f $prefix/runPipeline || test -d $tardir || tar -xf $tarball
    # Install files.
    test -f $prefix/initPipeline || cp -av $tardir/initPipeline $prefix/
    test -f $prefix/initPipeline
    test -f $prefix/runPipeline || cp -av $tardir/runPipeline $prefix/
    test -f $prefix/runPipeline
    cd -
    # Don't download the tests and databases into the image, because
    # the tests need write access and the database is large.  Create
    # symlinks instead:
    test -L /usr/bin/Test || ln -sv $DBDIR /usr/bin/Test
    test -L /usr/bin/DB || ln -sv $TESTDIR /usr/bin/DB

    # Make it easy to download and run the tests.
    cat <<EOF > /usr/bin/metamos_tests
#!/usr/bin/env bash

# Note to Singularity maintainer: many of the variables like \$TMPDIR,
# \$DBDIR, \$version, etc have been defined above and will be
# substituted during singularity bootstrap, except where they are
# escaped like \\\$url, \\\$tarball, etc.

fetch_db_if_necessary () {
    # We need data for tests.  The minimal database is 4.2 GB!
    url=ftp://ftp.cbcb.umd.edu/pub/data/treangen/minDBs.tar.gz
    tarball=\$(basename \$url)
    if ! [[ -d $DBDIR ]]; then
	cd $TMPDIR
	if ! [[ -f \$tarball ]]; then
	    wget -O \$tarball \$url
	fi
	tar -xvf \$tarball
    fi
}

fetch_test_if_necessary() {
    # The tests need write access in the Test/ directory.
    url=ftp://ftp.cbcb.umd.edu/pub/data/treangen/MA_fb_v${version}_linux.tar.gz
    tarball=\$(basename \$url)
    tardir=metAMOS_v${version}_binary
    if ! [[ -d $TESTDIR ]]; then
	cd $TMPDIR
	if ! [[ -f \$tarball ]]; then
	    wget -O \$tarball \$url
	fi
	tar -xvf \$tarball
	mv \$tardir/Test .
	rm -rf \$tardir
	# Fix hardcoded paths to initPipeline and runPipeline.
	sed -i -E "s#[.][.]/(initPipeline|runPipeline)#\1#g" $TESTDIR/*.sh
    fi
}

main() {
    # Make errors fatal.
    set -eo pipefail

    fetch_db_if_necessary
    fetch_test_if_necessary

    # Run the tests.
    cd $TESTDIR
    if ! [[ -z "\$@" ]]; then
        bash "\$@"
    else
        bash -x test_create.sh
	# run_test_fast.sh does not clean up after itself.
	rm -rf test3
        bash -x run_test3.sh
    fi
}

# Boilerplate for running main().
[[ "\$0" != "\$BASH_SOURCE" ]] || main "\$@"
EOF
    chmod +x /usr/bin/metamos_tests

%runscript
    /usr/bin/runPipeline

%test
    cat /usr/bin/metamos_tests
