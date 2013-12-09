ZeroVM gcc toolchain
=====
----
_How to build the full toolchain from scratch_

----

1. Install prerequisites

    Ubuntu:

        sudo su -c 'echo "deb [ arch=amd64 ] http://zvm.rackspace.com/v1/repo/ubuntu/ precise main" > /etc/apt/sources.list.d/zerovm-precise.list'
        wget -O- https://zvm.rackspace.com/v1/repo/ubuntu/zerovm.pkg.key | sudo apt-key add -
        sudo apt-get update
        sudo apt-get install build-essential flex git groff libc6-dev-i386 g++-multilib autoconf automake libtool libzmq3-dev bison libglib2.0-dev texinfo pkg-config

2. Set environment variables

    This guide assumes your working directory is `$HOME`, but you can use any base directory.

        export ZEROVM_ROOT=$HOME/zerovm
        export ZVM_PREFIX=$HOME/zvm-root
        export ZRT_ROOT=$HOME/zrt
        export PATH=$ZVM_PREFIX/bin:$PATH

3. Clone things

    ```
    git clone https://github.com/zerovm/zerovm.git $ZEROVM_ROOT
    git clone https://github.com/zerovm/validator.git $ZEROVM_ROOT/valz
    git clone https://github.com/zerovm/zrt.git $ZRT_ROOT
    git clone --recursive https://github.com/zerovm/toolchain.git $HOME/zvm-toolchain
    ```

4. Build zerovm

    ```
    cd $ZEROVM_ROOT/valz
    make validator
    sudo make install
    cd $ZEROVM_ROOT
    make all install PREFIX=$ZVM_PREFIX
    ```

5. Build toolchain

    ```
    cd $HOME/zvm-toolchain
    make -j8
    ```

    If something goes wrong you will need to DELETE everything (apart from zerovm and validator)
    in the $ZVM_PREFIX directory and only then do `make clean` and `make` (this is how the gcc toolchain works, sadly).

    Example of cleanup procedures:

    ```
    cd $HOME/zvm-toolchain
    make clean
    cd $ZVM_PREFIX
    rm -fr *
    cd $ZEROVM_ROOT
    make install PREFIX=$ZVM_PREFIX
    ```

6. Run ZeroVM tests

    ```
    cd $ZEROVM_ROOT
    ./ftests.sh
    ```

7. Install debugger

    Debugger prerequisites:

    ```
    sudo apt-get install libncurses5-dev libexpat1-dev
    ```

    ```
    cd $HOME/zvm-toolchain/SRC/gdb
    mkdir BUILD
    cd BUILD
    ../configure --program-prefix=x86_64-nacl- --prefix=$ZVM_PREFIX
    make -j4
    make install
    ```
