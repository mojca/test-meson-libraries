# Comparison of @rpath handling in Meson vs CMake

This document describes suboptimal handling of `@rpath` in Meson and the difference with CMake.

## Meson

```bash
export PREFIX=/tmp/inst
export DESTDIR=/tmp/dest

mkdir build && cd build
meson --prefix=$PREFIX ../src
ninja install
```

```bash
$ otool -L libtestlib.dylib 
libtestlib.dylib:
	@rpath/libtestlib.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

$ otool -L testbin
testbin:
	@rpath/libtestlib.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

# this is problematic
$ otool -L $DESTDIR$PREFIX/lib/libtestlib.dylib
/tmp/dest/tmp/inst/lib/libtestlib.dylib:
	@rpath/libtestlib.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

$ otool -L $DESTDIR$PREFIX/bin/testbin 
/tmp/dest/tmp/inst/bin/testbin:
	@rpath/libtestlib.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)
```

## CMake

```bash
export PREFIX=/tmp/inst
export DESTDIR=/tmp/dest

mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=$PREFIX ../src
make && make install DESTDIR=$DESTDIR
```

```bash
$ otool -L libtestlib.1.0.dylib 
libtestlib.1.0.dylib:
	@rpath/libtestlib.1.0.dylib (compatibility version 0.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

$ otool -L testbin 
testbin:
	@rpath/libtestlib.1.0.dylib (compatibility version 0.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

$ otool -L $DESTDIR$PREFIX/lib/libtestlib.1.0.dylib
/tmp/dest/tmp/inst/lib/libtestlib.1.0.dylib:
	/tmp/inst/lib/libtestlib.1.0.dylib (compatibility version 0.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)

$ otool -L $DESTDIR$PREFIX/bin/testbin 
/tmp/dest/tmp/inst/bin/testbin:
	/tmp/inst/lib/libtestlib.1.0.dylib (compatibility version 0.0.0, current version 1.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 400.9.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.0.0)
```

See also:

* https://stackoverflow.com/a/25379391/585897
* https://cmake.org/Wiki/CMake_RPATH_handling