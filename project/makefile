OUT?=./builds
TARGET?=main

all: build
	@gcc $(OUT)/*.o -o $(OUT)/$(TARGET)
	@$(OUT)/$(TARGET)
$(OUT):
	@mkdir -p $(OUT)

build: $(OUT)
	@gcc -I./headers -c ./srcs/a.c -o $(OUT)/a.o
	@gcc -I./headers -c ./srcs/b.c -o $(OUT)/b.o
	@gcc -I./headers -c ./srcs/c.c -o $(OUT)/c.o


clean:
	@rm -rf $(OUT)

.PHONY: all build clean