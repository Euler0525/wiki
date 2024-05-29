# Assembly Language

## `JG/JNG/JL/JNL`

|       asm       |  function  |    condition    |
| :-------------: | :--------: | :-------------: |
| `JG/JNLE LABLE` |  $a > b$   | `ZF=0 && SF=OF` |
| `JNG/JLE LABEL` | $a \leq b$ | `ZF=1 || SF≠OF` |
| `JL/JNGE LABEL` |  $a < b$   |     `SF≠OF`     |
| `JNL/JGE LABEL` | $a\geq b$  |     `SF=OF`     |

