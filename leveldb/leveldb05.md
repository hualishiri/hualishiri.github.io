title: leveldb源码剖析（005）-arena
date: 2014-10-12 14:11:34
categories:
- leveldb源码剖析
tags:
---
```
//arena.h
```

```
// Copyright (c) 2011 The LevelDB Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file. See the AUTHORS file for names of contributors.

#ifndef STORAGE_LEVELDB_UTIL_ARENA_H_
#define STORAGE_LEVELDB_UTIL_ARENA_H_

#include <vector>
#include <assert.h>
#include <stddef.h>
#include <stdint.h>

namespace leveldb {

class Arena {
 public:
  Arena();
  ~Arena();

  // Return a pointer to a newly allocated memory block of "bytes" bytes.
  char* Allocate(size_t bytes);

  // Allocate memory with the normal alignment guarantees provided by malloc
  char* AllocateAligned(size_t bytes);

  // Returns an estimate of the total memory usage of data allocated
  // by the arena (including space allocated but not yet used for user
  // allocations).
  size_t MemoryUsage() const {
    return blocks_memory_ + blocks_.capacity() * sizeof(char*);
  }

 private:
  char* AllocateFallback(size_t bytes);
  char* AllocateNewBlock(size_t block_bytes);

  // Allocation state
  char* alloc_ptr_;				//小内存块申请的内存池（小于k/4，则使用该方式分配）
  size_t alloc_bytes_remaining_;	//小内存池的剩余大小（已经为blocks_所管理）

  // Array of new[] allocated memory blocks
  std::vector<char*> blocks_;	//所有已经申请到并分配的内存块

  // Bytes of memory in blocks allocated so far
  size_t blocks_memory_;		//所有已经申请并分配的内存块的大小

  // No copying allowed
  Arena(const Arena&);
  void operator=(const Arena&);
};

//如果剩余下的内存块大小>bytes，则分配；否则
inline char* Arena::Allocate(size_t bytes) {
  // The semantics of what to return are a bit messy if we allow
  // 0-byte allocations, so we disallow them here (we don't need
  // them for our internal use).
  assert(bytes > 0);
  if (bytes <= alloc_bytes_remaining_) {
    char* result = alloc_ptr_;
    alloc_ptr_ += bytes;
    alloc_bytes_remaining_ -= bytes;
    return result;
  }
  return AllocateFallback(bytes);
}

}  // namespace leveldb

#endif  // STORAGE_LEVELDB_UTIL_ARENA_H_

```

```
//arena.cc
```

```
// Copyright (c) 2011 The LevelDB Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file. See the AUTHORS file for names of contributors.

#include "util/arena.h"
#include <assert.h>

namespace leveldb {

static const int kBlockSize = 4096;			//默认分配一块内存块的大小

Arena::Arena() {
  blocks_memory_ = 0;
  alloc_ptr_ = NULL;  // First allocation will allocate a block
  alloc_bytes_remaining_ = 0;
}

Arena::~Arena() {
  for (size_t i = 0; i < blocks_.size(); i++) {
    delete[] blocks_[i];
  }
}
//
char* Arena::AllocateFallback(size_t bytes) {
  //如果申请的内存大小大于k/4，则分配一块新的内存；
  //否则，则重新申请碎片内存池（kBlockSize大小）
  if (bytes > kBlockSize / 4) {
    // Object is more than a quarter of our block size.  Allocate it separately
    // to avoid wasting too much space in leftover bytes.
    char* result = AllocateNewBlock(bytes);
    return result;
  }

  // We waste the remaining space in the current block.
  alloc_ptr_ = AllocateNewBlock(kBlockSize);
  alloc_bytes_remaining_ = kBlockSize;

  char* result = alloc_ptr_;
  alloc_ptr_ += bytes;
  alloc_bytes_remaining_ -= bytes;
  return result;
}
//关于申请内存对齐的问题（待解决）
char* Arena::AllocateAligned(size_t bytes) {
  const int align = (sizeof(void*) > 8) ? sizeof(void*) : 8;	//获得平台默认的地址长度
  assert((align & (align-1)) == 0);   // align必须为2的n次方
  size_t current_mod = reinterpret_cast<uintptr_t>(alloc_ptr_) & (align-1);
  size_t slop = (current_mod == 0 ? 0 : align - current_mod);
  size_t needed = bytes + slop;
  char* result;
  if (needed <= alloc_bytes_remaining_) {
    result = alloc_ptr_ + slop;
    alloc_ptr_ += needed;
    alloc_bytes_remaining_ -= needed;
  } else {
    // AllocateFallback always returned aligned memory
    result = AllocateFallback(bytes);
  }
  assert((reinterpret_cast<uintptr_t>(result) & (align-1)) == 0);
  return result;
}
//分配bytes大小的内存，所有通过该函数分配的内存，都会被记录
char* Arena::AllocateNewBlock(size_t block_bytes) {
  char* result = new char[block_bytes];
  blocks_memory_ += block_bytes;
  blocks_.push_back(result);
  return result;
}

}  // namespace leveldb
```

**学习知识点**
1. 任何指针都可以赋值给void*，而不需要转换；
2. void*赋值给其他类型的指针则需要转换；
3. void指针不能解引用；
4. void指针不能参加指针运算；
5. uint8_t,uint16_t,uint32_t,uint64_t的位数是一定的，主要用户平台移植；
6. 该类是一个简单的内存管理工具类，对于小内存块（碎片）申请，则从一个4M的块中分割，如果大于1M的，则直接分割；
7. assert如果条件返回错误，则终止应用程序；
8. assert一般在调试阶段使用，调式结束后通过NDEBUG宏禁用；
9. 使用断言捕捉不应该发生的非法情况。不要混淆非法情况与错误情况之间的区别，后者是必然存在的并且是一定要作出处理的
10. 在函数的入口处，使用断言检查参数的有效性（合法性）
11. 在编写函数时，要进行反复的考查，并且自问：“我打算做哪些假定？”一旦确定了的假定，就要使用断言对假定进行检查。
12. intptr_t的使用方法（待续）
13. 粗粒度的内存管理方法
