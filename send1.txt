
  
[![CMake](https://github.com/mattcoding4days/extras/actions/workflows/cmake.yml/badge.svg?branch=dev)](https://github.com/mattcoding4days/extras/actions/workflows/cmake.yml)

<div align="center">
  <img width="442" height="320" src="assets/extras.png">
  <br>
</div>

###   Description
> This is a collection of C++ based tools to simplify the software development process.</br> 
> 
>*Why would someone want to use this?*
>
>In the case of someone writing C++ code and they need a little **'extra'** help this collection of C++ libraries can make the C++ programming experience a lot easier, (and more enjoyable). At present there is **'extras support'** for  CRC calculations, pseudo C++ keywords, command line options, standard string manipulation and interface drive programming, (GoF Abstract Factory Pattern).
>

	#include <extras/crcs.hpp>
	#include <extras/dotenv.hpp>
	#include <extras/vendor/cxxopts.hpp>
	#include <extras/interfaces.hpp>
	#include <extras/vendor/json.hpp>
	#include <extras/keywords.hpp>
	#include <extras/strings.hpp>
	#include <extras/support.hpp>
	
>
> Presently supporting Linux platforms is portable to Android, iPhone, iOS, Windows environments.
>
# Development Tools Installation
## Recommended Development Environment
Ideally, an 27" iMac running Desktop Parallels capable of installing 20.04.3 Ubuntu LTS would be a good start. However, assuming you have such a configuration, the following is what you want to do to setup your GCC environment, (on a virgin Ubuntu box). Open a terminal window and type:

	 sudo apt install git
	 mkdir Projects
	 cd Projects
	 git clone https://github.com/perriera/extras.git
	 sudo snap install --classic code # or code-insiders
	 code .

Now you need the GCC/Python tools for a C++ development environment:

`
sudo apt-get install build-essential libtool autotools-dev automake pkg-config bsdmainutils python3 libssl-dev libevent-dev libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-test-dev libboost-thread-dev libminiupnpc-dev libzmq3-dev libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler git ccache
`

For people interested in compiling a Bitcoin node: [click here](https://gist.github.com/jonatack/9f57d398656433de5a5e04d5e0e4447e)
	 
## C++ Tools
In addition to the above install you may wish to have C++ language formatting tools:
```
sudo apt update
sudo apt install clangd cppcheck clang-tidy python3-pip
pip install clang-format
pip install cmake-format
pip install cmake
```
If you get a message similar to the following:
```
WARNING: The scripts cmake, cpack and ctest are installed in '/home/perry/.local/bin' which is not on PATH.
Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
  ```
 Then add the path specified to your `~/.bashrc` script:
 
	vi ~/.bashrc

Add this to the end of ~/.bashrc 

	PATH=/home/perry/.local/bin:${PATH}
	
You should exit and restart the editor once the path has been edited. 

	code .
	
If for whatever reason cmake gives you grief, close the editor and enter the following:

	sudo snap install cmake --classic

Then restart your terminal box and then go back into your editor:

	cd Projects
	cd extras
	code .
	
## Visual Studio Code extensions
When prompted be sure to add the recommended extensions for C++, CMake and other source code support. 

## .vscode directory
Inside the `extras/` project directory you'll see 4 files:

 - c_cpp_properties.json
 - launch.json
 - settings.json
 - tasks.json

These govern things like which C++ standard to use, (in this case **C++17**), which test case to launch, file associations and key associations, (such that if you type **shift-control-B** a compile/make will begin). You may have to restart the editor once the path has been edited. 

# Extras Package Compilation
If you have not already done so install a local copy of extras onto your system, :
 
     git clone https://github.com/perriera/extras.git 
     cd extras
     mkdir build
     cd build
     cmake ..
     make
     ./run-unittests-extras

If the tests come back similar to the following you have a successful install:
```
perry@ubuntu:~/Projects/extras$ build/run-unittests-extras 

ASYNC=on
AUDIENCE=https://jackpot_pc_salmon
CLIENT_ID=sample_client_id
CLIENT_SECRET=34343-dfdfd-3343-fsfdf-334-sd
COOKIE=sample_cookie; did_compatible=sample_did_compatible
YOURAPPTOKEN=dev
===============================================================================
All tests passed (76 assertions in 30 test cases)
```


# Extras Package Installation
There are TWO ways to include perriera/extras into your application. 
## Method #1: Local installation
One is to install it locally onto your system. Just be sure to have your LD_LIBRARY_PATH setup to be able to find the installed extras package.

     export LD_LIBRARY_PATH=/usr/local/lib
     

### sudo checkinstall
While the `sudo make install` will work for most applications, should you wish to uninstall you will need to install **extras** using a slightly different approach. To be able to uninstall you will need to install the Ubuntu **checkinstall** package: [here](https://help.ubuntu.com/community/CheckInstall). 

`sudo apt-get update && sudo apt-get install checkinstall`

With the checkinstall package installed your installation process now becomes:
    
     git clone https://github.com/perriera/extras.git 
     cd extras
     mkdir build
     cd build
     cmake ..
     make
     ./run-unittests-extras
     sudo checkinstall

**Note**: During the checkinstall process be sure to change the name of the package from the default: **build** to **extras**. It'll be item #2 on the checkinstall parameter listings, (you can safely use the defaults for the rest of the items on the checkinstall).

Then when you need to uninstall this package:

    sudo dpkg -r extras

Should you run into a strange situation to where you issued the above command but the **extras** package appears to still be installed, run the following:

    sudo dpkg -r build

As you may have installed it earlier without changing the name of the package from **build** to **extras**.

## Method #2: CPM installation
Add the following to your CMakeLists.txt for CPM support. CPM support allows you to distribute your packages with other git repositories automatically downloaded, (for the users of your software). In the case of setting up distributed Debian packages, this is advantageous. 

	#
	# NOTE: Include 3rd party libraries, Perry and I maintain an open-source extras C++ library, which
	# is used extensively in our projects, it comes bundled with spdlog, cpr, and nlohmann json. extras has
	# project options that allow us to control how other libraries it includes are built. for example we
	# can tell extras to build spdlog as a static library (for faster compile times) by settings
	# MAKE_SPDLOG_SHARED OFF
	#
	CPMAddPackage(
	  NAME extras
	  GITHUB_REPOSITORY perriera/extras
	  VERSION 3.11.0
	  OPTIONS "MAKE_SPDLOG_SHARED OFF"
	)
	if(extras_ADDED)
	  #
	  # NOTE:  enable c++11 to avoid compilation errors, and force spdlog into release build
	  #
	  print(STATUS "Configuring extras build properties")
	  set_target_properties(extras PROPERTIES CMAKE_BUILD_TYPE Release)
	else()
	  print(WARNING "extras was not configured properly")
	endif()

Just be sure to have the desired **VERSION** of perrier/extras specified in the **VERSION** keyword above. To optimize CPM support on your projects be sure to set the environment variable for shared CPM libraries:

	export CPM_SOURCE_CACHE=$HOME/.cache/CPM

For more about CPM [see here](https://github.com/cpm-cmake/CPM.cmake)

## CMakeLists.txt
You just include the **extras** library to any targets in your CMakeLists.txt target_include_libraries specs

For example:

    target_link_libraries(run-unittests-extras
       chessmind::library
       extras
    )
    
The 2.3.8 version of the extras libraries installs the debug version by default. If you wish to use a production version of the library you can either comment out this line in the main CMakeLists.txt file and recompile:

    # add additional project options
    option(BUILD_TESTS "Build tests" ON)
    option(DEBUG "Debug Build" ON)

Or, as of 3.2.1 you must set DEBUG to false, (& BUILD_TESTS=false) on the command line of the cmake command and it will cause a production version of the library to be generated instead, (just be sure to delete the build directory before hand):

    cd ..
    rm -rf build
    cd build
    cmake -DDEBUG=false -DBUILD_TESTS=false ..
    make
    ./run-unittests-extras
    sudo make install

### FYI: ModernCppStarter
> If you are looking for  a solid C++ CMakeLists.txt skeleton project, (one that will do what you want it to do), <br/> checkout **ModernCppStarter**:</br>
> 

*"Setting up a new C++ project usually requires a significant amount of preparation and boilerplate code, even more so for modern C++ projects with tests, executables and continuous integration. This template is the result of learnings from many previous projects and should help reduce the work required to setup up a modern C++ project." -- The Lartians</br>*

https://github.com/TheLartians/ModernCppStarter/blob/master/LICENSE</br>
Copyright (c) 2019 The Lartians

### FYI: cpp_starter
> If you are looking for a solid C++ CMakeLists.txt skeleton project, (based on ModernCppStarter but includes support for **perriera/extras**), then you want to checkout, <br/> checkout **cpp_starter**:</br>
> 
[cpp_starter](https://github.com/perriera/cpp_starter)<br/>
This project will get you up and running with the latest CPM support as well as this library, (a real time saver).

## C++ namespace support
As of version 2.0.0 the (perriera) **extras** package now requires the use of the namespace **extras**.

    using namespace extras;

## extras/crcs
 > add **extras/crcs.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
Use of this library will give your application quick access to either crc16, crc32, or crc64 bit calculations.

    #include  <iostream>
    #include  <extras/crcs.hpp>
    #include "catch.hpp"

    using namespace extras;

    SCENARIO("Verify CRC instance", "[crcs]") {
        std::string data = "data_piece2;";
        auto& crc = CRC::instance().calculate(data);
        uint16_t result16 = crc;
        REQUIRE(result16 == 0x48C);
        uint32_t result32 = crc;
        REQUIRE(result32 == 0xAB5406BC);
        uint64_t result64 = crc;
        REQUIRE(result64 == 0xE37932611E9B4556);
    }

## extras/dotenv
 > add **extras/dotenv.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
Use of this class will give you a simpler means of managing environment variables

    #include  <iostream>
    #include  <extras/dotenv.hpp>
    #include "catch.hpp"

    using namespace extras;

    SCENARIO("Test DotENV load token file", "[test_dotenv]") {

        /**
        * @brief The DotENVInterface handles a lot of the
        * common issues with formatting Unix/Linux style
        * parameters.
        */

        extras::DotENV dotENV;
        std::stringstream ss;
        ss << "# ASYNC accepted values [ on : off ]" << std::endl;
        ss << "ASYNC=on" << std::endl;
        ss << "YOURAPPTOKEN=dev" << std::endl;
        ss << "CLIENT_ID=dsfs-fsdf-sdf-sfdsdf-sdfsdf" << std::endl;
        ss << "CLIENT_SECRET=34343-dfdfd-3343-fsfdf-334-sd" << std::endl;
        ss << "AUDIENCE=https://jackpot_pc_salmon" << std::endl;
        ss << "COOKIE=dskjfhksjdhdhfkjsdf-sdhfkjdsf-Q; ";
        ss << "did_compatible=sdfssdf-sdf sddf-sd f-sdce-" << std::endl;
        ss >> dotENV;
        std::stringstream test;
        std::stringstream ss2;
        ss2 << dotENV;
        extras::DotENV dotENV_compare;
        ss2 >> dotENV_compare;
        REQUIRE(dotENV == dotENV_compare);
        std::cout << dotENV_compare;
        REQUIRE(dotENV.value("YOURAPPTOKEN") == "dev");
    }


## extras/vendor/cxxopts
 > add **extras/vendor/cxxopts.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
 > 
This is a lightweight C++ option parser library, supporting the standard GNU style syntax for options.</br>
https://github.com/jarro2783/cxxopts/blob/master/LICENSE</br>
Copyright (c) 2014 Jarryd Beck 

    #include  <iostream>
    #include  <extras/vendor/cxxopts.hpp>
    #include "catch.hpp"

    using namespace extras;

    SCENARIO("Verify CXXOPTS instance", "[cxxopts]")
    {
        cxxopts::Options options("MyProgram", "One line description of MyProgram");
        options.add_options()
		  ("d,debug", "Enable debugging") // a bool parameter
		  ("i,integer", "Int param", cxxopts::value<int>())
		  ("f,file", "File name", cxxopts::value<std::string>())
		  ("v,verbose", "Verbose output", cxxopts::value<bool>()->default_value("false"));
    }

## extras/exceptions
 > add **extras/exceptions.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
 > 
Custom exceptions are now possible in your applications with the use of **CustomExceptionInterface**</br>
https://github.com/perriera/extras/blob/main/include/extras/exceptions.hpp</br>
(See [test/mock_exceptions.cpp](https://github.com/perriera/extras/blob/main/test/mock_exceptions.cpp), [test/test_exceptions.cpp](https://github.com/perriera/extras/blob/main/test/test_exceptions.cpp) & [OctalException](https://github.com/perriera/extras/blob/main/include/extras/octal_support.hpp) for more on this)


    #include  <iostream>
    #include  <extras/exceptions.hpp>
    #include "catch.hpp"

    using namespace extras;

    SCENARIO(
        "Test SpecificCustomException: demonstration (with assertion() methods)",
        "[exceptions_testcases]") {
        try {
            std::string port = "8080";
            SpecificCustomException::assertion(port, __INFO__);
            int a = 3;
            int b = 7;
            SpecificCustomException::assertion(a, b, __INFO__);
            FAIL("SpecificCustomException not thrown");
        } catch (SpecificCustomException& ex) {
            SUCCEED("SpecificCustomException thrown");
        } catch (GroupCustomException& ex) {
            FAIL("GroupCustomException thrown, (instead)");
        } catch (std::exception& ex) {
            FAIL("std::exception thrown, (instead)");
        } catch (...) {
            FAIL("... thrown, (instead)");
        }
    }


## extras/interfaces
 > add **extras/interfaces.hpp** to your C++ source</br>
 > (this library is header only at this point in time)</br>
 > **using namespace extras;**</br>
Especially useful to programmers that like to program using interfaces, (aka. GoF: Abstract factory pattern))

    #include <iostream>
    #include <extras/interfaces.hpp>

    //
    // CrossTableInterface
    // Encapsulates all behaviour CrossTableInterface
    //
    interface  CrossTableMapInterface {
       virtual  void  train(const  StatLines &statLines) pure;
       virtual  void  predict(const  FENLine &line) pure;
       virtual  void  size() const  pure;
       virtual  void  verbose(bool  on_or_off) pure;
    };

    concrete class CrossTableMap implements CrossTableMapInterface {
         void  train(const  StatLines &statLines) override;
         void  predict(const  FENLine &line) override;
         void  size() const  override;
         void  verbose(bool  on_or_off)  override;
    };

### FakeIt Mock Framework support, ([OctalInterface](https://github.com/perriera/extras/blob/main/include/extras/octal_support.hpp))
 > View **extras/octal_suppoer.hpp** to see a simple interface</br>
 > (see **mock_octal.cpp** in the **test/** directory)</br>
 > **using namespace extras;**</br>

To anyone wishing to know how to use mocks to write interfaces see the **OctalInterface** and the test cases written in the test/ directory.

## extras/vendor/json
 > add **extras/vendor/json.hpp** to your C++ source</br>
 > (this library is header only at this point in time)</br>
 > **using namespace extras;**</br>
 > 
This is JSON for C++</br>
https://github.com/nlohmann/json/blob/develop/LICENSE.MIT</br>
Copyright (c) 2013-2019 Niels Lohmann <http://nlohmann.me>

	namespace ns {
	    // a simple struct to model a person
	    struct person {
	        std::string name;
	        std::string address;
	        int age;
	    };
	}

	ns::person p = {"Ned Flanders", "744 Evergreen Terrace", 60};
	// convert to JSON: copy each value into the JSON object
	json j;
	j["name"] = p.name;
	j["address"] = p.address;
	j["age"] = p.age;

	{
	  "pi": 3.141,
	  "happy": true,
	  "name": "Niels",
	  "nothing": null,
	  "answer": {
	    "everything": 42
	  },
	  "list": [1, 0, 2],
	  "object": {
	    "currency": "USD",
	    "value": 42.99
	  }
	}

## extras/keywords, (now known as extras/types.hpp)
 > add **extras/keywords.hpp** to your C++ source</br>
 > (this library is header only at this point in time)</br>
 > **using namespace extras;**</br>
This header file defines constructs like the byte keyword:

	namespace extras
	{

	    /**
	     * @brief bytes
	     * 
	     * " Of the same size as char, but guaranteed to be unsigned. 
	     *   Contains at least the [0, 255] range.[5] ""
	     *    -- https://en.wikipedia.org/wiki/C_data_types
	     *    -- Wikipedia, August 6, 2021
	     * 
	     * [5]  ISO/IEC 9899:1999 specification, TC3 (PDF). p. 37, 
	     * § 6.2.6.1 Representations of types – General. 
	     * 
	     */

	    using byte = unsigned char;
	    using bytes = byte *;

	}

## extras/sockets
 > add **extras/sockets/SocketClient.hpp** or **extras/sockets/SocketServer.hpp** to your C++ source</br>
 > **using namespace extras;**</br>
This header file provides a basic wrapper around `socket(AF_INET, SOCK_STREAM, 0))` based functions for creating a client/server connection with any thing that takes a `"127.0.0.1"`, `"port"` relationship:

    interface SocketInterface {
        virtual void send(const std::string &msg) pure;
        virtual SocketInterface &read(int expectedMaxSize) pure;
        virtual ~SocketInterface() {}
        virtual operator std::string() const pure;
    };
    
    interface SocketClientInterface extends SocketInterface {
        virtual void connect() pure;
    };

    interface SocketServerInterface extends SocketInterface {
        virtual void accept() pure;
    };

**Client code**:

    #include <extras/sockets/SocketClient.hpp>
    #include <iostream>
    #include <sstream>

    using namespace std;
    using namespace extras;

    #define PORT 8000
    int main(int, char const*[]) {
        try {
            SocketClient client("localhost", PORT);
            client.connect();
            stringstream ss;
            ss << "Hello from SocketClient" << endl;
            client.send(ss.str());
            string msg = client.read();
            cout << msg << endl;
        } catch (SocketException& ex) {
            cout << ex.what() << endl;
        }
    }

**Server code**:

    #include <extras/sockets/SocketServer.hpp>
    #include <iostream>
    #include <sstream>

    using namespace std;
    using namespace extras;

    #define PORT 8000
    int main(int, char const*[]) {
        try {
            SocketServer server(PORT);
            server.accept();
            string msg = server.read();
            cout << msg << endl;
            stringstream ss;
            ss << "Hello from SocketServer" << endl;
            server.send(ss.str());
        } catch (SocketException& ex) {
            cout << ex.what() << endl;
        }
    }

## extras/bin2hex
 > add **extras/bin2hex/BinConverter.hpp** or **extras/bin2hex/HexConverter.hpp** to your C++ source</br>
 > **using namespace extras;**</br>
This header file provides a simple means of turning binary files into their hexadecimal represantations which may become really helpful when dealing with file transfers over socket connections:

For Binary to Hex:

    /**
    * @brief BinInterface
    *
    * Maintains a binary array in memory
    */

    using BinArray = std::vector<byte>;

    interface BinInterface {
        virtual const byte *array() const pure;
        virtual int size() const pure;
    };    

For Hex to Binary:

    /**
    * @brief HexInterface
    *
    * Maintains a hex array in memory
    */

    using HexLine = std::string;
    using HexArray = std::vector<std::string>;

    interface HexInterface {
        virtual HexArray array() const pure;
        virtual int lines() const pure;
        virtual int size() const pure;
    };

## extras/Sockets/Domains.hpp
 > add **extras/Sockets/Domains.hpp**  to your C++ source</br>
 > **using namespace extras;**</br>
This header file provides a simple means of checking for the online existance of a domain name as well as returning it's current IP address:

DomainInterface:

    using IPAddress = std::string;
    using DomainName = std::string;

    interface DomainInterface {
        /**
        * @brief actualPath
        * @return replace the '~' with the value gained from getenv('home')
        * @exception invalid path supplied
        */
        virtual IPAddress resolve() const pure;
        virtual DomainName name() const pure;
        virtual IPAddress ip() const pure;
        virtual bool exists() const pure;
    };
 
 Basic usage:

    IPAddress ip = ~Domain("cplusplus.org")

## extras/Sockets/FileTransferInterface.hpp
 > add **extras/Sockets/FileTransferInterface.hpp**  to your C++ source</br>
 > **using namespace extras;**</br>
This header file provides a simple means uploading and downloading a file across sockets.

    /**
    * @brief FileTransferInterface
    *
    * Transfers an file across sockets
    *
    */
    interface FileTransferInterface {
        virtual void upload(const HexInterface&, SocketInterface& socket) pure;
        virtual HexArray download(SocketInterface& socket) pure;
    };


Returns the current IP address of the domain, (or throws an exception if it does not exist)

## extras/spdlog, (as a shared library)
 > when you git clone this repository to your directory structure and build it using the install 
 > procedure, (defined above), the ever popular spdlog library will now be available to you as a shared 
 > library, (aka. libspdlogd.so). The need for this came about when the header-only version of spdlog
 > kept showing up as a static library, so we found a way to make it into a shared library for projects
 > that run into the same situaiton. 
 > 
Very fast, header-only/compiled, C++ logging library. </br>
https://github.com/gabime/spdlog/blob/v1.x/LICENSE</br>
Copyright (c) 2016 Gabi Melman.


    # Add spdlog which includes fmt library as well (two for one) 
    # This should install on the system when extras in included in 
    # another project
    CPMAddPackage(
        GITHUB_REPOSITORY gabime/spdlog
        VERSION 1.9.1
        OPTIONS "SPDLOG_BUILD_SHARED ON"  "SPDLOG_BUILD_EXAMPLE OFF"  "SPDLOG_BUILD_TESTS OFF"
        "SPDLOG_BUILD_EXAMPLE OFF"  "SPDLOG_ENABLE_PCH ON"  "SPDLOG_INSTALL ON"
    )

## extras/strings
 > add **extras/strings.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
How many times how you wanted to do something really simple with strings but found yourself re-inventing the wheel all the time. Well, this library is merely a collection of popular std::string manipulation techniques, (based on the C++ STL library). Expect additional features to be added as time goes on:

    #include <iostream>
    #include <extras/strings.hpp>

    using  namespace  std;
    using  namespace  extras;

    static  inline  void  ltrim(std::string  &s);
    static  inline  void  rtrim(std::string  &s);
    static  inline  void  trim(std::string  &s);
    static  inline  std::string  ltrim_copy(const  std::string  &s);
    static  inline  std::string  rtrim_copy(const  std::string  &s);
    static  inline  std::string  trim_copy(const  std::string  &s);
    std::deque<std::string> split(const  std::string  &s, char  delim);
    std::string  replace_all(const  std::string  &s, char  a, char  b);
    std::string  remove_all(const  std::string  &s, char  a);
    std::string  replace_all(std::string  str, const  std::string  &from, const  std::string  &to);
    inline  bool  ends_with(std::string  const  &value, std::string  const  &ending);
    inline  bool  starts_with(std::string  const  &value, std::string  const  &beginning);
    inline  bool  contains(std::string  const  &s1, std::string  const  &s2);
    inline  std::string  to_lower(const  std::string  &data);
    
## extras/paths
 > add **extras/paths.hpp** to your C++ source</br>
 > add **extras** library to your CMakeLists.txt target</br>
 > **using namespace extras;**</br>
For sublties such as replacing the '~' character in a path to it's home value we have the **Paths** class

    #include <cstdint>
    #include <extras/paths.hpp>
    #include <extras/strings.hpp>

    #include "catch.hpp"

    using namespace extras;

    SCENARIO("Verify PathsInterface default", "[paths_support]") {
        std::string value = Paths::instance().actualPath("~/Downloads");
        REQUIRE(value != "~/Downloads");
        REQUIRE(extras::contains(value, "/home/"));
        REQUIRE(!extras::contains(value, "~"));
    }

For **badness** the '~()' is overloaded to carry out this function

    /**
     * @brief overloaded ~() operator to remove the ~ from the path
     * @return the full path, (where the '~' is replaced with home path)
     */
    Paths &operator~() noexcept { return *this; }
    operator std::string() { return actualPath(_path); }

The test case:

    SCENARIO("Verify PathsInterface operator~()", "[paths_support]") {
        Paths path("~/Downloads");
        std::string value = ~path;
        REQUIRE(value != "~/Downloads");
        REQUIRE(extras::contains(value, "/home/"));
        REQUIRE(!extras::contains(value, "~"));
        std::cout << value << std::endl;
    }

Output the following:

    /home/perry/Downloads

This is rather ironic and kinda against recommended coding practises, (as we are inserting here subtle changes in the expected behavior of the operator). But given the ironic nature that the same '~' is being used to remove another '~' character causes me to ponder about the meaning of life, (or at least mine).

## extras/support
 > add **extras/support.hpp** to your C++ source</br>
 > This will include all the head files available in the extras/ directory.</br>
 > **using namespace extras;**</br>

## extras/CPM.cmake
 > when you git clone this repository to your directory structure you will see that our
 > CMakeLists.txt makes use of the CPM.cmake construct. When it comes to including other
 > git repositories inside your own, we have found no other tool as refined as this package 
 > as it includes a very used 'OPTIONS" parameter where C++ parameters necessary for the
 > repository you are including will be passed to the make file seemlessly, (see our spdlog
 > entry above))
 > 
Setup-free CMake dependency management</br>
https://github.com/cpm-cmake/CPM.cmake/blob/master/LICENSE</br>
Copyright (c) 2019 Lars Melchior

https://github.com/cpm-cmake/CPM.cmake

## Todo List:
The following are a series of features to be added to (perriera) extras in the near future. If there is anything you'd like to see added to the extras package please email us at perry.anderson@gmail.com, (or perry@exparx.com)

## Android support
Expect support for the Android platform in the near future
## iOS support
Expect support for the iOS platform in the near future
## iPhone support
Expect support for the iPhone platform in the near future
## Windows support
Expect support for the Windows platform in the near future

## Acknowledgements
> **(perriera) extras** has been brought to you by these fine vendors:
- [clang-format configurator](https://zed0.co.uk/clang-format-configurator/)</br>

After you:

        sudo apt update
        sudo apt install clangd
        sudo apt install cppcheck
        sudo apt install clang-tidy
        sudo apt install python3-pip
        pip install clang-format
        pip install cmake-format

Be sure to use [clang-format configurator](https://zed0.co.uk/clang-format-configurator/) for all for custom clang-format needs :)

Where you .clang-format can be as small as:

        BasedOnStyle: Google
        IncludeBlocks: Regroup

- [Matt Williams, Victoria, BC](https://www.linkedin.com/in/matt-williams-1291a8151/)</br>
  * Matt's attention to detail and knowledge of modern C++ coding techniques and standards have been an incredible [**Boost**](https://www.boost.org) to this library.
  * Thank you [Matt](https://github.com/mattcoding4days), :)

