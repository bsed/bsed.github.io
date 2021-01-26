---
title: "redis sds数据结构的指针技巧"
categories: [ "Linux" ]
tags: [ "redis","data-struct" ]
draft: false
slug: "pointer-to-sds-redis-data-structure"
date: "2014-07-07 21:08:00"
---

redis中没有直接使用char *，而是使用自定义的sds结构来表示字符串。sds的定义如下：

    typedef char *sds;
    struct sdshdr {
        int len;
        int free;
        char buf[];
    };

sdshdr中的len代表buf中字符串的长度（不包括结尾的’’），free代表buf剩余可用的长度。这种结构与char *相比有什么优点，请参考Redis设计与实现。


<!--more-->


上面看到sds这种类型就是char *，那sds和sdshdr有什么关系？先来看生成sds的函数：

    sds sdsnewlen(const void *init, size_t initlen) {
        struct sdshdr *sh;
    
        if (init) {
            sh = zmalloc(sizeof(struct sdshdr)+initlen+1);
        } else {
            sh = zcalloc(sizeof(struct sdshdr)+initlen+1);
        }
        if (sh == NULL) return NULL;
        sh->len = initlen;
        sh->free = 0;
        if (initlen && init)
            memcpy(sh->buf, init, initlen);
        sh->buf[initlen] = '\0';
        return (char*)sh->buf;
    }

每当生成sds的时候，会先生成sdshdr，然后返回sdshdr->buf。原来sds就是sdshdr->buf，sdshdr的意思应该是“sds header”。sds相关的函数大多以sds作为参数的类型，而不是sdshdr，当需要用到sdshdr的时候，redis使用了一个技巧，以便快速的通过sds得到sdshdr。以sdslen这个函数为例：

    static inline size_t sdslen(const sds s) {
        struct sdshdr *sh = (void*)(s-(sizeof(struct sdshdr)));
        return sh->len;
    }

通过将sds的地址减去sizeof(struct sdshdr)，便得到了sdshdr的地址。redis的文档里面已经解释了这个技巧。假如我们通过sdsnewlen("redis", 5)生成了一个sds，其sdshdr的地址为sh，那么像下图所示：
-----------
|5|0|redis|
-----------
^   ^
sh  sh->buf

sh = sh->buf - sizeof(struct sdshdr)。看到这里也许会有疑问，sizeof(strcut sdshdr)不应该是sizeof(int) + sizeof(int) + sizeof(char *)吗？C99标准中有如下规定：

As a special case, the last element of a structure with more than one named member may have an incomplete array type; this is called a flexible array member. In most situations, the flexible array member is ignored. In particular, the size of the structure is as if the flexible array member were omitted except that it may have more trailing padding than the omission would imply.

However, when a . (or ->) operator has a left operand that is (a pointer to) a structure with a flexible array member and the right operand names that member, it behaves as if that member were replaced with the longest array (with the same element type) that would not make the structure larger than the object being accessed; the offset of the array shall remain that of the flexible array member, even if this would differ from that of the replacement array. If this array would have no elements, it behaves as if it had one element but the behavior is undefined if any attempt is made to access that element or to generate a pointer one past it. 

sdshdr最后一个成员是未完整定义的数组类型，这种结构体的定义方式使buf成为可变数组成员。内核中也有flexible array member的应用：

     struct bts_action {
             u16 type;
             u16 size;
             u8 data[0];
     } __attribute__ ((packed));
    
    struct bts_action *var = kmalloc(sizeof(*var) + extra, GFP_KERNEL);

这样就减少了kmalloc的调用次数。这就是通过将buf的地址减去sizeof(struct sdshdr)可以得到sdshdr地址的原因。
参考


