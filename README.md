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

4. Можно собрать через Makefile с динамической библиотекой
```bash
nano Makefile # команды должны начинаться с табуляции
# Названия файлов
TARGET = myprogram
LIBRARY = libmycode.so
OBJ_FILES = main.o lib.o

# Компилятор и флаги
CXX = g++
CXXFLAGS = -fPIC -Wall
LDFLAGS = -L. -lmycode

# Правила сборки
all: $(LIBRARY) $(TARGET)

# Сборка объекта lib.o
lib.o: lib.cpp
	$(CXX) $(CXXFLAGS) -c lib.cpp -o lib.o

# Сборка объекта main.o
main.o: main.cpp
	$(CXX) -c main.cpp -o main.o

# Сборка динамической библиотеки
$(LIBRARY): lib.o
	$(CXX) -shared lib.o -o $(LIBRARY)

# Сборка основной программы
$(TARGET): main.o $(LIBRARY)
	$(CXX) main.o -o $(TARGET) $(LDFLAGS)

# Очистка
clean:
	rm -f $(OBJ_FILES) $(LIBRARY) $(TARGET)

.PHONY: all clean

$ make
g++ -fPIC -Wall -c lib.cpp -o lib.o
g++ -shared lib.o -o libmycode.so
g++ -c main.cpp -o main.o
g++ main.o -o myprogram -L. -lmycode

$ LD_LIBRARY_PATH=. ./myprogram
Hello from the library!
```
