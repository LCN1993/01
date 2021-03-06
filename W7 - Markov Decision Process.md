# MDP, Markov Decision Process

https://zhuanlan.zhihu.com/p/25498081
## Classical planning
+ **deterministic action** -> **non-deterministic (but with probability)**
+ static environment
    * Environments change only as the result of an action
+ perfect knowledge (full observability)
    * omniscience
+ single actor
    * omnipotence

## Markov Decision Process (MDP) 

+ 马尔可夫模型的几类子模型

| | 不考虑动作| 考虑动作|
|--- |----|----|
| 状态完全可见	|马尔科夫链(MC)|	马尔可夫决策过程(MDP)| 
| 状态不完全可见	| 隐马尔可夫模型(HMM)	| 不完全可观察马尔可夫决策过程(POMDP)|


+ The most common formulation of MDPs is a Discounted-Reward Markov Decision Process. Optimal solutions maximise the expected discounted __accumulated__ reward from the initial state _s_0_.

+ transition probability
+ policy, π
    * mapping from states to actions
    * 不像 classical 里面返回 action sequence, policy 对于每一个 state 都返回一个 action
        - 因为 non-deterministic
        - classical 算 heuristic 的话，不用全 states 都算，MDP 要全算但算好了就有所有 states 的
    * a plan is actually a policy
        - it is just that it is defined only for the states along which the plan is derived. E.g. given the plan "s --a--> s' --b--> s'' --c --> s''', it can be represented as the policy:
            ```
            s -> a
            s' -> b
            s'' -> c
            ```
+ reward `r(s, a, s_0)`
    * There are no action costs. These are modelled as negative rewards.
    * There are no goals. Each action receives a reward when applied. The value of the reward is dependent on the state in which it is applied.
+ a discount factor (can be iteratively defined)
    * `0 ≤ γ ≤ 1`
+ how to solve MDP
    * two common ways
        - both based on dynamic programming
            + value iteration
            + policy iteration
    
### Value Iteration
* V(s)
    - Bellman equation
        ```
        max(a in actions)
            (sum(all states)
                (P(s,a,s')*(reward(s,a,s')+γV(s'))))
        ```
    - processes:
      - 1) set v(s) as 0 for every state
      - 2) loop the Bellman equation for calculating the max one 
    - = `max(a in actions)(Q(s, a))`
        + Q-value: `Q(s, a)`
            + .
                ```
                sum(all states)
                    (P(s,a,s')*(reward(s,a,s')+γV(s')))
                ```
            * 针对不同的 action
            * 一个 action 可能去到不同的 state
                - P, reward 都要针对 state+action 才行
                - 所以 `sum(all states)`
        + converges exponentially fast to the optimal policy
            * proven
            * can be easily parallelised
                -  the values of states at step _t+1_ are dependent only on the value of other states at step _t_.
    - policy extraction
        + `argmax`

### Policy Iteration
+ need to calculates the value of each state of the MDP given that policy
    * policy evaluation
+ easier to compute than value iteration
    * the set of actions to consider is fixed by the policy that we have so far
* `V^{π}(s)`
    - expected discounted __accumulated__ reward/cost of following the policy π from _s_ to goal
    - weighted avg of cost of the possible state sequences 
* `Q^{π}(s,a)`
* finite number of iterations
    - unlike value iteration
    - because the number of policies is finite
        + `O(|A|^|S|)`

### polynomial state space size
* but exponential number of variables if using PDDL-like language
    - Solving MDPs with (admissible) Heuristic Search
        + ~~why think of it~~
            * ~~A* and IDA* manage to optimally solve problems with more than 10^20 states~~
            * ~~Often the set of states __reachable__ from _s_0_ using the optimal policy is much small that the set of total states~~
        + using the value function V as the heuristic if it is admissible
        + work only from the known initial state _s_0_ or any state reachable from _s_0_
        + Value/Policy Iteration vs Heuristic Search
            * ![iteration.vs.heuristic](pics/iteration.vs.heuristic.png)
            * value/policy iteration
                - solve exhaustively once
                    + more expensive
                    + can use the resulting policy many times for all states
            *  heuristic search
                - solve __online__ each time we encounter a __new__ state (not considered before)

## Partially-observable MDPs, POMDPs
+ MDPs assume that the agent always knows exactly what state it is in
    * not valid for many tasks
        - ~~an unmanned aerial vehicle searching in a earthquake zone for survivors~~
        - ~~a card-player agent will not know the cards its opponent holds~~
+ Partially-observable MDPs (POMDPs) relax the assumption of full-observability
    * initial belief state `b_0`
    * a sensor model given by probabilities `P_a(o|s), o ∈ Obs`
+ how to solve
    * same techs/algos as MDPs, but over a larger search space
        - case the POMDP problem as a standard MDP problem with a new state space
            + each state is a probability distribution over the set _S_. Thus, each state of the POMDP is a belief state, which defined the probability of being in each state S.
