<div class="hero-banner">
  <div>
    <div style="text-transform: uppercase; letter-spacing: 1px; font-size: 11px; font-weight: 700; color: #a5b4fc; margin-bottom: 8px;">Recipes for Simulating Stochastic Processes</div>
    <h1>The Stochastic Cookbook</h1>
    <div style="font-size: 16px; font-weight: 600; color: #cbd5e1; margin-top: -6px; margin-bottom: 16px;">
      with Chef <span style="color: #ffffff;">Subrat Sethi</span>, a quant enthusiast
    </div>
    <p>Master the art of simulating stochastic processes with comprehensive recipes for Geometric Brownian Motion, Ornstein-Uhlenbeck Process, Cox-Ingersoll-Ross Process, Heston Model and Vector Brownian Motion.</p>
    <a href="#recipes" class="hero-btn hero-btn-primary">Explore Recipes</a>
    <a href="#resources" class="hero-btn hero-btn-secondary">Other Resources</a>
  </div>
</div>

<div class="recipes-container">
  <h2 id="recipes" class="recipes-title">Recipes</h2>
  <p class="recipes-subtitle">Essential methods for simulating Gaussian and stochastic processes</p>

  <div class="recipe-grid">

    <div class="recipe-card">
      <div>
        <div class="recipe-header">
          <h3>Geometric Brownian<br>Motion</h3>
          <span class="recipe-number">01</span>
        </div>
        <div class="recipe-thumbnail">
          <svg width="100%" height="30" viewBox="0 0 120 30" fill="none">
            <path d="M0 28 Q40 25 70 15 T120 2" stroke="#6366f1" stroke-width="2.5" stroke-linecap="round"/>
          </svg>
        </div>
        <p class="recipe-description">Continuous log-normal asset price process with drift and diffusion, underlying the Black-Scholes framework.</p>
        <div class="recipe-tags">
          <span class="tag-pill">Black-Scholes</span>
          <span class="tag-pill">Itô Calculus</span>
          <span class="tag-pill">Log-Normal Returns</span>
        </div>
      </div>
      <a href="/recipes/geometric-brownian-motion/" class="btn-view-recipe">View Recipe</a>
    </div>

    <div class="recipe-card">
      <div>
        <div class="recipe-header">
          <h3>Ornstein-Uhlenbeck<br>Process</h3>
          <span class="recipe-number">02</span>
        </div>
        <div class="recipe-thumbnail">
          <svg width="100%" height="30" viewBox="0 0 120 30" fill="none">
            <path d="M0 15 L20 5 L40 25 L60 10 L80 20 L100 8 L120 15" stroke="#6366f1" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </div>
        <p class="recipe-description">Mean-reverting Gaussian process with constant volatility, widely applied in pairs trading and Vasicek rate modeling.</p>
        <div class="recipe-tags">
          <span class="tag-pill">Mean Reversion</span>
          <span class="tag-pill">Pairs Trading</span>
          <span class="tag-pill">Vasicek Model</span>
        </div>
      </div>
      <a href="/recipes/ornstein-uhlenbeck/" class="btn-view-recipe">View Recipe</a>
    </div>

    <div class="recipe-card">
      <div>
        <div class="recipe-header">
          <h3>Cox-Ingersoll-Ross<br>Process</h3>
          <span class="recipe-number">03</span>
        </div>
        <div class="recipe-thumbnail">
          <svg width="100%" height="30" viewBox="0 0 120 30" fill="none">
            <path d="M0 25 Q30 5 60 18 T120 12" stroke="#6366f1" stroke-width="2.5" stroke-linecap="round"/>
          </svg>
        </div>
        <p class="recipe-description">Non-negative mean-reverting process with square-root diffusion, essential for term-structure and Heston stochastic volatility.</p>
        <div class="recipe-tags">
          <span class="tag-pill">Feller Condition</span>
          <span class="tag-pill">Interest Rates</span>
          <span class="tag-pill">Non-Central Chi-Squared</span>
        </div>
      </div>
      <a href="/recipes/cox-ingersoll-ross/" class="btn-view-recipe">View Recipe</a>
    </div>

    <div class="recipe-card">
      <div>
        <div class="recipe-header">
          <h3>Heston Stochastic<br>Volatility</h3>
          <span class="recipe-number">04</span>
        </div>
        <div class="recipe-thumbnail">
          <svg width="100%" height="30" viewBox="0 0 120 30" fill="none">
            <path d="M0 20 L20 10 L40 25 L60 5 L80 18 L100 8 L120 22" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>
            <path d="M0 28 L25 22 L50 26 L75 15 L100 20 L120 10" stroke="#ef4444" stroke-width="1.5" stroke-dasharray="2 2"/>
          </svg>
        </div>
        <p class="recipe-description">Coupled 2D SDE combining log-normal asset price returns with CIR stochastic variance driven by correlated Brownian motion.</p>
        <div class="recipe-tags">
          <span class="tag-pill">Stochastic Volatility</span>
          <span class="tag-pill">Leverage Effect</span>
          <span class="tag-pill">Option Pricing</span>
        </div>
      </div>
      <a href="/recipes/heston-model/" class="btn-view-recipe">View Recipe</a>
    </div>

    <div class="recipe-card">
      <div>
        <div class="recipe-header">
          <h3>Multidimensional<br>Vector Brownian</h3>
          <span class="recipe-number">05</span>
        </div>
        <div class="recipe-thumbnail">
          <svg width="100%" height="30" viewBox="0 0 120 30" fill="none">
            <path d="M0 25 L30 15 L60 22 L90 8 L120 18" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>
            <path d="M0 20 L30 10 L60 17 L90 3 L120 13" stroke="#10b981" stroke-width="2" stroke-linecap="round"/>
            <path d="M0 15 L30 5 L60 12 L90 1 L120 8" stroke="#f59e0b" stroke-width="2" stroke-linecap="round"/>
          </svg>
        </div>
        <p class="recipe-description">Correlated d-dimensional Gaussian vector processes generated via Cholesky factor decomposition for multi-asset risk.</p>
        <div class="recipe-tags">
          <span class="tag-pill">Cholesky Factorization</span>
          <span class="tag-pill">Covariance Matrix</span>
          <span class="tag-pill">Portfolio Risk</span>
        </div>
      </div>
      <a href="/recipes/vector-brownian-motion/" class="btn-view-recipe">View Recipe</a>
    </div>

  </div>
</div>

<hr style="border: 0; border-top: 1px solid #e2e8f0; margin: 60px 0;">

<div class="recipes-container" id="resources">
  <h2 class="recipes-title">Resources</h2>
  <p class="recipes-subtitle">Connect with the project and explore more content</p>

  <div class="resources-grid">

  <div class="resource-card">
    <div style="margin-bottom: 12px; color: #0f172a;">
      <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="currentColor" class="bi bi-github" viewBox="0 0 16 16">
        <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27s1.36.09 2 .27c1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.01 8.01 0 0 0 16 8c0-4.42-3.58-8-8-8"/>
      </svg>
    </div>
    <h3 style="margin: 0 0 8px 0; font-size: 18px; color: #0f172a;">GitHub</h3>
    <p style="font-size: 13px; color: #64748b; margin: 0;">Browse the source code, contribute, or star the project on GitHub.</p>
    <a href="https://github.com/sub-shi" class="btn-link" target="_blank">View on GitHub</a>
  </div>


  <div class="resource-card">
    <div style="margin-bottom: 12px; color: #6366f1;">
      <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="currentColor" class="bi bi-file-earmark-check" viewBox="0 0 16 16">
        <path d="M10.854 7.854a.5.5 0 0 0-.708-.708L7.5 9.793 6.354 8.646a.5.5 0 1 0-.708.708l1.5 1.5a.5.5 0 0 0 .708 0z"/>
        <path d="M14 14V4.5L9.5 0H4a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2M9.5 3A1.5 1.5 0 0 0 11 4.5h2V14a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h5.5z"/>
      </svg>
    </div>
    <h3 style="margin: 0 0 8px 0; font-size: 18px; color: #0f172a;">SSRN</h3>
    <p style="font-size: 13px; color: #64748b; margin: 0;">Read research papers and preprints on SSRN.</p>
    <a href="https://ssrn.com" class="btn-link" target="_blank">View on SSRN</a>
  </div>

  <div class="resource-card">
    <div style="margin-bottom: 12px; color: #0f172a;">
      <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="currentColor" class="bi bi-medium" viewBox="0 0 16 16">
        <path d="M9.025 8c0 2.485-2.02 4.5-4.513 4.5A4.506 4.506 0 0 1 0 8c0-2.486 2.02-4.5 4.512-4.5A4.506 4.506 0 0 1 9.025 8m4.95 0c0 2.34-1.01 4.236-2.256 4.236S9.463 10.339 9.463 8c0-2.34 1.01-4.236 2.256-4.236S13.975 5.661 13.975 8M16 8c0 2.096-.355 3.795-.794 3.795-.438 0-.793-1.7-.793-3.795 0-2.096.355-3.795.794-3.795.438 0 .793 1.699.793 3.795"/>
      </svg>
    </div>
    <h3 style="margin: 0 0 8px 0; font-size: 18px; color: #0f172a;">Medium</h3>
    <p style="font-size: 13px; color: #64748b; margin: 0;">Read articles and essays on stochastic processes on Medium.</p>
    <a href="https://medium.com/@subratsethi9910" class="btn-link" target="_blank">Read on Medium</a>
  </div>

  <div class="resource-card">
    <div style="margin-bottom: 12px; color: #0a66c2;">
      <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="currentColor" class="bi bi-linkedin" viewBox="0 0 16 16">
        <path d="M0 1.146C0 .513.526 0 1.175 0h13.65C15.474 0 16 .513 16 1.146v13.708c0 .633-.526 1.146-1.175 1.146H1.175C.526 16 0 15.487 0 14.854zm4.943 12.248V6.169H2.542v7.225zm-1.2-8.212c.837 0 1.358-.554 1.358-1.248-.015-.709-.52-1.248-1.342-1.248S2.4 3.226 2.4 3.934c0 .694.521 1.248 1.327 1.248zm4.908 8.212V9.359c0-.216.016-.432.08-.586.173-.431.568-.878 1.232-.878.869 0 1.216.662 1.216 1.634v3.865h2.401V9.25c0-2.22-1.184-3.252-2.764-3.252-1.274 0-1.845.7-2.165 1.193v.025h-.016l.016-.025V6.169h-2.4c.03.678 0 7.225 0 7.225z"/>
      </svg>
    </div>
    <h3 style="margin: 0 0 8px 0; font-size: 18px; color: #0f172a;">LinkedIn</h3>
    <p style="font-size: 13px; color: #64748b; margin: 0;">Connect and engage on LinkedIn.</p>
    <a href="https://linkedin.com/in/subrat-sethi-0a7934208" class="btn-link" target="_blank">Connect on LinkedIn</a>
  </div>

  </div>
</div>
<footer class="custom-footer">
  <div class="footer-grid">
    <div>
      <h3 style="color:#ffffff; margin: 0 0 12px 0;">The Stochastic Cookbook</h3>
      <p style="font-size: 13px; line-height: 1.6; max-width: 320px; color: #94a3b8;">Mastering Gaussian processes through practical recipes and interactive learning.</p>
    </div>
    <div class="footer-column">
      <h4>Quick Links</h4>
      <ul>
        <li>
    <a href="https://github.com/sub-shi" target="_blank" style="display: flex; align-items: center; gap: 8px;">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-github" viewBox="0 0 16 16">
        <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27s1.36.09 2 .27c1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.01 8.01 0 0 0 16 8c0-4.42-3.58-8-8-8"/>
      </svg>
      GitHub
    </a>
  </li>
        <li>
    <a href="https://ssrn.com" target="_blank" style="display: flex; align-items: center; gap: 8px;">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-file-earmark-check" viewBox="0 0 16 16">
        <path d="M10.854 7.854a.5.5 0 0 0-.708-.708L7.5 9.793 6.354 8.646a.5.5 0 1 0-.708.708l1.5 1.5a.5.5 0 0 0 .708 0z"/>
        <path d="M14 14V4.5L9.5 0H4a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2M9.5 3A1.5 1.5 0 0 0 11 4.5h2V14a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h5.5z"/>
      </svg>
      SSRN
    </a>
  </li>
        <li>
    <a href="https://medium.com/@subratsethi9910" target="_blank" style="display: flex; align-items: center; gap: 8px;">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-medium" viewBox="0 0 16 16">
        <path d="M9.025 8c0 2.485-2.02 4.5-4.513 4.5A4.506 4.506 0 0 1 0 8c0-2.486 2.02-4.5 4.512-4.5A4.506 4.506 0 0 1 9.025 8m4.95 0c0 2.34-1.01 4.236-2.256 4.236S9.463 10.339 9.463 8c0-2.34 1.01-4.236 2.256-4.236S13.975 5.661 13.975 8M16 8c0 2.096-.355 3.795-.794 3.795-.438 0-.793-1.7-.793-3.795 0-2.096.355-3.795.794-3.795.438 0 .793 1.699.793 3.795"/>
      </svg>
      Medium
    </a>
  </li>
      </ul>
    </div>
    <div class="footer-column">
      <h4>Recipes</h4>
      <ul>
        <li><a href="/recipes/geometric-brownian-motion/">Geometric Brownian Motion</a></li>
        <li><a href="/recipes/Ornstein-Uhlenbeck/">Ornstein Uhlenbeck</a></li>
        <li><a href="/recipes/Cox-Ingersoll-Ross/">Cox Ingersoll Ross</a></li>
        <li><a href="/recipes/heston-model/">Heston Stochastic Volatility Model</a></li>
        <li><a href="/recipes/vector-brownian-motion/">Vector Brownian Motion</a></li>
      </ul>
    </div>
  </div>
  <div class="footer-bottom">
    © 2026 Subrat Sethi. All rights reserved.
  </div>
</footer>