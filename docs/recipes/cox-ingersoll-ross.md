# 02. Ornstein-Uhlenbeck Process

=== "Continuous-time"

    ## Mathematical Foundation

    The **Ornstein-Uhlenbeck (OU) process** $(X_t)_{t \ge 0}$ is a continuous-time Gaussian process that exhibits mean-reverting dynamics. It is defined by the stochastic differential equation (SDE):

    $$dX_t = \theta (\mu - X_t) dt + \sigma dW_t$$

    where:
    * $\theta > 0$ is the mean-reversion speed (stiffness).
    * $\mu \in \mathbb{R}$ is the long-term structural equilibrium mean.
    * $\sigma > 0$ is the volatility coefficient.
    * $W_t$ is a standard Wiener process.

    ---

    ### Continuous Trajectory Concept

    In continuous time, the trajectory $X(t)$ represents an uninterrupted Gaussian path relaxing toward equilibrium mean $\mu$. Because the deterministic drift $\theta(\mu - X_t)$ continuously pulls the state back toward $\mu$, the variance remains bounded over time, unlike standard Brownian motion.

    Using the integrating factor $e^{\theta t}$, we integrate the SDE directly to obtain the **exact continuous-time solution**:

    $$X(t) = \mu + (X_0 - \mu) e^{-\theta t} + \sigma \int_0^t e^{-\theta (t - s)} dW_s$$

    The stochastic integral term represents exponentially dampened Gaussian noise accumulated over time.

    ---

    ### Statistical Properties

    1. **Conditional Expectation:** $\mathbb{E}[X(t) \mid X_0] = \mu + (X_0 - \mu) e^{-\theta t}$
    2. **Conditional Variance:** $\text{Var}(X(t) \mid X_0) = \frac{\sigma^2}{2\theta} \left( 1 - e^{-2\theta t} \right)$
    3. **Covariance:** $\text{Cov}(X(s), X(t)) = \frac{\sigma^2}{2\theta} \left( e^{-\theta \vert{}t - s\vert{}} - e^{-\theta (t + s)} \right)$
    4. **Stationary Invariant Distribution:** As $t \to \infty$, $X(t) \sim \mathcal{N}\left(\mu, \frac{\sigma^2}{2\theta}\right)$

    ---

    ### Continuous-time Python Implementation

    Using the exact stochastic integral formulation, we evaluate continuous-time OU paths driven directly by continuous Brownian motion $W_t$:

    ```python
    import numpy as np

    def ou_continuous_path(X0=0.0, theta=2.0, mu=1.0, sigma=0.5, T=3.0, num_points=1000, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)

        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)
        
        # Standard Brownian motion increments dW
        dW = np.random.normal(0, np.sqrt(dt), size=(paths, num_points - 1))
        
        X = np.zeros((paths, num_points))
        X[:, 0] = X0
        
        # Evaluate stochastic integral sum for continuous trajectory
        for i in range(1, num_points):
            ti = t[i]
            s_grid = t[:i]
            weights = np.exp(-theta * (ti - s_grid))
            stochastic_integral = np.sum(dW[:, :i] * weights, axis=1)
            
            X[:, i] = mu + (X0 - mu) * np.exp(-theta * ti) + sigma * stochastic_integral
            
        return t, X
    ```

=== "Discrete-time"

    ## Discrete-time Recipes for OU Process

    To simulate the Ornstein–Uhlenbeck process on a discrete grid $t_0 < t_1 < \dots < t_N$ with uniform step size $\Delta t = \frac{T}{N}$, two discrete numerical formulations are available.

    ---

    ### Method 1: Discrete Exact Gaussian Transition Sampling

    #### Short Explanation
    Method 1 samples discrete step updates $X_{t_{i+1}}$ directly from the exact conditional Gaussian transition density derived via Itô's Lemma over step $\Delta t$.

    * **Zero Discretization Error ($\mathcal{O}(0)$):** Preserves exact conditional mean decay and variance damping for any step size $\Delta t$.
    * **Numerical Stability:** The exponential decay factor $e^{-\theta \Delta t} \in (0, 1)$ guarantees strictly stable relaxation toward $\mu$ without numerical overshooting.

    $$X_{t_{i+1}} = \mu + (X_{t_i} - \mu) e^{-\theta \Delta t} + \sqrt{\frac{\sigma^2}{2\theta} \left(1 - e^{-2\theta \Delta t}\right)} \, Z_i, \quad Z_i \sim \mathcal{N}(0, 1)$$

    ```python
    import numpy as np

    def simulate_ou_exact(X0=0.0, theta=2.0, mu=1.0, sigma=0.5, T=3.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        
        decay = np.exp(-theta * dt)
        step_std = np.sqrt((sigma**2 / (2 * theta)) * (1 - np.exp(-2 * theta * dt)))
        
        X = np.zeros((paths, steps))
        X[:, 0] = X0
        
        for i in range(1, steps):
            Z = np.random.normal(0, 1, paths)
            X[:, i] = mu + (X[:, i - 1] - mu) * decay + step_std * Z
            
        return t, X
    ```

    ---

    ### Method 2: Discrete Euler–Maruyama Discretization Scheme

    #### Short Explanation
    Method 2 applies a first-order finite-difference approximation to the linear differential equation $dX_t = \theta(\mu - X_t)dt + \sigma dW_t$.

    * **Order 0.5 Strong Error:** Approximates exponential decay using linear term $(1 - \theta \Delta t)$.
    * **Overshooting Risk:** For large step sizes $\Delta t > \frac{2}{\theta}$, the linear update multiplier $(1 - \theta \Delta t) < -1$ causes artificial, explosive numerical oscillations around mean $\mu$.

    $$X_{t_{i+1}} = X_{t_i} + \theta (\mu - X_{t_i}) \Delta t + \sigma \sqrt{\Delta t} \, Z_i, \quad Z_i \sim \mathcal{N}(0, 1)$$

    ```python
    import numpy as np

    def simulate_ou_euler(X0=0.0, theta=2.0, mu=1.0, sigma=0.5, T=3.0, steps=200, paths=1, seed=42):
        if seed is not None:
            np.random.seed(seed)
            
        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        
        X = np.zeros((paths, steps))
        X[:, 0] = X0
        
        for i in range(1, steps):
            Z = np.random.normal(0, 1, paths)
            X[:, i] = X[:, i - 1] + theta * (mu - X[:, i - 1]) * dt + sigma * np.sqrt(dt) * Z
            
        return t, X
    ```

=== "Simulation"

    <iframe src="/assets/simulations/ou_process.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>