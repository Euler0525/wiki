# C

## Coding Error

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

## ISC_Lab

- [ISC_Lab1](https://jyywiki.cn/ICS/2021/labs/Lab1.html)

```c
/**
 * ISC_Lab1: 大整数运算
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

## Structure Member Alignment

```c
#include <stddef.h>
#include <stdio.h>

// 1. Unrestrained
// 2. #pragma pack(1)
// 3. #pragma pack(2)

struct test {
    char x2;
    int x1;
    short x3;
    long long x4;
} __attribute__((packed));

struct test1 {
    char x2;
    int x1;
    short x3;
    long long x4;
};

struct test2 {
    char x2;
    int x1;
    short x3;
    long long x4;
} __attribute__((aligned(8)));

int main() {
    struct test t;
    printf("%zu, addr_x2=%p\n", offsetof(struct test, x2), &t.x2);
    printf("%zu, addr_x1=%p\n", offsetof(struct test, x1), &t.x1);
    printf("%zu, addr_x3=%p\n", offsetof(struct test, x3), &t.x3);
    printf("%zu, addr_x4=%p\n", offsetof(struct test, x4), &t.x4);
    printf("%zu\n", sizeof(struct test));

    printf("******\n");

    struct test1 t1;
    printf("%zu, addr_x2=%p\n", offsetof(struct test1, x2), &t1.x2);
    printf("%zu, addr_x1=%p\n", offsetof(struct test1, x1), &t1.x1);
    printf("%zu, addr_x3=%p\n", offsetof(struct test1, x3), &t1.x3);
    printf("%zu, addr_x4=%p\n", offsetof(struct test1, x4), &t1.x4);
    printf("%zu\n", sizeof(struct test1));

    printf("******\n");

    struct test2 t2;
    printf("%zu, addr_x2=%p\n", offsetof(struct test2, x2), &t2.x2);
    printf("%zu, addr_x1=%p\n", offsetof(struct test2, x1), &t2.x1);
    printf("%zu, addr_x3=%p\n", offsetof(struct test2, x3), &t2.x3);
    printf("%zu, addr_x4=%p\n", offsetof(struct test2, x4), &t2.x4);
    printf("%zu\n", sizeof(struct test2));

    return 0;
}

```

