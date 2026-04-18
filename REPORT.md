## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```sh
$ open https://cmake.org/
```
## Report
Представьте, что вы стажер в компании "Formatter Inc.".

### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.
```
$ cat > formatter_lib/CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(formatter)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
EOF
```
### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

```
$ mkdir -p formatter_ex
```
### Создаём formatter_ex.h
```
$ cat > formatter_ex/formatter_ex.h << 'EOF'

 #pragma once
 #include <string>

std::string formatter_ex(const std::string& message);
EOF
```
### Создаём formatter_ex.cpp
```
$ cat > formatter_ex/formatter_ex.cpp << 'EOF'

 #include "formatter_ex.h"
 #include "formatter.h"

std::string formatter_ex(const std::string& message) {
    return ">>> " + formatter(message) + " <<<";
}
EOF
```
### Создаём CMakeLists.txt
```
$ cat > formatter_ex/CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)
target_include_directories(formatter_ex PUBLIC 
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
)
target_link_libraries(formatter_ex formatter)
EOF
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

### Создаём директорию
```
$ mkdir -p hello_world

### Создаём hello_world.cpp
```
$ cat > hello_world/hello_world.cpp << 'EOF'
 #include "formatter_ex.h"
 #include <iostream>

int main() {
    std::cout << formatter_ex("Hello, World!") << std::endl;
    return 0;
}
EOF
```
# Создаём CMakeLists.txt
```
$ cat > hello_world/CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)
target_include_directories(hello_world PRIVATE 
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex
    ${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
)
target_link_libraries(hello_world formatter_ex formatter)
EOF
```
### Сборка проекта
```
$ cmake .. && cmake --build .
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- The C compiler identification is GNU 14.2.0
-- The CXX compiler identification is GNU 14.2.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (0.6s)
-- Generating done (0.0s)
-- Build files have been written to: /home/yulia/yuliyavroma-spec/workspace/projects/lab03/solver/build
[ 20%] Building CXX object CMakeFiles/solver.dir/solver.cpp.o
[ 40%] Building CXX object CMakeFiles/solver.dir/home/yulia/yuliyavroma-spec/workspace/projects/lab03/formatter_ex/formatter_ex.cpp.o
[ 60%] Building CXX object CMakeFiles/solver.dir/home/yulia/yuliyavroma-spec/workspace/projects/lab03/formatter_lib/formatter.cpp.o
[ 80%] Building CXX object CMakeFiles/solver.dir/home/yulia/yuliyavroma-spec/workspace/projects/lab03/solver_lib/solver.cpp.o
[100%] Linking CXX executable solver
[100%] Built target solver
```
### Запуск приложения
```
$ ./solver
---
x1 = 1.000000, x2 = 2.000000
---
```
