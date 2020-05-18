#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material.
#
#*****************************************************************************

#------------------------------------------------------------------------------
# <Put a Description Here>
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = HOST

# Common used host flags
#This allows to change the flags as needed

FLAGS = -Wall -Werror -g -O0 -std=c99
TARGET = c1m2

#Here go the Archicture specific FLAGS

#ifeq ($(PLATFORM), MSP432)



ifeq ($(PLATFORM), HOST)
	CC = gcc
	CFLAGS = $(FLAGS)
	CPPFLAGs = -DHOST $(INCLUDES)
	LDFLAGS = -wl, -Map=$(TARGET).map
	SIZE = size
else
	LINKER_FILE = -T msp432p401r.lds
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	ARCH_FlAGS = -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16

	# Include whatever are the necessary sources for MSP432
	SOURCES = 	main.c memory.c interrupts_msp432p401r_gcc.c \
				startup_msp432p401r_gcc.c \
				system_msp432p401r.c
	# Add in the flags
	CC = arm-none-eabi-gcc
	LINKER_PATH = -L ../
	CFLAGS = $(FLAGS) $(ARCH_FlAGS) -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS)
	LDFLAGS = -wl, -MAP=$(TARGET).map, $(LINKER_PATH) $(LINKER_FILE)
	SIZE = size
endif

# The object file variables

OBJS = $(SOURCES:.c=.o)
DEPS = $(SOURCES:.c=.d)
ASMS = $(SOURCES:.c=.asm)
PRES = $(SOURCES:=.c=.i)

# Here goes the -c flag to stop before linking
 %.o : %.c
 	%(CC) -c $< $(CPPFLAGs) $(CFLAGS) -o $@

# Here go the -E -M flags to generate the dependancies
%.d : %.c
	%(CC) = -E -M $< $(CPPFLAGs) -o $@

# Here goes the -S flag to create the assembly file
%.asm : %.c
	%(CC) = -S $< $(CPPFLAGs) $(CFLAGS) -o $@

# Here goes the -E to generate the preprocess file
%.i : %.c
	$(CC) -E $< $(CPPFLAGs) -o $@

# We add the phony that will be used to call the builds

# compile-all->generate object files from source files but do not link them
.PHONY: compile-all
	compile-all:$(OBJS)

# build->this will create the out, map, object, and dependancies for the sources
.PHONY: build
	build: $(DEPS)
	build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CPPFLAGs) $(CFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@

# clean->this will purge all files except the initial files that were there
.PHONY: clean
	clean: rm -f $(OBJS) $(DEPS) $(ASMS) $(PRES) $(TARGET).out $(TARGET).map 
