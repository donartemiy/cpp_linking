# cpp_linking
Пример линковки

```bash
# Создаём 2 файла
# Библиотека
$ nano lib.cpp
// lib.cpp
#include <iostream>

void hello() {
    std::cout << "Hello from the library!" << std::endl;
}

# Основная программа
$ nano main.cpp
// main.cpp
#include <iostream>

void hello(); // Объявление функции из библиотеки

int main() {
    hello(); // Вызов функции из библиотеки
    return 0;
}

```

Сборка
1. Компиляция исходников в объектные файлы
```bash
$ g++ -c lib.cpp -o lib.o    # Компилируем lib.cpp в объектный файл lib.o
$ g++ -c main.cpp -o main.o  # Компилируем main.cpp в объектный файл main.o
```
2. Статическая линковка:
```bash
$ g++ main.o -L. -lmycode -o myprogram
# -L. указывает компилятору искать библиотеки в текущем каталоге.
# -lmycode указывает компилятору использовать библиотеку libmycode.a.
./myprogram  # запуск программы
$ Hello from the library!
```
3. Динамическая линковка:
```bash
$ g++ -fPIC -shared lib.cpp -o libmycode.so  # Компиляция файла библиотеки в динамическую библиотеку
# -fPIC означает "Position Independent Code", который необходим для динамических библиотек.
# -shared указывает, что вы хотите создать динамическую библиотеку.
$ g++ main.o -L. -lmycode -o myprogram  # Линковка с динамической библиотекой

LD_LIBRARY_PATH=. ./myprogram  # Запуск программы. Нужно принудительно указать местоположение библиотеки, иначе ищет в /lib, /usr/lib
$ Hello from the library!
```
