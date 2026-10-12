# M/M/c：共享队列、多服务台

来源：O04 第 4 页，O03 第 5 页。[原公式](../assets/原页/O04/04.jpg)。

`c` 台相同服务台，每台服务率 `μ`，总到达率 `λ`，共享一个 FCFS 队列，无限容量与无限顾客源。

它不是直接把 `c` 个互不相干的 M/M/1 结果相加：状态 `n` 下总服务率为：

$$
\mu_n=\min(n,c)\mu.
$$

定义 `a=λ/μ`，单台平均利用率：

$$
\rho=\frac\lambda{c\mu}=\frac ac<1.
$$

## 稳态概率

$$
P_0=\left[\sum_{n=0}^{c-1}\frac{a^n}{n!}
+\frac{a^c}{c!(1-\rho)}\right]^{-1}.
$$

$$
P_n=\begin{cases}
\dfrac{a^n}{n!}P_0,&0\le n<c,\\[4pt]
\dfrac{a^n}{c!c^{n-c}}P_0,&n\ge c.
\end{cases}
$$

当 `n=c` 时两种形式相同，分段界点不能造成不一致。

## 平均指标

$$
\boxed{L_q=\frac{a^c\rho}{c!(1-\rho)^2}P_0},\qquad
L_s=L_q+\frac\lambda\mu.
$$

$$
W_q=\frac{L_q}\lambda,\qquad W_s=W_q+\frac1\mu.
$$

整理补充：泊松到达时所有服务台都忙、需要等待的概率为：

$$
P_{\mathrm{wait}}=\sum_{n=c}^{\infty}P_n
=\frac{a^c}{c!(1-\rho)}P_0.
$$

取 `c=1` 应恢复 M/M/1 的全部公式，是核对因子和分母幂次的好方法。

原复习页曾标注“多服务台不考”，但专章保留了完整公式，整理版仍收录知识；这条旁注不视为当前课程的考试范围说明。
