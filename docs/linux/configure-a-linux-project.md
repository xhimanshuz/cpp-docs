---
title: "Configure a C++ Linux project in Visual Studio"
ms.date: "11/12/2018"
ms.assetid: 4d7c6adf-54b9-4b23-bd23-5de0c825b768
---
# Configure a Linux Project

This topic describes how to configure a C++ Linux project that is based on a Linux project template in Visual Studio. For information about CMake Linux Projects in Visual Studio, see [Configure a Linux CMake Project ](cmake-linux-project.md).

## General settings

A variety of options can be configured for a Linux project with Visual Studio.  To view these options, select the **Project > Properties** menu, or right click on the project in **Solution Explorer** and select **Properties** from the context menu. The **General** settings appear.

![General configuration](media/settings_general.png)

By default, an executable (.out) is built with the tool.  To build a static or dynamic library, or to use an existing Makefile, use the **Configuration Type** selection.

For more information about the options in the property pages, see [Linux Project Property Page Reference](prop-pages-linux.md).

## Remote settings

To change settings pertaining to the remote Linux computer, configure the remote options that appear in the [General](prop-pages/general-linux.md) settings:

- To change the target Linux computer, use the **Remote Build Machine** entry.  This will allow you to select one of the connections created previously.  To create a new entry, please see the [Connecting to Your Remote Linux Computer](connect-to-your-remote-linux-computer.md) section.

- The **Remote Build Root Directory** determines the root location of where the project is built on the remote Linux computer.  This will default to **~/projects** unless changed.

- The **Remote Build Project Directory** is where this specific project will be built on the remote Linux computer.  This will default to **$(RemoteRootDir)/$(ProjectName)**, which will expand to a directory named after the current project, under the root directory set above.

> [!NOTE]
> To change the default C and C++ compilers, or the Linker and Archiver used to build the project, use the appropriate entries in the **C/C++ > General** section and the **Linker > General** section.  These could be set to use a certain version of GCC, or even the Clang compiler, for example. For more information see [C/C++ Properties (Linux C++)](prop-pages/c-cpp-linux.md) and [Linker Properties (Linux C++)](prop-pages/linker-linux.md).

## Include directories and IntelliSense support

**Visual Studio 2017 version 15.6 and earlier:**<br/>
By default, Visual Studio does not include any system-level include files from the Linux computer.  For example, items in the **/usr/include** directory are not present in Visual Studio.
For full [IntelliSense](/visualstudio/ide/using-intellisense) support, you will need to copy those files to some location on your development computer and point Visual Studio to this location.  One option is to use scp (Secure Copy) to copy the files.  On Windows 10, you can use [Bash on Windows](https://msdn.microsoft.com/commandline/wsl/about) to run scp.  For previous versions of Windows, you could use something like [PSCP (PuTTY Secure Copy)](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

You can copy the files by using a command similar to the following:

`scp -r linux_username@remote_host:/usr/include .`

Of course, replace the **linux_username** and **remote_host** values above for what's appropriate in your own environment.

Once the files are copied, use the **VC++ Directories** item in Project properties to tell Visual Studio where to find the additional include files that were just copied.

![VC++ Directories](media/settings_directories.png)

**Visual Studio 2017 version 15.7 and later:**<br/>
See [Manage Remote Headers for IntelliSense](#remote_intellisense).

## Copy sources

When building, the source files on your development PC are copied to the Linux computer and compiled there.  By default, all sources in the Visual Studio project are copied to the locations set in the settings above.  However, additional sources can also be added to the list, or copying sources can be turned off entirely, which is the default for a Makefile project.

- **Sources to copy** determines which sources are copied to the remote computer.  By default, the **\@(SourcesToCopyRemotely)** defaults to all source code files in the project, but does not include any asset/resource files, such as images.

- **Copy sources** can be turned on and off to enable and disable the copying of source files to the remote computer.

- **Additional sources to copy** allows you to add additional source files which will be copied to the remote system.  You can specify a semi-colon delimited list, or you can use the **:=** syntax to specify a local and remote name to use:

`C:\Projects\ConsoleApplication1\MyFile.cpp:=~/projects/ConsoleApplication1/ADifferentName.cpp;C:\Projects\ConsoleApplication1\MyFile2.cpp:=~/projects/ConsoleApplication1/ADifferentName2.cpp;`

## Build events

Since all compilation is happening on a remote computer, several additional Build Events have been added to the Build Events section in Project Properties.  These are **Remote Pre-Build Event**, **Remote Pre-Link Event**, and **Remote Post-Build Event**, and will occur on the remote computer before or after the individual steps in the process.

![Build Events](media/settings_buildevents.png)

## <a name="remote_intellisense"></a> IntelliSense for remote headers (Visual Studio 2017 version 15.7 and later)

When you add a new connection in **Connection Manager**, Visual Studio automatically detects the include directories for the compiler on the remote system. Visual Studio then zips up and copies those files to a directory on your local Windows machine. After that, whenever you use that connection in a Visual Studio or CMake project, the headers in those directories are used to provide IntelliSense.

This functionality depends on the Linux machine having zip installed. You can install zip by using this apt-get command:

```cmd
apt install zip
```

To manage your header cache, navigate to **Tools > Options, Cross Platform > Connection Manager > Remote Headers IntelliSense Manager**. To update the header cache after making changes on your Linux machine, select the remote connection and then select **Update**. Select **Delete** to remove the headers without deleting the connection itself. Select **Explore** to open the local directory in **File Explorer**. Treat this folder as read-only. To download headers for an existing connection that was created prior to version 15.3, select the connect and then select **Download**.

![Remote Header IntelliSense](media/remote-header-intellisense.png)

## See also

[Set compiler and build properties](../build/working-with-project-properties.md)<br/>
[C++ General Properties (Linux C++)](../linux/prop-pages/general-linux.md)<br/>
[VC++ Directories (Linux C++)](../linux/prop-pages/directories-linux.md)<br/>
[Copy Sources Project Properties (Linux C++)](../linux/prop-pages/copy-sources-project.md)<br/>
[Build Event Properties (Linux C++)](../linux/prop-pages/build-events-linux.md)
