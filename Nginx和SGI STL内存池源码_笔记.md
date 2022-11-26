# 01 面试常见的池

内存池 进程池 线程池 连接池

# 02 SGI STL的vector容器源码如何管理对象

SGI STL：两个 allocator 的实现：一级 allocator 内存管理 malloc/free 二级 allocator 内存池的实现

# 03 SGI STL二级空间配置器重要成员解读

```cpp
//内存池的粒度信息
enum {_ALIGN = 8};
enum {_MAX_BYTES = 128};
enum {_NFREELISTS = 16}; // _MAX_BYTES/_ALIGN
```

```cpp
//每一个内存块chunk块的头信息
union _Obj {
    union _Obj* _M_free_list_link;
    char _M_client_data[1];    /* The client sees this.        */
};
```

```cpp
//组织所有自由链表的数组，数组的每一个元素的类型是_Obj*，全部初始化为 0
static _Obj* __STL_VOLATILE _S_free_list[_NFREELISTS]; 
```

```cpp
//记录内存chunk块的分配情况
// Chunk allocation state.
static char* _S_start_free;
static char* _S_end_free;
static size_t _S_heap_size;

template <bool __threads, int __inst>
char* __default_alloc_template<__threads, __inst>::_S_start_free = 0;

template <bool __threads, int __inst>
char* __default_alloc_template<__threads, __inst>::_S_end_free = 0;

template <bool __threads, int __inst>
size_t __default_alloc_template<__threads, __inst>::_S_heap_size = 0;
```

# 04 两个重要的辅助函数`_S_round_up`和`_S_freelist_index`代码解读

```cpp
//将 __bytes 上调至最邻近的 8 的倍数
static size_t
    _S_round_up(size_t __bytes) 
{ return (((__bytes) + (size_t) _ALIGN-1) & ~((size_t) _ALIGN - 1)); }
```

`size_t` 无符号四字节 约等于 `unsigned int` 

```
/*
_ALIGN 1000
(size_t)_ALIGN-1 00000000 00000000 00000000 00000111
11111111 11111111 11111111 11111000
*/
```

最后三位是几都没区别，每次进位是多了 8 个。



```cpp
//返回 __bytes 大小的 chunk 块位于 free-list 中的编号
static  size_t _S_freelist_index(size_t __bytes) {
    return (((__bytes) + (size_t)_ALIGN-1)/(size_t)_ALIGN - 1);
}
```

比如传进来 8 ，输出 0 。运算公式为 `(8+7) / 8 - 1 = 0`



# 05 allocate内存分配函数代码解读

allocate 负责给容器开辟内存

大于 128 不受内存池管理了

二级指针遍历指针数组，解引用二级指针变为一级指针。如果为空，分配内存池；如果不为空，把下一个空的给Obj，返回不为空的



# 06 _S_refill函数代码解读

为空时开辟内存

纵向开辟计算出来的个数个小块





# 07 _S_chunk_alloc函数代码解读 - 1







# 08 deallocate内存释放函数代码解读







# 09 _S_chunk_alloc函数代码解读 - 2







# 10 _S_chunk_alloc函数代码解读 - 3







# 11 _S_chunk_alloc函数代码解读 - 4







# 12 reallocate内存扩容函数代码解读







# 13 nginx内存池创建函数代码解读







# 14 nginx内存池分配方案-小块内存分配代码解读







# 15 nginx内存池分配方案-大块内存分配释放代码解读







# 16 nginx内存池重置函数和小块内存回收方案代码解读







# 17 nginx内存池外部资源释放和内存池销毁代码解读





# 18 nginx源码编译测试内存池接口函数的功能







# 19 跟着做nginx内存池源码移植项目 - 1







# 20 跟着做nginx内存池源码移植项目 - 2






# 21 跟着做nginx内存池源码移植项目 - 3







# 22 跟着做SGI STL内存池源码移植项目 - 1







# 23 跟着做SGI STL内存池源码移植项目 - 2
