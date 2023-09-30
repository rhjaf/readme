The first rule is the default rule
Rule syntax:
```
 	target : preq1 preq2 ...
```
A variablbe named which can be overriden by $cmd$ or $env$
```make
RTE_SDK ?= x86_64
```
```make
include $(RTE_SDK)/mk/rte.vars.mk
```
```make
ifeq ($(RTE_SDK),)
	$(error "Please define RTE_SDK environment variable")
endif
```
assignment operators:
- variables defined with `:=` are expanded once, but variables defined with `=` are expanded whenever they are used

- `${}` and `$()` are the same.
- `$${dir}` will be passed like `$dir` to shell that are executing our makefile
```make
for dir in ${DIR}; do (cd $${dir}; ${MAKE}); done
```
But the better way of above command is
```make
.PHONY: $(DIR)
target: $(DIR)
$(DIR):
    $(MAKE) -C $@
```
By default make's targets are assumed to be file. `phony` target is simply a target that is always out-of-date, so whenever you ask make `<phony_target>`, it will run, independent from the state of the file system ( the $dependecies$ have not been changed). Some common make targets that are often phony are: `all`, `install`, `clean`, `distclean`, `TAGS`, `info`, `check`. Generally all targets in your Makefile which do not produce an output file with the same name as the target name should be `PHONY`.
`$@` is the file name of the target of the rule and it is a make automatic variable. another automatic variable is `$<`, which is the name of first prerequisite. `$?` is the name of all prerequisites that are newer than the target, with space between them. If the target does not exist, all prerequisites will be include. $^ is the name of all prerequisites.
for example:
```make
all: library.cpp main.cpp
```
the results `$@` = `all`, `$<` = `library.cpp`, `$^` = `library.cpp` `main.cpp`  
Defining source code directores to handle build
```make
SRCS-y := src/main_replay_copy_2_cores.c 
```
```make
%.o:	%.c
	$(CC) $(CFLAGS) -c $< -o $@
```
Some special variables:
```make
LDLIBS = -lpcap -lpthread -lm
LDFLAGS = -Wl,--rpath -Wl,/usr/local/lib
CFLAGS += -O3
```