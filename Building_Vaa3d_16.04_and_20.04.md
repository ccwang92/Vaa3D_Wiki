## <font color='red'>This tutorial roots from <a href='https://github.com/Vaa3D/Vaa3D_Wiki/wiki/Build-Vaa3D-on-Linux'>here</a></font>
Note: this is only my own experience of building Vaa3d on Ubuntu 16.04 and 20.04. You should follow the original <a href='https://github.com/Vaa3D/Vaa3D_Wiki/wiki/Build-Vaa3D-on-Linux'>tutorial</a> first. If failed, you may try this one. The pipeline is the same, but some specific operations may change.

## <font color='blue'> PREREQUISITES: Qt and C++ compilier </font>

You should have both Qt and C++ compiler ready on your system.

1. Download the Qt 4.8.6 (qt-everywhere-opensource-src-4.8.6.zip) <a href='https://download.qt.io/archive/qt/'>here</a>.

2. Unpack it to <code>/usr/local/Trolltech</code> such that your qmake path is <code>/usr/local/Trolltech/Qt-4.8.6/bin/qmake</code>.

3. build qt by executing "./configure" and "make" in the terminal in the qt folder (could spend ~30min).

Before configure, you may install some dependency libs:

<pre><code>$sudo apt-get install libfontconfig1-dev libfreetype6-dev libx11-dev libxcursor-dev libxext-dev libxfixes-dev libxft-dev libxi-dev libxrandr-dev libxrender-dev</code></pre>
Their functions can be found <a href='https://doc.qt.io/archives/qt-4.8/requirements-x11.html'>here</a>. Otherwise the GUI may look very old-fashioned.

If encounter error "You don't seem to have 'make' or 'gmake' in your PATH. cannot proceed", refer to <a href='https://stackoverflow.com/questions/7353703/error-while-configuring-qt-4-6-2-on-ubuntu-you-dont-seem-to-have-make-or'>this</a>: using 'unzip -a *.zip' to unzip the Qt source code.

If encounter error "bash: ./configure: /bin/sh^M: bad interpreter: No such file or directory", refer to <a href='https://stackoverflow.com/questions/2920416/configure-bin-shm-bad-interpreter'>this</a>: using doc2unix to change the format of ./configure.

If no makefile generated by configure, install 'libxext-dev' first.

3. Permanently add your qmake path to the system path:
    <ul><li>Edit the <code>~/.bashrc</code> file with a command line editor (e.g. <code>  nano ~/.bashrc</code>)</li>

    <li>Add the line to the end of it:
    <pre><code>  export PATH="/usr/local/Trolltech/Qt-4.8.6/bin:$PATH"    </code></pre></li></ul>

    <blockquote>This declares the new location <code>/usr/local/Trolltech/Qt-4.8.6/bin</code> as well as the original path declared as <code>$PATH</code>.</blockquote>

    <ul><li>Save the file in <code>nano</code> by clicking <code>control+o</code> and confirming the name of the file is <code>.bashrc</code> by hitting return. And the <code>control+x</code> to exit <code>nano</code>
    </li>
    <li>relaunch the Terminal and check that your <code>qmake</code> works by typing
    <pre><code>  $ qmake --version    </code></pre>
    </li></ul> <blockquote>which should produce
    <pre><code> QMake version 2.01a
    Using Qt version 4.8.6 in /usr/local/Trolltech/Qt-4.8.6/lib    </code></pre></blockquote>



<b>Note</b>:

For those who have installed Qtlib using Qt Installer directly, and at the same time also have other Qt versions installed, the computer might be messed up in a way it might not be clear which Qt library would be linked during building.  To resolve the issue, one can revise the  corresponding building script located inside the v3d_external repository (see the download instructions in the next session): v3d_external/build.macx for Mac and v3d_external/build.linux for Linux, by adding the following statements at the beginning (let's use Qt 4.8.6 as an example)

<pre><code>QT4=&lt;QT_4.8.6_PATH&gt;
PATH=$QT4/bin:$PATH
QMAKESPEC=$QT4/mkspecs/&lt;spec&gt;
</code></pre>

Here <QT_4.8.6_PATH> is the placeholder for the directory where Qt 4.8.6 is installed and <code>&lt;spec</code>> is the placeholder for specification. For example, <code>&lt;spec</code>> can be macx-g++ for Mac OS and linux-g++ for Linux, depending on what C++ complier you want to use.

## Things to do for Ubuntu
### Downgrade GCC
To compile Vaa3D on Ubuntu 16+, your GCC should be downgraded to GCC4.8 to be compatible with the ITK packages used in Vaa3D. 
To install GCC4.8 on 20.04, you may try two ways:

Way One:
1. Open source.list:
<pre><code>$ gedit /etc/apt/sources.list</code></pre>
2. Add two lines at the end of sources.list:
<pre><code>deb http://dk.archive.ubuntu.com/ubuntu/ trusty main universe
deb http://dk.archive.ubuntu.com/ubuntu/ trusty-updates main universe
</code></pre>
3. Then in the terminal:
<pre><code>$ sudo apt-get update
$ sudo apt-get install gcc-4.8
$ sudo apt-get install g++-4.8
</code></pre>
Way Two (PPA):
<pre><code>$ sudo apt-get install python-software-properties
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
$ sudo apt-get update
$ sudo apt-get install gcc-4.8
$ sudo apt-get install g++-4.8
</code></pre>

After successfully installing GCC4.8, set it as the priority with
<code><pre> $ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 40 --slave /usr/bin/g++ g++ /usr/bin/g++-4.8 </code></pre> 
OR
<code><pre> $ sudo update-alternatives --config gcc</code></pre>
Select the index of gcc 4.8 and enter.


### Make sure `opengl-dev` is installed
<code><pre> $ sudo apt-get install mesa-common-dev
$ sudo apt-get install libglu1-mesa-dev
</code></pre>
To successfully build terafly, you may also need to install the libhdf5-dev
<code><pre>$ sudo apt-get install libhdf5-dev
</code></pre>

<h2><font color='blue'>Download Source Code and Build  </font></h2>

You need to check out source code from github code repositories, one for main Vaa3D code and one for some released plugins. You can following the instructions for both Vaa3D program and its default plugins.

First, make sure you have <a href='https://en.wikipedia.org/wiki/Git_%28software%29'>GIT</a> installed.

<h3>Vaa3D Source Download</h3>
** Please use "--recursive" to clone the terafly submodule.
<pre><code>git clone --recursive  https://github.com/Vaa3D/v3d_external.git  
git clone https://github.com/Vaa3D/vaa3d_tools.git
</code></pre>


<h3>Setup Symbolic Directory links for Building Plugins</h3>
The vaa3d_tools contains the majority of the plugins. The plugin sources are separated from the trunk, so in order to build the plugins in vaa3d_tools into the main program using QMake (If you are using CMake, you don't need to set up the folder cross links, you could configure the install directories during CMake configuration time. However, many of the existing plugins can only be built with QMake right now.) The following folder setting needs to be done:

<hr />


<pre><code>cd vaa3d_tools
ln -s ../v3d_external/v3d_main v3d_main
ln -s ../v3d_external/bin bin
cd ../v3d_external
ln -s ../vaa3d_tools/released_plugins released_plugins_more
</code></pre>

<hr />


<h3>Build Instructions</h3>
For a quick build without plugins (test the previous setting is correct):
<pre><code>./build.linux -j4 -noplugins
</code></pre>

For a complete build:
<pre><code>./build.linux -j4
</code></pre>


The last few lines create symbolic links for necessary header files and output plugins.

You may replace the "-j4" using "-j8", "-j2", or "-j16", depending on how many CPU cores your have on your machine. Note that the "make" command on some systems (e.g. Ubuntu) may not recognize the "-j" parameter. In such case, you can just run "build.linux" without that parameter.

If you are using Qt-creator to debug and encounter the error that 'Could not load the Qt platform plugin "xcb"', try install 'libxcb-xinerama0'.


### Caveat:
After the building, you will see the vaa3d executable under "bin" folder.

But before you run "./bin/vaa3d" executable, please make sure you set the LD_LIBRARY_PATH by adding the following line to ~/.bashrc:
<code><pre>
export LD_LIBRARY_PATH= <your/local/path/to/v3d_external>/v3d_main/common_lib/lib/`
</code></pre>

Otherwise you might see the following error:
1 `v3d_external/bin/vaa3d: error while loading shared libraries: libtiff.so.5: cannot open shared object file: No such file or directory`
You can put this line to your .bashrc file to avoid typing when opening a new terminal.
2. error name:<code>libGL.so</code> or <code>libGL.so.1</code>
in`/usr/lib64/`folder,there are two links (`libGL.so` and `libGL.so.1`) which link to `libGL.so.1.7.0`.
Sometimes these links may break,so you need to delete these links and link to `libGL.so.1.7.0` again.
<pre><code>sudo rm /usr/lib64/libGL.so /usr/lib64/libGL.so.1
sudo ln -s /usr/lib64/libGL.so.1.7.0 /usr/lib64/libGL.so
sudo ln -s /usr/lib64/libGL.so.1.7.0 /usr/lib64/libGL.so.1
</code></pre>
<hr />