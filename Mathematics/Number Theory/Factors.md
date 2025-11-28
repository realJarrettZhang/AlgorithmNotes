<center><b><font size=6>Factors</font></b></center>



### 1. Integer Factorization

___

​	**The Fundamental Theorem of Arithmetic** states that any positive integer greater than $1$ can be uniquely factored into a product of finitely many primes:
$$
n=p_1^{c_1}p_2^{c_2}\cdots p_m^{c_m}
$$
​	Where $c_i \in \mathbb{N}_+$ and each $p_i$ is a prime. 

​	To obtain the **Prime Factorization** of $n$, one may apply **Trial Division**. Enumerate each integer $d$ from $2$ to $\left\lfloor\sqrt{n}\right\rfloor$. If $d \mid n$, repeatedly divide out all factors of $d$ from $n$ and record their multiplicities. Because any composite factor will have been removed before being reached in enumeration, the method yields the correct prime factorization. The time complexity is $O(\sqrt{n})$.

```cpp
void divide(int n)
{
    int m=0;
    for(int i=2;i<=sqrt(n);i++)
    {
        if(n%i==0)
        {
            factor[++m]=i,cnt[m]=0;
            while(n%i==0)
            {
                n/=i;
                cnt[m]++;
            }
        }
    }
    if(n>1)
    {
        factor[++m]=n;
        cnt[m]=1;
    }
    for(int i=1;i<=m;i++)
    {
        cout << factor[i] << '^' << cnt[i] << endl;
    }
}
```

​	Using the same idea, we can compute **the Set of Positive Divisors** of $n$:

```cpp
for(int i=1;i*i<=n;i++)
{
    if(n%i==0)
    {
        factor[m++]=i;
        if(i!=n/i) factor[m++]=n/i;
    }
}
```

​	Conversely, for each integer $d$, the numbers in $1 \sim n$ having $d$ as a divisor are exactly the multiples $\displaystyle d,2d,\cdots,\left\lfloor\frac{n}{d}\right\rfloor d$. Thus, the positive divisor sets for all integers in $1 \sim n$ can be computed in $\displaystyle O\!\left(\sum_{i=1}^n \frac{n}{i}\right) = O(n \log n)$ time:

```cpp
vector<int> factor[maxn];
for(int i=1;i<=n;i++)
{
    for(int j=1;j<=n/i;j++)
    {
        factor[i*j].push_back(i);
    }
}
```




### 2. Greatest Common Divisor

___

​	The most commonly used algorithm for computing the greatest common divisor is the **Euclidean Algorithm**, $\gcd(a,b) = \gcd(b, a \bmod b)$, with time complexity $O[\log(a+b)]$.

```cpp
int gcd(int a,int b)
{
    return b?gcd(b,a%b):a;
}
```

​	By extending the Euclidean algorithm, we may solve for integers $x$ and $y$ such that $\gcd(a,b) = ax + by$. Perform the following recursive computation:
$$
\gcd(b, a \bmod b) = b x' + (a \bmod b) y'
$$
​	This may be rewritten as:
$$
\begin{align}
\gcd(a,b) &= b x' + \left(a - b \left\lfloor \frac{a}{b} \right\rfloor\right) y' \\
&= a y' + b\left(x' - \left\lfloor \frac{a}{b} \right\rfloor y'\right)
\end{align}
$$

​	Thus, we obtain the solution:
$$
\begin{cases}
x = y' \\
y = \displaystyle x' - \left\lfloor \frac{a}{b} \right\rfloor y'
\end{cases}
$$

```cpp
int exgcd(int a,int b,int& x,int& y)
{
    if(b==0)
    {
        x=1,y=0;
        return a;
    }
    int d=exgcd(b,a%b,x,y);
    int z=x;x=y;y=z-y*(a/b);
    return d;
}
```
