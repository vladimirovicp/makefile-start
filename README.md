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

* Переменная CC хранит имя компилятора. В данном случае это g++, стандартный компилятор C++

* CFLAGS

	* **-c**- говорит компилятору только компилировать в .o (object) файл, не связывать.
	* **-Wall** - включает все предупреждения (очень полезно для поиска ошибок)

* Дополнительная цель clean, которая удаляет все объектные файлы и исполняемый файл.

Файлы с расширением **.o** (object files, объектные файлы) — это промежуточные файлы, которые создаются компилятором при сборке программы на языках C/C++ (и не только). Они содержат скомпилированный, но ещё не связанный машинный код.

## Посмотрим объектные файлы

### nm — просмотр символов (функций, переменных)

```bash
nm main.o 
                 U _GLOBAL_OFFSET_TABLE_
0000000000000000 T main
                 U _Z11print_hellov
                 U _Z9factoriali
                 U _ZNSolsEi
                 U _ZNSolsEPFRSoS_E
0000000000000017 r _ZNSt8__detail30__integer_to_chars_is_unsignedIjEE
0000000000000018 r _ZNSt8__detail30__integer_to_chars_is_unsignedImEE
0000000000000019 r _ZNSt8__detail30__integer_to_chars_is_unsignedIyEE
                 U _ZSt21ios_base_library_initv
                 U _ZSt4cout
                 U _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_
                 U _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc
```

Обозначения:

* T — символ в тексте (т.е. функция, определена в этом файле)
* U — неопределённый символ (нужен извне, будет найден на этапе связывания)

### objdump — подробный разбор

```bash
objdump -d main.o

main.o:     формат файла elf64-x86-64


Дизассемблирование раздела .text:

0000000000000000 <main>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	53                   	push   %rbx
   5:	48 83 ec 08          	sub    $0x8,%rsp
   9:	e8 00 00 00 00       	call   e <main+0xe>
   e:	48 8b 05 00 00 00 00 	mov    0x0(%rip),%rax        # 15 <main+0x15>
  15:	48 89 c6             	mov    %rax,%rsi
  18:	48 8d 05 00 00 00 00 	lea    0x0(%rip),%rax        # 1f <main+0x1f>
  1f:	48 89 c7             	mov    %rax,%rdi
  22:	e8 00 00 00 00       	call   27 <main+0x27>
  27:	48 8d 05 00 00 00 00 	lea    0x0(%rip),%rax        # 2e <main+0x2e>
  2e:	48 89 c6             	mov    %rax,%rsi
  31:	48 8d 05 00 00 00 00 	lea    0x0(%rip),%rax        # 38 <main+0x38>
  38:	48 89 c7             	mov    %rax,%rdi
  3b:	e8 00 00 00 00       	call   40 <main+0x40>
  40:	48 89 c3             	mov    %rax,%rbx
  43:	bf 05 00 00 00       	mov    $0x5,%edi
  48:	e8 00 00 00 00       	call   4d <main+0x4d>
  4d:	89 c6                	mov    %eax,%esi
  4f:	48 89 df             	mov    %rbx,%rdi
  52:	e8 00 00 00 00       	call   57 <main+0x57>
  57:	48 8b 15 00 00 00 00 	mov    0x0(%rip),%rdx        # 5e <main+0x5e>
  5e:	48 89 d6             	mov    %rdx,%rsi
  61:	48 89 c7             	mov    %rax,%rdi
  64:	e8 00 00 00 00       	call   69 <main+0x69>
  69:	b8 00 00 00 00       	mov    $0x0,%eax
  6e:	48 8b 5d f8          	mov    -0x8(%rbp),%rbx
  72:	c9                   	leave
  73:	c3                   	ret
```

тут мы видем во что компилятор превратил C++ код.