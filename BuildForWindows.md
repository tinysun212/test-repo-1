
Set Env Variable
----------------
Choose any directory for working directory
```
set WORKDIR=c:\swiftbuild
```

Install Packages
----------------------
```
Install Visual Studio Community 2015 Update 2

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

Prepare Cygwin Tool and Swift Cygwin port.
  - You will need bash in Cygwin to run sed.
  - You will need dlltool.exe in Cygwin.
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

Build cmark
-----------
```
Run "VS2015 x64 Native Tool Command Prompt"

mkdir %WORKDIR%\build\NinjaMSVC\cmark
cd %WORKDIR%\build\NinjaMSVC\cmark
cmake -G "Visual Studio 14 2015 Win64" -D CMAKE_BUILD_TYPE=RELEASE ..\..\..\cmark
"c:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" /p:Configuration=Release ALL_BUILD.vcxproj 
```

Build clang
-----------
```
Run "VS2015 x64 Native Tool Command Prompt" in ADMINISTRATOR mode (to run mklink.exe)

cd %WORKDIR%\llvm\tools
mklink /d clang ..\..\clang

mkdir %WORKDIR%\build\NinjaMSVC\llvm
cd %WORKDIR%\build\NinjaMSVC\llvm
cmake -G "Visual Studio 14 2015 Win64" -D CMAKE_BUILD_TYPE=RELEASE ..\..\..\llvm
"c:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" /p:Configuration=Release ALL_BUILD.vcxproj 
```

Build Swift
-----------
```
Run "VS2015 x64 Native Tool Command Prompt"
// You will use cmake, python, ninja, llvm tools here.

set PATH=%WORKDIR%\build\NinjaMSVC\llvm\release\bin;%PATH%

mkdir %WORKDIR%\build\NinjaMSVC\swift\bin
Following DLL's must be copied to %WORKDIR%/build/NinjaMSVC/swift/bin
  cmark.dll
  icudt56.dll
  icuin56.dll
  icuuc56.dll

cd %WORKDIR%\build\NinjaMSVC\swift

cmake -G Ninja ..\..\..\swift -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=clang  -DCMAKE_CXX_COMPILER=clang -DLIBXML2_LIBRARIES=%WORKDIR%\libxml2\lib\libxml2.a  -DLIBXML2_INCLUDE_DIR=%WORKDIR%\libxml2\include\libxml2  -DPKG_CONFIG_EXECUTABLE=c:\pkg-config\bin\pkg-config.exe -DUUID_INCLUDE_DIR=%WORKDIR%\uuid\include -DUUID_LIBRARY=%WORKDIR%\uuid\lib\uuid.lib -DICU_UC_INCLUDE_DIR=%WORKDIR%\icu\include -DICU_UC_LIBRARIES=%WORKDIR%\icu\lib64\icuuc.lib -DICU_I18N_INCLUDE_DIR=%WORKDIR%\icu\include -DICU_I18N_LIBRARIES=%WORKDIR%\icu\lib64\icuin.lib -DSWIFT_INCLUDE_DOCS=FALSE -DSWIFT_PATH_TO_CMARK_BUILD=%WORKDIR%\build\NinjaMSVC\cmark -DSWIFT_PATH_TO_CMARK_SOURCE=%WORKDIR%\cmark  -DCMAKE_CXX_FLAGS="-fms-extensions -fms-compatibility-version=19 -frtti " ..\..\..\swift

(In Cygwin64 Terminal)
// change to the directory %WORKDIR%\build\NinjaMSVC\swift
sed	-e 's;libclang\([^.]*\).a;clang\1.lib;g' \
	-e 's;swift\\libcmark.a;build\\NinjaMSVC\\cmark\\src\\Release\\cmark.lib;g' \
	-e 's;swift swiftc;swift.exe swiftc.exe;' \
	-e 's;swift swift-autolink-extract;swift.exe swift-autolink-extract.exe;' \
	-e 's;-fno-rtti ;;' \
	-e 's;DEFINES = -DGTEST_HAS_RTTI;DEFINES = -D_MT -D_DLL -DGTEST_HAS_RTTI;' \
	-e 's;-Wl,--allow-multiple-definition;-Wl,/FORCE:MULTIPLE;' \
    -e 's;LINK_FLAGS = -target;LINK_FLAGS = -Wl,msvcrt.lib -target;' \
	-e 's;LINK_PATH = -LC:;LINK_PATH = -Wl,/LIBPATH:C$:;' \
    -e 's;-ledit ;;g' \
	build.ninja > tt; mv tt build.ninja

Run
  cd %WORKDIR%\build\NinjaMSVC\swift
  ninja
```

Compile Swift.obj
-----------------


Build swiftCore (shared)
------------------------
```
**********************************************************************************
*** CAUTION: To build Swift.obj, should use swiftc.exe built on Cygwin
**********************************************************************************

cd %WORKDIR%/build/NinjaMSVC/swift/stdlib/public/core

dlltool -z allexp.orig.def --export-all-symbols ./windows/x86_64/Swift.obj ../../../lib/swift/windows/x86_64/libswiftRuntime.a  ../../../lib/swift/windows/x86_64/libswiftStdlibStubs.a

python %WORKDIR%/swift/misc/trim_exp.py allexp.orig.def allexp.def

(In VS2015 x64 Native Tool Command Prompt)
link /ERRORREPORT:PROMPT /OUT:"%WORKDIR%\build\NinjaMSVC\swift\lib\swift\windows\libswiftCore.dll" /INCREMENTAL:NO /NOLOGO /LIBPATH:%WORKDIR%/build/NinjaMSVC/llvm/Release/lib kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib ..\..\..\lib\swift\windows\x86_64\libswiftRuntime.a ..\..\..\lib\swift\windows\x86_64\libswiftStdlibStubs.a %WORKDIR%\icu\lib64\icuuc.lib %WORKDIR%\icu\lib64\icuin.lib /MANIFEST /MANIFESTUAC:"level='asInvoker' uiAccess='false'" /manifest:embed /PDB:"%WORKDIR%/build/NinjaMSVC/swift/bin/swiftCore.pdb" /SUBSYSTEM:CONSOLE /TLBID:1 /DYNAMICBASE /NXCOMPAT /IMPLIB:"%WORKDIR%\build\NinjaMSVC\swift\lib\swift\windows\libswiftCore.lib" /MACHINE:X64 /DLL %WORKDIR%\build\NinjaMSVC\swift\stdlib\public\core\windows\x86_64\Swift.obj /DEF:allexp.def /MERGE:.rdata=.rodata /IGNORE:4102,4197 msvcrt.lib
```

Build swiftSwiftOnoneSupport (shared)
-------------------------------------
```
**********************************************************************************
*** CAUTION: To build SwiftOnoneSupport.obj, should use swiftc.exe built on MSVC
***          To use with static link, rename .pdata to .qdata with hexa editor
**********************************************************************************

cd %WORKDIR%/build/NinjaMSVC/swift/bin

swiftc -emit-ir -sdk / -target x86_64-pc-windows-msvc -O -I %WORKDIR%/build/NinjaMSVC/swift/lib/swift/windows/x86_64 -module-cache-path %WORKDIR%/build/NinjaMSVC/swift/clang-module-cache -no-link-objc-runtime -Xfrontend -sil-serialize-all -parse-stdlib -module-link-name swiftSwiftOnoneSupport -force-single-frontend-invocation -parse-as-library -emit-module -emit-module-path %WORKDIR%/build/NinjaMSVC/swift/lib/swift/windows/x86_64/SwiftOnoneSupport.swiftmodule -o SwiftOnoneSupport.ll %WORKDIR%/swift/stdlib/public/SwiftOnoneSupport/SwiftOnoneSupport.swift

python %WORKDIR%/swift/misc/inject_dllimport.py SwiftOnoneSupport.ll SwiftOnoneSupport.new.ll

clang -c SwiftOnoneSupport.new.ll -o %WORKDIR%\build\NinjaMSVC\swift\stdlib\public\SwiftOnoneSupport\windows\x86_64\SwiftOnoneSupport.obj -mcmodel=large -target x86_64-pc-windows-msvc19.0.0

cd %WORKDIR%/build/NinjaMSVC/swift/stdlib/public/SwiftOnoneSupport

dlltool -z allexp.orig.def --export-all-symbols ../../../stdlib/public/SwiftOnoneSupport/windows/x86_64/SwiftOnoneSupport.obj

python %WORKDIR%/swift/misc/trim_exp.py allexp.orig.def allexp.def

(In VS2015 x64 Native Tool Command Prompt)
link /ERRORREPORT:PROMPT /OUT:"%WORKDIR%\build\NinjaMSVC\swift\lib\swift\windows\libswiftSwiftOnoneSupport.dll" /INCREMENTAL:NO /NOLOGO /LIBPATH:%WORKDIR%/build/NinjaMSVC/llvm/Release/lib kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib ..\..\..\lib\swift\windows\libswiftCore.lib /MANIFEST /MANIFESTUAC:"level='asInvoker' uiAccess='false'" /manifest:embed /PDB:"%WORKDIR%/build/NinjaMSVC/swift/bin/libswiftSwiftOnoneSupport.pdb" /SUBSYSTEM:CONSOLE /TLBID:1 /DYNAMICBASE /NXCOMPAT /IMPLIB:"%WORKDIR%/build/NinjaMSVC/swift/lib/swift/windows/libswiftSwiftOnoneSupport.lib" /MACHINE:X64 /DLL %WORKDIR%\build\NinjaMSVC\swift\stdlib\public\SwiftOnoneSupport\windows\x86_64\SwiftOnoneSupport.obj    /DEF:allexp.def msvcrt.lib /MERGE:.rdata=.rodata
```

Run with Interpreter
--------------------

Compile & Run with DLL
----------------------

Build Static library
--------------------

Link with Static library
------------------------
