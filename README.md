# how-to-use-cmake-and-pybind
This repository explains how to create a python module from c++ code base using pybind.

## 1. Install Dependencies 

### 1.1 Install Python 
I recommand to use `pyenv` to handle your python versioning if you use macOS
- https://docs.brew.sh/Homebrew-and-Python#python-3y
- https://www.freecodecamp.org/news/python-version-on-mac-update/

### 1.2 Install CMake
Download CMake from here --> https://cmake.org/download/

### 1.2.1 Add CMake Path 

add the following lines to your `~/.bash_profile`.

```
echo 'export PATH="<CMake_path>":"$PATH"' >> ~/.bash_profile
```
You can find the path once CMake is installed under `Tools` --> `How to Install For Command Line Use`.
CMake_path = /Applications/CMake.app/Contents/bin

## 2. Clone the [cmake_example](https://github.com/pybind/cmake_example) repository
```
git clone git@github.com:pybind/cmake_example.git
```
## 3. Create your projct folder 
```
.
├── project                   
```
## 4. Download [pybind11](https://github.com/pybind/pybind11) to this project folder

I recommand to include pybind as a submodle.

```
git clone git@github.com:pybind/pybind11.git
```
```
.
├── project                   
    ├── pybind11                    
```

For more options, consider the following link --> https://pybind11.readthedocs.io/en/stable/installing.html
## 5. Copy the relevant files from the cmake_example to your project folder

5.1 CMakeLists

```
cp ../cmake_example/CMakeLists.txt .
```
5.2 `src` directory with c++ code
```
cp ../cmake_example/src/ -r .

```
5.3 setup.py
```
cp ../cmake_example/setup.py .
```

## 6. Add a build directory for CMake

``` 
mkdir build
```
```
.
├── project                   
    ├── build
    ├── pybind11
    ├── src
    ├── CMakeLists.txt
    └── setup.py
```

## 7. Commpile and Run Test with CMake
```
cd build 
cmake .. 
make check -j 4
```
or just run this instead of the last line
```
make project
```

## 8. Create python wheel
Inside the project folder, run the following.

```
python setupy.py bdist_wheel
```
**make sure wheel is installed

## 9. Test if python wheel works

9.1 Create a virtual enviroment
```
python3 -m venv venv
```
9.3 Activate venv 
```
source venv/bin/activate
```
9.3 install pybind python wheel
```
pip install ../dist/cmake_example-0.0.1-cp310-cp310-macosx_12_0_x86_64.whl
``` 

9.4 Test in environemt
```
$ python
Python 3.10.0 (default, Sep 27 2022, 18:23:14) [Clang 14.0.0 (clang-1400.0.29.102)] on darwin
Type "help", "copyright", "credits" or "license" for more information.

>>> import cmake_example
>>> cmake_example.add(1,1)
2
>>> exit()
```

Our C++ code base works in python!


## 10. How to modifiy the c++ code
Curretnly, in this example our c++ code base is all combiined in one c++ file (`src/main.cpp`). If you have more compelx code you would like to spread over a couple of modules, you can do the following for example. 

### Current code:

```
#include <pybind11/pybind11.h>

#define STRINGIFY(x) #x
#define MACRO_STRINGIFY(x) STRINGIFY(x)

int add(int i, int j) {
    return i + j;
}

namespace py = pybind11;

PYBIND11_MODULE(cmake_example, m) {
    m.doc() = R"pbdoc(
        Pybind11 example plugin
        -----------------------

        .. currentmodule:: cmake_example

        .. autosummary::
           :toctree: _generate

           add
           subtract
    )pbdoc";

    m.def("add", &add, R"pbdoc(
        Add two numbers

        Some other explanation about the add function.
    )pbdoc");

    m.def("subtract", [](int i, int j) { return i - j; }, R"pbdoc(
        Subtract two numbers

        Some other explanation about the subtract function.
    )pbdoc");

#ifdef VERSION_INFO
    m.attr("__version__") = MACRO_STRINGIFY(VERSION_INFO);
#else
    m.attr("__version__") = "dev";
#endif
}
```

CMakeLists.txt
```
cmake_minimum_required(VERSION 3.4...3.18)
project(cmake_example)

add_subdirectory(pybind11)
pybind11_add_module(cmake_example src/main.cpp)

# EXAMPLE_VERSION_INFO is defined by setup.py and passed into the C++ code as a
# define (VERSION_INFO) here.
target_compile_definitions(cmake_example PRIVATE VERSION_INFO=${EXAMPLE_VERSION_INFO})
```

### New Code
Let's say we want to create a file that holds our math functions like add or subtract. Create a header file and c++ code file. 

math.hpp
```
int add(int i, int j);

int subtract(int i, int j);
```

math.cpp
```
#include "mymath.hpp"

int add(int i, int j) {
    return i + j;
}

int subtract(int i, int j) {
    return i - j;
}
```

main.cpp
```
#include <pybind11/pybind11.h>
#include "math.hpp"

#define STRINGIFY(x) #x
#define MACRO_STRINGIFY(x) STRINGIFY(x)

namespace py = pybind11;

PYBIND11_MODULE(cmake_example, m) {
    m.doc() = R"pbdoc(
        Pybind11 example plugin
        -----------------------

        .. currentmodule:: cmake_example

        .. autosummary::
           :toctree: _generate

           add
           subtract
    )pbdoc";

    m.def("add", &add, R"pbdoc(
        Add two numbers

        Some other explanation about the add function.
    )pbdoc");

    m.def("subtract", &subtract, R"pbdoc(
        Subtract two numbers

        Some other explanation about the subtract function.
    )pbdoc");

#ifdef VERSION_INFO
    m.attr("__version__") = MACRO_STRINGIFY(VERSION_INFO);
#else
    m.attr("__version__") = "dev";
#endif
}
```

Further, some adjustments in the CMakeLists.txt file is necessary.

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.4...3.18)
project(cmake_example)

# add custom library 
add_library(mymath STATIC src/mymath.cpp)
set_target_properties(mymath PROPERTIES POSITION_INDEPENDENT_CODE ON)

add_subdirectory(pybind11)
pybind11_add_module(cmake_example src/main.cpp) # add library in cmake

target_link_libraries(cmake_example PRIVATE mymath)

# EXAMPLE_VERSION_INFO is defined by setup.py and passed into the C++ code as a
# define (VERSION_INFO) here.
target_compile_definitions(cmake_example PRIVATE VERSION_INFO=${EXAMPLE_VERSION_INFO})
```

## 11. More complex library structure

Let's say you want to buiuld a project with a more sophisticated and complex module structre. 

Your project could look something like this:
```
.
├── project                   
    ├── build
    ├── pybind11
    ├── lib
        ├── add
            ├── add.cpp
            ├── add.hpp
        ├── subtract
            ├── subtract.cpp
            ├── subtract.hpp
    ├── src
        ├── main.cpp
    ├── CMakeLists.txt
    └── setup.py
```

Then you need to add the following CMakeLists.txt file.

```
.
├── project                   
    ├── build
    ├── pybind11
    ├── lib
        ├── add
            ├── add.cpp
            ├── add.hpp
            ├── CMakeLists.txt  # Inner Leave
        ├── subtract
            ├── subtract.cpp
            ├── subtract.hpp
            ├── CMakeLists.txt # Inner leave
        ├── CMakeLists.txt # Leave
    ├── src
        ├── main.cpp
    ├── CMakeLists.txt # Root 
    └── setup.py
```

The `Root`file would look something like this.

```
cmake_minimum_required(VERSION 3.4...3.18)

project(project)

# Set the output folder where your program will be created
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/bin)
# set this variable to specify a common place where CMake should put all executable files
# (instead of CMAKE_CURRENT_BINARY_DIR)
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
# set this variable to specify a common place where CMake should put all libraries
# (instead of CMAKE_CURRENT_BINARY_DIR)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

set(PROJECT_SRC_DIR ${PROJECT_SOURCE_DIR}/src)


# The following folder will be included
include_directories("${PROJECT_SOURCE_DIR}")

add_subdirectory(${PROJECT_SOURCE_DIR}/lib)

# add the executable
add_executable(${PROJECT_NAME} ${PROJECT_SRC_DIR}/main.cpp)

target_link_libraries(${PROJECT_NAME} PUBLIC add subtract)

# add the binary tree to the search path for include files
target_include_directories(
    supervoxel 
     PUBLIC
     ${PROJECT_BINARY_DIR}
     ${LIBRARY_OUTPUT_PATH}/add
     ${LIBRARY_OUTPUT_PATH}/subtract 
)
```

The outer `Leave` file:

```
set(ADD_DIR ${PROJECT_SOURCE_DIR}/lib/add)
set(SUBTRACT_DIR ${PROJECT_SOURCE_DIR}/lib/subtract)
add_subdirectory(${MATH_DIR})
```
The inner `Leave` files:

```
add_library(add STATIC add.cpp add.hpp)
```
and 
```
add_library(subtract STATIC subtract.cpp subtract.hpp)
```