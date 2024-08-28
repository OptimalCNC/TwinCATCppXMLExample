# Description

This repository present a simple example of using the modified [tinyxml2](https://github.com/huweiatgithub/tinyxml2) to parse XML file in TwinCAT3 C++ project.

This example will read an simple XML file, parse it, set a parameter with the parsed value.
In each cycle, it adds the parameter value to the input value and writes it to the output.

**Note:** The Visual Studio Solution has been configured with Release on TwinCAT RT (x64). Other configurations should be adjusted accordingly.

The XML file is hardcoded to `"%TC_RESOURCEPATH%XMLParser/Config.xml"` which expands to `C:\TwinCAT\3.1\Target\Resource\XMLParser/Config.xml` by default.
Other supported environment variables may be found [here](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/3260050187.html&id=).

| Virtual environment variable | Registry value | Default value       |
| ---------------------------- | -------------- | ------------------- |
| %TC_INSTALLPATH%             | InstallDir     | C:\TwinCAT\3.x \    |
| %TC_TARGETPATH%              | TargetDir      | C:\TwinCAT\3.x \Target\ |
| %TC_BOOTPRJPATH%             | BootDir        | C:\TwinCAT\3.x \Boot\ |
| %TC_RESOURCEPATH%            | ResourceDir    | C:\TwinCAT\3.x \Target\Resource\ |

Please add a XML file to aforementioned path. The example XML file is as follows:
```xml
<?xml version="1.0"?>
<DIFF><VALUE>2</VALUE>
</DIFF>
```

**Note:** It is possible to automatically deploy the XML file to the target in the PLC project, see [this](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/3260050187.html&id=).

# Steps to reproduce the project

## The `tinyxml2` library
We will compile the `tinyxml2` library as a static library and use it in the TwinCAT3 C++ project.
For this, we will follow the steps below:
1. Clone the `tinyxml2` repository as a submodule.
2. Create a new TwinCAT3 C++ static library project `tinyxml2`.
3. Add `tinyxml2.h` and `tinyxml2.cpp` files from cloned submodules to the project Header and Source Files Filter.
4. Since precompiled headers are used in the TwinCAT3 C++ project, we need to add the current project directory to the `Additional Include Directories` in the `tinyxml2` project settings so that the precompiled header file can be found.
5. Build the project, make sure no `Tc Sign` is applied.

## The Example Project
We present a simple example of reading an XML file using the `tinyxml2` library in the TwinCAT3 versioned C++ project.
For this, we will follow the steps below:
1. Create a new TwinCAT3 versioned C++ project `Example` and add a C++ Module.
2. Configure `TwinCAT signing` for the project.
3. Add the cloned path of submodule `tinyxml2` to the `Additional Include Directories` in the `Example` project settings.
4. Add the `tinyxml2.lib` file to the `Additional Dependencies` in the `Example` project settings.
5. Write codes using `ITcFileAccessPtr` interface with class `TcFileAccess` to read the XML file and parse it using the `tinyxml2` library.


# Details on the example project
The reading and parsing of the XML file are done in the function `SetObjectStatePS`, i.e., when the object transfers from `PREOP` to `SAFEOP` state.

To be specific,
- We create an instance of `TcFileAccess` class;
- The `TcFileAccess` instance was then set to `PREOP` state;
- We use the interface `ITcFileAccessPtr` pointing the `TcFileAccess` instance to read the XML file;
- We parse the result string using the `tinyxml2` library and set the parameter with the parsed value.

Finally, in `SetObjStateSP`, we set the instance `TcFileAccess` to `INIT` state and dereference it (this is done automatically by setting the smart pointer to `NULL`).
