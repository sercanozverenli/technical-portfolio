<style>
  
  .sidebar-nav { padding: 0; }
  .sidebar-nav > ul { list-style: none; padding: 0; margin: 0; }
  .sidebar-nav details { margin: 4px 0; }
  .sidebar-nav details summary {
    cursor: pointer; padding: 6px 8px; font-weight: 600;
    font-size: 14px; color: #2c3e50; list-style: none;
    display: flex; align-items: center; gap: 6px; border-radius: 4px;
  }
  .sidebar-nav details summary::-webkit-details-marker { display: none; }
  .sidebar-nav details summary::before {
    content: '▶'; font-size: 9px;
    transition: transform 0.2s; display: inline-block;
  }
  .sidebar-nav details[open] summary::before { transform: rotate(90deg); }
  .sidebar-nav details summary:hover { background: #f0f4f8; }
  .sidebar-nav details ul { list-style: none; padding: 0 0 0 20px; margin: 2px 0; }
  .sidebar-nav details ul li a {
    display: block; padding: 5px 8px; font-size: 13px;
    color: #4a5568; text-decoration: none; border-radius: 4px;
  }
  .sidebar-nav details ul li a:hover { background: #f0f4f8; color: #2c3e50; }
  .sidebar-nav .home-link {
    display: block;
    padding: 6px 8px;
    font-weight: 700;
    font-size: 16px;
    color: #2c3e50;
    text-decoration: none;
    border-radius: 4px;
    margin-bottom: 12px;
    letter-spacing: 0.02em;
  }
  .sidebar-nav .home-link:hover { background: #f0f4f8; }
</style>

<div class="sidebar-nav">
  <a class="home-link" href="#/en/">Home</a>

  <details open>
    <summary>Experiments</summary>
    <ul>
      <li><a href="#/en/projects/experiments/exploratory-data-analysis">Exploratory Data Analysis</a></li>
    </ul>
  </details>

  <details>
    <summary>Apps</summary>
    <ul>
      <!-- Opens when ready -->
    </ul>
  </details>

  <details>
    <summary>Systems</summary>
    <ul>
      <!-- All hidden until DRDRS is complete -->
    </ul>
  </details>
</div>
