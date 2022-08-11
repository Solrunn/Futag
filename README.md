# Оглавление

- [Оглавление](#оглавление)
  - [1. Описание](#1-описание)
  - [2. Инструкция по сборке](#2-инструкция-по-сборке)
  - [3. Примеры использования](#3-примеры-использования)
  - [4. Сборка из исходного кода](#4-сборка-из-исходного-кода)
  - [5. Авторы](#5-авторы)
  - [6. Статьи](#6-статьи)

## 1. Описание

FUTAG — это автоматизированный инструмент генерации фаззинг-целей для программных библиотек.
В отличие от обычных программ, программная библиотека может не содержать точки входа и не принимать входные данные, поэтому создание вручную фаззинг-цели для анализа программных библиотек остается проблемой и требует ресурсов. Одним из решением данной проблемы является автоматизация процесса создания фаззинг-целей, что уменьшает количество затрачиваемых ресурсов.
FUTAG использует инструменты Clang и Clang LLVM в качестве внешнего интерфейса для анализа библиотек и генерации фаззинг-целей.
FUTAG во время работы использует статический анализ для поиска:
- Зависимостей сущностей (типы данных, функции, структуры и т.д.) в исходном коде целевой библиотеки.
- Контекста использования библиотеки.
Далее информация, полученная по результатам статического анализа, используется для генерации фаззинг-целей.

Данный проект основан на LLVM со статическим анализом Clang, а также LLVM lto и распространяется под лицензией ["GPL v3 license"](https://llvm.org/docs/DeveloperPolicy.html#new-llvm-project-license-framework)

- В настоящее время Futag поддерживает генерацию для библиотек языка Си

## 2. Установка

Данная инструкция позволяет собрать копию проекта и запустить её в Unix-подобной системе. 

### Зависимости

Инструмент FUTAG основан на [LLVM-project](https://llvm.org/). Для компиляции проекта необходимо, чтобы следующие пакеты были установлены в вашей системе:

- [CMake](https://cmake.org/) >=3.13.4 [cmake-3.19.3-Linux-x86_64.sh](https://github.com/Kitware/CMake/releases/download/v3.19.3/cmake-3.19.3-Linux-x86_64.sh) - Makefile/workspace generator
- [GCC](https://gcc.gnu.org/)>=5.1.0 C/C++ compiler
- [Python](https://www.python.org/) >=3.8 Automated test suite
- [pip](https://pypi.org/project/pip/) >=22.0.4
- [zlib](http://zlib.net/) >=1.2.3.4 Compression library
- [GNU Make](http://savannah.gnu.org/projects/make) 3.79, 3.79.1 Makefile/build processor

Для получения более детальной информации о зависимостях, необходимых для сборки LLVM, вы можете ознакомиться с документацией по указанной [ссылке](https://llvm.org/docs/GettingStarted.html#requirements)

### Установка:

- Скачать релиз futag-llvm-package.tar.gz

- Установить зависимости:
```bash
  ~$ pip install -r futag-llvm-package/requirements.txt
```

- Установить python-пакет Futag можно по пути futag-llvm-package/python-package/futag-1.1.tar.gz:
```bash
  ~$ pip install futag-1.1.tar.gz
```


## 3. Использования

- Запуск сборки, проверки и анализа

```python
# package futag must be already installed
from futag.preprocessor import *

testing_lib = Builder(
    "Futag/futag-llvm-package/", # path to the futag-llvm-package
    "path/to/library/source/code" # library root
)
testing_lib.auto_build()
testing_lib.analyze()
```

- Генерация и компиляция драйверов

```python
# package futag must be already installed
from futag.generator import *

g = Generator(
"Futag/futag-llvm-package/", # path to the futag-llvm-package
"path/to/library/source/code" # library root
)

# Generate fuzz drivers
g.gen_targets()

# Compile fuzz drivers
g.compile_targets()
```
- Успешно скомпилированные цели находятся в каталоге futag-fuzz-drivers. Каждый драйвер находится внутри своей поддиректории.

- Фаззить скомпилированные цели

```python
from futag.fuzzer import *
f = Fuzzer("/Futag/futag-llvm-package", 
"path/to/library/source/code")
f.fuzz()
```

Подобную информацию можно прочитать [по ссылке](https://github.com/ispras/Futag/tree/main/src/python/futag-package)

## 4. Сборка из исходного кода

- Склонируйте проект:

```bash
  ~$ git clone https://github.com/ispras/Futag
```
- Подготовьте директорию "custom-llvm" запустив скрипт:
```bash
  ~/Futag/custom-llvm$ ./prepare.sh
```
Этот скрипт создает директорию Futag/build и копирует скрипт Futag/custom-llvm/build.sh в неё

- Запустите в "Futag/build" скопированный скрипт:

```bash
  ~/Futag/build$ ./build.sh
```

- В результате инструмент будет установлен в директорию Futag/futag-llvm-package


## 5. Авторы

- Thien Tran (thientc@ispras.ru)
- Shamil Kurmangaleev (kursh@ispras.ru)
- Theodor Arsenij Larionov-Trichkin (tlarionov@ispras.ru)

## 6. Статьи

- C. T. Tran and S. Kurmangaleev, ["Futag: Automated fuzz target generator for testing software libraries"](https://ieeexplore.ieee.org/document/9693749) 2021 Ivannikov Memorial Workshop (IVMEM), 2021, pp. 80-85, doi: 10.1109/IVMEM53963.2021.00021.
