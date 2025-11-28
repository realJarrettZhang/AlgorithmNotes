<center><b><font size=6>Hash</font></b></center>



### 1. Fundamental Concepts

___

​	The core idea is to map a string to an integer while constraining the value range to facilitate efficient comparison. For a string $s = s_0 s_1 \ldots s_{n-1}$, the Hash function is defined as:
$$
h(s) = (s_0 b^{n-1} + s_1 b^{n-2} + \cdots + s_{n-1} b^0)\ \text{mod}\ p \qquad \gcd(b,p)=1
$$

```cpp
// Natural overflow with unsigned long long
unsigned long long hash(const string& s)
{
    unsigned long long val=0;
    for(auto i:s)
    {
        val=val*base+i;
    }
    return val;
}
````

```cpp
// Twin-prime double hashing
const int mod1=1e9+7,mod2=1e9+9;
pair<int,int> hash(const string& s)
{
    int val1=0,val2=0;
    for(auto i:s)
    {
        val1=(val1*base+i)%mod1;
        val2=(val2*base+i)%mod2;
    }
    return make_pair(val1,val2);
}
```

​	A **Hash Collision** occurs when two distinct strings map to the same hash value. Let the total number of possible strings be $d$, and let $n$ denote the number of strings being hashed. The collision probability is:
$$
p(n,d)=1-\frac{d!}{d^n(d-n)!}\approx1-\exp\left(-\frac{n(n-1)}{2d}\right)
$$



### 2. Rolling Optimization

---

​	The time complexity of a single hash computation is $O(n)$. For multiple substring-hash queries, we preprocess all prefixes. Let $f(k)$ denote the hash value of $s[0 \cdots k]$, and compute prefix values via:
$$
f(k+1) = f(k)\times b + s_{k+1}
$$

​	With $O(n)$ preprocessing of $b^i$, each substring hash can be queried in $O(1)$ time (or $O(\log n)$ using fast exponentiation):
$$
h(s[l \cdots r]) = f(r) - b^{,r-l} \times f(l)
$$

