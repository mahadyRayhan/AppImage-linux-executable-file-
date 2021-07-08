# AppImage-linux-executable(QT)

## Whais is AppImage?

AppImage is a format for distributing portable software on Linux without needing superuser permissions to install the application. It tries also to allow Linux distribution-agnostic binary software deployment for application developers, also called upstream packaging.

To create an AppImage we need some dependencies to be installed.

### AppImage Builder
<hr>
For Debian/Ubuntu system, we need some dependencies to install AppImage. Run this command to get all the dependencies needed to install AppImage builder.

> sudo apt install -y python3-pip python3-setuptools patchelf desktop-file-utils libgdk-pixbuf2.0-dev fakeroot strace fuse

Next download the appImage toolkit from the github and change the toolkit permission.

> sudo wget https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage -O /usr/local/bin/appimagetool

> sudo chmod +x /usr/local/bin/appimagetool

Now install appImage builder

> sudo pip3 install appimage-builder

### Docker
<hr>
To install docker you can execute this command:

> sudo apt-get install docker.io

Appimage builder must be able to use docker without root permissions. So, you need to change docker permission. To do that, you can execute the following commands.

> sudo groupadd docker

> sudo usermod -aG docker $USER

If you still have some permission error you may execute this command.
> sudo chmod 666 /var/run/docker.sock

### QT
<hr>
As this AppImage use QT, so you have to install QT in your system. To install qt, execute the following commands.
> sudo apt-get install build-essential

> sudo apt-get install qtcreator

> sudo apt-get install qt5-default

For my example I use CMake. So you need to install CMake also. You also need git to pull the code from GitHub. To do that, you can execute the following command.

> sudo apt-get install git cmake

depending on your application, you may need more dependency to be installed.

After finishing the dependencies installation open a terminal. Download the sample code from Github. To get the code, execute the following command.

> git clone https://github.com/mahadyRayhan/AppImage-linux-executable/tree/AppImage-QT

Next enter the directory. Execute following command to go to the project directory:
> cd AppImage-linux-executable

In the folder you will find several files and folders. Folder will look like this.

<pre>
.
├── CMakeLists.txt
├── my_log.conf
├── res
│   ├── QTAppImage-hello-world.desktop
│   └── QTAppImage-hello-world.png
└── src
    ├── easylogging++.cc
    ├── easylogging++.h
    ├── main.cpp
    ├── MainForm.ui.qml
    ├── main.qml
    ├── my_log.conf
    ├── qml.qrc
    └── qml.qrc.depends


</pre>

Before executing the next commands let me describe the code a little. There are two folders (res(resource) and src) and one CMake file in the project.

#### SRC
<hr>
In the src folder, there is a main.cpp file, Which is the starting point of our program. Also, there are some additional files related to QT UI. There is also a extra library file name **easylogging++**. This is basically used for implement Logginf in the project.

#### RES
<hr>
Next res(resource) directory. There are two different files in the res directory. A .desktop file and .png file. Remember .desktop file and .png file is very important. the desktop file is simply a shortcut that is used to launch applications in Linux. and the png file will be the icon of the project. To create an AppImage, you must have to provide these two file.

##### Desktop file:
<hr>
If you open the desktop file, you file find something like this:

<pre>
[Desktop Entry]
Version=1.0
Type=Application
Name=QT-hello-world-AppImage
Comment=Hello World To AppImage
TryExec=QTAppImage-hello-world
Exec=QTAppImage-hello-world
Icon=QTAppImage-hello-world
MimeType=image/x-foo;
Categories=Utility;
</pre>

1. [Desktop Entry]
> The first line of every desktop file and the section header to identify the block of key-value pairs associated with the desktop. Necessary for the desktop to recognize the file correctly.

2. Version=1.0
> This is the version of your application. In my application, it is 1.0. You can specify any version number you want like 1.0.1 or anything else.

3. Type=Application
> Tells the desktop that this desktop file pertains to an application. Other valid values for this key are Link and Directory.

4. Name=QT-hello-world-AppImage
> Names of your application for the main menu and any launchers.

5. Comment=Hello word to AppImage
> Describes the application. Used as a tooltip.

6. Exec=QTAppImage-hello-world
> The command that starts this application from a shell. It can have arguments.

7. Icon=QTAppImage-hello-world
> The icon name is associated with this application.

There are some other arguments like **Encoding**, **Terminal**, etc. You can check them from other online sources.

**NOTE: keys are case sensitive. Type=Application is not the same as type=Application or TYPE=Application. Please remember that.**

Not, let's talk about CMake file. In the main branch I created 3 seperate CMake files (one in src folder, one in res folded and another one is in the root). But in this branch, i only create one CMake files. That means i actually marge the 3 CMake files and make one one CMake file. You can follow anyone you like.

Now we know have some basic idea about all of our files, lets make the project.
Execute the following command. I build the project in release mode and “/usr” as prefix.

> cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr

Next install the application into AppDir. Execute the following command:

> make install DESTDIR=AppDir

**NOTE: please do not skip installation rules in the CMake file like this "set (CMAKE_SKIP_INSTALL_RULES True)". It will create an error.**

After executing this command, you will have a execuatable in “AppDir/usr/bin/” directory.

### Generating the recipe
<hr>
Appimage-builder is capable of inspecting the runtime dependencies of a given application to create a recipe for packaging it as AppImage. This can be done using the --generate argument. Execute the following command:

> appimage-builder --generate

The tool will prompt a questionnaire to gather the minimal required information to execute the application. If a desktop entry is found in the AppDir it will be used to fill the fields but you will be able to edit all the values. Make sure of specifying the executable path properly otherwise the execution will fail.

<pre>
INFO:Generator:Searching AppDir
? ID [Eg: com.example.app]: QTAppImage-hello-world
? Application Name: QT-hello-world-AppImage
? Icon: QTAppImage-hello-world
? Executable path: usr/bin/QTAppImage-hello-world
? Arguments [Default: $@]: $@
? Version [Eg: 1.0.0]: latest
? Update Information [Default: guess]: guess
? Architecture: x86_64
</pre>

### Creating the AppImage
<hr>
This is the final step of creating an app image. Up until now, if everything works, then we will call the appimage builder to generate the appimage.
While generating the app image, the appimage builder will test the generated app in different platforms like Fedora, Debian, Arch, Centos, etc. If you want to skip this test part you can add --skip-test argument.

The command will be 
> appimage-builder --skip-test

if the process success, you will get a working appimage for your hello-world project and you can run it in any Linux-based system from a terminal or just double-clicking it.

