CC?=gcc
CP?=cp
INSTALL ?= install
SRCDIR:=src/
RODIR:=obj/
DODIR:=debugobj/
INSTALLLOCATION := /usr/lib
OFILES:=$(patsubst %.c,%.o,$(wildcard $(SRCDIR)*.c))
ROFILES:= $(patsubst $(SRCDIR)%,$(RODIR)%,$(OFILES))
DOFILES:= $(patsubst $(SRCDIR)%,$(DODIR)%,$(OFILES))

TARGET := libcontiguousmalloc.so
TESTEXEC := test
TESTFILES := test.c
RTARGET := $(RODIR)$(TARGET)
DTARGET := $(DODIR)$(TARGET)
INC:= -I ../ -I inc/

LDFLAGS := -flto
RLDFLAGS:= $(LDFLAGS) -s -fPIC -shared
DLDFLAGS:= -fPIC -shared

CFLAGS := -flto
COMMONCFLAGS := -Wall -Werror -Wextra -Wfatal-errors -std=gnu11 $(INC)
LIBCFLAGS := -MMD -MP -fPIC
RCFLAGS := $(CFLAGS) $(LIBCFLAGS) -O2 $(COMMONCFLAGS)
DCFLAGS := $(LIBCFLAGS) -DDEBUG -g3 $(COMMONCFLAGS)
TCFLAGS := $(CFLAGS) $(COMMONCFLAGS)

-include $(ROFILES:%.o=%.d)
-include $(DOFILES:%.o=%.d)

.DEFAULT_GOAL := all

.PHONY: all release clean debug install testexec

all: release testexec

release: $(TARGET)

debug: $(DTARGET)

testexec: $(TESTEXEC)

$(RODIR) $(DODIR):
	mkdir -p $@

$(RODIR)%.o: $(SRCDIR)%.c
	$(CC) $(RCFLAGS) -c $< -o $@

$(RTARGET): $(ROFILES) | $(RODIR)
	$(CC) -o $@ $^ $(RLDFLAGS)

$(TARGET) : $(RTARGET)
	$(CP) $^ $@

$(DODIR)%.o: $(SRCDIR)%.c
	$(CC) $(DCFLAGS) -c $< -o $@

$(DTARGET) : $(DOFILES) | $(DODIR)
	$(CC) -o $@ $^ $(DLDFLAGS)

$(TESTEXEC) : $(TESTFILES) $(TARGET)
	$(CC) $(TCFLAGS) -L . -lcontiguousmalloc $< -o $@

install: $(RTARGET)
	$(INSTALL) -m 755 $< $(INSTALLLOCATION)

clean:
	@rm -rf $(RODIR) $(DODIR) $(TESTEXEC) $(TARGET)
