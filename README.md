# makefile-start

## links 
* [Makefile для самых маленьких](https://habr.com/ru/articles/155201/)
* [Что такое Makefile и как начать его использовать](https://ru.hexlet.io/blog/posts/makefile-as-task-runner)
* [Книга Си](https://api.pageplace.de/preview/DT0400.9785970602294_A45921231/preview-9785970602294_A45921231.pdf)
* [Справочник Си](https://ru.cppreference.com/w/c.html)


## Plugin vscode

* C/C++
* C/C++ Extension Pack
* Makefile Tools

```bash
apt-get install gcc-c++
g++ --version
x86_64-alt-linux-g++ (GCC) 13.2.1 20240128 (ALT Sisyphus 13.2.1-alt3)
Copyright (C) 2023 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

```bash
apt-get install make
make --version
GNU Make 4.4
Эта программа собрана для x86_64-alt-linux-gnu
Copyright (C) 1988-2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

## Компиляция руками

Самый простой способ собрать программу:
```bash
g++ main.cpp hello.cpp factorial.cpp -o hello
```

```bash
./hello
Hello World!
The factorial of 5 is 120
```
Каждый раз набирать такое неудобно, поэтому будем автоматизировать.

## Самый простой Мейкфайл

>цель: зависимости
>[tab] команда


### Makefile-1
```bash
all:
	g++ main.cpp hello.cpp factorial.cpp -o hello
```


```bash
make -f Makefile-1
```

### vscode настройка tab
* Внизу справа (в строке состояния) найди надпись Spaces: 4 или Tab Size: 4.
* Кликни на неё — откроется меню.
* Выбери: "Indent Using Tabs
* Нажми "Convert Indentation to Tabs", если хочешь сразу заменить пробелы на табы в текущем файле.


### Makefile-2
```bash
all: hello

hello: main.o factorial.o hello.o
	g++ main.o factorial.o hello.o -o hello

main.o: main.cpp
	g++ -c main.cpp

factorial.o: factorial.cpp
	g++ -c factorial.cpp

hello.o: hello.cpp
	g++ -c hello.cpp

clean:
	rm -rf *.o hello
```

```bash
make -f Makefile-2 clean
```


### Makefile-3

Переменные — очень удобная штука. Для их использования надо просто присвоить им значение до момента их использования. После этого можно подставлять их значение в нужное место вот таким способом: $(VAR)

```bash
# Это комментарий, который говорит, что переменная CC указывает компилятор, используемый для сборки
CC=g++
#Это еще один комментарий. Он поясняет, что в переменной CFLAGS лежат флаги, которые передаются компилятору
CFLAGS=-c -Wall

all: hello

hello: main.o factorial.o hello.o
	$(CC) main.o factorial.o hello.o -o hello

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

factorial.o: factorial.cpp
	$(CC) $(CFLAGS) factorial.cpp

hello.o: hello.cpp
	$(CC) $(CFLAGS) hello.cpp

clean:
	rm -rf *.o hello
```