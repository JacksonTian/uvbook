examples=\
	helloworld\
	idle-basic\
	uvcat\
	uvtee\
	onchange\
	thread-create\
	queue-work\
	progress\
	tcp-echo-server\
	dns\
	udp-dhcp\
	idle-compute\
	ref-timer\
	spawn\
	detach\
	proc-streams\
	cgi\
	pipe-echo-server\
	multi-echo-server\
	tty\
	tty-gravity\
	interfaces\
	locks \
	signal

UV_PATH=$(shell pwd)/../libuv
UV_LIB = $(UV_PATH)/libuv.a
CFLAGS=-g -Wall -I$(UV_PATH)/include

uname_S=$(shell uname -s)

ifeq (Darwin, $(uname_S))
CFLAGS+=-framework CoreServices
SHARED_LIB_FLAGS=-bundle -undefined dynamic_lookup -o plugin/libhello.dylib
endif

ifeq (Linux, $(uname_S))
CFLAGS+=-lrt -ldl -lm -lev -pthread
SHARED_LIB_FLAGS=-shared -Wl,-soname,libhello.so -o plugin/libhello.so
PLUGIN_EXE_FLAGS=-Wl,-export-dynamic
endif

all: $(UV_LIB) plugin/plugin proc-streams/test cgi/tick multi-echo-server/worker uvwget/uvwget
	for dir in $(examples); do cd $$dir; echo "--- `pwd`"; gcc $(CFLAGS) -o $$dir main.c $(UV_LIB); cd ..; done

plugin/plugin: plugin/*.c
	gcc $(CFLAGS) $(PLUGIN_EXE_FLAGS) -o plugin/plugin plugin/main.c plugin/plugin.c $(UV_LIB)
	gcc -g -Wall -c -fPIC -o plugin/hello.o plugin/hello.c
	gcc $(SHARED_LIB_FLAGS) plugin/hello.o

proc-streams/test: proc-streams/test.c
	gcc -g -Wall -o proc-streams/test proc-streams/test.c

cgi/tick: cgi/tick.c
	gcc -g -Wall -o cgi/tick cgi/tick.c

multi-echo-server/worker: multi-echo-server/worker.c
	gcc $(CFLAGS) -o multi-echo-server/worker multi-echo-server/worker.c $(UV_LIB)

uvwget/uvwget: uvwget/main.c
	gcc $(CFLAGS) `curl-config --cflags --libs` -o uvwget/uvwget uvwget/main.c $(UV_LIB)

$(UV_LIB):
	make -C ../libuv

clean:
	for dir in $(examples); do cd $$dir; rm -f $$dir; rm -rf $$dir.dSYM; cd ..; done
	rm -rf plugin/*.o plugin/libhello.*
	rm -rf plugin/plugin plugin/plugin.dSYM
	rm -rf proc-streams/test proc-streams/test.dSYM
	rm -rf cgi/tick cgi/tick.dSYM
	rm -rf multi-echo-server/worker multi-echo-server/worker.dSYM
	rm -rf uvwget/uvwget uvwget/uvwget.dSYM

.PHONY: clean
