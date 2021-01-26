---
title: "error: use of undeclared identifier 'assert'     assert(real == test);"
categories: [ "C/Cpp" ]
tags: [ "c++","casert.h" ]
draft: false
slug: "error-use-of-undeclared-identifier-assert---assertreal-test"
date: "2020-10-11 09:23:00"
---

问题：
```
Should be added at the top of the file.  The version specified may be lower
  if you wish to support older CMake versions for this project.  For more
  information run "cmake --help-policy CMP0000".
This warning is for project developers.  Use -Wno-dev to suppress it.

-- Configuring done
-- Generating done
-- Build files have been written to: /Users/kelvin/work/person/LeetCode/build
[ 40%] Building CXX object include/CMakeFiles/leetcode.dir/listnode.o
[ 40%] Building CXX object include/CMakeFiles/leetcode.dir/btree.o
[ 60%] Linking CXX static library libleetcode.a
[ 60%] Built target leetcode
[ 80%] Building CXX object src/CMakeFiles/10.isMatch.dir/10.isMatch.o
/Users/kelvin/work/person/LeetCode/src/10.isMatch.cc:38:5: error: use of undeclared identifier 'assert'
    assert(real == test);
    ^
1 error generated.
make[3]: *** [src/CMakeFiles/10.isMatch.dir/10.isMatch.o] Error 1
make[2]: *** [src/CMakeFiles/10.isMatch.dir/all] Error 2
make[1]: *** [src/CMakeFiles/10.isMatch.dir/rule] Error 2
```
添加如下头部：


<!--more-->


`#include <cassert>`

