# Dylib

<p align="center">
  <a href="https://github.com/martin-olivier/dylib/releases/tag/v2.0.0">
    <img src="https://img.shields.io/badge/Version-2.0.0-blue.svg" alt="version"/>
  </a>
  <a href="https://github.com/martin-olivier/dylib/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-orange.svg" alt="license"/>
  </a>
  <a href="https://isocpp.org/">
    <img src="https://img.shields.io/badge/Compatibility-C++11-darkgreen.svg" alt="cppversion"/>
  </a>
</p>

<p align="center">
  <a href="https://github.com/martin-olivier/dylib/actions/workflows/CI.yml">
    <img src="https://github.com/martin-olivier/dylib/actions/workflows/CI.yml/badge.svg" alt="ci"/>
  </a>
  <a href="https://codecov.io/gh/martin-olivier/dylib">
    <img src="https://codecov.io/gh/martin-olivier/dylib/branch/main/graph/badge.svg?token=4V6A9B7PII" alt="codecov"/>
  </a>
</p>

The goal of this C++ library is to load dynamic libraries (.so, .dll, .dylib) and access its functions and global variables at runtime.  

`⭐ Don't forget to put a star if you like the project!`

# Compatibility
Works on `Linux`, `Windows`, `MacOS`

# Installation

You can fetch `dylib` to your project using `CMake`:
```cmake
include(FetchContent)

FetchContent_Declare(
    dylib
    GIT_REPOSITORY "https://github.com/martin-olivier/dylib"
    GIT_TAG        "v2.0.0"
)

FetchContent_MakeAvailable(dylib)
```

You can also click [HERE](https://github.com/martin-olivier/dylib/releases/download/v2.0.0/dylib.hpp) to download the `dylib` header file.  

# Documentation

## Constructor

The `dylib` class can load a dynamic library from the system library path
```c++
// Load "foo" library from the system library path

dylib lib("foo");
```
The `dylib` class can also load a dynamic library from a specific path
```c++
// Load "foo" lib from relative path "./libs"

dylib lib("./libs", "foo");

// Load "foo" lib from full path "/usr/lib"

dylib lib("/usr/lib", "foo");
```

The `dylib` class will automatically add os decorations to the library name, but you can disable that by setting `decorations` parameter to false
```c++
// Windows -> "foo.dll"
// MacOS:  -> "libfoo.dylib"
// Linux:  -> "libfoo.so"

dylib lib("foo");

// Windows -> "foo.lib"
// MacOS:  -> "foo.lib"
// Linux:  -> "foo.lib"

dylib lib("foo.lib", false);
```

## Get a function or a variable 

`get_function`  
Get a function from the dynamic library currently loaded in the object  

`get_variable`  
Get a global variable from the dynamic library currently loaded in the object
```c++
// Load "foo" dynamic library

dylib lib("foo");

// Get the function "adder" (get_function<T> will return T*)

auto adder = lib.get_function<double(double, double)>("adder");

// Get the variable "pi_value" (get_variable<T> will return T&)

double pi = lib.get_variable<double>("pi_value");

// Use the function "adder" with "pi_value"

double result = adder(pi, pi);
```

## Miscellaneous tools

`has_symbol`  
Returns true if the symbol passed as parameter exists in the dynamic library, false otherwise  

`native_handle`  
Returns the dynamic library handle  
```c++
void example(dylib &lib)
{
    if (lib.has_symbol("GetModule"))
        std::cout << "GetModule symbol has been found" << std::endl;

    dylib::native_handle_type handle = lib.native_handle();
    void *sym = dlsym(handle, "GetModule");
}
```

## Exceptions

`load_error`  
This exception is raised when the library failed to load or the library encountered symbol resolution issues  

`symbol_error`  
This exception is raised when the library failed to load a symbol  

Those exceptions inherit from `dylib::exception`
```c++
try {
    dylib lib("foo");
    double pi_value = lib.get_variable<double>("pi_value");
    std::cout << pi_value << std::endl;
}
catch (const dylib::load_error &e) {
    // failed loading "foo" lib
}
catch (const dylib::symbol_error &e) {
    // failed loading "pi_value" symbol
}
```

# Example

A full example about the usage of the `dylib` library is available [HERE](example)

# Tests

To build unit tests, enter the following commands:
```sh
cmake . -B build -DBUILD_TESTS=ON
cmake --build build
```

To run unit tests, enter the following command inside "build" directory:
```sh
ctest
```
