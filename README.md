# how-to-use-cmake-and-pybind
This repository explains how to create a python module from c++ code base using pybind.

## 1. Install Dependencies 

### 1.1 Install Python 
I recommand to use pyenv to handle your python versioning if you use macOS
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
or just run this insead of the last line
```
make project
```

## 8. Create python wheel

```
python setupy.py bdist_wheel
```
**make sure wheel is install 

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
#TODO
