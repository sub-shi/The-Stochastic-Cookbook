# 05. Vector Brownian Motion

=== "Continuous-time"

    ## Mathematical Foundation

    A **Multidimensional or Vector Brownian Motion** $\mathbf{W}_t = (W_t^1, W_t^2, \dots, W_t^d)^T$ is a $d$-dimensional Gaussian process with correlated components. It is defined by the stochastic vector SDE:

    $$d\mathbf{W}_t = \mathbf{L} \, d\mathbf{Z}_t$$

    where:
    * $\mathbf{Z}_t = (Z_t^1, Z_t^2, \dots, Z_t^d)^T$ is a vector of $d$ independent standard 1D Wiener processes.
    * $\mathbf{L} \in \mathbb{R}^{d \times d}$ is the lower-triangular **Cholesky factor** of a symmetric positive-semidefinite covariance or correlation matrix $\mathbf{\Sigma} = \mathbf{L} \mathbf{L}^T$.

    ---

    ### Continuous Trajectory Concept

    In continuous time, Vector Brownian Motion models a spatial trajectory where component Wiener paths $W_t^i$ move in a correlated coordinate space. The lower-triangular structure of the Cholesky matrix $\mathbf{L}$ filters independent Gaussian white noise $\mathbf{Z}_t$ into instantaneous cross-asset covariance structures $\mathbf{\Sigma}$.

    ---

    ### Covariance Structure & Itô Isometry

    For component processes $W_t^i$ and $W_t^j$:

    1. **Expectation:** $\mathbb{E}[\mathbf{W}_t] = \mathbf{0}_{d \times 1}$
    2. **Instantaneous Cross-Covariance:** $d\langle W^i, W^j \rangle_t = \Sigma_{ij} \, dt$
    3. **Covariance Matrix at time $t$:** $\text{Cov}(\mathbf{W}_t) = t \, \mathbf{\Sigma}$

    ---

    ### Cholesky Factorization Algorithm

    Given a target correlation matrix $\mathbf{\Sigma}$, the elements $L_{i,j}$ ($j \le i$) are computed recursively:

    $$L_{i,j} = \begin{cases}      \sqrt{\Sigma_{i,i} - \sum_{k=1}^{j-1} L_{i,k}^2} & \text{if } i = j \\      \frac{1}{L_{j,j}} \left( \Sigma_{i,j} - \sum_{k=1}^{j-1} L_{i,k} L_{j,k} \right) & \text{if } i > j      \end{cases}$$

    ---

    ### Continuous-time Python Implementation

    Using the continuous Cholesky linear transformation $\mathbf{W}(t) = \mathbf{L} \mathbf{Z}(t)$, we generate continuous $d$-dimensional correlated trajectories:

    ```python
    import numpy as np

    def vector_brownian_continuous_path(Sigma, T=1.0, num_points=1000, seed=42):
        if seed is not None:
            np.random.seed(seed)

        d = Sigma.shape[0]
        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)

        # 1. Compute lower triangular Cholesky factor L
        L = np.linalg.cholesky(Sigma)

        # 2. Generate independent standard Brownian increments dZ
        dZ = np.random.normal(0, np.sqrt(dt), size=(d, num_points - 1))
        
        # 3. Apply Cholesky transformation: dW = L @ dZ
        dW = L @ dZ

        # 4. Integrate increments over time
        W = np.zeros((d, num_points))
        W[:, 1:] = np.cumsum(dW, axis=1)

        return t, W
    ```

=== "Discrete-time"

    ## Discrete-time Recipes for Vector Brownian Motion

    To simulate a $d$-dimensional correlated Gaussian process on a discrete time grid $t_0 < t_1 < \dots < t_N$ with step size $\Delta t = \frac{T}{N}$, two discrete numerical formulations are available.

    ---

    ### Method 1: Discrete Exact Multivariate Gaussian Increments

    #### Short Explanation
    Method 1 samples discrete step updates $\mathbf{\Delta W}_i = \mathbf{W}_{t_{i+1}} - \mathbf{W}_{t_i}$ directly from the exact multivariate normal distribution $\mathcal{N}_d(\mathbf{0}, \Delta t \, \mathbf{\Sigma})$ using static Cholesky matrix decomposition.

    * **Zero Discretization Error ($\mathcal{O}(0)$):** Preserves the exact cross-asset covariance matrix $\mathbf{\Sigma}$ over all step sizes $\Delta t$.
    * **Analytical Exactness:** Injects spatial asset correlations directly into discrete log-returns without time-step truncation error.

    $$\mathbf{W}_{t_{i+1}} = \mathbf{W}_{t_i} + \sqrt{\Delta t} \, \mathbf{L} \, \mathbf{Z}_i, \quad \mathbf{Z}_i \sim \mathcal{N}(\mathbf{0}, \mathbf{I}_d)$$

    ```python
    import numpy as np

    def simulate_vector_brownian_discrete(Sigma, T=1.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)

        d = Sigma.shape[0]
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)

        # Lower-triangular Cholesky factor
        L = np.linalg.cholesky(Sigma)
        
        W = np.zeros((paths, d, steps))

        for p in range(paths):
            # Independent normal random matrix (d x steps-1)
            Z = np.random.normal(0, 1, (d, steps - 1))
            
            # Correlated increments
            dW = np.sqrt(dt) * (L @ Z)
            
            # Path integration
            W[p, :, 1:] = np.cumsum(dW, axis=1)

        return t, W
    ```

    ---

    ### Method 2: Discrete Vector Euler–Maruyama Scheme

    #### Short Explanation
    Method 2 applies a first-order vector finite-difference expansion to the multivariate asset system SDE $\mathbf{dS}_t = \boldsymbol{\mu} \odot \mathbf{S}_t dt + \boldsymbol{\sigma} \odot \mathbf{S}_t \odot d\mathbf{W}_t$.

    * **Order 0.5 Strong Error:** Linear multiplicative step update accumulating discretization bias over coarse grids.
    * **Boundary Risk:** Requires truncation handling for extreme negative draws if applied to raw price levels rather than log-transformed coordinates.

    $$\mathbf{S}_{t_{i+1}} = \mathbf{S}_{t_i} \odot \left( \mathbf{1} + \boldsymbol{\mu} \Delta t + \boldsymbol{\sigma} \sqrt{\Delta t} \odot (\mathbf{L} \mathbf{Z}_i) \right), \quad \mathbf{Z}_i \sim \mathcal{N}_d(\mathbf{0}, \mathbf{I}_d)$$

    ```python
    import numpy as np

    def simulate_vector_gbm_euler(S0_vec, mu_vec, sigma_vec, Sigma_corr, T=1.0, steps=200, seed=42):
        if seed is not None:
            np.random.seed(seed)

        d = len(S0_vec)
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        L = np.linalg.cholesky(Sigma_corr)

        S = np.zeros((d, steps))
        S[:, 0] = S0_vec

        for i in range(1, steps):
            Z = np.random.normal(0, 1, d)
            dW = np.sqrt(dt) * (L @ Z)
            S[:, i] = S[:, i - 1] + mu_vec * S[:, i - 1] * dt + sigma_vec * S[:, i - 1] * dW
            S[:, i] = np.maximum(S[:, i], 1e-8)  # Boundary truncation floor

        return t, S
    ```

=== "Simulation"

    <iframe src="/assets/simulations/vector_brownian.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>