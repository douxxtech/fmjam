CC = gcc
STD_CFLAGS = -Wall -std=gnu99 -c -g

# Enable ARM-specific options only on ARM, and compilation of the app only on ARM
UNAME := $(shell uname -m)

# Determine Raspberry Pi version (if 2 or greater)
RPI_VERSION := $(shell cat /proc/device-tree/model | grep -a -o "Raspberry\sPi\s[0-9]" | grep -o "[0-9]")

# Detect Raspberry Pi Zero 2 W specifically and override RPI_VERSION
ifneq (,$(findstring Zero 2 W,$(shell tr -d '\0' < /proc/device-tree/model)))
    $(info Detected Raspberry Pi Zero 2 W → forcing RPI_VERSION=3)
    RPI_VERSION := 3
endif

# Determine the hardware platform and set proper compilation flags
ifeq ($(UNAME), armv6l)
	ARCH_CFLAGS = -march=armv6 -O3 -mtune=arm1176jzf-s -mfloat-abi=hard -mfpu=vfp -ffast-math
	TARGET = 1
else ifeq ($(shell expr $(RPI_VERSION) \> 1), 1)
	ifeq ($(UNAME), armv7l)
		ARCH_CFLAGS = -march=armv7-a -O3 -mtune=arm1176jzf-s -mfloat-abi=hard -mfpu=vfp -ffast-math
	else ifeq ($(UNAME), aarch64)
		ARCH_CFLAGS = -march=armv8-a -O2 -pipe -fstack-protector-strong -fno-plt -ffast-math
	endif
	ifeq ($(shell expr $(RPI_VERSION) \>= 4), 1)
		TARGET = 4
	else
		TARGET = 2
	endif
else
	ARCH_CFLAGS = -O3
	TARGET = other
endif
CFLAGS = $(STD_CFLAGS) $(ARCH_CFLAGS) -DRASPI=$(TARGET)

ifneq ($(TARGET), other)

app: fmjam.o mailbox.o
	$(CC) $(LDFLAGS) -o fmjam fmjam.o mailbox.o -lm

endif

fmjam.o: fmjam.c mailbox.h
	$(CC) $(CFLAGS) fmjam.c