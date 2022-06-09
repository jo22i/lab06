[![Actions' artifacts is here](https://github.com/jo22i/lab06/actions/workflows/action.yml/badge.svg)](https://github.com/jo22i/lab06/actions/workflows/action.yml)

# Лабораторная работа №6

## Выполнил - студент группы ИУ8-22 Мельников Егор

### Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами.</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)


1. Создаём CMakeLists.txt для каждой библиотеки по отдельности (в нашем случае я взял готовые из [предыдущей лабораторной работы](https://github.com/jo22i/lab03), а также для сборки исполняемого файла solver_app

```cmake
cmake_minimum_required(VERSION 3.10)

project(solver_app)

include_directories("../formatter_lib")
include_directories("../formatter_ex_lib")
include_directories("../solver_lib")

add_library(formatter STATIC "../formatter_lib/formatter.cpp")
add_library(formatter_ex STATIC "../formatter_ex_lib/formatter_ex.cpp")
add_library(solver STATIC "../solver_lib/solver.cpp")

add_executable(solver_app "../solver_application/equation.cpp")

target_link_libraries(solver_app formatter_ex formatter solver)

include("../CPackConfig.cmake")
```

2. Создаём файл CPackConfig.cmake для указания менеджеру пакетов CPack на особенности пакетирования файлов в различные архивы/установочные пакеты

```cmake
install(TARGETS solver_app)
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})

set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")

set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/../README.md)

set(CPACK_RPM_PACKAGE_NAME "solver_app")
set(CPACK_RPM_PACKAGE_GROUP "solver")
set(CPACK_RPM_PACKAGE_VERSION 1.0.0)
set(CPACK_RPM_PACKAGE_RELEASE 1)

set(CPACK_DEBIAN_PACKAGE_NAME "libsolver_app-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.10")
set(CPACK_DEBIAN_PACKAGE_VERSION 1.0.0)
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "jo22i")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)

set(CPACK_ARCHIVE_FILE_NAME "solver_app")

include(CPack)
```
3. Создаём .yml файл для автоматической сборки программы и её пакетироваия и загрузки в качестве артефактов на GitHub Actions

```yml
name: CPack_action

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  Build:
    runs-on: ubuntu-latest
    
    steps:
    - name: checkout
      uses: actions/checkout@v3
      
    - name: Creating directory for future binary and source code
      run: |
        mkdir artifacts
    
    - name: Build a *solver* application
      run: |
        cmake -H. -B_build
        cmake --build _build --target package
        cd _build
        cpack -G "TGZ"
        cpack -G "DEB"
        cpack -G "RPM"
        cpack -G "ZIP"
        mv *.tar.gz ../../artifacts
        mv *.deb ../../artifacts
        mv *.rpm ../../artifacts
        mv *.zip ../../artifacts
      shell: bash
      working-directory: solver_application

    - name: Upload_artifacts
      uses: actions/upload-artifact@v3
      with:
        name: artifact
        path: artifacts
```
