[ARC112F](https://atcoder.jp/contests/arc112/tasks/arc112_f)

<!-- more -->

首先对于交换操作，可以想到将当前手牌 hash 一下，减少交换操作的影响。

对于一张面值为 $i$ 的牌，定义其分值为 $2^{i-1}(i-1)!$​ ，定义初始手牌的分值 $A$ 为每张手牌的分值和。特判 $A=0$ 的情况，那么接下来可以保证手牌一定非空。

如果将 $2n$ 张面值为 $n$ 的牌换成一张面值为 $1$ 的牌，总分值会减去 $M=(2^{n}n!-1)$​​，除此之外，交换操作不会改变总分值。因此要尽可能地进行交换操作，直到对于每一个 $i(1\le i\le n)$​，面值为 $i$ 的牌的张数小于 $2i$。

先考虑一个简单情况：假如 $m=0$​​，那么最终的总分值一定是 $A \space mod \space M$，又因为最终的手牌一定无法再次进行交换操作，因此相对应的每张牌的个数同样唯一，时间复杂度为 $\Theta(n)$。

对于一般的情况，用类似的方法求出第 $i$ 个牌堆的分值 $B_i$，显然加牌堆的操作等价于分值直接相加。因此可以列出如下等式：
$$
A+B_1x_1+B_2x_2+\dots+B_mx_m\equiv v(\bmod M)
$$
其中，$v$ 为最终的总分值，移项后可得：
$$
B_1x_1+B_2x_2+\dots+B_mx_m-Mx_{m+1}=v-A
$$
这是一个多元一次不定方程，根据裴蜀定理，该方程有整数解当且仅当 $v-A$​​​ 是 $g=\gcd(B1,B2,\dots,B_m,M)$​​​​ 的倍数。而当原方程有整数解时，必然可以构造出一组非负整数解。这样就去除了牌堆的影响，现在的问题是，对于分值 $v$​​，满足 $0\le v<M$​ 且 $v\equiv A(\bmod g)$​ 时，对应的手牌总数最少是多少。

其中一种实现方式是暴力枚举 $v$​​，令 $v_0=A\mod\space g,v_1=v_0+g,v_2=v_1+g\dots$​​，对于每一个 $v$​​ ，用 $\Theta(n)$​​ 的时间更新答案。总时间复杂度为 $O(\frac {M\times n} g)$​​，由于 $M=2^nn!-1\approx10^{18}$​​，所以当 $g$​​ 比较小的时候枚举量是无法接受的。

考虑根号分治，当 $g\ge \sqrt M$ 时，直接使用上述做法，复杂度为 $O(\frac {M\times n} g)$。

当 $g< \sqrt M$ 的时候，根据分治法的思路，应该从**反面**思考这个问题。

直接 dp，令 $f(i)$ 表示当分值为 $i$ 时，最少要多少张牌。

转移的时候可以枚举加入什么牌，即 $f(x)+1\rightarrow f((x+2^{l-1}(l-1)!)\bmod M)$​。

虽然这个状态数是 $O(M)$ 的，但是最后的答案可以写成：
$$
\min_{v\equiv A(\bmod g)} f(v)
$$
因此，可以将模 $g$ 余数相同的放到同一个状态里，由于更新和求答案都是求最小值，这样并不会影响正确性和转移，同时将状态降为 $O(g)$。

由于加入一张牌等同于当前分值加上这张牌的分值，因此可以进行转移：$f(x)+1 \rightarrow f((x+p) \mod g)$​ ，其中 $p$ 是新加入的牌的分值，整个转移过程可以看作求有向图的最短路，可以用 bfs 解决，时间复杂度为 $O(n\times g)$​。​​

因此总复杂度看似为 $O(n\sqrt M)$​​​​​，但由于​ $g$​​​ 是 $M$​​​ 的约数，而 $M=2^nn!-1$​​​，所以 $g$​​​ 无法非常接近 $\sqrt M$​​​，换句话说，就是无法达到上界，打表发现，运算量最大值是当 $n=16,g=1214827$​​​​ 时，$n\times g=14577924$​​​​，因此该做法可以通过本题。

(by ShmilyTY)