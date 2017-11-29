project('Test libraries on mac', 'cpp')

testlib = shared_library('testlib', 'testlib.cpp', install: true)
executable('testbin', 'testbin.cpp', link_with : testlib, install: true)
