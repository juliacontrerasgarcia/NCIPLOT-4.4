include Makefile.inc

BINS=nciplot
OBJS= xtb_config.o param.o tools_io.o tools_math.o reader.o props.o nci_cluster.o nci_energy.o nci_xtb.o nciplot.o
LIBS=
INCLUDE=

#############################################################################
# xtb. The compilation stops with "xtb missing" if xtb is not found. It is
# looked for, in this order:
#  1. in $XTB_HOME, the directory of xtb, set like NCIPLOT_HOME:
#       export XTB_HOME=/home/xxxx/xtb-6.7.1-linux-x86_64/xtb-dist
#     (file XTB_HOME/bin/xtb, XTB_HOME/xtb-dist/bin/xtb or XTB_HOME/xtb)
#  2. in this src_4.4/ directory: the folder of the xtb release (as unpacked
#     from xtb-*-linux-x86_64.tar.xz), xtb-dist/, xtb/, bin/ or the file xtb.
# The path found is written to xtb_config.f90 and built into nciplot
# (xtb_config.o is first in OBJS so that the check comes before compiling).
#############################################################################
XTB_IN_HOME = bin/xtb xtb-dist/bin/xtb xtb bin/xtb.exe xtb-dist/bin/xtb.exe xtb.exe
XTB_IN_SRC = $(wildcard xtb-*/xtb-dist/bin/xtb xtb-*/bin/xtb xtb-*/xtb-*/bin/xtb.exe xtb-*/bin/xtb.exe) \
             xtb-dist/bin/xtb xtb/bin/xtb xtb/xtb-dist/bin/xtb bin/xtb xtb \
             xtb-dist/bin/xtb.exe xtb/bin/xtb.exe bin/xtb.exe xtb.exe
# first candidate that is a file (a directory called xtb does not count)
xtb_first = $(shell for c in $(1); do if [ -f "$$c" ]; then echo "$$c"; break; fi; done)

XTB_EXE :=
XTB_FROM :=
ifneq ($(strip $(XTB_HOME)),)
  XTB_EXE := $(call xtb_first,$(addprefix $(patsubst %/,%,$(XTB_HOME))/,$(XTB_IN_HOME)))
  XTB_FROM := XTB_HOME
endif
ifeq ($(XTB_EXE),)
  XTB_EXE := $(call xtb_first,$(XTB_IN_SRC))
  XTB_FROM := src_4.4
  ifneq ($(XTB_EXE),)
    XTB_EXE := $(CURDIR)/$(XTB_EXE)
  endif
endif

%.o: %.f90
	$(FC) -c $(FCFLAGS) $(INCLUDE) -o $@ $<

%.o: %.f
	$(FC) -c $(FCFLAGS) $(INCLUDE) -o $@ $<

# General targets

all: $(BINS)

debug:
	$(MAKE) clean
	$(MAKE) DEBUG=1 $(BINS)

clean:
	rm -f core *.mod *.o xtb_config.f90

veryclean:
	rm -f core *.mod *.o xtb_config.f90

mrproper:
	rm -f core *.mod *.o xtb_config.f90 $(BINS)

nciplot: $(OBJS) $(LIBS)
	$(FC) -o nciplot $(LDFLAGS) $(OBJS) $(LIBS)

# "make xtb" only shows where xtb is (or stops with "xtb missing")
xtb: xtb_config.f90

# xtb_config.f90 is written at every make, but only replaced (and so
# recompiled) when the path of xtb changes.
xtb_config.f90: FORCE
ifeq ($(XTB_EXE),)
	@echo "ERROR: xtb missing."
	@if [ -n "$(XTB_HOME)" ]; then \
	  echo "  XTB_HOME = $(XTB_HOME): no bin/xtb, xtb-dist/bin/xtb or xtb in it."; \
	else \
	  echo "  XTB_HOME is not set."; \
	fi
	@echo "  No xtb in $(CURDIR)"
	@echo "  (looked for xtb-*/xtb-dist/bin/xtb, xtb-*/bin/xtb, xtb-dist/bin/xtb, xtb/bin/xtb, bin/xtb, xtb)."
	@ls -d xtb*.tar.xz xtb*.zip 2>/dev/null | sed 's/^/  Archive found here, unpack it first: /' || true
	@echo "  Set XTB_HOME to the directory of xtb (export XTB_HOME=/path/to/xtb-dist)"
	@echo "  or unpack xtb in $(CURDIR), then run make again."
	@exit 1
else
	@echo "xtb found ($(XTB_FROM)): $(XTB_EXE)"
	@{ echo "! Written by the Makefile: path of xtb found at compilation. Do not edit."; \
	  echo "module xtb_config"; \
	  echo "   implicit none"; \
	  echo "   character(len=*), parameter :: xtb_exe_compiled = ''&"; \
	  echo "$(XTB_EXE)" | fold -w 60 | sed "s/^/      \/\/ '/; s/\$$/' \&/"; \
	  echo "      // ''"; \
	  echo "   character(len=*), parameter :: xtb_found_by = '$(XTB_FROM)'"; \
	  echo "end module xtb_config"; } > xtb_config.f90.new
	@if cmp -s xtb_config.f90.new xtb_config.f90; then rm -f xtb_config.f90.new; \
	else mv -f xtb_config.f90.new xtb_config.f90; fi
endif

FORCE:

# Module dependencies: compiling x.f90 creates x.o and the .mod file of its
# module, so every object depends on the objects of the modules it uses.
tools_io.o: param.o
reader.o: param.o tools_io.o
props.o: param.o tools_io.o tools_math.o reader.o
nci_cluster.o: param.o tools_io.o reader.o
nci_energy.o: param.o reader.o nci_cluster.o
xtb_config.o: xtb_config.f90
nci_xtb.o: param.o tools_io.o xtb_config.o
nciplot.o: param.o tools_io.o tools_math.o reader.o props.o nci_cluster.o nci_energy.o nci_xtb.o

.PHONY: all debug clean veryclean mrproper xtb FORCE
