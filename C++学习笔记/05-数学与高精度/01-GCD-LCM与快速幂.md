# GCD、LCM 与快速幂

## 最大公约数：欧几里得算法

$$
\gcd(a,b)=\gcd(b,a\bmod b),\qquad \gcd(a,0)=a.
$$

```cpp
// 本模板要求 a >= 0、b >= 0；约定 gcd(0,0)=0。
long long gcdEuclid(long long a, long long b) {
    while (b != 0) {
        long long r = a % b;
        a = b;
        b = r;
    }
    return a;
}
```

不要求 `a >= b`，无需先交换大小。正整数时次数为 `O(log(min(a,b)))` 量级。C++17 可直接使用 `<numeric>` 中的 `std::gcd`。

## 最小公倍数

$$
\operatorname{lcm}(a,b)=\frac{a}{\gcd(a,b)}\cdot b.
$$

```cpp
// 需要 <limits>、<stdexcept>；依赖 gcdEuclid；要求 a、b 非负。
long long lcmSafe(long long a, long long b) {
    if (a == 0 || b == 0) return 0;
    long long q = a / gcdEuclid(a, b);
    if (q > numeric_limits<long long>::max() / b)
        throw overflow_error("lcm exceeds long long");
    return q * b;
}
```

先除后乘能减少中间溢出，结果本身超范围时仍须处理。原写法 `a*b/gcd(a,b)` 可能在乘法时溢出，且 `(0,0)` 时会除以 0。

## 模运算

对正模数 `m`：

$$
(a+b)\bmod m=((a\bmod m)+(b\bmod m))\bmod m,
$$

$$
(a\cdot b)\bmod m=((a\bmod m)(b\bmod m))\bmod m.
$$

累加时可每一步取模，避免最终总和过大，但中间加法 / 乘法也要在所用类型范围内。C++ 负数取余可能为负，可在取余后发现结果负数时加一次 `m`。

## 快速幂

利用指数的二进制表示，反复将底数平方；当前指数位为 1 时把底数乘入答案。求 `a^b mod m` 的乘法次数为 `O(log b)`。

```cpp
// 要求 b >= 0，1 <= mod <= 10^9；该限制保证 long long 乘法不溢出。
long long modPow(long long a, long long b, long long mod) {
    a %= mod;
    if (a < 0) a += mod;
    long long result = 1 % mod;
    while (b > 0) {
        if (b & 1LL) result = result * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return result;
}
```

`b==0` 返回 `1 % mod`，所以 `mod==1` 时结果为 0；此模板按空乘积约定处理 `0^0`。负指数和模数为 0 不适用。模数更大时，需使用能保证乘法不溢出的实现，如平台支持的更宽整数类型。

不取模的高精度幂见[高精度四则运算](04-高精度四则运算.md)。[原始快速幂截图](../assets/快速幂/快速幂模板.png)。
