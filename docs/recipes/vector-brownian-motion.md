# 05. Vector Brownian Motion

=== "Continuous-time"

    ## Mathematical Foundation

    A **Multidimensional or Vector Brownian Motion** $\mathbf{W}_t = (W_t^1, W_t^2, \dots, W_t^d)^T$ is a $d$-dimensional Gaussian process with correlated components. It is defined by the stochastic vector SDE:

    $$d\mathbf{W}_t = \mathbf{L} \, d\mathbf{Z}_t$$

    where:
    * $\mathbf{Z}_t = (Z_t^1, Z_t^2, \dots, Z_t^d)^T$ is a vector of $d$ independent standard 1D Wiener processes.
    * $\mathbf{L} \in \mathbb{R}^{d \times d}$ is the lower-triangular **Cholesky factor** of a symmetric positive-semidefinite covariance or correlation matrix $\mathbf{\Sigma} = \mathbf{L} \mathbf{L}^T$.

    ---

    ### Covariance Structure & Itô Isometry

    For component processes $W_t^i$ and $W_t^j$:

    1. **Expectation:** $\mathbb{E}[\mathbf{W}_t] = \mathbf{0}_{d \times 1}$
    2. **Instantaneous Cross-Covariance:** $d\langle W^i, W^j \rangle_t = \Sigma_{ij} \, dt$
    3. **Covariance Matrix at time $t$:** $\text{Cov}(\mathbf{W}_t) = t \, \mathbf{\Sigma}$

    ---

    ### Cholesky Factorization Algorithm

    Given a target correlation matrix $\mathbf{\Sigma}$, the elements $L_{i,j}$ ($j \le i$) are computed recursively:

    $$L_{i,j} = \begin{cases}     \sqrt{\Sigma_{i,i} - \sum_{k=1}^{j-1} L_{i,k}^2} & \text{if } i = j \\     \frac{1}{L_{j,j}} \left( \Sigma_{i,j} - \sum_{k=1}^{j-1} L_{i,k} L_{j,k} \right) & \text{if } i > j     \end{cases}$$

    ---

    ### Continuous-time Python Implementation

    Using the continuous Cholesky linear transformation $\mathbf{W}(t) = \mathbf{L} \mathbf{Z}(t)$, we generate continuous $d$-dimensional correlated trajectories:

    ```python
    import numpy as np

    def vector_brownian_continuous_path(Sigma, T=1.0, num_points=1000, seed=42):
        """
        Continuous-time evaluation of d-dimensional Vector Brownian Motion via Cholesky decomposition.
        
        Parameters:
        - Sigma: (d x d) symmetric positive-definite covariance/correlation matrix
        - T: Time horizon
        - num_points: Continuous time resolution
        """
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

    ### Exact Multivariate Gaussian Increments

    At each discrete time step $\Delta t$, the increment vector $\Delta \mathbf{W}_i = \mathbf{W}_{t_{i+1}} - \mathbf{W}_{t_i}$ is sampled directly from the multivariate normal distribution $\mathcal{N}(\mathbf{0}, \Delta t \, \mathbf{\Sigma})$:

    $$\mathbf{W}_{t_{i+1}} = \mathbf{W}_{t_i} + \sqrt{\Delta t} \, \mathbf{L} \, \mathbf{Z}_i, \quad \mathbf{Z}_i \sim \mathcal{N}(\mathbf{0}, \mathbf{I}_d)$$

    ```python
    import numpy as np

    def simulate_vector_brownian_discrete(Sigma, T=1.0, steps=200, paths=1, seed=42):
        """
        Simulates discrete-time multidimensional Vector Brownian Motion paths.
        
        Returns:
        - t: Time grid
        - W: Array of shape (paths, d, steps)
        """
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

=== "Simulation"

    <iframe src="/assets/simulations/vector_brownian.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>