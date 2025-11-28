<center><b><font size=6>Simulated Annealing</font></b></center>



### 1. Concepts

___

​	The Simulated Annealing algorithm is a stochastic optimization method based on a **Monte Carlo** iterative search strategy. Its underlying rationale stems from the analogy between the annealing process of solid materials in physics and general combinatorial optimization problems. During the search for an optimal solution, SA introduces a time-varying probability of accepting uphill moves, which gradually declines to zero. This mechanism enables the algorithm to escape local optima with some probability and ultimately converge toward a global optimum.



### 2. Algorithmic Model

___

Define an energy function $E(x)$ to evaluate the quality of a state $x$ (lower energy corresponds to a better state).

1. Generate a new state $x'$ from the current state $x$ according to a predefined transformation rule.
2. Compute the energy difference: $\Delta E = E(x') - E(x)$.
3. If $\Delta E < 0$, accept $x'$ as the new current state; otherwise, accept it with probability $e^{-\frac{\Delta E}{T}}$ (**Metropolis Criterion**).
4. Continue iterating; when the temperature $T$ approaches $0$, consider the current state as optimal.



### 3. Optimization Strategies

___

1. Prefer transformation rules that generate a new state via a simple modification of the current state.
2. Because the energy difference typically depends only on the changed components, incremental computation is recommended.
3. For multimodal (multi-peak) functions, SA can be applied in segmented phases to search for the global optimum.
4. Maintain the best state encountered during the annealing process.



### 4. Implementation

___

```cpp
double anneal()
{
    for(double t=temperature;t>eps&&(double)clock()/CLOCKS_PER_SEC<max_time;t*=rate)
    {
        double new_e,new_s=get(s,t,&new_e),delta=new_e-e;
        if(delta<0||random(0,1)<exp(-delta/t))
        {
            s=new_s,e=new_e;
            if(e<ans_e) ans=s,ans_e=e;
        }
    }
    return ans;
}
```
