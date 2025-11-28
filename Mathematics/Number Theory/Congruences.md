
<center><b><font size=6>Congruences</font></b></center>



### 1. Modular Arithmetic

___

​	In **the Set of Equivalence Classes Modulo** $n$, $\mathbb{Z}/n\mathbb{Z}=\{[a]_n \mid 0 \le a \le n-1\}$, define addition and multiplication modulo $n$:
$$
[a]_n+[b]_n=[a+b]_n\\
[a]_n\cdot[b]_n=[a\cdot b]_n
$$

​	The structure $(\mathbb{Z}/n\mathbb{Z}, +)$ forms a **Finite Abelian Group** of size $|\mathbb{Z}/n\mathbb{Z}| = n$. Similarly, $((\mathbb{Z}/n\mathbb{Z})^\times, \cdot)$ is also a finite abelian group, whose elements are all classes in $\mathbb{Z}/n\mathbb{Z}$ that are coprime to $n$:
$$
(\mathbb{Z}/n\mathbb{Z})^\times=\{[a]_n\in\mathbb{Z}/n\mathbb{Z}\mid\gcd(a,n)=1\}
$$

​	The size of $(\mathbb{Z}/n\mathbb{Z})^\times$ is $\varphi(n)$, the **Euler's Phi Function**:
$$
\varphi(n)=n\times\prod_{\text{prime}\ p\mid n}\left(1-\frac1p\right)
$$

```cpp
// Factorization-based computation of phi(x)
int phi(int x)
{
    int val=x;
    for(int i=2;i<=sqrt(x);i++)
    {
        if(x%i==0)
        {
            val=val/i*(i-1);
            while(x%i==0) x/=i;
        }
    }
    if(x>1) val=val/x*(x-1);
    return val;
}
````

```cpp
// O(nloglog n) sieve method
void phi_table(int n,int* phi)
{
	memset(phi,0,sizeof(phi));
	phi[1]=1;
	for(int i=2;i<=n;i++)
	{
		if(phi[i]==0)
		{
			for(int j=i;j<=n;j+=i)
			{
				if(phi[j]==0) phi[j]=j;
				phi[j]=phi[j]/i*(i-1);
			}
		}
	}
}
```

​	A nonempty closed subset of a finite group is a **Subgroup**. Thus, subgroups may be generated via closure. For a group $(S, \oplus)$ and any element $a \in S$:
$$
a^{(k)}=\bigoplus_{i=1}^ka=\underbrace{a\oplus a\oplus\cdots\oplus a}_k\\
\langle a \rangle:=\{a^{(k)}\mid k\geqslant1\}
$$

​	The size of the generated subgroup $(\langle a\rangle, \oplus)$ equals the **Order** of the generator $a$:
$$
|\langle a \rangle|=\text{ord}(a):=\min\{k\in\mathbb{N}_+\mid a^{(k)}=a^{(0)}\}
$$



### 2. Linear Congruences

---

​	For any positive integers $a$ and $n$, in $\mathbb{Z}/n\mathbb{Z}$ we have $a^{(k)} = ka \bmod n$, and
$$
\langle a \rangle=\langle\gcd(a,n)\rangle=\{0,\gcd(a,n),2\gcd(a,n),\cdots,n-\gcd(a,n)\}\\
|\langle a \rangle|=\frac{n}{\gcd(a,n)}
$$

​	By Bezout’s identity, there exist $x^\prime$ and $y^\prime$ such that $\gcd(a,n)=ax^\prime+ny^\prime$. The congruence $ax\equiv b\pmod n$ has a particular solution $\displaystyle x_0=\frac{b}{\gcd(a,n)}\times x^\prime$ if and only if $\gcd(a,n)\mid b$, and its general solution is $\displaystyle x_i=x_0+i\times\frac{n}{\gcd(a,n)}$, where $i=0,1,\cdots,\gcd(a,n)-1$.

​	The implementation below returns the smallest non-negative solution, or $-1$ if no solution exists. Its time complexity is $O[\log n+\gcd(a,n)]$.

```cpp
solve_modular_linear_equation(a,b,n)
{
    int x,y,d=exgcd(a,n,x,y);
    if(b%d) return -1;
    n/=d;
    return ((long long)x*(b/d)%n+n)%n;
}
```



### 3. Chinese Remainder Theorem

---

​	Let $n = n_1 n_2 \cdots n_k$, where the factor $n_i$ are pairwise coprime. For any $a\in\mathbb{Z}/n\mathbb{Z}$, define $a_i = a \bmod n_i \in \mathbb{Z}/n_i\mathbb{Z}$. There is a bijection:
$$
a\leftrightarrow(a_1,a_2,\cdots,a_k)
$$
​	And algebraically:
$$
\mathbb{Z}/n\mathbb{Z}\cong\prod_{i=1}^k\mathbb{Z}/n_i\mathbb{Z}
$$

​	For a system of linear congruences
$$
\left\{\begin{matrix}
x\equiv a_1\pmod{n_1}\\ 
x\equiv a_2\pmod{n_2}\\ 
\vdots\\
x\equiv a_k\pmod{n_k}\\ 
\end{matrix}\right.
$$
​	With pairwise coprime $n_i$, the solution is obtained as follows:

* Compute $n=n_1n_2\cdots n_k$
* For each $i$:

  * Compute $\displaystyle m_i=\frac{n}{n_i}$.
  * Compute the modular inverse $m_i^{-1}\pmod{n_i}$.
  * Compute $c_i=m_im_i^{-1}$.
* The solution is $x\equiv\sum_{i=1}^ka_ic_i\pmod n$

```cpp
int crt(int k,int* a,int* n)
{
    long long prod=1,ans=0;
    for(int i=0;i<k;i++)
    {
        prod=prod*n[i];
    }
    for(int i=0;i<k;i++)
    {
        long long m=prod/n[i],inv,y;
        exgcd(m,n[i],inv,y);
        ans=(ans+a[i]*m*inv%prod)%prod;
    }
    return (ans%prod+prod)%prod;
}
```



### 4. Modular Exponentiation

---

​	**Euler’s theorem** states that for any integer $n > 1$:
$$
\forall a\in(\mathbb{Z}/n\mathbb{Z})^\times,a^{\varphi(n)}\equiv1\pmod n
$$

​	For a prime $p$, this gives **Fermat’s Little Theorem**:
$$
\forall a\in(\mathbb{Z}/p\mathbb{Z})^\times,a^{p-1}\equiv1\pmod p
$$

​	Euler’s theorem can be extended to compute $a^k\bmod n$ for arbitrary $a,n$:
$$
a^k=\begin{cases}
a^{k\bmod\varphi(n)}&\gcd(a,n)=1\\
a^k&\gcd(a,n)\ne1,k<\varphi(n)\\
a^{k\bmod\varphi(n)+\varphi(n)}&\gcd(a,n)\ne1,k\geqslant\varphi(n)
\end{cases}\pmod n
$$

​	If $\text{ord}(g)=|(\mathbb{Z}/n\mathbb{Z})^\times|$, then $g$ is called a **Primitive Root** modulo $n$, and $(\mathbb{Z}/n\mathbb{Z})^\times$ is cyclic. Primitive roots exist if and only if $n=1,2,4,p^e,2p^e$ where $p$ is an odd prime and $e\in\mathbb{N}_+$. The number of primitive roots modulo $n$ is $\varphi(\varphi(n))$.

​	If $g$ is a primitive root of $(\mathbb{Z}/n\mathbb{Z})^\times$, then for any $a\in (\mathbb{Z}/n\mathbb{Z})^\times$ there exists $\text{ind}_g(a)$ such that $g^{\text{ind}_g(a)}\equiv a\pmod n$. This $\text{ind}_g(a)$ is the **Discrete Logarithm** of $a$, modulo $n$, to the base $g$.
$$
x\equiv y\pmod{\varphi(n)}\Leftrightarrow g^x\equiv g^y\pmod{n}
$$

​	Use the **Baby-Step Giant-Step Algorithm** to compute discrete logarithms. For $g^x\equiv a\pmod n$, set $x=i\lceil\sqrt{n}\rceil-j$. The equation becomes $g^{i\lceil\sqrt{n}\rceil}=ag^j$. For all $j$ in the range $0\sim\lceil\sqrt{n}\rceil-1$, store $ag^{j}\bmod n$ in a hash table. Then enumerate all $i$ in the range $0\sim\lceil\sqrt{n}\rceil$, compute $g^{i\lceil\sqrt{n}\rceil}\bmod n$, query the hash table for a matching value of $ag^j$, and update the solution accordingly. This algorithm runs in $O(\sqrt{n})$ time.

```cpp
// Compute the smallest non-negative solution
// return -1 if no solution exists
int baby_step_giant_step(int g,int a,int n)
{
    map<int,int> h;
    h.clear();a%=n;
    int t=(int)sqrt(n)+1;
    for(int j=0;j<t;j++)
    {
        int val=(long long)a*power(g,j,n)%n;
        h[val]=j;
    }
    g=power(g,t,n);
    if(g==0) return a==0?1:-1;
    for(int i=0;i<=t;i++)
    {
        int val=power(g,i,n);
        int j=h.find(val)==h.end()?-1:h[val];
        if(j>=0&&i*t-j>=0) return i*t-j;
    }
    return -1;
}
```

