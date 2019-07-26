
# Table of Contents

1.  [What is it?](#org4015808)
2.  [Detailed example](#org6fc8a9b)
    1.  [Project creation](#orgbca92e6)
        1.  [Project file listings](#orgecda087)
    2.  [Build dir creation](#org06a682d)
    3.  [Project compilation](#org1c632d8)
    4.  [Running unit tests](#org956a701)
    5.  [Project installation](#org455aacc)
3.  [Further information](#orgfe161ec)



<a id="org4015808"></a>

# What is it?

A simple script to create a [Meson](https://mesonbuild.com) C++ project skeleton with [Doxygen](http://www.doxygen.nl/) and
[gtest](https://github.com/google/googletest) configured.

**Usage:**

    ./create_project --help 

    Usage: create_project PROJECT_ROOT_DIRECTORY -f
    
       Creates a directory 'PROJECT_ROOT_DIRECTORY' and populates it with some configuration files.
       By default *never* overwrite files, use -f to force overwriting


<a id="org6fc8a9b"></a>

# Detailed example


<a id="orgbca92e6"></a>

## Project creation

To create a skeleton project in `/tmp/MyProject`, run

    ./create_project /tmp/MyProject 

    Create directory /tmp/MyProject
    Create file      /tmp/MyProject/meson.build
    Create directory /tmp/MyProject/src
    Create file      /tmp/MyProject/src/meson.build
    Create directory /tmp/MyProject/src/MyProject
    Create file      /tmp/MyProject/src/MyProject/meson.build
    Create file      /tmp/MyProject/src/MyProject/hello.hpp
    Create file      /tmp/MyProject/src/MyProject/hello.cpp
    Create directory /tmp/MyProject/subprojects
    Create file      /tmp/MyProject/subprojects/gtest.wrap
    Create directory /tmp/MyProject/test
    Create file      /tmp/MyProject/test/meson.build
    Create file      /tmp/MyProject/test/hello.cpp
    Create directory /tmp/MyProject/bin
    Create file      /tmp/MyProject/bin/meson.build
    Create file      /tmp/MyProject/bin/hello.cpp
    Create directory /tmp/MyProject/sandbox
    Create file      /tmp/MyProject/sandbox/meson.build
    Create file      /tmp/MyProject/sandbox/hello.cpp
    Create directory /tmp/MyProject/doc
    Create directory /tmp/MyProject/doc/figures
    Create file      /tmp/MyProject/doc/meson.build
    Create file      /tmp/MyProject/doc/Doxyfile.in
    Create file      /tmp/MyProject/doc/bibliography.bib

You get a basic Meson project with the following directory hierarchy:

    + bin/                           <- binaries (installed)
    	+ hello.cpp
    	+ meson.build
    + doc/
    	+ bibliography.bib       <- doxygen + bibliography file (installed)  
    	+ Doxyfile.in
    	+ meson.build
    + sandbox/                       <- "sandbox" binaries (not installed)
    	+ hello.cpp
    	+ meson.build
    + meson.build                    <- root meson file
    + src/
    	+ MyProject/             <- your "MyProject" library (installed)
    		+ hello.cpp
    		+ hello.hpp
    		+ meson.build
    	+ meson.build
    + subprojects/                   <- subprojects, here gtest (not installed)
    	+ gtest.wrap
    + test/                          <- unit tests (not installed)
    	+ hello.cpp
    	+ meson.build

<div class="NOTES">
**Note:** if you rerun 

    ./create_project /tmp/MyProject

you will get this error message:

    Create directory /tmp/MyProject
    File /tmp/MyProject/meson.build already exists, aborted! (you can use -f to force overwrite)

The `create_project` never overwrite files unless you use the `-f` flag

    ./create_project /tmp/MyProject -f

</div>


<a id="orgecda087"></a>

### Project file listings

The created `hello` files are as follows.

1.  The `MyProject` library

        cat /tmp/MyProject/src/MyProject/hello.hpp
    
        /// @file
        /// @brief Hello lib file
        include <string>
        
        namespace MyProject {
        
          /// @brief A function 
          ///
          /// A bibtex reference example @cite pakkanen_meson_build_system
          ///
          /// Usage example @include hello.cpp
          std::string hello();
            
        }
    
        cat /tmp/MyProject/src/MyProject/hello.cpp
    
        
        include "hello.hpp"
        
        namespace MyProject {
        
          std::string hello() {
            return "Hello MyProject";
          };
            
        }

2.  The project unit tests

        cat /tmp/MyProject/test/hello.cpp
    
        
        include "MyProject/hello.hpp"
        include <gtest/gtest.h>
        
        using namespace MyProject;
        
        TEST(Hello, demo)
        {
          ASSERT_EQ(hello(),"Hello MyProject");
        }

3.  The `bin/hello.cpp` file

        cat /tmp/MyProject/bin/hello.cpp
    
        /// @file
        /// @brief Hello executable file
        include "MyProject/hello.hpp"
        include <iostream>
        
        using namespace MyProject;
        
        int main()
        {
          std::cout << hello() << std::endl;
        }

4.  The `sandbox/hello.cpp` file

        cat /tmp/MyProject/bin/hello.cpp
    
        /// @file
        /// @brief Hello executable file
        include "MyProject/hello.hpp"
        include <iostream>
        
        using namespace MyProject;
        
        int main()
        {
          std::cout << hello() << std::endl;
        }


<a id="org06a682d"></a>

## Build dir creation

To create your project build directory with `/tmp/InstallDirectory` as
installation directory run:

    cd /tmp/MyProject
    meson build --prefix=/tmp/InstallDirectory

you will get 

    The Meson build system
    Version: 0.49.2
    Source dir: /tmp/MyProject
    Build dir: /tmp/MyProject/build
    Build type: native build
    Project name: MyProject
    Project version: 0.0.1
    Native C++ compiler: ccache c++ (gcc 8.3.0 "c++ (Debian 8.3.0-6) 8.3.0")
    Build machine cpu family: x86_64
    Build machine cpu: x86_64
    Downloading gtest source from https://github.com/google/googletest/archive/release-1.8.0.zip
    Download size: 1586287
    Downloading: ..........
    Downloading gtest patch from https://wrapdb.mesonbuild.com/v1/projects/gtest/1.8.0/5/get_zip
    Download size: 2084
    Downloading: ..........
    
    |
    |Executing subproject gtest 
    |
    |Project name: gtest
    |Project version: 1.8.0
    |Native C++ compiler: ccache c++ (gcc 8.3.0 "c++ (Debian 8.3.0-6) 8.3.0")
    |Dependency threads found: YES 
    |Dependency threads found: YES (cached)
    |Dependency threads found: YES (cached)
    |Dependency threads found: YES (cached)
    |Build targets in project: 0
    |
    |Subproject gtest finished.
    Program doxygen found: YES (/usr/bin/doxygen)
    Program dot found: YES (/usr/bin/dot)
    Configuring Doxyfile using configuration
    Build targets in project: 5
    Found ninja-1.8.2 at /usr/bin/ninja


<a id="org1c632d8"></a>

## Project compilation

To compile your project (with its doxygen documentation) type:

    cd build
    ninja 

you will get:

    
    [1/12] Compiling C++ object 'src/MyProject/63d4f26@@MyProject@sha/hello.cpp.o'.
    [2/12] Compiling C++ object 'sandbox/b7ad567@@hello@exe/hello.cpp.o'.
    [3/12] Linking target src/MyProject/libMyProject.so.
    [4/12] Compiling C++ object 'bin/51a1f05@@hello@exe/hello.cpp.o'.
    [5/12] Generating doc with a custom command.
    warning: ignoring unknown tag `y' at line 2277, file doc/Doxyfile
    Searching for include files...
    Searching for files in directory /tmp/MyProject/src/MyProject
    Searching for files in directory /tmp/MyProject/bin
    Searching for example files...
    Searching for files in directory /tmp/MyProject/sandbox
    Searching for images...
    Searching for files in directory /tmp/MyProject/doc/figures
    Searching for dot files...
    Searching for msc files...
    Searching for dia files...
    Searching for files to exclude
    Searching INPUT for files to process...
    Searching for files in directory /tmp/MyProject/src/MyProject
    Searching for files in directory /tmp/MyProject/bin
    Reading and parsing tag files
    Parsing files
    Preprocessing /tmp/MyProject/src/MyProject/hello.cpp...
    Parsing file /tmp/MyProject/src/MyProject/hello.cpp...
    Preprocessing /tmp/MyProject/src/MyProject/hello.hpp...
    Parsing file /tmp/MyProject/src/MyProject/hello.hpp...
    Preprocessing /tmp/MyProject/bin/hello.cpp...
    Parsing file /tmp/MyProject/bin/hello.cpp...
    Building group list...
    Building directory list...
    Building namespace list...
    Building file list...
    Building class list...
    Associating documentation with classes...
    Computing nesting relations for classes...
    Building example list...
    Searching for enumerations...
    Searching for documented typedefs...
    Searching for members imported via using declarations...
    Searching for included using directives...
    Searching for documented variables...
    Building interface member list...
    Building member list...
    Searching for friends...
    Searching for documented defines...
    Computing class inheritance relations...
    Computing class usage relations...
    Flushing cached template relations that have become invalid...
    Computing class relations...
    Add enum values to enums...
    Searching for member function documentation...
    Creating members for template instances...
    Building page list...
    Search for main page...
    Computing page relations...
    Determining the scope of groups...
    Sorting lists...
    Freeing entry tree
    Determining which enums are documented
    Computing member relations...
    Building full member lists recursively...
    Adding members to member groups.
    Computing member references...
    Inheriting documentation...
    Generating disk names...
    Adding source references...
    Adding xrefitems...
    Sorting member lists...
    Generating citations page...
    Counting data structures...
    Resolving user defined references...
    Finding anchors and sections in the documentation...
    Transferring function references...
    Combining using relations...
    Adding members to index pages...
    Generating style sheet...
    Generating search indices...
    Generating example documentation...
    Generating file sources...
    Generating code for file /tmp/MyProject/src/MyProject/hello.hpp...
    Generating file documentation...
    Generating docs for file /tmp/MyProject/bin/hello.cpp...
    Generating docs for file /tmp/MyProject/src/MyProject/hello.hpp...
    Generating page documentation...
    Generating docs for page citelist...
    Generating group documentation...
    Generating class documentation...
    Generating namespace index...
    Generating graph info page...
    Generating directory documentation...
    Generating index page...
    Generating page index...
    Generating module index...
    Generating namespace index...
    Generating namespace member index...
    Generating annotated compound index...
    Generating alphabetical compound index...
    Generating hierarchical class index...
    Generating member index...
    Generating file index...
    Generating file member index...
    Generating example index...
    finalizing index lists...
    writing tag file...
    Running dot...
    Running dot for graph 1/1
    lookup cache used 6/65536 hits=10 misses=6
    finished...
    [6/12] Generating symbol file 'src/MyProject/63d4f26@@MyProject@sha/libMyProject.so.symbols'.
    [7/12] Linking target sandbox/hello.
    [8/12] Compiling C++ object 'test/9f86d08@@hello_exe@exe/.._subprojects_googletest-release-1.8.0_googletest_src_gtest_main.cc.o'.
    [9/12] Linking target bin/hello.
    [10/12] Compiling C++ object 'test/9f86d08@@hello_exe@exe/hello.cpp.o'.
    [11/12] Compiling C++ object 'test/9f86d08@@hello_exe@exe/.._subprojects_googletest-release-1.8.0_googletest_src_gtest-all.cc.o'.
    [12/12] Linking target test/hello_exe.

<div class="NOTES">
**Note:**
You can visualize the generated documentation with:

    chromium doc/html/index.html 

</div>


<a id="org956a701"></a>

## Running unit tests

To run unit tests, run:

    ninja test

you will get:

    [0/1] Running all tests.
    1/1 hello_test                              OK       0.01 s 
    
    Ok:                    1
    Expected Fail:         0
    Fail:                  0
    Unexpected Pass:       0
    Skipped:               0
    Timeout:               0
    
    Full log written to /tmp/MyProject/build/meson-logs/testlog.txt


<a id="org455aacc"></a>

## Project installation

Finally, if you want to install your project, run:

    ninja install 

you will get:

    [0/1] Installing files.
    Installing src/MyProject/libMyProject.so to /tmp/InstallDirectory/lib/x86_64-linux-gnu
    Installing bin/hello to /tmp/InstallDirectory/bin
    Installing /tmp/MyProject/build/doc/html/tab_h.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/sync_on.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/graph_legend.md5 to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/dir_68267d1309a1af8e8297ef4c3efbcdba.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/pages.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/jquery.js to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/menu.js to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/folderclosed.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/dir_12739b51ecd9f955b0759ea118dd308c.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/hello_8hpp_source.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/nav_f.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/tab_s.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/splitbar.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/graph_legend.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/closed.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/bdwn.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/tabs.css to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/doxygen.css to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/citelist.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/index.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/tab_a.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/bin_2hello_8cpp.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/open.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/menudata.js to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/hello_8hpp.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/graph_legend.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/nav_h.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/bc_s.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/doxygen.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/nav_g.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/dynsections.js to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/dir_2ea30aa2956a8db99dd22aa5e597f384.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/files.html to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/sync_off.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/folderopen.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/tab_b.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/doc.png to /tmp/InstallDirectory/share/doc/html
    Installing /tmp/MyProject/build/doc/html/search/all_1.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/search_r.png to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/all_0.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/close.png to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/search.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/search_l.png to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/files_0.html to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/searchdata.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/nomatches.html to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/pages_0.html to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/all_0.html to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/mag_sel.png to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/search.css to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/pages_0.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/all_1.html to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/search_m.png to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/build/doc/html/search/files_0.js to /tmp/InstallDirectory/share/doc/html/search
    Installing /tmp/MyProject/src/MyProject/hello.hpp to /tmp/InstallDirectory/include/MyProject


<a id="orgfe161ec"></a>

# Further information

Some Meson official site links:

-   [How do I do X in Meson?](https://mesonbuild.com/howtox.html)
-   [Meson's reference manual](http://mesonbuild.com/Reference-manual.html)

