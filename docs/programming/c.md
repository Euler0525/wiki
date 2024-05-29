# C

## 编码错误

```c
# include <stdio.h>

int main()
{
    char* arr1 = "\xef\xbf\xbd\xef\xbf\xbd";
    printf("%s\n", arr1);

    char arr2[1];
    printf("%s\n", arr2);

    char* arr3;
    arr3 = (char*)malloc(6);
    printf("%s\n", arr3);

    char* arr4 = "\xef\xbb\xef\xbb\xef\xbb";
    printf("%s\n", arr4);

    return 0;
}
```

---

```c
/**
 * 只使用分支、循环、局部变量 (使用整数的位宽至多为64
 * bit)、赋值语句、位运算和加减法实现 $a \times b \mod m$
 */
#include <stdint.h>

uint64_t multimod(uint64_t a, uint64_t b, uint64_t m) {
    while (a > m) {
        a -= m;
    }
    while (b > m) {
        b -= m;
    }
    uint64_t result = 0;
    uint64_t su = UINT64_MAX - m + 1;

    for (uint64_t i = 64; i != 0; --i) {
        // 移位时溢出
        if (result >> 63) {
            result = result << 1;
            result += su;
        } else {
            result = result << 1;
        }
        result = (result > m) ? (result - m) : result;

        if ((a >> (i - 1)) & 1) {
            // 加法溢出(考虑b始终不变, 短路减少重复判断)
            if ((result >> 63 & b >> 63) |
                ((result >> 63 | b >> 63) & (result >> 62 & b >> 62))) {
                result = result + b;
                result += su;
            } else {
                result = result + b;
            }
        }
        result = (result > m) ? (result - m) : result;
    }
    result = (result > m) ? (result - m) : result;

    return result;
}
```

## 链表高效串联

`ll` 指针时钟指向下一个节点挂载位置

- 初始时，`ll = &chain`：表示第一个节点应该挂载到 `chain` 指针的位置；
- 每次 `*ll = cl`：将当前节点 `cl` 赋值给 `ll` 指向的位置，第一次是 `chain`，之后是前一个节点的 `next`；
- 然后 `ll = &cl->next`：将 `ll` 移动到当前节点 `cl` 的 `next` 指针的地址，为下一次挂载做准备；

通过这种方式，**头节点和后续节点的处理逻辑完全统一**，没有任何分支判断，代码更简洁、高效。

```c
// https://github.com/nginx/nginx/blob/master/src/core/ngx_buf.c
ngx_chain_t *
ngx_create_chain_of_bufs(ngx_pool_t *pool, ngx_bufs_t *bufs)
{
    u_char       *p;
    ngx_int_t     i;
    ngx_buf_t    *b;
    ngx_chain_t  *chain, *cl, **ll;

    p = ngx_palloc(pool, bufs->num * bufs->size);
    if (p == NULL) {
        return NULL;
    }

    ll = &chain;

    for (i = 0; i < bufs->num; i++) {

        b = ngx_calloc_buf(pool);
        if (b == NULL) {
            return NULL;
        }

        /*
         * set by ngx_calloc_buf():
         *
         *     b->file_pos = 0;
         *     b->file_last = 0;
         *     b->file = NULL;
         *     b->shadow = NULL;
         *     b->tag = 0;
         *     and flags
         *
         */

        b->pos = p;
        b->last = p;
        b->temporary = 1;

        b->start = p;
        p += bufs->size;
        b->end = p;

        cl = ngx_alloc_chain_link(pool);
        if (cl == NULL) {
            return NULL;
        }

        cl->buf = b;
        *ll = cl;
        ll = &cl->next;
    }

    *ll = NULL;

    return chain;
}
```

