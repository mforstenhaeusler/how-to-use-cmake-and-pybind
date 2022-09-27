# how-to-use-cmake-and-pybind
This repository explains how to create a python module from c++ code base using pybind.

## 1. Install Dependencies 

### 1.1 Install Python 

### 1.2 Install CMake

### 1.2.1 Add CMake Path 

## 2. Clone the [cmake_example](https://github.com/pybind/cmake_example) repository

## 3. Create your projct folder 
```
.
├── project                   # Compiled files (alternatively `dist`)
├── docs                    # Documentation files (alternatively `doc`)
├── src                     # Source files (alternatively `lib` or `app`)
├── test                    # Automated tests (alternatively `spec` or `tests`)
├── tools                   # Tools and utilities
├── LICENSE
└── README.md
```

## 4. Download [pybind11](https://github.com/pybind/pybind11) to this project folder
```
.
├── project                   # Compiled files (alternatively `dist`)
    ├── pybind11                    # Documentation files (alternatively `doc`)
```
## 5. Copy the relevant files from the cmake_example to your project folder

### 5.1 CMakeLists

```
cp ../cmake_example/CMakeLists.txt .
```
### 5.2 `src` directory with c++ code
```
cp ../cmake_example/src/ -r .

```
### 5.3 setup.py
```
cp ../cmake_example/setup.py .
```

## 6. Add a build directory for CMake

``` 
mkdir build
```
```
.
├── project                   # Compiled files (alternatively `dist`)
    ├── build                    # Documentation files (alternatively `doc`)
    ├── pybind11                    # Documentation files (alternatively `doc`)
    ├── src                    # Documentation files (alternatively `doc`)
    ├── CMakeLists.txt                    # Documentation files (alternatively `doc`)
    ├── setup.py                    # Documentation files (alternatively `doc`)

## Commpile and Run Test with CMake
```
cd build 
cmake .. 
make check -j 4
```
