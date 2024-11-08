We want to thank the reviewer for the valuable comments and agreements on the theoretical analyses and experiments of this work. We will address each question in our response.

2. **Regarding SMACv2**

We greatly appreciate your suggestion to include experiments with SMACv2. It is an insightful recommendation, and we agree that adding such experiments would enhance the credibility of our results. However, due to the limited response window and space constraints, we regret that we are unable to include this experiment at this time. Nevertheless, we will certainly consider your suggestion and, if possible, incorporate the performance curves in future work.

3. **Regarding Parameter Settings**

The parameter settings for all baseline algorithms are available in the code under `src/config/algs`, ensuring transparency. Specifically, parameters such as parallel runners, buffer size, batch size, Adam optimizer, and TD-Lambda come from the PyMARL2 framework (Hu et al., 2021, reference [2] in the paper), and the corresponding code is available on GitHub or in our README. This framework fine-tunes many MARL algorithms, including all of our baselines, to maximize their performance. The reasonableness and consistency of these parameters are well-established.

Furthermore, we have made no changes to the baseline code or parameters in the PyMARL2 framework. Our implementation of POWQMIX follows the same consistency.

For clarity, the key hyperparameters are:

- `epsilon_start`: 1.0
- `epsilon_finish`: 0.05
- `epsilon_anneal_time`: 500,000
- `runner`: "parallel"
- `batch_size_run`: 8 (Note: we use 8 parallel runners for all algorithms, as using 1 runner significantly impacts performance)
- `buffer_size`: 5000
- `batch_size`: 128
- `optimizer`: 'adam'

For WQMIX-specific parameters:

- `alpha`: 0.1
- `weight`: 1

We assure you that **no parameters were adjusted to artificially lower performance**, and we guarantee the consistency and comparability of all algorithms.