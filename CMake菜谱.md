# CMakeLists.txt

> - **文件名称**：区分大小写，必须命名为`CMakeLists.txt`，CMake才能够解析。
> - **大小写**：cmake**语言不区分大小写**，但**参数区分**。

## 函数

### cmake_minimum_required

> 设置cmake的最低要求。

- 设置cmake最低版本3.5，否则发出致命错误。

  ```cmake
  cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
  ```

### project

> 声明项目。

- 声明了项目的名称和支持的项目语言。

  ```cmake
  project(demo LANGUAGES CXX)
  ```

  > LANGUAGES不指定时，默认是C++，【CXX代表C++】。

### add_executable

> 生成一个可执行文件。

- 可执行文件 `demo` 通过编译和链接源文件 `demo.cpp` 生成的。

  ```cmake
  add_executable(demo demo.cpp)
  ```

### add_library

> 生成必要的构建指令，将指定的源码编译到库中。
>
> - 第一个参数是目标名。【可重名】，因为并不唯一决定库名，还有后缀。
> - 第二个参数是库类型。【可忽略】
>   - **默认**：`BUILD_SHARED_LIBS`是CMake的一个全局标志。查询 `BUILD_SHARED_LIBS` 全局变量。如果为 `false` 或者 `未定义`；将生成一个静态库。
>   - **可选值**
>     - `STATIC` ：用于创建静态库，即编译文件的打包存档，以便在链接其他目标时使用。
>     - `SHARED` ：用于创建动态库，即可以动态链接，并在运行时加载的库。
>     - `OBJECT` ：可将给定`add_library`的列表中的源码编译到目标文件，不将它们归档到静态库中，也不能将它们链接到共享对象中。如果需要一次性创建静态库和动态库，那么使用对象库尤其有用。
>     - `MODULE` ：又为DSO组。与`SHARED`库不同，它们不链接到项目中的任何目标，不过可以进行动态加载。该参数可以用于构建运行时插件。

- 创建目标——静态库。

  ```cmake
  add_library(message STATIC Message.hpp Message.cpp)
  ```

### target_link_libraries

> 将库链接到可执行文件。还确保可执行文件可以正确地依赖消息库。

- 将目标库链接到可执行目标。

  ```cmake
  target_link_libraries(hello-world message)
  ```

### message

> 打印信息
>
> - 第一个参数是消息类型。【可忽略】
>   - `无` ：重要消息。
>   - `STATUS` ：非重要消息。
>   - `WARNING` ：警告，会继续执行。
>   - `AUTHOR_WARNING` ：警告，会继续执行。
>   - `SEND_ERROR` ：错误，继续执行，跳过生成的步骤。
>   - `FATAL_ERROR` ：错误，终止所有处理过程。

- 打印 `USE_LIBRARY` 的值。

  ```cmake
  message(STATUS "Compile sources into a library? ${USE_LIBRARY}")
  ```

  

### option

> 以选项的形式显示逻辑开关，用于外部设置，从而切换构建系统的生成行为。
>
> ```cmake
> option(option_variable "help string" initial value)
> ```
>
> - `option_variable` ：该选项的变量的名称。
> - `"help string"` ：记录选项的字符串，在CMake的终端或图形用户界面中可见。
> - `[initial value]`：选项的默认值，可以是`ON`或`OFF`。

- 用选项修改 `USE_LIBRARY` 的值，并设置其默认值为`OFF`

  ```cmake
  option(USE_LIBRARY, "Compile sources into a library", OFF)
  ```

- 使用方式

  ```shell
  cmake -D USE_LIBRARY=ON ..
  ```

### cmake_dependent_option

> ```cmake
> cmake_dependent_option(OPT_VAR "OPT_VAR_DES" DEF_VAL_1 "CONDITION_EXP" DEF_VAR_2)
> ```
>
> `cmake_dependent_option`的目的是要定义一个`option`，这个 `option` 就是 `OPT_VAR`。
>
>  `option` 的描述是 `OPT_VAR_DES`，这个 `option` 的默认值不是常量，而是 `DEF_VAL_1` 或者 `DEF_VAL_2`。
>
> （`DEF_VAL_1`和`DEF_VAL_2`不同，但只能是`ON`或者`OFF`之一），具体是哪一个，取决于表达式 `CONDITION_EXP`。
>
> - 如果表达式 `CONDITION_EXP` 为 `true`，则默认值是 `DEF_VAL_1`。
> - 如果表达式 `CONDITION_EXP` 为 `false`，则默认值是 `DEF_VAL_2`。
>
> 也可以通过 `-D` 直接指定值。

- 如果`USE_LIBRARY`为`ON`，`MAKE_STATIC_LIBRARY`默认值为`OFF`，否则`MAKE_SHARED_LIBRARY`默认值为`ON`。

  ```cmake
  include(CMakeDependentOption)
  
  cmake_dependent_option(
      MAKE_STATIC_LIBRARY "Compile sources into a static library" OFF
      "USE_LIBRARY" ON
      )
      
  cmake_dependent_option(
      MAKE_SHARED_LIBRARY "Compile sources into a shared library" ON
      "USE_LIBRARY" ON
      )
  ```

  

### list

### set

## 流程控制

### if-else

```cmake
if(USE_LIBRARY)
    # add_library will create a static library
    # since BUILD_SHARED_LIBS is OFF
    add_library(message ${_sources})
    add_executable(hello-world hello-world.cpp)
    target_link_libraries(hello-world message)
else()
    add_executable(hello-world hello-world.cpp ${_sources})
endif()
```

- `true` ：`1`、`ON`、`YES`、`true`、`Y` 或 `非零数` 。
- `flase` ： `0`、`OFF`、`NO`、`false`、`N`、`IGNORE、NOTFOUND`、空字符串，或者以`-NOTFOUND`为后缀。

## 全局标志

### BUILD_SHARED_LIBS

> 指定生成库的类型。

# cmake使用示例

> - **定义**：构建系统生成器。

## cmake ..

> 从指定目录中寻找  `CMakeLists.txt` 文件，并在当前目录生成构建器。

- **生成内容**
  - `Makefile`: `make`将运行指令来构建项目。
  - `CMakefile`：包含临时文件的目录，CMake用于检测操作系统、编译器等。此外，根据所选的生成器，它还包含特定的文件。
  - `cmake_install.cmake`：处理安装规则的CMake脚本，在项目安装时使用。
  - `CMakeCache.txt`：如文件名所示，CMake缓存。CMake在重新运行配置时使用这个文件。

## cmake -H. -Bbuild

> 命令跨平台命令
>
> CMake标准使用方式

- `-H.` ：在当前目录寻找  `CMakeLists.txt` 文件。
- `-Bbuild` ：在一个名为`build`的目录中生成所有的文件。

## cmake --build . --target [target-name]

> 按标签构建项目。

## camke -G Ninja ..

> 切换生成器为 ninja。

- **生成内容**
  - `build.ninja`和`rules.ninja`：包含Ninja的所有的构建语句和构建规则。
  - `CMakeCache.txt`：CMake会在这个文件中进行缓存，与生成器无关。
  - `CMakeFiles`：包含由CMake在配置期间生成的临时文件。
  - `cmake_install.cmake`：CMake脚本处理安装规则，并在安装时使用。

## cmake -D USE_LIBRARY=ON ..

> `CMakeLists.txt` 文件中定义`option`后，可通过 `-D` 在外部修改变量值。