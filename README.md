To test this with `meson`:

```bash
export PREFIX=/tmp/inst
export DESTDIR=/tmp/dest

mkdir build && cd build
meson --prefix=$PREFIX ../src
ninja install
```

For CMake see:

* https://stackoverflow.com/a/25379391/585897
* https://cmake.org/Wiki/CMake_RPATH_handling