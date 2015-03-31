title: leveldb源码剖析（001）-testharness
date: 2014-10-11 18:34:26
categories:
- leveldb源码剖析
tags:
---

-----

```
//testharness.h
// Copyright (c) 2011 The LevelDB Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file. See the AUTHORS file for names of contributors.

#ifndef STORAGE_LEVELDB_UTIL_TESTHARNESS_H_
#define STORAGE_LEVELDB_UTIL_TESTHARNESS_H_

#include <stdio.h>
#include <stdlib.h>
#include <sstream>
#include "leveldb/env.h"
#include "leveldb/slice.h"
#include "util/random.h"

namespace leveldb {
namespace test {

// Run some of the tests registered by the TEST() macro.  If the
// environment variable "LEVELDB_TESTS" is not set, runs all tests.
// Otherwise, runs only the tests whose name contains the value of
// "LEVELDB_TESTS" as a substring.  E.g., suppose the tests are:
//    TEST(Foo, Hello) { ... }
//    TEST(Foo, World) { ... }
// LEVELDB_TESTS=Hello will run the first test
// LEVELDB_TESTS=o     will run both tests
// LEVELDB_TESTS=Junk  will run no tests
//
// Returns 0 if all tests pass.
// Dies or returns a non-zero value if some test fails.
extern int RunAllTests();	//向外提供统一的测试运行接口

// Return the directory to use for temporary storage.
extern std::string TmpDir();	//返回一个临时目录

// Return a randomization seed for this run.  Typically returns the
// same number on repeated invocations of this binary, but automated
// runs may be able to vary the seed.
extern int RandomSeed();	//获得随机种子

// An instance of Tester is allocated to hold temporary state during
// the execution of an assertion.
/*
每一个assertion对应一个测试类，该类以临时对象存在
如果环境变量 LEVELDB_TESTS定义了，将会运行所有包
含LEVELDB_TEST的字符串的测试用例
*/
 class Tester {
 private:
  bool ok_;		//当前状态，是否出错
  const char* fname_;	//测试用例所在的文件名称
  int line_;	//测试用例所在的文件行数
  std::stringstream ss_;	//附加信息

 public:
  Tester(const char* f, int l)
      : ok_(true), fname_(f), line_(l) {
  }
	//如果assertion没有通过则打印错误信息
  ~Tester() {
    if (!ok_) {
      fprintf(stderr, "%s:%d:%s\n", fname_, line_, ss_.str().c_str());
      exit(1);
    }
  }

  Tester& Is(bool b, const char* msg) {
    if (!b) {
      ss_ << " Assertion failure " << msg;
      ok_ = false;
    }
    return *this;
  }

  Tester& IsOk(const Status& s) {
    if (!s.ok()) {
      ss_ << " " << s.ToString();
      ok_ = false;
    }
    return *this;
  }
//#define在类内使用
//将所有的二元比较操作全部使用define和模板函数进行封装
//宏的定义参考（链接）
#define BINARY_OP(name,op)                              \
  template <class X, class Y>                           \
  Tester& name(const X& x, const Y& y) {                \
    if (! (x op y)) {                                   \
      ss_ << " failed: " << x << (" " #op " ") << y;    \
      ok_ = false;                                      \
    }                                                   \
    return *this;                                       \
  }

//使用宏定义了各个模板函数
  BINARY_OP(IsEq, ==)
  BINARY_OP(IsNe, !=)
  BINARY_OP(IsGe, >=)
  BINARY_OP(IsGt, >)
  BINARY_OP(IsLe, <=)
  BINARY_OP(IsLt, <)
#undef BINARY_OP

  // Attach the specified value to the error message if an error has occurred
//类内使用模板函数
//当一些错误放生是，将一下特殊信息发送到ss中
  template <class V>
  Tester& operator<<(const V& value) {
    if (!ok_) {
      ss_ << " " << value;
    }
    return *this;
  }
};


//定义宏
//本写法定义一个临时对象，然后调用其类中的函数
//类的构造函数(参数).方法名(参数)； 	临时产生一个对象，然后调用方法，销毁
#define ASSERT_TRUE(c) ::leveldb::test::Tester(__FILE__, __LINE__).Is((c), #c)
#define ASSERT_OK(s) ::leveldb::test::Tester(__FILE__, __LINE__).IsOk((s))
#define ASSERT_EQ(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsEq((a),(b))
#define ASSERT_NE(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsNe((a),(b))
#define ASSERT_GE(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsGe((a),(b))
#define ASSERT_GT(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsGt((a),(b))
#define ASSERT_LE(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsLe((a),(b))
#define ASSERT_LT(a,b) ::leveldb::test::Tester(__FILE__, __LINE__).IsLt((a),(b))

#define TCONCAT(a,b) TCONCAT1(a,b)
#define TCONCAT1(a,b) a##b			//将a和b连接起来，宏的特殊用法   

//测试类定义，_Run方法向外统一提供接口，供用户写测试用例
#define TEST(base,name)                                                 \
class TCONCAT(_Test_,name) : public base {                              \
 public:                                                                \
  void _Run();                                                          \
  static void _RunIt() {                                                \
    TCONCAT(_Test_,name) t;                                             \
    t._Run();                                                           \
  }                                                                     \
};                                                                      \
//注册刚定义类中的RunIt函数，并忽略返回结果
bool TCONCAT(_Test_ignored_,name) =                                     \
  ::leveldb::test::RegisterTest(#base, #name, &TCONCAT(_Test_,name)::_RunIt); \ 
//后边的代码没有写完，需要用户使用测试用例的时候在后边补齐，后边写的代码
//也正是用户的测试用例
void TCONCAT(_Test_,name)::_Run()

// Register the specified test.  Typically not used directly, but
// invoked via the macro expansion of TEST.
extern bool RegisterTest(const char* base, const char* name, void (*func)());


}  // namespace test
}  // namespace leveldb

#endif  
```

--------

```
//testharness.cc
// STORAGE_LEVELDB_UTIL_TESTHARNESS_H_
// Copyright (c) 2011 The LevelDB Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file. See the AUTHORS file for names of contributors.

#include "util/testharness.h"

#include <string>
#include <stdlib.h>
#include <sys/stat.h>
#include <sys/types.h>

namespace leveldb {
namespace test {
//使用无名的命名空间，将该空间内的变量的名字限制在文件内
namespace {
struct Test {
  const char* base;
  const char* name;
  void (*func)();
};
std::vector<Test>* tests;
}

bool RegisterTest(const char* base, const char* name, void (*func)()) {
  if (tests == NULL) {
    tests = new std::vector<Test>;
  }
  Test t;
  t.base = base;
  t.name = name;
  t.func = func;
  tests->push_back(t);
  return true;
}
//根据环境变量LEVELDB_TESTS，对测试用例进行过滤
int RunAllTests() {
  const char* matcher = getenv("LEVELDB_TESTS");

  int num = 0;
  if (tests != NULL) {
    for (size_t i = 0; i < tests->size(); i++) {
      const Test& t = (*tests)[i];
      if (matcher != NULL) {
        std::string name = t.base;
        name.push_back('.');
        name.append(t.name);
        if (strstr(name.c_str(), matcher) == NULL) {
          continue;
        }
      }
      fprintf(stderr, "==== Test %s.%s\n", t.base, t.name);
      (*t.func)();
      ++num;
    }
  }
  fprintf(stderr, "==== PASSED %d tests\n", num);
  return 0;
}
//返回临时创建的文件目录
std::string TmpDir() {
  std::string dir;
  Status s = Env::Default()->GetTestDirectory(&dir);
  ASSERT_TRUE(s.ok()) << s.ToString();
  return dir;
}
//根据环境变量返回随机种子数
int RandomSeed() {
  const char* env = getenv("TEST_RANDOM_SEED");
  int result = (env != NULL ? atoi(env) : 301);
  if (result <= 0) {
    result = 301;
  }
  return result;
}

}  // namespace test
}  // namespace leveldb
```

------

```
//arean_test.cc
// Copyright (c) 2011 The LevelDB Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file. See the AUTHORS file for names of contributors.

#include "util/arena.h"

#include "util/random.h"
#include "util/testharness.h"

namespace leveldb {
//声明一个类，作为测试用例类的父类，空类
class ArenaTest { };
//测试类的定义，类的测试名字叫Empty,随意的字符串
TEST(ArenaTest, Empty) {
  Arena arena;
}
//简单的测试用例
TEST(ArenaTest, Simple) {
  std::vector<std::pair<size_t, char*> > allocated;
  Arena arena;
  const int N = 100000;
  size_t bytes = 0;
  Random rnd(301);
  for (int i = 0; i < N; i++) {
    size_t s;
    if (i % (N / 10) == 0) {
      s = i;
    } else {
      s = rnd.OneIn(4000) ? rnd.Uniform(6000) :
          (rnd.OneIn(10) ? rnd.Uniform(100) : rnd.Uniform(20));
    }
    if (s == 0) {
      // Our arena disallows size 0 allocations.
      s = 1;
    }
    char* r;
    if (rnd.OneIn(10)) {
      r = arena.AllocateAligned(s);
    } else {
      r = arena.Allocate(s);
    }

    for (size_t b = 0; b < s; b++) {
      // Fill the "i"th allocation with a known bit pattern
      r[b] = i % 256;
    }
    bytes += s;
    allocated.push_back(std::make_pair(s, r));
    ASSERT_GE(arena.MemoryUsage(), bytes);
    if (i > N/10) {
      ASSERT_LE(arena.MemoryUsage(), bytes * 1.10);
    }
  }
  for (size_t i = 0; i < allocated.size(); i++) {
    size_t num_bytes = allocated[i].first;
    const char* p = allocated[i].second;
    for (size_t b = 0; b < num_bytes; b++) {
      // Check the "i"th allocation for the known bit pattern
      ASSERT_EQ(int(p[b]) & 0xff, i % 256);
    }
  }
}

}  // namespace leveldb
//运行测试类
int main(int argc, char** argv) {
  return leveldb::test::RunAllTests();
}
```

------

学习知识点：
1.在类内使用宏定义和模板定义可以定义大量的类似的函数；
2.可以通过：构造函数（参数）.函数名（参数）;来使用一个临时对象调用函数名；
3.#define FUNC(a) #a      //等价于“a”
4.#define FUNC(a,b) a##b        等价于ab和到一块
5.const char * getenv(constchar*); 获得某个指定的环境变量
6.一个无名的命名空间，命名空间中变量的作用域为本文件夹
