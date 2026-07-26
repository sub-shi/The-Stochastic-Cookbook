# 03. Cox-Ingersoll-Ross Process

=== "Continuous-time"

    ## Mathematical Foundation

    The **Cox-Ingersoll-Ross (CIR) process** $(X_t)_{t \ge 0}$ is a continuous-time stochastic process widely used in short-rate modeling and stochastic volatility (e.g., Heston model). It is defined by the stochastic differential equation (SDE):

    $$dX_t = \kappa (\theta - X_t) dt + \sigma \sqrt{X_t} dW_t$$

    where:
    * $\kappa > 0$ is the rate of mean reversion.
    * $\theta > 0$ is the long-term mean level.
    * $\sigma > 0$ is the volatility coefficient.
    * $W_t$ is a standard Wiener process.

    ---

    ### The Feller Condition

    A key property of the CIR process is that $X_t$ cannot drop below zero. Strictly positive trajectories are guaranteed if the **Feller Condition** holds:

    $$2\kappa\theta > \sigma^2$$

    * **If $2\kappa\theta > \sigma^2$:** The origin $X = 0$ is inaccessible; the process remains strictly positive ($X_t > 0$ almost surely).
    * **If $2\kappa\theta \le \sigma^2$:** The process can touch zero, but it is immediately reflected back into the positive domain.

    ---

    ### Statistical Properties

    1. **Expectation:** $\mathbb{E}[X_t \mid X_0] = \theta + (X_0 - \theta) e^{-\kappa t}$
    2. **Variance:** $\text{Var}(X_t \mid X_0) = X_0 \frac{\sigma^2}{\kappa} \left( e^{-\kappa t} - e^{-2\kappa t} \right) + \theta \frac{\sigma^2}{2\kappa} \left( 1 - e^{-\kappa t} \right)^2$
    3. **Exact Distribution:** Conditional on $X_s$, $c \cdot X_t$ (where $c = \frac{4\kappa}{\sigma^2(1 - e^{-\kappa(t-s)})}$) follows a **non-central chi-squared distribution** $\chi^2(d, \lambda)$ with:
       * Degrees of freedom: $d = \frac{4\kappa\theta}{\sigma^2}$
       * Non-centrality parameter: $\lambda = c \cdot X_s e^{-\kappa(t-s)}$

    ---

    ### Continuous-time Python Implementation

    Evaluating continuous-time CIR paths via exact non-central chi-squared transition integrals:

    ```python
    import numpy as np
    from scipy.stats import ncx2

    def cir_continuous_path(X0=0.05, kappa=2.0, theta=0.05, sigma=0.15, T=3.0, num_points=1000, paths=1, seed=42):
        """
        Evaluates continuous CIR process trajectories using non-central chi-squared transition distributions.
        """
        if seed is not None:
            np.random.seed(seed)

        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)
        
        d = (4 * kappa * theta) / (sigma**2)
        c = (sigma**2 * (1 - np.exp(-kappa * dt))) / (4 * kappa)
        
        X = np.zeros((paths, num_points))
        X[:, 0] = X0
        
        for i in range(1, num_points):
            ncp = (4 * kappa * X[:, i - 1] * np.exp(-kappa * dt)) / (sigma**2 * (1 - np.exp(-kappa * dt)))
            # Sample exact transition from non-central chi-squared
            X[:, i] = c * np.random.noncentral_chisquare(df=d, nonc=ncp, size=paths)
            
        return t, X
    ```

=== "Discrete-time"

    ## Discrete-time Recipes for CIR Process

    ### Method 1: Exact Non-Central Chi-Squared Sampling (Recommended)

    Samples the exact transition distribution without time discretization bias:

    ```python
    import numpy as np

    def simulate_cir_exact(X0=0.05, kappa=2.0, theta=0.05, sigma=0.15, T=3.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        
        df = (4 * kappa * theta) / (sigma**2)
        c = (sigma**2 * (1 - np.exp(-kappa * dt))) / (4 * kappa)
        
        X = np.zeros((paths, steps))
        X[:, 0] = X0
        
        for i in range(1, steps):
            ncp = (4 * kappa * X[:, i - 1] * np.exp(-kappa * dt)) / (sigma**2 * (1 - np.exp(-kappa * dt)))
            X[:, i] = c * np.random.noncentral_chisquare(df=df, nonc=ncp, size=paths)
            
        return t, X
    ```

    ---

    ### Method 2: Full Truncation Euler-Maruyama Scheme

    Standard Euler schemes fail for square-root diffusions when $X_t < 0$. The **Full Truncation** variant maintains numerical stability by truncating inside the square root:

    $$X_{t_{i+1}} = X_{t_i} + \kappa (\theta - X_{t_i}^+) \Delta t + \sigma \sqrt{X_{t_i}^+} \sqrt{\Delta t} \, Z_i, \quad \text{where } X^+ = \max(X, 0)$$

    ```python
    def simulate_cir_truncated_euler(X0=0.05, kappa=2.0, theta=0.05, sigma=0.15, T=3.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        
        X = np.zeros((paths, steps))
        X[:, 0] = X0
        
        for i in range(1, steps):
            Z = np.random.normal(0, 1, paths)
            X_pos = np.maximum(X[:, i - 1], 0.0)
            X[:, i] = X[:, i - 1] + kappa * (theta - X_pos) * dt + sigma * np.sqrt(X_pos) * np.sqrt(dt) * Z
            X[:, i] = np.maximum(X[:, i], 0.0)  # Floor step
            
        return t, X
    ```

=== "Simulation"

    <iframe src="/assets/simulations/cir_process.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>