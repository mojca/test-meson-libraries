To test this:

```bash
export PREFIX=/tmp/inst
export DESTDIR=/tmp/dest

mkdir build && cd build
meson --prefix=$PREFIX ../src
ninja install
```
