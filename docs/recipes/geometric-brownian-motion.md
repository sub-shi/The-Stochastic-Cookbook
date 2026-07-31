# 01. Geometric Brownian Motion

=== "Continuous-time"

    ## Mathematical Foundation

    A **Geometric Brownian Motion (GBM)** is a continuous-time stochastic process $(S_t)_{t \ge 0}$ defined by the stochastic differential equation (SDE):

    $$dS_t = \mu S_t dt + \sigma S_t dW_t$$

    where:
    * $\mu \in \mathbb{R}$ is the constant percentage drift rate (expected return).
    * $\sigma > 0$ is the constant percentage volatility coefficient.
    * $W_t$ is a standard Wiener process (Brownian motion).

    ---

    ### Continuous Trajectory Concept

    In continuous time, the sample path $S(t)$ represents an uninterrupted, non-differentiable trajectory across time $t \in [0, T]$. Because the stochastic driver $dW_t$ has non-zero quadratic variation ($(dW_t)^2 = dt$), ordinary calculus fails, requiring **Itô's Calculus** to solve the SDE.

    Applying Itô's Lemma to the log-transformation $f(S_t) = \ln(S_t)$ removes state dependence, yielding the exact closed-form continuous-time trajectory solution:

    $$S(t) = S_0 \exp \left( \left( \mu - \frac{1}{2}\sigma^2 \right) t + \sigma W(t) \right)$$

    The term $-\frac{1}{2}\sigma^2$ is the **Itô convexity correction**, ensuring that the expected asset value satisfies $\mathbb{E}[S(t)] = S_0 e^{\mu t}$.

    ---

    ### Continuous-time Python Implementation

    Using the exact analytical Itô solution, we evaluate continuous time trajectories $S(t)$ driven directly by continuous Brownian motion $W(t)$ without numerical discretization error:

    ```python
    import numpy as np

    def gbm_continuous_trajectory(S0=100.0, mu=0.05, sigma=0.20, T=1.0, num_points=1000, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)

        # Continuous time vector grid
        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)

        # Generate continuous Brownian Motion path W(t)
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

    To simulate Geometric Brownian Motion on a computer, the continuous time horizon $T$ is divided into a discrete grid $t_0 < t_1 < \dots < t_N$ with uniform step size $\Delta t = \frac{T}{N}$. Discrete updates transform infinitesimal Brownian differentials $dW_t$ into finite Gaussian increments $\Delta W_i \sim \mathcal{N}(0, \Delta t) \stackrel{d}{=} \sqrt{\Delta t} Z_i$.

    ---

    ### Method 1: Discrete Exact Exponential Sampling

    #### Short Explanation
    Method 1 samples discrete step-to-step state transitions $S_{t_{i+1}}$ directly from the process's true continuous log-normal transition density derived via Itô's Lemma.

    * **Zero Discretization Error ($\mathcal{O}(0)$):** Because it evaluates the exact log-return distribution over $\Delta t$, path expectations and moments are exact regardless of step size $\Delta t$.
    * **Boundary Safety:** Exponentiation guarantees strict positivity ($S_{t_{i+1}} > 0$) almost surely across all time steps, preventing non-physical negative prices.

    $$\ln\left(\frac{S_{t_{i+1}}}{S_{t_i}}\right) \sim \mathcal{N}\left( \left(\mu - \frac{1}{2}\sigma^2\right)\Delta t, \, \sigma^2 \Delta t \right)$$

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

    ### Method 2: Discrete Euler–Maruyama Discretization Scheme

    #### Short Explanation
    Method 2 is a first-order numerical finite-difference approximation that freezes the drift and diffusion coefficients over each step $\Delta t$, replacing differential $dS_t$ with finite step difference $\Delta S_i = S_{t_{i+1}} - S_{t_i}$.

    * **Order 0.5 Strong Error:** Accumulates discretization error over long time horizons unless $\Delta t \to 0$.
    * **Boundary Risk:** Uses linear addition ($S_{t_{i+1}} = S_{t_i}(1 + \dots)$) instead of exponentiation. Large negative random draws $Z_i < -\frac{1 + \mu \Delta t}{\sigma \sqrt{\Delta t}}$ can cause asset prices to cross zero into non-physical negative values ($S_{t_{i+1}} < 0$).

    $$S_{t_{i+1}} = S_{t_i} + \mu S_{t_i} \Delta t + \sigma S_{t_i} \sqrt{\Delta t} \, Z_i, \quad Z_i \sim \mathcal{N}(0, 1)$$

    ```python
    import numpy as np

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
            S[:, i] = np.maximum(S[:, i], 1e-8)  # Truncation boundary safety
            
        return t, S
    ```

=== "Simulation"

    <iframe src="/assets/simulations/gbm.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>