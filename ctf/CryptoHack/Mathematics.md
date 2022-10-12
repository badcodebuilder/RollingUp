# Mathematics

## Brainteasers Part 1

### Modular Binomials

$N=pq$

$c_1\equiv (2p+3q)^{e_1}\mod{N}$

$c_2\equiv (3p+5q)^{e_2}\mod{N}$

已知 $N$, $c_1$, $c_2$, $e_1$, $e_2$，求 $p$ 和 $q$

+ 解法1：解二次方程

    很自然地想到展开试试，展开因为中间有 $p^iq^j(i,j>0)$ 就消掉了中间项，但是留着两头看起来也没什么用，指数不对。

    那么把指数齐次再展开，得到了关于 $p^{e_1e_2}$ 和 $q^{e_1e_2}$ 的二次方程（就是系数有点大），并且 $gcd(p^{e_1e_2},N)=p$，解出其中一个即可

+ 解法2：因式分解

    考虑到有这样的等式： $a^k-b^k=(a-b)(a^{k-1}+a^{k-2}b+\dots+ab^{k-2}+b^{k-1})$，所以让

    $d_1\equiv 5^{e_1e_2}(2p+3q)^{e_1e_2}\mod{N}\equiv (10p+15q)^{e_1e_2}\mod{N}$

    $d_2\equiv 2^{e_1e_2}(5p+7q)^{e_1e_2}\mod{N}\equiv (10p+14q)^{e_1e_2}\mod{N}$

    则 $d_1-d_2\equiv q((10p+15q)^{e_1e_2-1}+\dots+(10p+14q)^{e_1e_2-1})\mod{N}$

    所以 $gcd(d_1-d_2,N)=q$

两种方法殊途同归