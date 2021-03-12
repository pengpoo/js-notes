## IEEE浮点数格式和JS中数值的存储

![929003983D187C22B995EC3BAA4739D6](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/11/20210311172711.png)

----

![1D77F9AFE1164F53C0A77FFD4DAF4C8F](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/11/20210311172734.png)

----

![7E80AEED487F661576CADE908281E5D8](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/11/20210311172744.png)

----

**JS中的`Number.MAX_VALUE`和`Number.MIN_VALUE`**

当符号位为0、指数取到1023、小数位全为1时，为可表示的最大值，也就是

```javascript
let maxValue = (-1) ** 0 * 2 ** 1023 * (Number.parseInt('1'.repeat(53), 2) * 2 ** (-52));
maxValue === Number.MAX_VALUE; // true
```

最小精度为0.0000…1(51个0，1个1)(二进制的)，能表示的最小非零值为

```javascript
let minValue = (-1) ** 0 * 2 ** (-1022) * (Number.parseInt('0'.repeat(52) + '1', 2) * 2 ** (-52));
minValue === Number.MIN_VALUE;
```

**JS中的`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`**

```javascript
var max_safe_int = (-1)**0 * 2**52 * (Number.parseInt("1".repeat(53),2) * 2**-52);
max_safe_int === Number.MAX_SAFE_INTEGER;

-1 * max_safe_int === Number.MIN_SAFE_INTEGER;
```

