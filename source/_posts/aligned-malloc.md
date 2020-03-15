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

{% codeblock lang:h %}

#include <stdio.h>
#include <stdlib.h>

void *aligned_malloc(size_t bytes, size_t alignment) {
    void *p1 ,*p2; // basic pointer needed for computation.

    if((p1 =(void *) malloc(bytes + alignment + sizeof(size_t)))==NULL)
        return NULL;   
    *((size_t *)p2-1)=(size_t)p1;

    return p2;
}


void aligned_free(void *p ) {
    free((void *)(*((size_t *) p-1)));
}

int main() {
    int *ptr = (int*)aligned_malloc(1024,12); 
}

{% endcodeblock %}