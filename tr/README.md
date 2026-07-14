# Kişisel Ar-Ge Merkezi | Teknik Proje Portföyü 

Bu platform; **Veri Bilimi**, **Makine Öğrenmesi** ve **Yazılım Mimarisi** konularına odaklanan merkezi bir kişisel araştırma ve geliştirme laboratuvarı işlevi görmektedir. 

Temel amaç; analitik iş akışlarını belgelemek, teorik modelleri üretime hazır yapılara dönüştürmek ve birbirinden bağımsız, modüler bir sistem portföyü sürdürmektir.

Planlanan ve geliştirilen modülleri, sistem mimarilerini veya deneysel çalışmaları incelemek için **gezinme menüsünü** kullanabilirsiniz.


<!-- Dashboard Ana Konteynırı -->
<div id="dashboard-root">
  <div class="dashboard-container">    
    <!-- SOL TARAF: 4 Kategori Kartı -->
    <div class="stats-grid">      
      <!-- Kart: Sistemler -->
      <div class="stat-card purple">
        <div class="card-header">
          <div class="card-icon"><i data-lucide="layers"></i></div>
          <div class="card-title-group">
            <h3>Sistemler</h3>
            <p><span id="count-systems-total">0</span> Proje</p>
          </div>
        </div>
        <div class="card-body">
          <div class="status-row">
            <img src="docs/icons/status-idea.png" class="status-mini-icon"> <span>Planlanan</span>
            <span class="status-count" id="count-systems-idea">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-wip.png" class="status-mini-icon"> <span>Devam Eden</span>
            <span class="status-count" id="count-systems-wip">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-done.png" class="status-mini-icon"> <span>Tamamlanan</span>
            <span class="status-count" id="count-systems-done">0</span>
          </div>
        </div>
        <a href="#" target="_blank" class="card-footer">
          <div class="github-link">
            <img src="docs/icons/github-icon.png" class="github-mini-img"> GitHub Kaynak Kodları
          </div>
          <i data-lucide="arrow-right" class="arrow"></i>
        </a>
      </div>
      <!-- Kart: Uygulamalar -->
      <div class="stat-card green">
        <div class="card-header">
          <div class="card-icon"><i data-lucide="layout-grid"></i></div>
          <div class="card-title-group">
            <h3>Uygulamalar</h3>
            <p><span id="count-apps-total">0</span> Proje</p>
          </div>
        </div>
        <div class="card-body">
          <div class="status-row">
            <img src="docs/icons/status-idea.png" class="status-mini-icon"> <span>Planlanan</span>
            <span class="status-count" id="count-apps-idea">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-wip.png" class="status-mini-icon"> <span>Devam Eden</span>
            <span class="status-count" id="count-apps-wip">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-done.png" class="status-mini-icon"> <span>Tamamlanan</span>
            <span class="status-count" id="count-apps-done">0</span>
          </div>
        </div>
        <a href="#" target="_blank" class="card-footer">
          <div class="github-link">
            <img src="docs/icons/github-icon.png" class="github-mini-img"> GitHub Kaynak Kodları
          </div>
          <i data-lucide="arrow-right" class="arrow"></i>
        </a>
      </div>
      <!-- Kart: Analitik Modüller -->
      <div class="stat-card blue">
        <div class="card-header">
          <div class="card-icon"><i data-lucide="line-chart"></i></div>
          <div class="card-title-group">
            <h3>Analitik Modüller</h3>
            <p><span id="count-analytics-total">0</span> Proje</p>
          </div>
        </div>
        <div class="card-body">
          <div class="status-row">
            <img src="docs/icons/status-idea.png" class="status-mini-icon"> <span>Planlanan</span>
            <span class="status-count" id="count-analytics-idea">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-wip.png" class="status-mini-icon"> <span>Devam Eden</span>
            <span class="status-count" id="count-analytics-wip">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-done.png" class="status-mini-icon"> <span>Tamamlanan</span>
            <span class="status-count" id="count-analytics-done">0</span>
          </div>
        </div>
        <a href="#" target="_blank" class="card-footer">
          <div class="github-link">
            <img src="docs/icons/github-icon.png" class="github-mini-img"> GitHub Kaynak Kodları
          </div>
          <i data-lucide="arrow-right" class="arrow"></i>
        </a>
      </div>
      <!-- Kart: Rehberler -->
      <div class="stat-card orange">
        <div class="card-header">
          <div class="card-icon"><i data-lucide="book-open"></i></div>
          <div class="card-title-group">
            <h3>Rehberler</h3>
            <p><span id="count-guides-total">0</span> Proje</p>
          </div>
        </div>
        <div class="card-body">
          <div class="status-row">
            <img src="docs/icons/status-idea.png" class="status-mini-icon"> <span>Planlanan</span>
            <span class="status-count" id="count-guides-idea">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-wip.png" class="status-mini-icon"> <span>Devam Eden</span>
            <span class="status-count" id="count-guides-wip">0</span>
          </div>
          <div class="status-row">
            <img src="docs/icons/status-done.png" class="status-mini-icon"> <span>Tamamlanan</span>
            <span class="status-count" id="count-guides-done">0</span>
          </div>
        </div>
        <a href="#" target="_blank" class="card-footer">
          <div class="github-link">
            <img src="docs/icons/github-icon.png" class="github-mini-img"> GitHub Kaynak Kodları
          </div>
          <i data-lucide="arrow-right" class="arrow"></i>
        </a>
      </div>
    </div>
    <!-- SAĞ TARAF: Öne Çıkan Proje ve Sistem Diyagramı -->
    <div class="featured-section">
      <div class="featured-card">
        <div class="featured-badge">
          <i data-lucide="star"></i> Öne Çıkan Proje
        </div>
        <h2>Amplify Core</h2>
        <p>Veri Güvenilirliği ve Karar Yönlendirme Sistemi</p>        
        <hr class="divider">        
        <div class="global-stats">
          <div class="stat-item">
            <div class="stat-icon-bg"><i data-lucide="file-text"></i></div>
            <div class="stat-info">
              <span class="stat-value" id="global-total-projects">0</span>
              <span class="stat-label">Toplam Proje</span>
            </div>
          </div>
          <div class="stat-item">
            <div class="stat-icon-bg"><i data-lucide="code-2"></i></div>
            <div class="stat-info">
              <span class="stat-value" id="global-total-docs">0</span>
              <span class="stat-label">Toplam Doküman</span>
            </div>
          </div>
        </div>        
        <div class="diagram-area">
          <span class="diagram-title">Sistem Diyagramı</span>
          <div class="diagram-image-container">
            <!-- sekil-1.png buraya eklendi -->
            <img src="docs/diagrams/amplify-core/sekil-1.png" alt="Sistem Diyagramı" class="featured-diagram-img">
          </div>
        </div>
      </div>
    </div>

  </div>
</div>
