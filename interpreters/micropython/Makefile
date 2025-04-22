############################################################################
# interpreters/micropython/Makefile
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.  The
# ASF licenses this file to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance with the
# License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the
# License for the specific language governing permissions and limitations
# under the License.
#
############################################################################

include $(APPDIR)/Make.defs

VARIANT_MY = standard

MPY_DIR  = micropython
MPY_PORT_DIR = $(MPY_DIR)/ports/unix
MPY_PORT_VARIANT_DIR = $(MPY_PORT_DIR)/variants/$(VARIANT_MY)

UNAME_S := NuttX
PROGNAME  = micropython
PRIORITY  = $(CONFIG_INTERPRETERS_MICROPYTHON_PRIORITY)
STACKSIZE = $(CONFIG_INTERPRETERS_MICROPYTHON_STACKSIZE)
MODULE    = $(CONFIG_INTERPRETERS_MICROPYTHON)

CFLAGS_EXTRA += -Wno-undef
CFLAGS_EXTRA += -Wno-dangling-pointer -Wno-enum-int-mismatch -Wno-sign-compare
CFLAGS += -DMICROPY_UNIX_STACKLIMIT=$(CONFIG_INTERPRETERS_MICROPYTHON_STACKSIZE)
CFLAGS += -DMICROPY_UNIX_DEFAULT_HEAPSIZE=$(CONFIG_INTERPRETERS_MICROPYTHON_HEAPSIZE)
CFLAGS += -DMICROPY_USE_INTERNAL_PRINTF=0
CFLAGS += -Dmain=micropython_main

export CFLAGS
export CFLAGS_EXTRA

# Tell micropython to use the correct compiler
MPY_OVERRIDES = "CROSS_COMPILE=$(CROSSDEV)"
#MPY_OVERRIDES += "MICROPY_ROM_TEXT_COMPRESSION=1"
MPY_OVERRIDES += "MICROPY_PY_BTREE=0"
MPY_OVERRIDES += "MICROPY_PY_FFI=0"
MPY_OVERRIDES += "MICROPY_PY_SOCKET=0"
MPY_OVERRIDES += "MICROPY_PY_THREAD=0"
MPY_OVERRIDES += "MICROPY_PY_TERMIOS=0"
MPY_OVERRIDES += "MICROPY_PY_USSL=0"
#MPY_OVERRIDES += "MICROPY_USE_READLINE=0"

.PHONY: Force

.built: .built_lib

.built_lib: $(MPY_DIR)/mpy-cross/mpy-cross $(MPY_PORT_DIR)/libmicropython.a
	@echo built ar libmicropython.a to libapp.a
	@-mkdir /tmp/libmicropython
	@ar x $(MPY_PORT_DIR)/libmicropython.a --output /tmp/libmicropython
	$(call ARLOCK, $(call CONVERT_PATH,$(BIN)), /tmp/libmicropython/*.o)
	@rm -r -f /tmp/libmicropython
	@touch $@

$(MPY_PORT_DIR)/libmicropython.a: Force
	$(Q) $(MAKE) -C $(MPY_PORT_DIR) libmicropython.a VARIANT=$(VARIANT_MY) $(MPY_OVERRIDES)

$(MPY_DIR)/mpy-cross/mpy-cross:
	$(Q) $(MAKE) -C $(MPY_DIR)/mpy-cross

context::
	$(Q) test -d micropython || git clone ~/work/micropython -b v1.16

clean::
	$(call DELFILE, $(MPY_PORT_DIR)/libmicropython.a)
	$(call DELFILE, .built_lib)
	$(Q) test ! -d $(MPY_PORT_DIR) || make -C $(MPY_PORT_DIR) clean VARIANT=$(VARIANT_MY)

distclean::
	$(call DELDIR, micropython)

include $(APPDIR)/Application.mk
