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




## Weakness
### 1. novelty

事实上，本文的新颖性主要体现在两方面：1 是不需要遍历整个联合动作空间来找到最优联合动作。2 是有能成功收敛到全局最优解的理论保证，在具体实现中也不需要近似方法。接下来我详细说明一下。对于加权训练的框架来说，Qtot（存在单调性约束，服务于CTDE框架）和Q^*(无单调性约束，用于拟合真实联合动作价值函数)是必要的。在加权训练时，我们需要找到Q^ *的最优联合动作，提高这些动作的权重，这样训练Qtot能理论保证收敛到全局最优解，能还原Q *的策略，如CW-QMIX证明的那样。然而，Q^ *不具备单调性约束，找到最优联合动作理论上需要遍历整个联合动作空间。然而，在实际的实现当中，CW-QMIX方法采用了一种近似方法，而在这种近似之后则不再能严格支撑理论了。而我们的方法没有采取这种牺牲，而是引入了Qr模块（我们将在下一节详细叙述设计Qr的motivation），通过理论1来识别潜在最优联合动作，通过理论2来证明基于潜在最优联合动作而不是真实最优联合动作的加权方式也是能保障Qtot能收敛到最优解的。因此，我们既避免了遍历整个联合动作空间，也无需为了实际应用做出近似。就我们目前的认知来看，这种将潜在最优逐步收敛至全局最优的思想是新颖的，而且有拓展到其他领域的应用潜力。

### 2. motivation of Qr

关于Qr的motivation我们鼓励读者查阅Appendix C获得很详细的阐述，我们在这再简述一下。Qr的设计是motivated的以及对于识别潜在最优联合动作是至关重要的。其从形式上与QPLEX一致，但其用法却是大不相同。Qr设计用来拟合Q^ *以及识别潜在最优联合动作，与之相关的两条重要性质是Qr满足IGM条件以及每个联合动作都对应一个独立的Qr(s,a)。这能保证对于满足Qr(s,a)==Q(s,a^)的联合动作自然也是满足Q^ *(s,a) >= Q^ *(s,a^)的，而且argmaxQ^ *一定不会遗漏掉，正如Appendix B中理论1的证明叙述那样。这样定义的潜在联合动作集合保证了argmaxQ^ *不会漏掉同时集合内所有联合动作相比a^都是潜在上更优的。

另一个关键的细节是Qr与Qtot共享Qi作为输入，这意味着argmax Qr = argmax Qtot。如理论2的证明所述，Qr找到潜在联合动作集合，Qtot训练时增大这些联合动作权重，在一次迭代后argmax Qtot能获得更高的Q^ *值，由于Qr与Qtot共享Q^，潜在最优联合动作集合得到收缩，在不断的迭代训练之后，能收敛至argmax Qtot = argmax Q^ *。更进一步，根据CW-QMIX的证明，如果Qtot能还原Q^ *的最优联合动作，Q^ * 将逐步收敛为真实最优联合动作价值函数Q *，至此，Qtot也收敛到了全局最优解。我们也可以从Appendix C.3看到更直观的例子。

### 3. theoretical guarantee

上述回答已经表示POWQMIX有非常坚实的理论保障。相比而言，QPLEX虽然具有IGM-FREE这样优秀的性质，但却不具备能收敛至全局最优解的理论保障。一方面QPLEX的作者没有提出对应的理论证明，另一方面本文以及ResQ论文的矩阵游戏都表明了在非单调性较强的矩阵游戏中 比如[[1,2,3],]，QPLEX会陷入局部最优解。因为那些次优联合动作会阻碍QPLEX收敛到全局最优解。而我们的算法，如Appendix C.3所示，如果当前的a^是(C,C)，那么Qr可以识别出(A,A),(C,C)为潜在联合动作集合，对这两个联合动作加权，能稳定地引导Qtot收敛为argmax Qtot = (A,A)。

### 4. 参数选择

## Questions

