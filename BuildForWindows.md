
Set Env Variable
----------------
Choose any directory for working directory
```
set WORKDIR=c:\swiftbuild
```

Install Packages
----------------------
```


Install cmake-3.5.2
  1) Download from https://cmake.org/download/
  2) set PATH=%PATH%;C:\Program Files (x86)\CMake\bin

Install Python 2.7.11
  1) Download from https://www.python.org/downloads/
  2) set PATH=%PATH%;C:\Python27

Install libxml2-2.9.3
  1) Download
	http://xmlsoft.org/sources/win32/64bit/libxml2-2.9.3-win32-x86_64.7z
  2) Extract and check directory structure 
        %WORKDIR%\libxml2\include\libxml2\libxml
		%WORKDIR%\libxml2\lib

Install pkg-config_0.26-1
  1) Download 3 files
    http://ftp.gnome.org/pub/gnome/binaries/win32/dependencies/pkg-config_0.26-1_win32.zip
    http://ftp.gnome.org/pub/gnome/binaries/win32/dependencies/gettext-runtime_0.18.1.1-2_win32.zip
    http://ftp.gnome.org/pub/gnome/binaries/win32/glib/2.28/glib_2.28.8-1_win32.zip
  2) Extract and check directory structure 
		C:\pkg-config\bin\pkg-config.exe
		C:\pkg-config\bin\libglib-2.0-0.dll
		C:\pkg-config\bin\intl.dll
  (referenced: http://stackoverflow.com/questions/1710922/how-to-install-pkg-config-in-windows)

Install icu4c-56
  1) Download
    http://site.icu-project.org/download/56#TOC-ICU4C-Download
	choose icu4c-56_1-Win64-msvc10.zip
  2) Extract and check directory structure 
	  %WORKDIR%\icu\bin64
	  %WORKDIR%\icu\include
	  %WORKDIR%\icu\lib64
  
Install Ninja
  1) Download
    https://github.com/ninja-build/ninja/releases
	Choose ninja-win.zip
  2) Make sure your PATH include the installed directory```
```

Patch cmake
-----------
Backup and overwrite ```Windows-Clang.cmake```
```
     Copy swift/misc/Windows-Clang.cmake in repository
     to C:\Program Files (x86)\CMake\share\cmake-3.5\Modules\Platform
```

More Install
------------
```
Install uuid.zip
  1) Download
    http://XXXXXX
  2) Extract and check directory structure 
     %WORKDIR%\uuid\include
	 %WORKDIR%\uuid\lib
	 %WORKDIR%\uuid\uuid.c
    (source is included - dummy file)

Install utils.zip (Python invoker)
  1) Download
    http://XXXXXX
  2) Extract and check directory structure 
     %WORKDIR%\swift\utils\gyb.exe
	 %WORKDIR%\swift\utils\line-directive.exe
	 %WORKDIR%\swift\utils\gyb.c
     (source is included - /misc/gyb.cpp)

Prepare Cygwin Tool
  - You need dlltool.exe in Cygwin.
  - You need swift.exe built under Cygwin.
     swift.exe for MSVC will be built, but we should use Cygwin version to build the standard library.
	 
Prepare Favorate Hexa Editor.
  - HxD will be good if you don't have other. ( https://mh-nexus.de/en/hxd )
```

Download sources
----------------
```
  export WORK_DIR=<working directory>
  cd $WORK_DIR
  
  git clone https://github.com/tinysun212/swift-windows.git swift
  git clone https://github.com/tinysun212/swift-llvm-cygwin.git llvm
  git clone https://github.com/tinysun212/swift-clang-cygwin.git clang
  git clone https://github.com/apple/swift-cmark.git cmark
  git clone https://github.com/ninja-build/ninja.git

  cd swift; git checkout swift-cygwin-20160428 ; cd ..
  cd llvm; git checkout swift-cygwin-20160428 ; cd ..
  cd clang; git checkout swift-cygwin-20160428 ; cd ..
  cd cmark; git checkout 6873b; cd ..
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
  TBD
