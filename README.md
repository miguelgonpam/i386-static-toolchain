# i386-static-toolchain
A static cross-toolchain for i386. Runs on x86_64 but can compile and manipulate i386 programs.
You can download the last release <a href="https://github.com/miguelgonpam/i386-static-toolchain/releases/tag/i386-static-compiler">here</a>

## Description
<p align="justify">
This repository contains the binaries required to compile and manipulate i386 programs in an x86_64 arch.
The main purpouse is to be able to compile legacy i386 programs (without i686 instructions) the <a href="https://github.com/miguelgonpam/Sim-i386-32bit">i386 simulator</a> can execute. This repository contains the compiled binaries, so no compilation of gcc nor binutils is necessary, because all the time it takes to compile it.
</p>

## Compiling
<p align="justify">
<a href="https://github.com/richfelker/musl-cross-make">Musl-cross-make</a> was used. The process of compiling requires two steps.
  <ul>
    <li> Compiling a native arch static toolchain</li>
    <li> Using that toolchain to compile the cross static toolchain</li>
  </ul>
For example, in this case, the following process was followed.
</p>

```
git clone https://github.com/richfelker/musl-cross-make
cd musl-cross-make

cat>config.mak<<EOF
TARGET = x86_64-linux-musl
OUTPUT = /tmp/x86_64-linux-musl
COMMON_CONFIG += CC="gcc -static --static"
COMMON_CONFIG += CXX="g++ -static --static"
EOF

make -j$(nproc)
sudo make install

cat>config.mak<<EOF
TARGET = i386-linux-musl
OUTPUT = /tmp/i386-linux-musl-cross
COMMON_CONFIG += CC="/tmp/x86_64-linux-musl/bin/x86_64-linux-musl-gcc -static --static"
COMMON_CONFIG += CXX="/tmp/x86_64-linux-musl/bin/x86_64-linux-musl-g++ -static --static"
EOF

make clean
make -j$(nproc)
sudo make install

```
Please change the first target with your current architecture, and the second target with the cross destination architecture.

Watch out compiling for any other arch not supported by <a href="https://github.com/richfelker/musl-cross-make">musl-cross-make</a>.

Result of running `file i386-linux-musl-gcc` must be something like this.
```
i386-linux-musl-cross/bin/i386-linux-musl-gcc: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, with debug_info, not stripped
```

You can find more info on the musl-cross-make's `config.mak.dist` file or <a href="https://github.com/richfelker/musl-cross-make/issues/153">here</a>.
