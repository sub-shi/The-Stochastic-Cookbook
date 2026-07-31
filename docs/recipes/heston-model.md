# 04. Heston Stochastic Volatility Model

=== "Continuous-time"

    ## Mathematical Foundation

    The **Heston Stochastic Volatility Model** couples an asset price process $S_t$ with a mean-reverting variance process $v_t$ (a CIR process) driven by two correlated Brownian motions $W_t^S$ and $W_t^v$:

    $$\begin{aligned}     dS_t &= r S_t dt + \sqrt{v_t} S_t dW_t^S \\     dv_t &= \kappa (\theta - v_t) dt + \sigma \sqrt{v_t} dW_t^v     \end{aligned}$$

    where:
    * $r$ is the risk-free interest rate (drift).
    * $\kappa > 0$ is the mean-reversion speed of variance.
    * $\theta > 0$ is the long-term structural variance level.
    * $\sigma > 0$ is the volatility of variance ("vol of vol").
    * $d\langle W^S, W^v \rangle_t = \rho \, dt$, with $\rho \in [-1, 1]$ capturing the **leverage effect** (correlation between returns and volatility shocks).

    ---

    ### Continuous Trajectory Concept

    In continuous time, the Heston model generates a two-dimensional coupled stochastic trajectory $(S(t), v(t))$. Asset log-returns depend dynamically on instantaneous variance $v_t$, which itself oscillates mean-revertively around long-term variance $\theta$.

    ---

    ### Leverage Effect & Volatility Smiles

    * **Negative Correlation ($\rho < 0$):** Typical in equity markets. A drop in asset price ($S_t \downarrow$) coincides with a spike in variance ($v_t \uparrow$), producing negative skewness and a prominent volatility smile/skew.
    * **Positive Correlation ($\rho > 0$):** Common in commodity markets where price surges coincide with elevated volatility.

    ---

    ### Continuous-time Stochastic Vector Representation

    Using Cholesky decomposition on the 2D Wiener driver $\mathbf{W}_t = (W_t^S, W_t^v)^T$:

    $$\begin{pmatrix} dW_t^S \\ dW_t^v \end{pmatrix} = \begin{pmatrix} 1 & 0 \\ \rho & \sqrt{1 - \rho^2} \end{pmatrix} \begin{pmatrix} dZ_t^1 \\ dZ_t^2 \end{pmatrix}$$

    where $Z_t^1, Z_t^2$ are independent standard Wiener processes.

    ```python
    import numpy as np

    def heston_continuous_vector_path(
        S0=100.0, v0=0.04, r=0.02, kappa=2.0, theta=0.04, sigma=0.3, rho=-0.7, 
        T=1.0, num_points=1000, paths=1, seed=42
    ):
        if seed is not None:
            np.random.seed(seed)

        t = np.linspace(0, T, num_points)
        dt = T / (num_points - 1)
        sqrt_dt = np.sqrt(dt)

        S = np.zeros((paths, num_points))
        v = np.zeros((paths, num_points))
        S[:, 0] = S0
        v[:, 0] = v0

        for i in range(1, num_points):
            Z1 = np.random.normal(0, 1, paths)
            Z2_indep = np.random.normal(0, 1, paths)
            Z2 = rho * Z1 + np.sqrt(1 - rho**2) * Z2_indep

            v_pos = np.maximum(v[:, i - 1], 0.0)

            # Continuous exact log-price integration step
            S[:, i] = S[:, i - 1] * np.exp((r - 0.5 * v_pos) * dt + np.sqrt(v_pos) * sqrt_dt * Z1)
            
            # Variance SDE step
            v[:, i] = v[:, i - 1] + kappa * (theta - v_pos) * dt + sigma * np.sqrt(v_pos) * sqrt_dt * Z2
            v[:, i] = np.maximum(v[:, i], 0.0)

        return t, S, v
    ```

=== "Discrete-time"

    ## Discrete-time Recipes for Heston Model

    Simulating the two-dimensional Heston system on a discrete time grid $t_0 < t_1 < \dots < t_N$ with uniform step size $\Delta t = \frac{T}{N}$ requires handling both the correlated Brownian drivers and the square-root variance boundary.

    ---

    ### Method 1: Discrete Exact Non-Central Chi-Squared Conditional Sampling

    #### Short Explanation
    Method 1 samples variance $v_{t_{i+1}}$ directly from the exact non-central chi-squared distribution derived from the CIR variance SDE, then updates log-asset prices $S_{t_{i+1}}$ conditionally using correlated normal increments.

    * **Zero Variance Discretization Error ($\mathcal{O}(0)$):** Generates exact conditional variance updates without time-step bias.
    * **Boundary Safety:** Ensures variance $v_t \ge 0$ naturally without needing artificial zero truncation when Feller's condition holds.

    $$v_{t_{i+1}} = \frac{1}{c} \, Y_i, \quad Y_i \sim \chi^2_d(\Lambda_i)$$

    $$c = \frac{4\kappa}{\sigma^2(1 - e^{-\kappa \Delta t})}, \quad d = \frac{4\kappa\theta}{\sigma^2}, \quad \Lambda_i = c v_{t_i} e^{-\kappa \Delta t}$$

    ```python
    import numpy as np

    def simulate_heston_exact(
        S0=100.0, v0=0.04, r=0.02, kappa=2.0, theta=0.04, sigma=0.3, rho=-0.7, 
        T=1.0, steps=200, paths=1, seed=42
    ):
        if seed is not None:
            np.random.seed(seed)

        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        sqrt_dt = np.sqrt(dt)

        df = (4 * kappa * theta) / (sigma**2)
        c = (sigma**2 * (1 - np.exp(-kappa * dt))) / (4 * kappa)

        S = np.zeros((paths, steps))
        v = np.zeros((paths, steps))
        S[:, 0] = S0
        v[:, 0] = v0

        for i in range(1, steps):
            # 1. Sample exact variance transition
            ncp = (4 * kappa * v[:, i - 1] * np.exp(-kappa * dt)) / (sigma**2 * (1 - np.exp(-kappa * dt)))
            v[:, i] = c * np.random.noncentral_chisquare(df=df, nonc=ncp, size=paths)

            # 2. Update log-price conditionally using correlated Brownian motion
            Z1 = np.random.normal(0, 1, paths)
            v_pos = np.maximum(v[:, i - 1], 0.0)
            S[:, i] = S[:, i - 1] * np.exp((r - 0.5 * v_pos) * dt + np.sqrt(v_pos) * sqrt_dt * Z1)

        return t, S, v
    ```

    ---

    ### Method 2: Discrete Full Truncation Euler–Maruyama Scheme

    #### Short Explanation
    Because $v_t$ can drop below zero under standard finite-difference discretization, the **Full Truncation Euler** scheme applies a non-negativity constraint ($v^+ = \max(v, 0)$) inside both drift and diffusion terms while preserving state evolution.

    * **Order 0.5 Strong Error:** Achieves strong convergence of $\mathcal{O}(\sqrt{\Delta t})$ as time step $\Delta t \to 0$.
    * **High Execution Speed:** Highly optimized for production engines due to simple standard normal random draws.

    $$v_{t_{i+1}} = v_{t_i} + \kappa \left(\theta - v_{t_i}^+\right) \Delta t + \sigma \sqrt{v_{t_i}^+} \sqrt{\Delta t} \, Z_i^v$$

    $$S_{t_{i+1}} = S_{t_i} \exp \left( \left(r - \frac{1}{2} v_{t_i}^+\right) \Delta t + \sqrt{v_{t_i}^+} \sqrt{\Delta t} \, Z_i^S \right)$$

    where $v^+ = \max(v, 0)$ and $Z^v = \rho Z^S + \sqrt{1 - \rho^2} Z^{\perp}$.

    ```python
    import numpy as np

    def simulate_heston_euler(
        S0=100.0, v0=0.04, r=0.02, kappa=2.0, theta=0.04, sigma=0.3, rho=-0.7, 
        T=1.0, steps=200, paths=1, seed=42
    ):
        if seed is not None:
            np.random.seed(seed)

        dt = T / (steps - 1)
        t = np.linspace(0, T, steps)
        sqrt_dt = np.sqrt(dt)

        S = np.zeros((paths, steps))
        v = np.zeros((paths, steps))
        S[:, 0] = S0
        v[:, 0] = v0

        for i in range(1, steps):
            Z_S = np.random.normal(0, 1, paths)
            Z_indep = np.random.normal(0, 1, paths)
            Z_v = rho * Z_S + np.sqrt(1 - rho**2) * Z_indep

            v_pos = np.maximum(v[:, i - 1], 0.0)

            # Update asset price
            S[:, i] = S[:, i - 1] * np.exp((r - 0.5 * v_pos) * dt + np.sqrt(v_pos) * sqrt_dt * Z_S)

            # Update variance with full truncation
            v[:, i] = v[:, i - 1] + kappa * (theta - v_pos) * dt + sigma * np.sqrt(v_pos) * sqrt_dt * Z_v
            v[:, i] = np.maximum(v[:, i], 0.0)

        return t, S, v
    ```

=== "Simulation"

    <iframe src="/assets/simulations/heston_model.html" style="width: 100%; height: 680px; border: none; border-radius: 12px; overflow: hidden;" scrolling="no"></iframe>