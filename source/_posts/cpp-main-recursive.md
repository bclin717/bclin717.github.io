---
title: C/C++ 遞迴 main 的用法
categories:
  - 軟體研討
date: 2016-05-03 22:42:56
tags:
---

今天看見朋友貼了一個入門的程式作業：「輸入 5 則輸出 12345 ，輸入 9 則輸出 123456789。」 大家第一個想到的解法應該是迴圈了，基本上也是用迴圈解會比較合適：

{% codeblock lang:cpp %}
#include <stdlib.h> 
#include <stdio.h> 
int main() {
  int i, t; 
  scanf("%d", &t); 
  for(i = 1; i &lt;= t; i++) { 
    printf("%d", i); 
  } 
  return 0; 
}
{% endcodeblock %}

這個寫法如果寫錯，大概要去面壁了。當然，程式本來就有很多種寫法，撇除一些比較骯髒的寫法，如 if-else 搭配 printf，我們可以用遞迴來解，寫成副程式進行遞迴是最基本的寫法之一：

```cpp
#include<stdio.h>
#include<stdlib.h>

int go(int n) {
  if(n == 1) {
    return 1;
  } else {
    int x = go(n-1);
    printf("%d", x);
    return x+1;
  }
}

int main(int argc, char const **argv) {
  int n;
  scanf("%d", &amp;n);
  printf("%d", go(n));
  return 0;
}
```


似乎有點多，如果我們能不寫副程式而實現遞迴呢？沒錯，直接遞迴 main() 也是一種作法：

```cpp
#include<stdio.h>
#include<stdlib.h>

int main(int argc, char **argv)
{
  int n, result;
  n = atoi(argv[1]);
  if(n == 1) {
    result = 1;
  } else {
    char buffer[16];
    char *pt = buffer;
    char **pt2 = &amp;pt - 1;
    sprintf(buffer, "%d", n-1);
    result = main(2, pt2) + 1;
  }
  printf("%d", result);
  return result;
}
```

若是用 Static 變數則可以更簡化：

```cpp
#include<stdio.h>
#include<stdlib.h>

int main(int argc, char **argv) {
  static int n = 0;
  if(++n &lt;= atoi(argv\[1\])) {
    printf("%d", n);
    return main(2, argv);
  }
  return 0;
}
```

筆者在帶大學計概課程的時候是這樣寫的：

```cpp
#include <stdio.h>
int main(int argc, char **argv) {
  printf("%d", argc-1);
  if(argc-1 < atoi(argv[1])) return main(++argc, argv);
}
```


算是複習一下遞迴的概念與寫法，程式有點髒請見諒。不過，寫了這樣一篇遞迴文，還是老話一句： 「遞迴只應天上有，凡人該當用迴圈」。