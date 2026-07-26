# 04. Heston Stochastic Volatility Model

=== "Continuous-time"

    ## Mathematical Foundation

    The **Heston Stochastic Volatility Model** couples an asset price process $S_t$ with a mean-reverting variance process $v_t$ (a CIR process) driven by two correlated Brownian motions $W_t^S$ and $W_t^v$:

    $$\begin{aligned}     dS_t &= r S_t dt + \sqrt{v_t} S_t dW_t^S \\     dv_t &= \kappa (\theta - v_t) dt + \sigma \sqrt{v_t} dW_t^v     \end{aligned}$$

    where:
    * $r$ is the risk-free interest rate.
    * $\kappa > 0$ is the mean-reversion speed of variance.
    * $\theta > 0$ is the long-term variance level.
    * $\sigma > 0$ is the volatility of variance ("vol of vol").
    * $d\langle W^S, W^v \rangle_t = \rho \, dt$, with $\rho \in [-1, 1]$ capturing the **leverage effect** (correlation between returns and volatility shocks).

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
        """
        Continuous vector evaluation of Heston asset price and volatility trajectories.
        """
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

    ### Full Truncation Euler-Maruyama Scheme

    Because $v_t$ can drop below zero under standard Euler discretization, the **Full Truncation** scheme truncates $v_t$ inside the drift and diffusion terms while preserving the state trajectory:

    $$v_{t_{i+1}} = v_{t_i} + \kappa \left(\theta - v_{t_i}^+\right) \Delta t + \sigma \sqrt{v_{t_i}^+} \sqrt{\Delta t} \, Z_i^v$$

    $$S_{t_{i+1}} = S_{t_i} \exp \left( \left(r - \frac{1}{2} v_{t_i}^+\right) \Delta t + \sqrt{v_{t_i}^+} \sqrt{\Delta t} \, Z_i^S \right)$$

    where $v^+ = \max(v, 0)$ and $Z^v = \rho Z^S + \sqrt{1 - \rho^2} Z^{\perp}$.

    ```python
    import numpy as np

    def simulate_heston_euler(
        S0=100.0, v0=0.04, r=0.02, kappa=2.0, theta=0.04, sigma=0.3, rho=-0.7, 
        T=1.0, steps=200, paths=1, seed=42
    ):
        """
        Full Truncation Euler-Maruyama simulation for the Heston model.
        """
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