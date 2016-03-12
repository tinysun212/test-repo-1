
Install cygwin64 packages
-------------------------
```
 Devel/git                2.7.0-1
      /cmake              3.3.2-1   -- we will use other build
      /clang              3.7.1-1   -- we will use other build
      /gcc-core           5.3.0-3
      /gcc-g++            5.3.0-3
      /swig               3.0.7-1
      /pkg-config         0.29-1
  Libs/libuuid-devel      2.25.2-2
      /libicu-devel       56.1-1
      /libedit-devel      20130712-1
      /libxml2-devel      2.9.3-1
      /libsqlite3_0       3.11.0-2
      /libstdc++6         5.3.0-3
      /libncurses-devel   6.0-4.20160305
      /libiconv-devel     1.14-3
```
// NOW USE clang 3.7.1-1 from Cygwin released
Prepare Clang 3.8 version
-------------------------
  You can download it in https://github.com/tinysun212/swift-cygwin-bin or build yourself.
  If you build the swift binary successfully, you will also get your clang build.

// NOW NO NEED TO PATCH cc, c++, clang, clang++
Replace cc, c++, clang, clang++
-------------------------------
```
  cd /usr/bin
  rm c++
  rm cc
  rm clang++
  mv clang.exe clang.old.exe
  ln -s clang-3.8.exe c++
  ln -s clang-3.8.exe cc
  ln -s clang-3.8.exe clang++.exe
  ln -s clang-3.8.exe clang.exe
  
  cd /usr/lib/gcc/i686-pc-cygwin
  ln -s 4.9.3  4.7.3

  cd /usr/lib/gcc/i686-pc-cygwin/4.7.3/include/c++
  ln -s x86_64-pc-cygwin i686-pc-cygwin

  cd /usr/lib/clang
  ln -s x86_64-pc-cygwin/3.5.2  3.8.0
```

// NOW USE gcc 5.3.0-3 library set
Patch gcc header
----------------
  
  The header file 'c++config.h' must be modified. (remove a define)
```
  Edit /usr/lib/gcc/x86_64-pc-cygwin/5.3.0/include/c++/x86_64-pc-cygwin/bits/c++config.h Line 980
	Change definition of _GLIBCXX_HAVE_TLS as follows
      #define _GLIBCXX_HAVE_TLS 1
      #if defined (__clang__)
      #undef _GLIBCXX_HAVE_TLS
      #endif    
``` 

// NEED CLANG header patch
Patch clang header
------------------

  The header file 'limits.h' must be modified.
```
  Edit /usr/lib/clang/3.7.1/include/limits.h Line 28
        Insert a line which defines _GCC_NEXT_LIMITS_H as follows
    #ifndef __CLANG_LIMITS_H
    #define __CLANG_LIMITS_H

->  #define _GCC_NEXT_LIMITS_H
    /* The system's limits.h may, in turn, try to #include_next GCC's limits.h.
       Avert this #include_next madness. */
```

Download sources
----------------
```
  export WORK_DIR=<working directory>
  cd $WORK_DIR
  
  git clone https://github.com/tinysun212/swift-cygwin.git swift
  git clone https://github.com/tinysun212/swift-llvm-cygwin.git llvm
  git clone https://github.com/tinysun212/swift-clang-cygwin.git clang
  git clone https://github.com/apple/swift-lldb.git lldb
  git clone https://github.com/apple/swift-cmark.git cmark
  git clone https://github.com/apple/swift-llbuild.git llbuild
  git clone https://github.com/apple/swift-package-manager.git swiftpm
  git clone https://github.com/apple/swift-corelibs-xctest.git
  git clone https://github.com/apple/swift-corelibs-foundation.git
  git clone https://github.com/ninja-build/ninja.git

  cd swift; git checkout swift-cygwin-DEVELOPMENT-SNAPSHOT-2016-02-08-a ; cd ..
  cd llvm; git checkout swift-cygwin-DEVELOPMENT-SNAPSHOT-2016-02-08-a ; cd ..
  cd clang; git checkout swift-cygwin-DEVELOPMENT-SNAPSHOT-2016-02-08-a ; cd ..
  cd lldb; git checkout 952b4; cd ..
  cd cmark; git checkout 6873b; cd ..
  cd llbuild; git checkout a4a79; cd ..
  cd swiftpm; git checkout 3780a; cd ..
  cd swift-corelibs-xctest; git checkout 2f785; cd ..
  cd swift-corelibs-foundation; git checkout 720a9; cd ..
  cd ninja; git checkout 2eb1cc9; cd ..
```
  
Build
-----
```
  cd $WORK_DIR/swift
  utils/build-script -R
```
  
Build Troubleshoot
------------------
Currently, the build processing needs some manual works.
```
1.  ninja: error: unknown target 'swift-test-stdlib-cygwin-x86_64'

    /usr/bin/cmake --build /cygdrive/c/Work/swift_msvc/build/Ninja-ReleaseAssert/swift-cygwin-x86_64 -- -j3 all


----
2.  clang-3.8: error: no such file or directory: 'lib/libclang.dll.a'

    cd $WORK_DIR/build/*eAssert/llvm*
    cp -p bin/cygclang.dll lib/libclang.dll.a   

    cd $WORK_DIR/swift
    utils/build-script -R

3. ninja: error: unknown target 'swift-stdlib-cygwin-x86_64'

    /usr/bin/cmake --build $WORK_DIR/build/*eAssert/swift-cygwin-x86_64 -- -j3 all
    
4.  Linking CXX executable bin/swift.exe

    cd $WORK_DIR/build/*eAssert/cmark*/src
    cp -p cygcmark-0.22.0.dll libcmark.a
    cd $WORK_DIR/build/*eAssert/swift*/bin
    cp -p ../../cmar*/src/cygcmark-0.22.0.dll .

    cd $WORK_DIR/build/*eAssert/swift*
    ../ninja*/ninja

5.  clang: error: no such file or directory: 'lib/swift/windows/x86_64/libswiftCore.dll.a'

    cd $WORK_DIR/build/*eAssert/swift*/lib/swift/windows/x86_64
    ln -s ../cygswiftCore.dll libswiftCore.dll.a 

    cd $WORK_DIR/build/*eAssert/swift*
    ../ninja*/ninja
```	
