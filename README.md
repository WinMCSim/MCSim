# MCSim
CMake build configuration

## Building Libs

### libSBML

[CMake build config for dependencies](https://github.com/sbmlteam/libSBML-dependencies).

(libSBML-5.16.0-core-src.tar.gz from) [sourceforge](https://sourceforge.net/projects/sbml/).

[Instructions](http://sbml.org/Software/libSBML/5.16.0/docs//cpp-api/libsbml-installation.html#windows-configuring)

### GSL
[CMake build config](https://github.com/ampl/gsl)

## Locating Build Dependencies

### Microsoft Visual C++ 2017
```
deps/VS/v15x64/libSBML
deps/VS/v15x64/gsl
```

### MinGW-w64
```
deps/mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/libSBML
deps/mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/gsl
```