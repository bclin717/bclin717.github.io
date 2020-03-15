---
title: 如何實作一個 aligned_alloc() ？
categories:
  - 軟體研討
date: 2020-03-15 17:05:38
tags:
---

因為面試被問到，所以就稍微研究了一下。

# What is aligned_alloc() ?

{% codeblock lang:h %}
void *aligned_alloc( size_t alignment, size_t size );
{% endcodeblock %}

根據 cppreference，aligned_alloc() 是分配 size 位元組未初始化的存儲空間，按照 alignment 指定對齊。size 參數必須是 alignment 的整數倍。 


# Implementation

我們先用 malloc 向系統要一塊空間，但 malloc 給出來的並不是對齊的，所以一開始使用 malloc 時，要多要一點空間，確保就算偏移很多也是可以對齊。
一開始先要一塊空間，是 目標大小 + 對齊的量 + 一個size_t的大小。
```c
void *p1;
p1 = (void *) malloc(bytes + alignment + sizeof(size_t))
```

設想一個簡單的情況，我們要 16 Bytes 的空間以 16 Bytes 對齊，運氣好的話一開始就對齊了，運氣不好，可能偏移了 15 Bytes，我們需要額外的空間做對齊。
這時我們把 molloc 給我們的 address 加上偏移量與 size_t 的大小，確保我們 shift 過一個 alignment block。
```c
size_t addr = (size_t) p1 + alignment + sizeof(size_t);
```

接著把這個 address 跟偏移量做 mod 運算，取得實際偏移量，再用 address 減去偏移量，把指標往回挪，藉此得出對齊後的位址。

```c
void *p2;
p2 = (void*)(addr - (addr % alignment));
```

但是，free 時我們必須用 malloc 給的記憶體位址才能把整塊都 free 掉。所以我們必須保留 p1 的記憶體位址，怎麼做呢？我們剛剛多要了一塊 size_t 的大小，於是我們把 p2（對齊的位址）往前一個 size_t 大小的位址，塞入了 p1 的位址。這樣我們之後 free 時只要呼叫這個位址，就可以 free 整塊 malloc 給我們的空間。

```c
*((size_t*)p2 - 1) = (size_t)p1;
```

# Code

```c
#include <stdio.h>
#include <stdlib.h>

void *aligned_malloc(size_t bytes, size_t alignment) {
    void *p1 ,*p2;

    if((p1 = (void*) malloc(bytes + alignment + sizeof(size_t))) == NULL)
        return NULL;
        
    size_t addr = (size_t) p1 + alignment + sizeof(size_t);
    p2 = (void*)(addr - (addr % alignment));

    *((size_t*)p2 - 1) = (size_t)p1;

    return p2;
}

void aligned_free(void *p ) {
    free((void*)(*((size_t*) p - 1)));
}

int main() {
    int *ptr = (int*)aligned_malloc(1024,12);
    aligned_free((void*)ptr);
}
```


