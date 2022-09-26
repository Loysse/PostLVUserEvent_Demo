#
# To generate:
# - 1 Adapt the project configuration
# - 2 Generate the library for gcc : make liblrvt
# - 3 Build the project            : make all
#
# To clean the compilation  : make clean
# To clean for distribution : make distclean
#

#
# Project configuration
#
PROGROOT=.

PATH := /c/msys64/mingw32/bin:$(PATH)

# May be : debug, run, linuxtools
BUILD_MODE=debug

# Cross compilation 
CROSS=

# Labview tools dependecies (depend on machine)
CINTOOLS="/C/Program Files (x86)/National Instruments/LabVIEW 2016/cintools"
LIBLVRT="/C/Program Files (x86)/National Instruments/Shared/LabVIEW Run-Time/2016/lvrt.dll"
LABVIEW="/C/Program Files (x86)/National Instruments/LabVIEW 2016/LabVIEW.exe"

#
# Compilation configuration
#
CC=$(CROSS)gcc
CXX=$(CROSS)g++
GENDEF=$(CROSS)gendef
DLLTOOL=$(CROSS)dlltool

#
# Object files to generate
#
OBJS = skeleton_lv_event.o

#
# Include path
#
CFLAGS=-I $(CINTOOLS)

#
# Link options - lvrt or labview.exe
#
LDFLAGS=-L $(PROGROOT) -Wl,--subsystem,windows -mwindows -static-libgcc -shared -Wl,--output-def=skeleton_lv_event.def

#
# Build configuration
#
ifeq ($(BUILD_MODE),debug)
	CFLAGS += -g
else ifeq ($(BUILD_MODE),run)
	CFLAGS += -O2
else ifeq ($(BUILD_MODE),linuxtools)
	CFLAGS += -g -pg -fprofile-arcs -ftest-coverage
	LDFLAGS += -pg -fprofile-arcs -ftest-coverage
else
    $(error Build mode $(BUILD_MODE) not supported by this Makefile)
endif

.PHONY: liblvrt clean distclean

all:	skeleton_lv_event_lv.dll skeleton_lv_event_lvrt.dll

liblvrt: lvrt.a labview.a

skeleton_lv_event_lv.dll:	$(OBJS)
	$(CXX) -o $@ $^ $(LDFLAGS) -l labview

skeleton_lv_event_lvrt.dll:	$(OBJS)
	$(CXX) -o $@ $^ $(LDFLAGS) -l lvrt

%.o:	$(PROGROOT)/%.cpp
	$(CXX) -c $(CFLAGS) $(CXXFLAGS) $(CPPFLAGS) -o $@ $<

%.o:	$(PROGROOT)/%.c
	$(CXX) -c $(CFLAGS) $(CPPFLAGS) -o $@ $<

lvrt.def:
	$(GENDEF) - $(LIBLVRT) > lvrt.def

lvrt.a: lvrt.def
	$(DLLTOOL) -d lvrt.def -D $(LIBLVRT) -l liblvrt.a

labview.def:
	$(GENDEF) - $(LABVIEW) > labview.def

labview.a: labview.def
	$(DLLTOOL) -d labview.def -D $(LABVIEW) -l liblabview.a

clean:
	rm -fr $(OBJS) skeleton_lv_event*.dll skeleton_lv_event*.def

distclean:
	rm -rf *.o *.dll *.a *.def
