# 01. Geometric Brownian Motion

=== "Continuous-time"

    ## Mathematical Foundation

    A **Geometric Brownian Motion (GBM)** is a continuous-time stochastic process $(S_t)_{t \ge 0}$ defined by the stochastic differential equation (SDE):

    $$dS_t = \mu S_t dt + \sigma S_t dW_t$$

    where:
    * $\mu \in \mathbb{R}$ is the constant drift rate.
    * $\sigma > 0$ is the constant volatility coefficient.
    * $W_t$ is a standard Wiener process.

    ---

    ### Analytical Solution via Itô's Lemma

    Applying Itô's Lemma to $f(S_t) = \ln(S_t)$ yields the exact closed-form continuous-time solution:

    $$S_t = S_0 \exp \left( \left( \mu - \frac{1}{2}\sigma^2 \right) t + \sigma W_t \right)$$

    ---

    ### Continuous-time Python Implementation

    Using the exact analytical Itô solution, we evaluate continuous time paths $S_t$ driven directly by continuous Brownian motion $W_t$ without numerical discretization error:

    ```python
    import numpy as np

    def gbm_continuous_path(S0=100.0, mu=0.05, sigma=0.20, T=1.0, num_points=1000, paths=1, seed=42):
        """
        Continuous-time evaluation of Geometric Brownian Motion using its exact closed-form solution:
        S(t) = S0 * exp((mu - 0.5 * sigma^2) * t + sigma * W(t))
        """
        if seed is not None:
            np.random.seed(seed)

        # Continuous time vector
        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)

        # Generate continuous Brownian Motion W(t)
        dW = np.random.normal(0, np.sqrt(dt), size=(paths, num_points - 1))
        W = np.zeros((paths, num_points))
        W[:, 1:] = np.cumsum(dW, axis=1)  # W(t) path construction

        # Evaluate exact continuous solution S(t)
        drift = (mu - 0.5 * sigma**2) * t
        diffusion = sigma * W
        
        S = S0 * np.exp(drift + diffusion)
        return t, S
    ```

=== "Discrete-time"

    ## Discrete-time Recipes for GBM

    ### Method 1: Discrete Exact Transition Sampling

    Sampling discrete step-to-step transitions $S_{t_{i+1}}$ over time step $\Delta t$:

    $$S_{t_{i+1}} = S_{t_i} \exp \left( \left( \mu - \frac{1}{2}\sigma^2 \right) \Delta t + \sigma \sqrt{\Delta t} \, Z_i \right), \quad Z_i \sim \mathcal{N}(0, 1)$$

    ```python
    import numpy as np

    def simulate_gbm_discrete_exact(S0=100.0, mu=0.05, sigma=0.20, T=1.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        
        Z = np.random.normal(0, 1, (paths, steps - 1))
        log_returns = (mu - 0.5 * sigma**2) * dt + sigma * np.sqrt(dt) * Z
        
        log_paths = np.zeros((paths, steps))
        log_paths[:, 1:] = np.cumsum(log_returns, axis=1)
        
        return t, S0 * np.exp(log_paths)
    ```

    ---

    ### Method 2: Euler-Maruyama Discretization Scheme

    First-order numerical approximation of the differential equation:

    $$S_{t_{i+1}} = S_{t_i} + \mu S_{t_i} \Delta t + \sigma S_{t_i} \sqrt{\Delta t} \, Z_i$$

    ```python
    def simulate_gbm_euler(S0=100.0, mu=0.05, sigma=0.20, T=1.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        S = np.zeros((paths, steps))
        S[:, 0] = S0
        
        for i in range(1, steps):
            Z = np.random.normal(0, 1, paths)
            S[:, i] = S[:, i - 1] + mu * S[:, i - 1] * dt + sigma * S[:, i - 1] * np.sqrt(dt) * Z
            S[:, i] = np.maximum(S[:, i], 1e-8)
            
        return t, S
    ```

=== "Simulation"

    <iframe src="/assets/simulations/gbm.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>