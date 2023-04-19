# Makefile tutor
# Summary

Initially intended to help 42 students to step up their Makefile skills through **a C destined documented template** that evolves gradually, **version by version**. With the aim of making them more digestible and even tasty 🍔

Count **1 hour** to complete this tutorial plus some time to play with the [**examples**](https://github.com/clemedon/Makefile_tutor/tree/main/projects). For those of you who are wondering, as I write this line I invested a total of 102 hours and 20 minutes in the making of this tutorial.

**TL;DR** Refer to the bold text.

[**→ GitHub Page ←**](https://clemedon.github.io/Makefile_tutor/)<br>
[**→ GitHub Repo ←**](https://github.com/clemedon/Makefile_tutor/)

<hr>

**Roadmap**

- [x] [GitHub Page](https://clemedon.github.io/Makefile_tutor/)
- [x] A [ready to make](#usage) project of each template version
- [x] [bold text](#usage) that acts as a summary
- [x] [v1](#version-1) Simplest C project
- [x] [v2](#version-2) Project that include headers
- [x] [v3](#version-3) Project with any kind of directory structure
- [x] [v4](#version-4) Static library *+ introduce auto-gen dependencies*
- [x] [v5](#version-5) Project that uses libraries
- [ ] POSIX Makefile

# Usage

This tutorial is designed to be read line by line **linearly at first**.

Then it can be quickly navigated thanks to the:

- **Brief** of each version which is visible from the [**Index**](#index).
- **Text in bold** that compile the essence of this tutorial.
- [**Return to Index ↑**](#index) buttons at the end of each version.

Each version of the template has an assigned directory in the [**projects**](https://github.com/clemedon/Makefile_tutor/tree/main/projects) directory of the repository, to play with a Makefile open a terminal and run:

```bash
git clone https://github.com/clemedon/Makefile_tutor.git
cd Makefile_tutor/projects
```

```bash
cd <template_version>
make <target>
```

**`C++` users** have to replace `CC = clang` with `CXX = g++` and `CFLAGS` with `CXXFLAGS`.

# Glossary

Each **version** the template has **3 sections**:

- **Structure** is the project structure sketch.
- **Brief** is a summary made from the bold parts of the template comments.
- **Template** is a commented Makefile template with comments (that are always
  placed at the end of the template part that concerns them).

Our **template** will be articulated around the following **parts**:

- `### BEG` Mark the template **beginning**.
- `INGREDIENTS` Build **variables**.
- `UTENSILS` Shell **commands**.
- `RECIPES` Build and extra **rules**.
- `SPEC` **Special targets**.
- `#### END` Mark the template **end**.

According to make a **`rule`** is made of:

- `targets` are the names of the **goals** we want to make.
	- It can be a file or an action,
		- the latter leading to a *pseudo target*.
- `prerequisites` are the goals that must be achieved so that the `rule` can execute.
	- so, the **targets dependencies**.
- `recipe` is the set of lines that **begins with a `TAB`** character and appear in a rule context.

```make
target: prerequisite
    recipe line 1
    recipe line 2
    …
```

# Syntax

The template uses a combination of Makefile syntax and shell script syntax.

- **shell script syntax**
	- only on recipe lines,
	- by default those lines have to **start with a `TAB`** character to be differentiated by make (and passed to the shell).
- **Makefile syntax** is used for **all the other lines**.

About the **equal signs**:

- := **simply expand** the defined variable (like C =).
- = **recursively expand** the defined variable (the expression is expanded afterward, when (and each time) the variable is used).

```make
A := Did $(C)
B = Did $(C)

C  = you understand ?

all:
    $(info $(A)) # output "Did"
    $(info $(B)) # output "Did you understand ?"
```

About **variables**

- `${VAR}` and `$(VAR)` is exactly the same.

## Automatic Variables expansion

- `$<` **leftmost prerequisite**
- `$@` **current target**
- `$^` **all prerequisites**
- `$(@D)` **directory part** of the file name of the target
- `$(@F)` **file part** of the file name of the target

*Cf. Version 1 / Automatic variables in practice.*

# Template

## Index

[**Version 1 / Simplest C project**](#version-1)

> - 42 C coding style conventions
> - builtin variables
> - The C compilation implicit rule
> - pattern rule contains `%` character
> - Automatic variables in practice
> - Illustration of a `make all`
> - C build recap
> - parallelization enabled by `make --jobs`
> - the `.PHONY:` special target

[**Version 2 / Project that include headers**](#version-2)

> - preprocessor's flags
> - print a custom message
> - C compilation implicit rule is overwritten
> - *default goal* `all` appears first
> - `.SILENT:` silences the rules output

[**Version 3 / Project with any kind of directory structure**](#version-3)

> - split the line with a `backslash`
> - substitution reference so `main.c` becomes `src/main.c`
> - generate the `OBJ_DIR` based on `SRC_DIR`
> - compilation rule uses multiple source and object directories

[**Version 4 / Static library**](#version-4)

> - when a header file is modified the executable will rebuild
> - automatically generate a list of dependencies
> - build directory
> - dependency files must be included
> - hyphen symbol to prevent make from complaining
> - creates a static library

[**Version 5 / Project that uses libraries**](#version-5)

> - system library linked by default
> - `addprefix` make function
> - flags and libraries used by the linker
> - `dir` function
> - Build with a library
> - Linking with a library
> - builds each of the required libraries
> - call rules recursively

[**Bonus**](#bonus)

> - `make` and `run` the *default goal*
> - print `<target>` recipe without executing it
> - print the value of an arbitrary variable
> - update the git repository

## Version 1 - Simplest C project

### V1 Structure

```
    before build:    after build:
    .                .
    ├── Makefile     ├── Makefile
    └── main.c       ├── main.o
                     ├── main.c
                     └── icecream
```

### V1 Brief

- 42 C coding style conventions
- builtin variables
- The C compilation implicit rule
- pattern rule contains `%` character
- Automatic variables in practice
- Illustration of a `make all`
- C build recap
- parallelization enabled by `make --jobs`
- the `.PHONY:` special target

### V1 Template

```make
####################################### BEG_1 ####

NAME        := icecream

#------------------------------------------------#
#   INGREDIENTS                                  #
#------------------------------------------------#
# SRCS      source files
# OBJS      object files
#
# CC        compiler
# CFLAGS    compiler flags

SRCS        := main.c
OBJS        := main.o

CC          := clang
CFLAGS      := -Wall -Wextra -Werror

#------------------------------------------------#
#   UTENSILS                                     #
#------------------------------------------------#
# RM        force remove
# MAKEFLAGS make flags

RM          := rm -f
MAKEFLAGS   += --no-print-directory

#------------------------------------------------#
#   RECIPES                                      #
#------------------------------------------------#
# all       default goal
# $(NAME)   linking .o -> binary
# clean     remove .o
# fclean    remove .o + binary
# re        remake default goal

all: $(NAME)

$(NAME): $(OBJS)
    $(CC) $(OBJS) -o $(NAME)

clean:
    $(RM) $(OBJS)

fclean: clean
    $(RM) $(NAME)

re:
    $(MAKE) fclean
    $(MAKE) all

#------------------------------------------------#
#   SPEC                                         #
#------------------------------------------------#

.PHONY: clean fclean re

####################################### END_1 ####
```

- the 42 C coding style conventions dictate:
	- The choice of the `CC` and `CFLAGS` values,
	- `NAME`, `clean`, `fclean`, `all` and `re` as the basic rules
	- not using a wildcard to auto generate the sources list
	- do not hesitate to disagree and change it (like renaming `clean` and `fclean` to the more GNU conventional `mostlyclean` and `clean` respectively). 

- **builtin variables**
	- you can find them all with `make --print-data-base` command.
	 - the implicit rules can be found with `make -p -f/dev/null | less`.
	- `MAKE` and `MAKEFLAGS` are **builtin variables** like `CFLAGS` 
	- `MAKE` value corresponds to the `make` executable being run
	- `MAKEFLAGS` to its flags.
		- When a Makefile is executed from another Makefile, `MAKEFLAGS` variables are inherited from the caller's.
		- `--no-print-directory` to `MAKEFLAGS` content to have a clearer output,
			- try to remove it and `make re` to see the difference.

- **The C compilation implicit rule** looks like this:

```make
%.o: %.c
    $(CC) $(CFLAGS) $(CPPFLAGS) -c -o $@ $<
```

- `%.o` expands to each objects, `%.c` to each sources, `$@` to the first  target (which is `%.o`) and `$<` to the leftmost prerequisite (which is `%.c`).
- A **pattern rule** is a rule whose target **contains** a **`%` character**.
	- `%` means "exactly one of them".
		- each `.o` requires a `.c` with the same name
		- `$(CC)[…]` recipe line will execute as many times as there are `.o: .c` pairs,
		- creating a `.o` for every `.c` , one at a time. 
  - `-c`  → only compile without linking
  - `-o` → specify objects name

- **Illustration of a `make all`**:

```make
all: $(NAME)                            3 ← 2

$(NAME): $(OBJS)                        2 ← 1
    $(CC) $(OBJS) -o $(NAME)

%.o: %.c                                1 ← 0
    $(CC) $(CFLAGS) -c -o $@ $<
```

- **C build recap**
	- `%.o` → sources to be compiled into objects,
	- `-c`  → only compile without linking.
	- `-o` → specify the objects name.

- For the `re` rule we have no choice but make an external call to our Makefile because we should not rely on the order in which prerequisites are specified.
	- For example `re: fclean all` wouldn't be reliable if **parallelization** was **enabled by `make --jobs`**.

- `.PHONY:` special target
	- special targets → make ejecuta las `recipes` de esa `rule` aunque haya un archivo con ese mismo nombre
		- al contrario que en los demás casos
	- Try to remove the `.PHONY: re`, create a file named `re` at the Makefile level  in the project directory and run `make re`. It won't work.
		- si creas un archivo que se llame `all` no da problemas.
			- no tiene `recipes`, sólo `prerequisites`.
			- lo que make se salta son los `recipes`, la llamada a los `prerequisites` se sigue haciendo.

---

## Version 2 - con headers.h

### V2 Structure

```
    before build:     after build:
    .                 .
    ├── Makefile      ├── Makefile
    ├── main.c        ├── main.o
    └── icecream.h    ├── main.c
                      ├── icecream.h
                      └── icecream
```

### V2 Brief

- preprocessor's flags
- print a custom message
- C compilation implicit rule is overwritten
- *default goal* `all` appears first
- `.SILENT:` silences the rules output

### V2 Template

```make
####################################### BEG_2 ####

NAME        := icecream

#------------------------------------------------#
#   INGREDIENTS                                  #
#------------------------------------------------#
# SRCS      source files
# OBJS      object files
#
# CC        compiler
# CFLAGS    compiler flags
# CPPFLAGS  preprocessor flags

SRCS        := main.c
OBJS        := main.o

CC          := clang
CFLAGS      := -Wall -Wextra -Werror
CPPFLAGS    := -I .
```

- `CPPFLAGS`
	- **preprocessor's flags** like `-I <include_dir>`,
	- permite no tener que escribir el path completo del header.h en los sources si los header.h están en `include dir`
		- `#include "icecream.h"` instead of `#include "../../path/to/include/icecream.h"`.

```make
#------------------------------------------------#
#   UTENSILS                                     #
#------------------------------------------------#
# RM        force remove
# MAKEFLAGS make flags

RM          := rm -f
MAKEFLAGS   += --no-print-directory

#------------------------------------------------#
#   RECIPES                                      #
#------------------------------------------------#
# all       default goal
# $(NAME)   linking .o -> binary
# %.o       compilation .c -> .o
# clean     remove .o
# fclean    remove .o + binary
# re        remake default goal

all: $(NAME)

$(NAME): $(OBJS)
    $(CC) $(OBJS) -o $(NAME)
    $(info CREATED $(NAME))

%.o: %.c
    $(CC) $(CFLAGS) $(CPPFLAGS) -c -o $@ $<
    $(info CREATED $@)

clean:
    $(RM) $(OBJS)

fclean: clean
    $(RM) $(NAME)

re:
    $(MAKE) fclean
    $(MAKE) all
```

- `info` → **print a custom message**
	- *We prefer `info` to shell `echo` because it is a make function.
	- `echo` sólo puede ser usada en `recipes`, `info` en cualquier lado.

- no importa el orden de las `rules`, siempre que la primera sea `all`, la que va a ser triggered por `make`

```make
#------------------------------------------------#
#   SPEC                                         #
#------------------------------------------------#

.PHONY: clean fclean re
.SILENT:
```

- .SILENT → silenciar impresión de los `recipe` de cada `rule` especificada como `prerequisite`
	- `prerequisite` vacío → todas las `rules`
	- *To silence at the recipe-line level we can prefix the wanted recipe lines with an `@` symbol.*

```make
####################################### END_2 ####
```

[**Return to Index ↑**](#index)

## Version 3 - any kind of dir structure

### V3 Structure

```
    before build:          after build:
    .                      .
    ├── src                ├── src
    │   ├── base           │   ├── base
    │   │   ├── water.c    │   │   ├── water.c
    │   │   └── milk.c     │   │   └── milk.c
    │   ├── arom           │   ├── arom
    │   │   └── coco.c     │   │   └── coco.c
    │   └── main.c         │   └── main.c
    ├── include            ├── obj
    │   └── icecream.h     │   ├── base
    └── Makefile           │   │   ├── water.o
                           │   │   └── milk.o
                           │   ├── arom
                           │   │   └── coco.o
                           │   └── main.o
                           ├── include
                           │   └── icecream.h
                           ├── Makefile
                           └── icecream
```

### V3 Brief

- split the line with a `backslash`
- substitution reference so `main.c` becomes `src/main.c`
- generate the `OBJ_DIR` based on `SRC_DIR`
- compilation rule uses multiple source and object directories

### V3 Template

```make
####################################### BEG_3 ####

NAME        := icecream

#------------------------------------------------#
#   INGREDIENTS                                  #
#------------------------------------------------#
# SRC_DIR   source directory
# OBJ_DIR   object directory
# SRCS      source files
# OBJS      object files
#
# CC        compiler
# CFLAGS    compiler flags
# CPPFLAGS  preprocessor flags

SRC_DIR     := src
OBJ_DIR     := obj
SRCS        := \
    main.c          \
    arom/coco.c     \
    base/milk.c     \
    base/water.c
SRCS        := $(SRCS:%=$(SRC_DIR)/%)
OBJS        := $(SRCS:$(SRC_DIR)/%.c=$(OBJ_DIR)/%.o)

CC          := clang
CFLAGS      := -Wall -Wextra -Werror
CPPFLAGS    := -I include
```

- We can **split the line** by ending it **with a `backslash`** to increase the readability of `SRCS` content and facilitate its modification.

- **substitution reference** `$(SRCS:%=$(SRC_DIR)/%)`
	- cada item de `SRCS` representado por `%` → `%` más `$(SRC_DIR)/`
		- `main.c` →`src/main.c`.
	- `OBJS` hace lo mismo para convertir `src/main.c` en `src/main.o`, dedicated to the `OBJ_DIR`.


```make
#------------------------------------------------#
#   UTENSILS                                     #
#------------------------------------------------#
# RM        force remove
# MAKEFLAGS make flags
# DIR_DUP   duplicate directory tree

RM          := rm -f
MAKEFLAGS   += --no-print-directory
DIR_DUP     = mkdir -p $(@D)
```

- `DIR_DUP` **generate the `OBJ_DIR` based on `SRC_DIR`** structure
	- `mkdir -p`  crea un directorio y sus padres
	- el directorio será el del target, `$(@D)` de la `rule` en la que se ejecute la `recipe` que llame a `DIR_DUP`.

```make
#------------------------------------------------#
#   RECIPES                                      #
#------------------------------------------------#
# all       default goal
# $(NAME)   linking .o -> binary
# %.o       compilation .c -> .o
# clean     remove .o
# fclean    remove .o + binary
# re        remake default goal

all: $(NAME)

$(NAME): $(OBJS)
    $(CC) $(OBJS) -o $(NAME)
    $(info CREATED $(NAME))

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
    $(DIR_DUP)
    $(CC) $(CFLAGS) $(CPPFLAGS) -c -o $@ $<
    $(info CREATED $@)

clean:
    $(RM) $(OBJS)

fclean: clean
    $(RM) $(NAME)

re:
    $(MAKE) fclean
    $(MAKE) all
```

- The **compilation rule** `.o: %.c` becomes `$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c`
	- cause our structure **uses dedicated source and object directories**.

```make
#------------------------------------------------#
#   SPEC                                         #
#------------------------------------------------#

.PHONY: clean fclean re
.SILENT:

####################################### END_3 ####
```

[**Return to Index ↑**](#index)

## Version 4 - static library

### V4 Structure

Builds a **library** →no hay `main.c`.
generamos **dependencies**, que se guardan con los objects, así que renombramos el directorio de `obj` a uno más genérico, `build`.

```
    before build:          after build:
    .                      .
    ├── src                ├── src
    │   ├── base           │   ├── base
    │   │   ├── water.c    │   │   ├── water.c
    │   │   └── milk.c     │   │   └── milk.c
    │   └── arom           │   └── arom
    │       └── coco.c     │       └── coco.c
    ├── include            ├── include
    │   └── icecream.h     │   └── icecream.h
    └── Makefile           ├── .build
                           │   ├── base
                           │   │   ├── water.o
                           │   │   ├── water.d
                           │   │   ├── milk.o
                           │   │   └── milk.d
                           │   └── arom
                           │       ├── coco.o
                           │       └── coco.d
                           ├── Makefile
                           └── libicecream.a
```

### V4 Brief

- when a header file is modified the executable will rebuild
- automatically generate a list of dependencies
- build directory
- dependency files must be included
- hyphen symbol to prevent make from complaining
- creates a static library

### V4 Template

```make
####################################### BEG_4 ####

NAME        := libicecream.a

#------------------------------------------------#
#   INGREDIENTS                                  #
#------------------------------------------------#
# SRC_DIR   source directory
# SRCS      source files
#
# BUILD_DIR object directory
# OBJS      object files
# DEPS      dependency files
#
# CC        compiler
# CFLAGS    compiler flags
# CPPFLAGS  preprocessor flags

SRC_DIR     := src
SRCS        :=  \
    arom/coco.c \
    base/milk.c \
    base/water.c
SRCS        := $(SRCS:%=$(SRC_DIR)/%)

BUILD_DIR   := .build
OBJS        := $(SRCS:$(SRC_DIR)/%.c=$(BUILD_DIR)/%.o)
DEPS        := $(OBJS:.o=.d)

CC          := clang
CFLAGS      := -Wall -Wextra -Werror
CPPFLAGS    := -MMD -MP -I include
AR          := ar
ARFLAGS     := -r -c -s
```

- make controla los timestamp de los  `.c`, pero no los `.h` → **when a header file is modified** make won't consider **the executable** to be out of date → **will** not **rebuild** it.
	- to change this → add the header files as additional prerequisites en la compilación.
		- Doing this manually for multiple sources and headers is both tedious and error prone.
		- `-MMD` in `CPPFLAGS` → the compiler will **automatically generate a list of dependencies** for each object file encountered during the compilation. → no hace falta incluir los `.h` como `prerequisite`.
			- `-MP` → prevents errors that are triggered if a header file has been deleted or renamed.
  - Dependency files must be included into our Makefile right after the objects creation → to obtain their names we copy `OBJS` into `DEPS` and use *substitution reference* to turn `.o` into `.d`.

- Una librería estática no es un binario, sino una colección de objetos.
	- `ar` → **creates a static library** during the linking step of the build.
	- `-r` to replace the older objects with the new ones with
	- `-c` to create the library if it does not exist and
	- `-s` to write an index into the archive or update an existing one.

```make
#------------------------------------------------#
#   UTENSILS                                     #
#------------------------------------------------#
# RM        force remove
# MAKEFLAGS make flags
# DIR_DUP   duplicate directory tree

RM          := rm -f
MAKEFLAGS   += --no-print-directory
DIR_DUP     = mkdir -p $(@D)

#------------------------------------------------#
#   RECIPES                                      #
#------------------------------------------------#
# all       default goal
# $(NAME)   link .o -> library
# %.o       compilation .c -> .o
# clean     remove .o
# fclean    remove .o + binary
# re        remake default goal

all: $(NAME)

$(NAME): $(OBJS)
    $(AR) $(ARFLAGS) $(NAME) $(OBJS)
    $(info CREATED $(NAME))

$(BUILD_DIR)/%.o: $(SRC_DIR)/%.c
    $(DIR_DUP)
    $(CC) $(CFLAGS) $(CPPFLAGS) -c -o $@ $<
    $(info CREATED $@)

-include $(DEPS)

clean:
    $(RM) $(OBJS) $(DEPS)

fclean: clean
    $(RM) $(NAME)

re:
    $(MAKE) fclean
    $(MAKE) all
```

- **Dependency files** `DEPS`are written in the make language and **must be included** into our Makefile to be read. The `include` directive work the same as C `#include`, it tells make to suspend its current Makefile reading and read the included files before continuing.
	- include the dependencies after they are created → after the compilation rule that invoke `-MMD` via `$(CPPFLAGS)`.
	- el `-` inicial de `-include $(DEPS)` →  **to prevent make from complaining** when a non-zero status code is encountered, which can be caused here by a missing file from our generated dependency files list.

```make
#------------------------------------------------#
#   SPEC                                         #
#------------------------------------------------#

.PHONY: clean fclean re
.SILENT:

####################################### END_4 ####
```

[**Return to Index ↑**](#index)

## Version 5 - Use of libraries

### V5 Structure

Builds an `icecream` **program that uses** `libbase` and `libarom` **libraries**. Both libraries are v4 based.

```
    before build:              after build:
    .                          .
    ├── src                    ├── src
    │   └── main.c             │   └── main.c
    ├── lib                    ├── lib
    │   ├── libbase            │   ├── libbase
    │   │   ├── src            │   │   ├── src
    │   │   │   ├── water.c    │   │   │   ├── water.c
    │   │   │   └── milk.c     │   │   │   └── milk.c
    │   │   ├── include        │   │   ├── include
    │   │   │   └── base.h     │   │   │   └── base.h
    │   │   └── Makefile       │   │   ├── .build
    │   └── libarom            │   │   │   ├── water.o
    │       ├── src            │   │   │   ├── water.d
    │       │   ├── coco.c     │   │   │   ├── milk.o
    │       │   └── cherry.c   │   │   │   └── milk.d
    │       ├── include        │   │   ├── Makefile
    │       │   └── arom.h     │   │   └── libbase.a
    │       └── Makefile       │   └── libarom
    ├── include                │       ├── src
    │   └── icecream.h         │       │   ├── coco.c
    └── Makefile               │       │   └── cherry.c
                               │       ├── include
                               │       │   └── arom.h
                               │       ├── .build
                               │       │   ├── coco.o
                               │       │   ├── coco.d
                               │       │   ├── cherry.o
                               │       │   └── cherry.d
                               │       ├── Makefile
                               │       └── libarom.a
                               ├── include
                               │   └── icecream.h
                               ├── .build
                               │   ├── main.o
                               │   └── main.d
                               ├── Makefile
                               └── icecream
```

### V5 Brief

- system library linked by default
- `addprefix` make function
- flags and libraries used by the linker
- `dir` function
- Build with a library
- Linking with a library
- builds each of the required libraries
- call rules recursively

### V5 Template

```make
# @author   clemedon (Clément Vidon)
####################################### BEG_5 ####

NAME        := icecream

#------------------------------------------------#
#   INGREDIENTS                                  #
#------------------------------------------------#
# LIBS        libraries to be used
# LIBS_TARGET libraries to be built
#
# INCS        header file locations
#
# SRC_DIR     source directory
# SRCS        source files
#
# BUILD_DIR   build directory
# OBJS        object files
# DEPS        dependency files
#
# CC          compiler
# CFLAGS      compiler flags
# CPPFLAGS    preprocessor flags
# LDFLAGS     linker flags
# LDLIBS      libraries name

LIBS        := arom base m
LIBS_TARGET :=            \
    lib/libarom/libarom.a \
    lib/libbase/libbase.a

INCS        := include    \
    lib/libarom/include   \
    lib/libbase/include

SRC_DIR     := src
SRCS        := main.c
SRCS        := $(SRCS:%=$(SRC_DIR)/%)

BUILD_DIR   := .build
OBJS        := $(SRCS:$(SRC_DIR)/%.c=$(BUILD_DIR)/%.o)
DEPS        := $(OBJS:.o=.d)

CC          := clang
CFLAGS      := -Wall -Wextra -Werror
CPPFLAGS    := $(addprefix -I,$(INCS)) -MMD -MP
LDFLAGS     := $(addprefix -L,$(dir $(LIBS_TARGET)))
LDLIBS      := $(addprefix -l,$(LIBS))
```

-  `m` de `LIBS` no está en  `LIBS_TARGET` porque `m` is a **system library** (`libm` for mathematical functions found in `math.h`).
	- `libm` no se linka por defecto, al contrario que `libc` (we don't need to provide `-lc` flag to our linker)
		- hay que incluirla en librerías a linkar pero no hace falta especificar el path.

- `CPPFLAGS` we use **`addprefix`** that, as its name suggests is a **make function** that allows you to add a prefix, here a `-I` to each of the item found in `$(INCS)` (that contains the paths to our project and libraries headers).

- `LDFLAGS` and `LDLIBS` contain the **flags and libraries** that will be **used by the linker** `ld` to link the library to our project sources.

- The **`dir` function** means that we want to keep only directory part of the given item, there exists a `notdir` function that does the opposite to keep only the file name of the given item.

- **Build with a library** requires three flags: `-I` tell the compiler where to find the lib header files, `-L` tells the linker where to look for the library and `-l` the name of this library (without its conventional `lib` prefix).

For example: `-I lib/libarom/include -L lib/libarom -l arom`

```make

#------------------------------------------------#
#   UTENSILS                                     #
#------------------------------------------------#
# RM        force remove
# MAKEFLAGS make flags
# DIR_DUP   duplicate directory tree

RM          := rm -f
MAKEFLAGS   += --silent --no-print-directory
DIR_DUP     = mkdir -p $(@D)

#------------------------------------------------#
#   RECIPES                                      #
#------------------------------------------------#
# all       default goal
# $(NAME)   link .o -> archive
# $(LIBS)   build libraries
# %.o       compilation .c -> .o
# clean     remove .o
# fclean    remove .o + binary
# re        remake default goal
# run       run the program
# info      print the default goal recipe

all: $(NAME)

$(NAME): $(OBJS) $(LIBS_TARGET)
    $(CC) $(LDFLAGS) $(OBJS) $(LDLIBS) -o $(NAME)
    $(info CREATED $(NAME))

$(LIBS_TARGET):
    $(MAKE) -C $(@D)

$(BUILD_DIR)/%.o: $(SRC_DIR)/%.c
    $(DIR_DUP)
    $(CC) $(CFLAGS) $(CPPFLAGS) -c -o $@ $<
    $(info CREATED $@)

-include $(DEPS)

clean:
    for f in $(dir $(LIBS_TARGET)); do $(MAKE) -C $$f clean; done
    $(RM) $(OBJS) $(DEPS)

fclean: clean
    for f in $(dir $(LIBS_TARGET)); do $(MAKE) -C $$f fclean; done
    $(RM) $(NAME)

re:
    $(MAKE) fclean
    $(MAKE) all
```

- **Linking with a library** requires special attention to the order of the linking flags. In our case we need to make sure that `$(LDFLAGS)` and `$(LDLIBS)` passes respectively before and after the `$(OBJS)` in the linking recipe.

- `$(LIBS_TARGET)` rule **builds each of the required libraries** found in the `INGREDIENTS` part. It is a `$(NAME)` prerequisite for the same reason as `$(OBJS)` because our *final goal* needs the libraries as well as the objects to be built.

- As both rules `clean` and `fclean` appear in the Makefile of all our `$(LIBS_TARGET)` we can **call** these **rules** for each of them **recursively** using a shell `for` loop. Here again we use the `dir` function to only keep the directory part of the library.

```make
#------------------------------------------------#
#   SPEC                                         #
#------------------------------------------------#

.PHONY: clean fclean re
.SILENT:

####################################### end_5 ####
```

[**Return to Index ↑**](#index)

## Bonus

### Extra rules

```make
.PHONY: run
run: re
    -./$(NAME)
```

- `run` is a simple rule that **`make` and `run` the default goal**. We start the shell command with the `hyphen` symbol to prevent make from interrupting its execution if our program execution returns a non-zero value.

```make
info-%:
    $(MAKE) --dry-run --always-make $* | grep -v "info"
```

- `info-<target>` rule will execute a `make <target>` command with `--dry-run` to **print** the **`<target>` recipe without executing it**, `--always-make` to `make` even if the targets already exist and `grep -v` to filter the output. `$*` expands to the value given in place of the `%` at the rule call. For example with `make info-re` `$*` will expand to `re`.

```make
print-%:
    $(info '$*'='$($*)')
```

- The `print-<variable>` that works like `print-<rule>` will **print the value of an arbitrary variable**, for example a `make print-CC` will output `CC=clang`.

```make
.PHONY: update
update:
    git stash
    git pull
    git submodule update --init
    git stash pop
```

- The `update` rule will **update the git repository** to its last version, as well as its *git submodules*. `stash` commands saves eventual uncommitted changes and put them back in place once the update is done.

# Sources

| Topic | Website |
| ----- | ------- |
| doc | [**docs.w3cub.com**](https://docs.w3cub.com/gnu_make/)  |
| manual | [**gnu.org**](https://www.gnu.org/software/make/manual/html_node)  |
| a richer tutorial | [**makefiletutorial.com**](https://makefiletutorial.com/)  |
| order-only exquisite | [**stackoverflow.com**](https://stackoverflow.com/a/68584653)  |
| c libraries | [**docencia.ac.upc.edu**](https://docencia.ac.upc.edu/FIB/USO/Bibliografia/unix-c-libraries.html#creating_static_archive)  |
| auto-deps gen | [**make.mad-scientist.net**](http://make.mad-scientist.net/papers/advanced-auto-dependency-generation/)  |
| auto-deps gen | [**scottmcpeak.com**](https://scottmcpeak.com/autodepend/autodepend.html)  |
| auto-deps gen | [**microhowto.info**](http://www.microhowto.info/howto/automatically_generate_makefile_dependencies.html)  |
| include statement | [**gnu.org**](https://www.gnu.org/software/make/manual/html_node/Include.html)  |
| redis makefile | [**github.com**](https://github.com/redis/redis/blob/unstable/src/Makefile)  |

# Contact

```
cvidon   42
clemedon icloud
```

<sub><i>Copyright 2022 Clément Vidon. All Rights Reserved.</i></sub>
