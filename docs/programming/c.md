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
