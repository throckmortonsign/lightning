#! /usr/bin/make

# Designed to be run one level up
daemon-wrongdir:
	$(MAKE) -C .. daemon-all

daemon-all: daemon/lightningd

DAEMON_SRC :=					\
	daemon/configdir.c			\
	daemon/json.c				\
	daemon/lightningd.c			\
	daemon/log.c				\
	daemon/pseudorand.c
DAEMON_OBJS := $(DAEMON_SRC:.c=.o)

DAEMON_JSMN_OBJS := daemon/jsmn.o
DAEMON_JSMN_HEADERS := daemon/jsmn/jsmn.h

DAEMON_HEADERS :=				\
	daemon/configdir.h			\
	daemon/json.h				\
	daemon/lightningd.h			\
	daemon/log.h				\
	daemon/pseudorand.h

$(DAEMON_OBJS): $(DAEMON_HEADERS) $(DAEMON_JSMN_HEADERS) $(BITCOIN_HEADERS) $(CORE_HEADERS) $(GEN_HEADERS) $(CCAN_HEADERS)
$(DAEMON_JSMN_OBJS): $(DAEMON_JSMN_HEADERS)

check-source: $(DAEMON_SRC:%=check-src-include-order/%)
check-source: $(DAEMON_HEADERS:%=check-hdr-include-order/%)
check-daemon-makefile:
	@if [ "`echo daemon/*.h`" != "$(DAEMON_HEADERS)" ]; then echo DAEMON_HEADERS incorrect; exit 1; fi

# Git submodules are seriously broken.
daemon/jsmn/jsmn.c:
	git submodule update daemon/jsmn/
	[ -f $@ ] || git submodule update --init daemon/jsmn/

daemon/jsmn.o: daemon/jsmn/jsmn.c
	$(COMPILE.c) -DJSMN_STRICT=1 $(OUTPUT_OPTION) $<

daemon/lightningd: $(DAEMON_OBJS) $(DAEMON_JSMN_OBJS) $(CORE_OBJS) $(BITCOIN_OBJS) $(CCAN_OBJS) libsecp256k1.a