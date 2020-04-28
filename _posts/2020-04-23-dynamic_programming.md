---
toc: true
layout: post
description: Reward, State, Action!
categories: [markdown]
title: Dynamic Programming in RL
---

Recall that the state-value function for an arbitrary policy \pi is given by: $v_\pi(s) = \Sigma_a\pi(a \mid s) \Sigma_{s',r}p(s',r|s,a)[r + \gamma v_\pi(s')]$. If the dynamics of the environment is given, the problem simplifies to solving a system of linear equations with $ |S| $ equations, but requires tedious computation. Hence, iterative solution methods to save the day.

*The key idea is of RL in general is to use value functions and structure the search for good policies.* Optimal value functions are given by the bellman equations:

1. $v_*(s) = max_a\sum_{s', r}p(s',r\mid s,a)[r + \gamma v_*(s')]$  
2. $q_*(s, a) = \sum_{s',r}p(s',r\mid s,a)[r + \gamma max_{a'}q_*(s',a')]$

Using these equations as update rules, Dynamic Programming algorithms are obtained.

# Policy Evaluation
Policy evaluation simply means computing the state-value function $v_\pi(s)$ for a policy $\pi$. From the bellman equation: $v_{\pi}(s) = \sum_a\p(a\mid s) \sum_{s',r}p(s',r\mid s,a)[r + \gamma \v_\pi{s'}]$. 
So, let's assume an arbitrary value function $v_0$ that initializes every state to an arbitray value execpt the terminal state, which must be 0. Now, applying the bellman equation to $v_0$, let the new value function be $v_1$ i.e. $v_1(s) = \sum_ap(a\mid s) \sum_{s',r}p(s',r\mid s,a)[r + \gamma \v_0{s'}]$. But, if $v_0$ was actually $v_\pi$ ,then $v_1(s)$ must be equal to $v_\pi(s)$. Continuing this equation, if $v_0$ is replaced by $v_1$ and the result is $v_2$, the same condition must hold. Hence, generalizing this update,
$$v_{k+1}(s) = \sum_a\p(a\mid s) \sum_{s',r}p(s',r\mid s,a)[r + \gamma \v_{k}{s'}]$$, as $k \rightarrow \infty$, $v_k(s) \rightarrow v_\pi(s)$

# Policy improvement
Policy improvement, as the name suggests, algorithmically tries to find a better policy given an arbitrary policy $\pi$ or as the book suggests, *would it be better or worse to change the policy at a given state $s$ following $\pi$.* This can be done using equation 2 from the bellman equations.

Policy improvement is defined as for two deterministic policies $\pi$ and $\pi^{'}$, $\pi^'$ is said to be better than $\pi$ if: $q(s, \pi^'(s)) \geq v_\pi(s)$. Therefore $v_{\pi^'}(s) \geq v_{\pi}(s)$. Similarly applying the bellman equation for policy update, we obtain: 
>$\pi^'(s) = argmax_a(\sum_{s',r}p(s',r\mid s, a)[r + \gamma v_\pi(s')])$

and therefore 

>$v_{\pi^'}(s) = max_a(\sum_{s',r}p(s',r\mid s,a)[r + \gamma v_{\pi^'}(s')])$

# Policy iteration
Now, given an arbitrary policy, we can now arrive at an approach to find a better policy by 
1. Evaluating the current policy(using Policy evaluation)
2. Improving the current policy(using Policy impprovement)

## Algorithm
1. Initialization $V(s) \in R$, $\pi(s) \in A(s),  \forall s \in S$
2. Policy evaluation
    * $\Delta \leftarrow 0$
    * for each $s \in S$:
        - $v \leftarrow V(s)$
        - $V(s) \leftarrow \sum_{s',r}p(s',r\mid s,\pi(s))[r + \gamma \V{s'}]$
        - $\Delta \leftarrow max(\Delta, \mid v - V(s) \mid)
    until  $\Delta < \theta$
3. Policy iteration
    * policy_stable $\leftarrow$ True
    * for each $s \in S$
        - old_action $\leftarrow \pi(s)$
        - $\pi^(s) = argmax_a(\sum_{s',r}p(s',r\mid s, a)[r + \gamma v_\pi(s')])$
        - if old_action != $\pi(s)$, then policy_stable $\leftarrow$ False.
    * if policy_stalbe == True, return $v_*$, $\pi_*$ 
4. Repeat

Problem: Each iteration requires evaluation and improvement, which is expensive since policy evaluation itself involves multiple iterations of sweeps through a state set. Hence, __value iteration__.

# Value Iteration
Value iteration truncates the policy evaluation part of the policy iteration algorithm. The idea is to solve the previous problem of multiple state visits in the evaluation stage. This is solved by maximizing the value function of a given state by visiting all posible actions and weighing each of their values through the transition probability, which in turn reduces the need for policy iteration. Hence, the algorithm now becomes:

## Algorithm
1. Initialization $V(s) \in R$, $\pi(s) \in A(s),  \forall s \in S$
2. Policy evaluation
    * $\Delta \leftarrow 0$
    * for each $s \in S$:
        - $v \leftarrow V(s)$
        - $V(s) \leftarrow \max_a \sum_{s',r}p(s',r\mid s,a)[r + \gamma V{s'}]$
        - $\Delta \leftarrow max(\Delta, \mid v - V(s) \mid)
    until  $\Delta < \theta$
    * $\pi(s) = argmax_a\sum_{s',r} p(s',r\mid s,a)[r + \gamma V(s')]
3. Repeat

This algorithm also converges to the ideal policy after a long time, but reduces the complexity in comparison to Policy iteration.