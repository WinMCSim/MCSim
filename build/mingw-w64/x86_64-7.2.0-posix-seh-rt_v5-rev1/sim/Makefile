# adapted from https://stackoverflow.com/a/39033569

COMPPROD = mingw-w64
COMPPRODVER = x86_64-7.2.0-posix-seh-rt_v5-rev1

# Set project directory one level above the Makefile directory. $(CURDIR) is a GNU make variable containing the path to the current working directory
PROJDIR := $(realpath $(CURDIR)/.)
SOURCEDIR := $(PROJDIR)/../../../..
BUILDDIR := $(PROJDIR)/Intermediate
LIBINCLUDEDIRS = ../../../../deps/$(COMPPROD)/$(COMPPRODVER)/gsl/include
LIBDIRS = ../../../../deps/$(COMPPROD)/$(COMPPRODVER)/gsl/lib/
LIBS = gslcblas gsl
INSTALLROOT = ../../../../bin/$(COMPPROD)/$(COMPPRODVER)

# Name of the final executable
TARGET = sim.exe

# Decide whether the commands will be shown or not
VERBOSE = FALSE

# Create the list of directories
DIRS = . sim deps/model/src
SOURCEDIRS = $(foreach dir, $(DIRS), $(addprefix $(SOURCEDIR)/, $(dir)))
TARGETDIRS = $(foreach dir, $(DIRS), $(addprefix $(BUILDDIR)/, $(dir)))

# Generate the GCC includes parameters by adding -I before each source folder
INCLUDES = $(foreach dir, $(SOURCEDIRS), $(addprefix -I, $(dir)))

# includes for libraries
LIBINCLUDES = $(foreach dir, $(LIBINCLUDEDIRS), $(addprefix -I, $(dir)))

# search path for static libs
LIBSEARCHPATH = $(foreach dir, $(LIBDIRS), $(addprefix -L, $(dir)))

# linked static libs
LINKEDLIBS = $(foreach lib, $(LIBS), $(addprefix -l, $(lib)))

# Add this list to VPATH, the place make will look for the source files
VPATH = $(SOURCEDIRS)

# Create a list of *.c sources in DIRS
SOURCES = $(foreach dir,$(SOURCEDIRS),$(wildcard $(dir)/*.c))

# Define objects for all sources
OBJS := $(subst $(SOURCEDIR),$(BUILDDIR),$(SOURCES:.c=.o))

# Define dependencies files for all objects
DEPS = $(OBJS:.o=.d)

# Name the compiler
CC = gcc

# OS specific part
ifeq ($(OS),Windows_NT)
	CP = xcopy /Y
	RM = del /F /Q 
	RMDIR = -RMDIR /S /Q
	MKDIR = -mkdir
	ERRIGNORE = 2>NUL
	SEP=\\
else
	CP = cp
	RM = rm -rf 
	RMDIR = rm -rf 
	MKDIR = mkdir -p
	ERRIGNORE = 2>/dev/null
	SEP=/
endif

# Remove space after separator
PSEP = $(strip $(SEP))

# Hide or not the calls depending of VERBOSE
ifeq ($(VERBOSE),TRUE)
	HIDE =  
else
	HIDE = @
endif

# Define the function that will generate each rule
define generateRules
$(1)/%.o: %.c
	@echo Building $$@
	$(HIDE)$(CC) -c $$(INCLUDES) $$(LIBINCLUDES) -o $$(subst /,$$(PSEP),$$@) $$(subst /,$$(PSEP),$$<) -MMD
endef

# Indicate to make which targets are not files
.PHONY: all clean directories install uninstall

all: directories $(TARGET)

$(TARGET): $(OBJS)
	$(HIDE)echo Linking $@
	$(HIDE)$(CC) $(OBJS) -o $(TARGET) $(LIBSEARCHPATH) $(LINKEDLIBS)

# Include dependencies
-include $(DEPS)

# Generate rules
$(foreach targetdir, $(TARGETDIRS), $(eval $(call generateRules, $(targetdir))))

directories: 
	$(HIDE)$(MKDIR) $(subst /,$(PSEP),$(TARGETDIRS)) $(ERRIGNORE)

# Remove all objects, dependencies and executable files generated during the build
clean:
	$(HIDE)$(RMDIR) $(subst /,$(PSEP),$(BUILDDIR)) $(ERRIGNORE)
	$(HIDE)$(RM) $(TARGET) $(ERRIGNORE)
	@echo Cleaning done ! 

install: $(TARGET)
	$(HIDE)$(MKDIR) $(subst /,$(PSEP),$(INSTALLROOT)) $(ERRIGNORE)
	$(HIDE)$(CP) $< $(subst /,$(PSEP),$(INSTALLROOT)) $(ERRIGNORE)

uninstall:
	$(HIDE)$(RM) $(subst /,$(PSEP),$(INSTALLROOT))$(PSEP)$(TARGET) $(ERRIGNORE)
