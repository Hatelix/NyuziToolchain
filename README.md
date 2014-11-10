This is a toolchain for a parallel processor architecture called Nyuzi 
(https://github.com/jbush001/NyuziProcessor). It includes a C/C++ 
compiler, assembler, linker and work-in-progress debugger (lldb). Questions
or comments can be directed to the mailing list here:
https://groups.google.com/forum/#!forum/nyuzi-processor-dev

## Building

### Requirements
- gcc 4.8+ or Apple clang 4.2+
- cmake 2.8.8
- python 2.7
- libxml (including headers)
- zlib (including headers)
- bison 2.7+
- flex 2.5+
- swig (http://www.swig.org/) with python wrappers
- libedit (http://thrysoee.dk/editline/)
- ncurses

On Ubuntu, these can be installed by using: 

    sudo apt-get install libxml2-dev cmake gcc g++ python bison flex zlib1g-dev swig python-dev libedit-dev ncurses-dev

(On Fedora, replace python-dev with python-devel)

On MacOS Mavericks, the command line compiler can be installed by typing
`xcode-select --install`. It will also be installed automatically if you 
download XCode from the Mac App Store. I'd recommend installing a package 
manager like MacPorts (https://www.macports.org/) to obtain the remaining 
dependencies. Be sure to open a new terminal to do the build after installing 
MacPorts, because it installs alternate versions of some utilities and 
updates the PATH. Most of the libraries should already be installed by 
default, but Apple likes their command line utilities... finely aged... 
so bison will be out of date and won't work. *However*, if you upgrade 
flex, you'll get a build error, so you'll want to use the system supplied 
version. Here's the port command to install the dependencies:

    sudo port install cmake bison swig swig-python

### Compiling

Assuming the proper packages are installed, configure and build using the following commands. 

```
$ mkdir build
$ cd build
$ cmake ..
$ make
$ sudo make install
```

A few other things to note:

* The name of the target is 'nyuzi'.
* There are also bits of an autoconf based build system in this project.  It doesn't work.
* Using sudo on make install as described can leave files with root ownership in your build directory, which can then cause cryptic build errors later when building as non-root.  Doing a 'sudo chown -R &#x60;whoami&#x60; .' in the build directory will fix this.
* If you want to make changes to the compiler, add '-DCMAKE_BUILD_TYPE=Debug' as a parameter to the cmake command enable a debug build.  This enables the -debug flag for command line tools, which is important to see various transormations and output of intermediate passes.

## Invoking compiler

Once built, the toolchain will be installed into /usr/local/llvm-nyuzi

    /usr/local/llvm-nyuzi/bin/clang -o program.elf test_program.c 

## Running tests

Change PATH environment variable to include the binary directory (build/bin). This is only required 
for llvm-lit based tests. The test can be run as follows (assuming you are at the top of the project 
directory):

```
export PATH=<BUILDDIR>/build/bin:$PATH
llvm-lit test
llvm-lit tools/clang/test/CodeGen/nyuzi*
```

## Running whole program tests

There are a set of tests in https://github.com/jbush001/NyuziProcessor/tree/master/tests/compiler
Each test case is compiled and then run in the instruction set simulator.
The output is checked for validity. This is similar to the test-suite project
in LLVM. Instructions are found in that directory.
