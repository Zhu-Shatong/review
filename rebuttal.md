# Review-6-4-(1)

**Review:**

This work introduces POWQMIX, a weighted training method for multi-agent reinforcement learning based on identifying potentially optimal joint actions. Using a novel $Q_r$ module, POWQMIX assigns weights to joint actions during training, focusing on those likely to be optimal. This approach allows POWQMIX to gradually converge to the optimal policy without exhaustive search or approximation. Experimental results across diverse environments demonstrate POWQMIX's robustness and effectiveness, especially in handling non-monotonic reward problems.

**The strengths of this work are summarized as follows.**

1. POWQMIX effectively addresses non-monotonic reward problems by focusing on potentially optimal joint actions.
2. This paper provides a series of theoretical proofs regarding convergence.
3. The ablation study in POWQMIX is well-designed.

**The drawbacks of this work are summarized as follows.**

1. The paper’s novelty is somewhat limited, as POWQMIX primarily builds on existing concepts from algorithms like WQMIX, QPLEX, and ResQ, combining and modifying them rather than introducing fundamentally new techniques.
2. The authors did not provide specific parameter settings for each baseline algorithm. In some cases, such as ResQ, the configurations differ from those in the original papers (for example, it uses 1 runner instead of 8 parallel runners, and different batch size), potentially impacting comparability. Please state clearly in the main text about the configuration.
3. POWQMIX introduces additional computational cost due to the extra $Q_r$ module.

**Questions:**

1. In the $Q_r$ network architecture, how does including the joint action  $\mathbf{a}$ as an input can avoid a monotonic relationship between $Q_r$ and $Q_i$? Please give a more detailed explanation.
2. As SMACv2 presents more complex challenges than SMAC, authors can add experiments in SMACv2 to make the results more convincing.
3. Please provide the specific configuration details for each baseline algorithm.
4. Could setting $\alpha=0$ during training hinder early exploration by not updating suboptimal actions?
5. There are some minor issues with the definition formula of $A_{tgm}$ in Theorem 1, please review it again.



## Questions



### 1 novelty           existing-concepts and combining

与icml相同，创新点的强调

wqmix：这个不可避免，因为我们就是在加权这一分支进行进一步优化

qplex：我们使用了优势函数以满足igm条件，我们的思路也确实受到了qpex的启发。但是我们的思路是有迹可循的，不是进行强行融合。我们看中了加入a来break free，这在qplex中没有证明，但是我们证明了break free，这也是Qr组件的三大关键条件之一；qplex也不是加权方法，不稳定，我们加权是稳定的收敛过程，即使被认为是融合，我们也分析出了其不稳定的原因并解决了，性能优于qplex。



### 2 parameter setting & configuration details       1 state clearly      2 reason

说清楚参数，应该不难

解释为什么要这么设置，需要理由



### 3 large network and cost

不提了



### 4 explanation of a, how to break away from the mono constrain

可以解释，（是否有必要强调 qplex是无意识地加入a的，也没有将性能提升归功于a，也没有证明；我们意识到了）



### 5 smacv2



### 6 是否会因为: 不更新次优动作 -> 阻碍早期探索

理论层面说一说

同时，可以看ablation；

在非单调的predator，是这样的，我们可以理解为sparse；

在复杂环境，反而学得慢



### 7 typo 确实

定义句写错了，不是集合，intro里面是对的



# Review-4-5

**Review:**

**Summary:** The paper proposes a new weighting scheme to promote the learning of the optimal joint actions in the value-decomposition framework. With this new weighting, based on an optimal joint actions recovering module, the proposed algorithm is capable of retaining the IGM property on the learned representation, while also overcoming the limitations imposed by the monotonicity constraint of QMIX and achieving TGM. Experimental results show how the method can really overcome non-monotonicity in practise, and outperform existing baselines.

**Strengths:** In my opinion, the main strength of this paper lies in its idea of overcoming the difficulties in weighting encountered by CW-QMIX, which in theory relies on the full knowledge of which are the true optimal joint actions, but in practise has to make approximations that can degrade its performance. The idea of having a module to recognize potentially optimal actions, so to give more weight to them, is interesting. Additionally, the paper is quite clear and easy to read, and the experimental methodology is sound (although I have some concerns on the analysis of the results and their significance, please see my Questions below).

**Weaknesses:** Probably, the main criticism of this paper is the way in which it tries to achieve the aforementioned benefits. The idea of simply learning another Q-value representation and using it to do a very similar weighting to CW-QMIX is a bit incremental, and does **not really add enough** to the existing literature in my opinion. Moreover, the experimental results of the ablation studies seem to point out that, with careful utilisation of (W)QMIX, similar performance levels can be achieved, which makes me question the utility of the current proposal from an empirical perspective as well.

**Questions:**

- In Definition 1, why do we now have the $Q_i$s to condition on the observations $o_i$ and not the individual histories $\tau_i$ as usual? This also appears at times later in the method presentation.  (maybe typo)
- In Table 1, why are algorithms named POWOMIX and CW-OMIX in captions (b) and (f) respectively? Also, why there are two $Q_{tot}$ in caption (f)?     (typo)
- In Equation (10) (and its practical version Equation (11)), you are setting the weights $w(s,\mathbf{a})$ to be 1 for the optimal action and $\alpha\in[0,1)$ for the non-optimal ones. But then, in Theorem 2, you are basically restricting yourself to have $\alpha=0$. 
- Moreover, in your experiments, you use the value of 5 instead of 1 for the optimal actions. Why such a discrepancy between the methodological explanation and the actual instantiation (or the theoretically sound version) of your method?     (same as icml)
- The results of your ablation studies does not seem very convincing: indeed, from Figure 6(a) and 6(b) we can see how both variants of WQMIX, given the same number of parameters, can easily **outperform your algorithm** on the two non-monotonic predator-prey instances, and **on the SMAC map QMIX is almost matching with that**. This really makes me question if it not actually a matter of having more parameters (or at least, of a careful tuning of their number) that made your algorithm perform the best in the previously shown experiments. Similar considerations can be made for the weighting comparison, where CW-QMIX-5 is outperforming your algorithm on the two predator-prey instances, and QMIX is doing do on SMAC. Your comments to the results in this sub-section also do not help in this, as these mainly refer to things shown in the "full capacity" experiments to discuss the validity of your POWQMIX, rather than in the ablated experiments themselves...





### 1 typo

诚恳道歉



### 2 0~1 变成 1

0~1是weighted框架的条件，我们可以在理论层面讨论时，肯定是不能武断（我们在ablation 尝试了非0情况）；

但是，我们的powqmix，就是基于alpha=0；在network阶段，以及我们的理论，都说明了，只有alpha=0才能得正



### 3 weight5的问题

同上



### 4 ablation

还要怎么说

在两个场景都可以发挥大最大

其他的 W和Q 此消彼长，一个收敛快一点，在另一个就几乎学不到













# Review-5-4

**Review:**

**Strengths:**

- Extending value decomposition algorithms to handle non-monotonic tasks is a valuable topic.
- The paper includes thorough theoretical analyses and proofs.
- Several experiments and ablation studies are presented to showcase the effectiveness of the proposed method.

**Weaknesses:**

- The novelty of the work is limited. The idea of assigning higher weights to optimal actions is not new. And the use of WQMIX with QPLEX to identify optimal actions seems not novel.
- The motivation behind the use of the $Q_r$ structure is unclear. For readers unfamiliar with QPLEX, it is difficult to grasp the reasoning behind this design choice and understand its properties.
- A key concern is the heavy reliance on QPLEX's ability to correctly identify optimal actions. As such, I am not convinced that POWQMIX offers better theoretical guarantees than QPLEX. While POWQMIX performs better in some tasks where QPLEX fails, it is crucial to explicitly discuss the differences and provide theoretical insights, as this seems to be central to POWQMIX’s performance. The authors mention that QPLEX may be **unstable** during training, but this explanation lacks clarity and depth.
- The selection of hyperparameters, such as the constant C and the weights assigned to optimal and non-optimal actions, appears heuristic and would benefit from further justification.

**Questions:**

1. Does QPLEX's failure to identify the optimal action in the matrix game result from instability? How does the $Q_r$ structure in POWQMIX successfully represent all joint actions, while QPLEX does not?
2. Are there any theoretical results showing that the $Q_r$ module in POWQMIX offers better guarantees than QPLEX, or that it avoids failures in cases where QPLEX struggles?



## Questions



### 1 novelty

还是 w ，我们就是解决weighted问题的

qplex



### 2 超参数的选择

C：你说的对 需要增加实验（我个人认为现在的C肯定不是最优的，能进一步压榨性能，或许对其他reviewer 也有帮助）

weight：可以理解为 先做了ablation再进行了实验，我们的pow更适合5-0，所以我们进行了实验（也就是我们默认原生的powqmix就用5）。其他baseline使用了weight1-0是因为我们尊重原文的实现；为了展示与调研“5”的具体作用，我们也进行了ablation，均可以进行比较



# Review-6-4-(2)

**Review:**

The paper is generally well structured logically, with a clear introduction, method description, experiments, and conclusion sections. The use of mathematical notations and theorems aids in precisely describing the proposed POWQMIX algorithm.

The experiment parts are concrete and solid. From the theoretical perspective, the paper relies on assumptions such as the convergence of $Q_r$ and $Q_{tot}$ to derive its theoretical findings regarding the optimality of these converged points. 

Essentially, the approach **utilizes QPLEX to identify optimal actions** and adjusts the weighting of these actions to train WQMIX. Therefore, it seems unnecessary if QPLEX can effectively identify optimal actions, including the WQMIX component. Although it is possible that the method could still identify optimal actions even when QPLEX fails to do so, it remains unclear whether this outcome is sporadic or has a theoretical foundation.



一个关于方法设计的疑问：如果 QPLEX 已经能够有效识别最优动作（并且这其中也包括了 WQMIX 组件），那么在这种情况下，为何还要调整这些最优动作的权重来训练 WQMIX？这样做可能是不必要的，假如 QPLEX 的作用已经足够。