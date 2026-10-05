<!DOCTYPE html>
<html lang="pt-BR">
  <head>
    <meta charset="UTF-8">
    <title>Painel do Supervisor - Dashboard Raio X</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <!-- TomSelect CSS e JS -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/tom-select/2.2.2/css/tom-select.bootstrap5.min.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tom-select/2.2.2/js/tom-select.complete.min.js"></script>

    <!-- Chart.js e Plugin DataLabels -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-datalabels@2.2.0"></script>

    <style>
      :root {
        --primary: #f4511e;
        --primary-hover: #d84315;
        --accent-badge: #fbe9e7;
        --accent-badge-text: #f4511e;
        --bg-body: #f4f6f8;
        --bg-card: #ffffff;
        --bg-header: #ffffff;
        --header-text: #2b3648;
        --bg-filter-bar: #ffffff;
        --bg-input: #ffffff;
        --border-color: #e0e0e0;
        --text-main: #2b3648;
        --text-muted: #6c757d;
        --shadow: 0 4px 14px rgba(0, 0, 0, 0.05);
      }

      body.dark-mode {
        --bg-body: #121212;
        --bg-card: #1e1e1e;
        --bg-header: #1e1e1e;
        --header-text: #ffffff;
        --bg-filter-bar: #1e1e1e;
        --bg-input: #2d2d2d;
        --border-color: #333333;
        --text-main: #e2e8f0;
        --text-muted: #9ca3af;
        --shadow: 0 4px 16px rgba(0, 0, 0, 0.4);
        --accent-badge: #3d231d;
        --accent-badge-text: #ff7043;
      }

      * { 
        box-sizing: border-box; 
        margin: 0; 
        padding: 0; 
        font-family: 'Inter', sans-serif; 
      }

      /* Troca de tema imediata: evita a sensação de pausa ao alternar entre claro/escuro. */
      html.theme-switching *,
      html.theme-switching *::before,
      html.theme-switching *::after {
        transition: none !important;
        animation: none !important;
      }

      body { 
        background-color: var(--bg-body); 
        color: var(--text-main); 
        padding-bottom: 40px; 
        min-height: 100vh; 
      }

      /* ESTILOS DO SPLASH SCREEN / OVERLAY DE CARREGAMENTO */
      #splashScreenOverlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background-color: #fff9f6;
        z-index: 9999999;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        overflow: hidden;
        transition: opacity 0.5s ease, visibility 0.5s ease;
      }

      body.dark-mode #splashScreenOverlay {
        background-color: #121212;
      }

      @keyframes floatCircle1 {
        0% { transform: translate(0, 0) scale(1); }
        50% { transform: translate(30px, 20px) scale(1.05); }
        100% { transform: translate(-20px, -15px) scale(0.98); }
      }

      @keyframes floatCircle2 {
        0% { transform: translate(0, 0) scale(1); }
        50% { transform: translate(-35px, -25px) scale(1.06); }
        100% { transform: translate(25px, 15px) scale(0.95); }
      }

      .splash-bg-circle-1 {
        position: absolute;
        width: 750px;
        height: 750px;
        border-radius: 50%;
        border: 50px solid rgba(244, 81, 30, 0.06);
        top: -250px;
        left: -250px;
        pointer-events: none;
        animation: floatCircle1 9s ease-in-out infinite alternate;
      }

      .splash-bg-circle-2 {
        position: absolute;
        width: 850px;
        height: 850px;
        border-radius: 50%;
        border: 60px solid rgba(244, 81, 30, 0.05);
        bottom: -300px;
        right: -300px;
        pointer-events: none;
        animation: floatCircle2 11s ease-in-out infinite alternate;
      }

      .splash-content-card {
        background: #ffffff;
        padding: 55px 70px;
        border-radius: 32px;
        box-shadow: 0 16px 50px rgba(244, 81, 30, 0.08);
        display: flex;
        flex-direction: column;
        align-items: center;
        text-align: center;
        max-width: 680px;
        width: 92%;
        z-index: 2;
      }

      body.dark-mode .splash-content-card {
        background: #1e1e1e;
        box-shadow: 0 16px 50px rgba(0, 0, 0, 0.6);
      }

      .splash-logo-box {
        width: 320px;
        margin-bottom: 28px;
      }

      .splash-logo-box img {
        width: 100%;
        height: auto;
        object-fit: contain;
      }

      .splash-title {
        font-size: 26px;
        font-weight: 700;
        color: var(--text-main);
        margin-bottom: 6px;
      }

      .splash-subtitle {
        font-size: 13px;
        font-weight: 700;
        color: #f4511e;
        letter-spacing: 1.2px;
        text-transform: uppercase;
        margin-bottom: 34px;
      }

      .splash-progress-circle-container {
        position: relative;
        width: 140px;
        height: 140px;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 28px;
      }

      .splash-progress-circle-container svg {
        width: 100%;
        height: 100%;
        transform: rotate(-90deg);
      }

      .splash-circle-bg {
        fill: none;
        stroke: var(--border-color);
        stroke-width: 8;
      }

      .splash-circle-bar {
        fill: none;
        stroke: #f4511e;
        stroke-width: 8;
        stroke-linecap: round;
        stroke-dasharray: 283;
        stroke-dashoffset: 283;
        transition: stroke-dashoffset 0.2s ease;
      }

      .splash-percent-text {
        position: absolute;
        font-size: 26px;
        font-weight: 700;
        color: var(--text-main);
      }

      .splash-status-text {
        font-size: 12px;
        font-weight: 700;
        color: var(--text-muted);
        letter-spacing: 1px;
        text-transform: uppercase;
        margin-bottom: 20px;
      }

      .splash-progress-bar-wrapper {
        width: 100%;
        height: 10px;
        background-color: var(--border-color);
        border-radius: 10px;
        overflow: hidden;
      }

      .splash-progress-bar-fill {
        height: 100%;
        width: 0%;
        background: linear-gradient(90deg, #f4511e, #ff7043);
        border-radius: 10px;
        transition: width 0.2s ease;
      }

      .header { 
        background-color: var(--bg-header); 
        color: var(--header-text); 
        padding: 18px 32px; 
        display: flex; 
        align-items: center; 
        justify-content: space-between; 
        border-bottom: 1px solid var(--border-color); 
        box-shadow: var(--shadow); 
      }

      .brand-section { 
        display: flex; 
        align-items: center; 
        gap: 16px; 
      }

      .logo-container { 
        width: 230px; 
        height: 46px; 
        overflow: hidden; 
        border-radius: 6px; 
        display: flex; 
        align-items: center; 
        justify-content: center; 
      }

      .header-logo-img { 
        width: 100%; 
        height: 100%; 
        object-fit: cover; 
      }

      .brand-title h1 { 
        font-size: 21px; 
        font-weight: 700; 
        color: var(--header-text); 
        border-left: 2px solid var(--border-color); 
        padding-left: 14px; 
      }

      .header-actions { 
        display: flex; 
        align-items: center; 
        gap: 12px; 
      }

      .btn-metas { 
        background-color: var(--primary); 
        border: 1px solid var(--primary-hover); 
        color: #ffffff; 
        padding: 9px 18px; 
        border-radius: 20px; 
        cursor: pointer; 
        font-size: 13px; 
        font-weight: 600; 
        display: flex; 
        align-items: center; 
        gap: 8px; 
        text-decoration: none; 
        box-shadow: 0 2px 8px rgba(244, 81, 30, 0.25); 
      }

      .btn-metas:hover { 
        background-color: var(--primary-hover); 
        transform: translateY(-1px); 
      }

      .btn-refresh { 
        background-color: #0288d1; 
        border: 1px solid #0277bd; 
        color: #ffffff; 
        padding: 9px 16px; 
        border-radius: 20px; 
        cursor: pointer; 
        font-size: 13px; 
        font-weight: 600; 
        display: flex; 
        align-items: center; 
        gap: 8px; 
        transition: all 0.2s ease; 
      }

      .btn-refresh:hover { 
        background-color: #01579b; 
        transform: translateY(-1px); 
      }

      .btn-clear { 
        background-color: #d93838; 
        border: 1px solid #c53030; 
        color: #ffffff; 
        padding: 9px 16px; 
        border-radius: 20px; 
        cursor: pointer; 
        font-size: 13px; 
        font-weight: 600; 
        display: flex; 
        align-items: center; 
        gap: 8px; 
      }

      .theme-toggle-btn { 
        background-color: #343a40; 
        border: 1px solid #2f3439; 
        color: #ffffff; 
        padding: 9px 16px; 
        border-radius: 20px; 
        cursor: pointer; 
        font-size: 13px; 
        font-weight: 600; 
        display: flex; 
        align-items: center; 
        gap: 8px; 
        transition: all 0.2s ease; 
      }

      .theme-toggle-btn:hover {
        background-color: #232a31;
        transform: translateY(-1px);
      }

      .tv-mode-btn {
        background-color: #6d4aff;
        border: 1px solid #5c3ff0;
        color: #ffffff;
        padding: 9px 16px;
        border-radius: 20px;
        cursor: pointer;
        font-size: 13px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 8px;
        transition: all 0.2s ease;
        box-shadow: 0 2px 8px rgba(109,74,255,0.25);
      }

      .tv-mode-btn:hover {
        background-color: #5c3ff0;
        transform: translateY(-1px);
      }

      body.tv-mode {
        overflow-y: auto;
        padding-bottom: 82px;
      }

      body.tv-mode .header {
        padding: 10px 20px;
        min-height: 58px;
      }

      body.tv-mode .logo-container {
        width: 180px;
        height: 38px;
      }

      body.tv-mode .brand-title h1 {
        font-size: 18px;
      }

      body.tv-mode .header-actions > *:not(#tvModeBtn):not(#themeToggleBtn) {
        display: none !important;
      }

      body.tv-mode .theme-toggle-btn {
        display: flex !important;
      }

      body.tv-mode .tv-mode-btn {
        background-color: #111827;
        border-color: #374151;
      }

      body.tv-mode .container {
        padding: 0 10px 14px;
        margin-top: 8px;
      }

      body.tv-mode .filter-wrapper {
        display: none !important;
      }

      /* Mantém os títulos das categorias da Matriz visíveis no Modo TV. */
      body.tv-mode .section-title-kpi {
        display: flex !important;
        margin-top: 8px;
        margin-bottom: 10px;
      }

      /* Mantém as informações do colaborador visíveis no Modo TV. */
      body.tv-mode .collaborator-card {
        display: block !important;
        margin: 0 0 10px !important;
        padding: 10px 14px !important;
        min-height: auto !important;
      }

      body.tv-mode .collaborator-main-info {
        display: grid !important;
        grid-template-columns: 118px minmax(0, 1fr) 220px 220px !important;
        align-items: start !important;
        column-gap: 18px !important;
      }

      body.tv-mode .collaborator-details {
        grid-column: 2 !important;
        grid-row: 1 !important;
      }

      body.tv-mode .ranking-emphasis-card {
        grid-column: 3 !important;
        grid-row: 1 !important;
      }

      body.tv-mode .cert-emphasis-card {
        grid-column: 4 !important;
        grid-row: 1 !important;
      }

      body.tv-mode .collaborator-avatar {
        width: 118px !important;
        height: 108px !important;
        min-width: 118px !important;
        flex: none !important;
        grid-column: 1 !important;
        grid-row: 1 !important;
        margin-top: 4px !important;
        border-radius: 10px !important;
        object-fit: cover !important;
      }

      body.tv-mode .collaborator-details {
        grid-column: 2 !important;
        grid-row: 1 !important;
        min-width: 0 !important;
        width: 100% !important;
      }

      body.tv-mode .collaborator-name {
        font-size: 20px !important;
        margin: 2px 0 7px !important;
      }

      body.tv-mode .collaborator-meta-grid {
        display: flex !important;
        flex-wrap: wrap !important;
        gap: 5px 18px !important;
        margin-bottom: 8px !important;
      }

      body.tv-mode .meta-item {
        font-size: 15px !important;
      }

      body.tv-mode .colab-badges-row {
        display: flex !important;
        flex-wrap: wrap !important;
        gap: 6px !important;
        margin-bottom: 7px !important;
      }

      body.tv-mode .badge-highlight {
        font-size: 10px !important;
        padding: 5px 8px !important;
      }

      body.tv-mode .colab-cities-container {
        margin-top: 5px !important;
        padding-top: 5px !important;
      }

      body.tv-mode .colab-cities-label {
        font-size: 10px !important;
      }

      body.tv-mode .city-chip {
        font-size: 10px !important;
        padding: 3px 7px !important;
      }

      /* Modo TV: grade mais compacta, com menos espaço entre os cards. */
      body.tv-mode .kpi-grid {
        grid-template-columns: repeat(5, minmax(0, 1fr));
        gap: 8px;
      }

      body.tv-mode .kpi-card {
        min-height: 118px;
        padding: 12px 14px 10px;
        border-radius: 10px;
      }

      body.tv-mode .kpi-title {
        font-size: 11px;
      }

      body.tv-mode .kpi-value {
        font-size: 28px;
      }

      body.tv-mode .kpi-meta {
        font-size: 10px;
      }

      @media (min-width: 1600px) {
        body.tv-mode .kpi-grid {
          grid-template-columns: repeat(6, minmax(0, 1fr));
          gap: 8px;
        }
      }

      @media (min-width: 2200px) {
        body.tv-mode .kpi-grid {
          grid-template-columns: repeat(7, minmax(0, 1fr));
          gap: 8px;
        }
      }

      .container { 
        width: 100%; 
        padding: 0 32px; 
        margin-top: 24px; 
      }

      .filter-wrapper { 
        background-color: var(--bg-filter-bar); 
        border-radius: 14px; 
        padding: 20px 24px; 
        border: 1px solid var(--border-color); 
        margin-bottom: 24px; 
        box-shadow: var(--shadow); 
      }

      .filter-grid { 
        display: grid; 
        grid-template-columns: repeat(auto-fit, minmax(170px, 1fr)); 
        gap: 16px; 
      }

      .filter-group { 
        display: flex; 
        flex-direction: column; 
        gap: 6px; 
      }

      .filter-group label { 
        font-size: 11px; 
        font-weight: 700; 
        color: var(--text-muted); 
        text-transform: uppercase; 
        display: flex; 
        align-items: center; 
        gap: 6px; 
      }

      .filter-group label i { 
        color: var(--primary); 
      }

      /* CONTROLE DO TOMSELECT (MULTIPLE E SINGLE) */
      .ts-wrapper.single .ts-control, .ts-wrapper.multi .ts-control { 
        background-color: var(--bg-input) !important; 
        color: var(--text-main) !important; 
        border: 1px solid var(--border-color) !important; 
        border-radius: 8px !important; 
        padding: 6px 12px !important; 
        min-height: 42px !important; 
        font-size: 13px !important; 
        box-shadow: none !important; 
      }

      .ts-dropdown { 
        background-color: var(--bg-card) !important; 
        color: var(--text-main) !important; 
        border: 1px solid var(--border-color) !important; 
        border-radius: 8px !important; 
        box-shadow: var(--shadow) !important; 
        font-size: 13px !important; 
      }

      .ts-dropdown .ts-dropdown-content {
        max-height: 300px !important;
      }

      .ts-dropdown .option { 
        color: var(--text-main) !important; 
        padding: 8px 12px !important; 
      }

      .ts-dropdown .option.active { 
        background-color: var(--primary) !important; 
        color: #ffffff !important; 
      }

      /* ESTILO PADRÃO DAS PÍLULAS DE FILTRO MULTI */
      .ts-wrapper.multi .ts-control div.item { 
        background-color: var(--primary) !important; 
        color: #ffffff !important; 
        border-radius: 4px; 
        padding: 2px 6px; 
        font-size: 12px !important; 
      }

      /* CORREÇÃO DO FILTRO DE COLABORADOR (SINGLE MAXITEMS:1) PARA FICAR LARANJA */
      #filterNome + .ts-wrapper.single.has-items .ts-control {
        background-color: var(--bg-input) !important;
      }

      #filterNome + .ts-wrapper.single.has-items .ts-control > div.item {
        background-color: var(--primary) !important;
        color: #ffffff !important;
        border-radius: 4px;
        padding: 3px 8px;
        font-size: 12px !important;
        font-weight: 600;
        display: inline-flex;
        align-items: center;
        gap: 6px;
      }

      /* BLOCO DO COLABORADOR — FOTO À ESQUERDA E CONTEÚDO EM COLUNA ÚNICA */
      .collaborator-card { 
        background-color: var(--bg-card); 
        border-radius: 14px; 
        border: 1px solid var(--border-color); 
        border-left: 6px solid var(--primary); 
        padding: 22px 28px; 
        display: block; 
        box-shadow: var(--shadow); 
        margin-bottom: 24px; 
      }

      .collaborator-main-info { 
        display: grid; 
        grid-template-columns: 130px minmax(0, 1fr) 220px 220px; 
        align-items: start; 
        column-gap: 18px; 
        width: 100%; 
      }

      .colab-emphasis-card {
        min-height: 92px;
        padding: 12px 14px;
        border-radius: 10px;
        border: 1px solid transparent;
        display: grid;
        grid-template-columns: 38px minmax(0, 1fr);
        grid-template-rows: auto auto;
        align-items: center;
        column-gap: 10px;
        row-gap: 2px;
        box-sizing: border-box;
        box-shadow: 0 2px 7px rgba(0,0,0,.06);
      }
      .ranking-emphasis-card {
        background: #e8f0fe;
        border-color: #c7dbfb;
        color: #1558a6;
        border-left: 4px solid #5b8def;
      }
      /* Ranking: mantém o número e todas as estrelas dentro do card, inclusive fora do Modo TV. */
      .ranking-emphasis-card .emphasis-card-value {
        min-width: 0;
        max-width: 100%;
        display: flex;
        align-items: center;
        flex-wrap: wrap;
        gap: 2px;
        white-space: normal;
        overflow: visible;
        line-height: 1.05;
      }
      .ranking-position-text {
        display: inline-block;
        white-space: nowrap;
      }
      .ranking-stars {
        display: inline-flex;
        flex-wrap: wrap;
        align-items: center;
        gap: 0 1px;
        line-height: 1;
        white-space: normal;
      }
      .ranking-star {
        display: inline-block;
        font-size: 19px;
        line-height: 1;
      }
      .ranking-runner {
        display: inline-block;
        font-size: 21px;
        line-height: 1;
        margin-left: 3px;
        flex: 0 0 auto;
      }
      @media (max-width: 800px) {
        .ranking-star { font-size: 17px; }
      }
      .cert-emphasis-card {
        background: #fef7e0;
        border-color: #f6df9b;
        color: #8a5a00;
        border-left: 4px solid #f2b233;
      }
      .colab-emphasis-card .emphasis-card-icon {
        grid-row: 1 / 3;
        width: 38px;
        height: 38px;
        border-radius: 9px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 16px;
        background: rgba(255,255,255,.72);
      }
      .emphasis-card-label {
        font-size: 10px;
        font-weight: 800;
        letter-spacing: .35px;
        opacity: .82;
        align-self: end;
      }
      .emphasis-card-value {
        font-size: 23px;
        line-height: 1;
        font-weight: 800;
        white-space: nowrap;
        align-self: start;
      }
      .cert-emphasis-card.badge-cert-good {
        background: #d4edda;
        border-color: #b8dfbf;
        color: #155724;
        border-left-color: #43a047;
      }
      .cert-emphasis-card.badge-cert-mid {
        background: #fef7e0;
        border-color: #f6df9b;
        color: #8a5a00;
        border-left-color: #f2b233;
      }
      .cert-emphasis-card.badge-cert-low {
        background: #f8c4c8;
        border-color: #ef9aa1;
        color: #9b1c25;
        border-left-color: #e57373;
      }
      .cert-emphasis-card.badge-neutral {
        background: #f1f3f5;
        border-color: #d9dde2;
        color: #7a838d;
      }

      .collaborator-avatar { 
        width: 130px; 
        height: 111px; 
        min-width: 130px; 
        flex: none;
        grid-column: 1;
        grid-row: 1;
        align-self: start;
        justify-self: start;
        margin-top: -4px;
        border-radius: 10px !important; 
        object-fit: cover;
        object-position: center;
        border: 3px solid var(--primary); 
        background-color: var(--bg-input); 
        display: block;
      }

      .collaborator-details { 
        display: flex; 
        flex-direction: column; 
        gap: 8px; 
        width: 100%; 
        min-width: 0;
        grid-column: 2;
        grid-row: 1;
      }

      .section-tag { 
        display: inline-block; 
        background-color: var(--accent-badge); 
        color: var(--accent-badge-text); 
        font-size: 11px; 
        font-weight: 700; 
        padding: 3px 10px; 
        border-radius: 6px; 
        text-transform: uppercase; 
        width: fit-content; 
      }

      .collaborator-name { 
        font-size: 21px; 
        font-weight: 700; 
        color: var(--text-main); 
      }

      .collaborator-meta-grid { 
        display: flex; 
        flex-wrap: wrap; 
        gap: 16px 32px; 
      }

      .meta-item { 
        display: flex; 
        align-items: center; 
        gap: 8px; 
        font-size: 13.5px; 
        color: var(--text-muted); 
      }

      .meta-item i { 
        color: var(--primary); 
      }

      .meta-item strong { 
        color: var(--text-main); 
      }

      .colab-badges-row { 
        display: flex; 
        flex-wrap: wrap; 
        gap: 12px; 
        margin-top: 6px; 
      }

      .badge-highlight { 
        display: inline-flex; 
        align-items: center; 
        gap: 8px; 
        padding: 8px 16px; 
        border-radius: 8px; 
        font-size: 13px; 
        font-weight: 700; 
        box-shadow: 0 2px 4px rgba(0,0,0,0.03); 
        border: 1px solid transparent; 
      }

      .badge-ranking { 
        background-color: #e8f0fe; 
        color: #1a73e8; 
        border-color: #d2e3fc; 
      }
      body.dark-mode .badge-ranking { 
        background-color: #172b4d; 
        color: #8ab4f8; 
        border-color: #28426b; 
      }

      .badge-q1 { 
        background-color: #d4edda; 
        color: #155724; 
        border-color: #c3e6cb; 
      }
      body.dark-mode .badge-q1 { 
        background-color: #1b4332; 
        color: #81c995; 
        border-color: #2d6a4f; 
      }

      .badge-q2 { 
        background-color: #e6f4ea; 
        color: #137333; 
        border-color: #ceead6; 
      }
      body.dark-mode .badge-q2 { 
        background-color: #13271d; 
        color: #81c995; 
        border-color: #1b4332; 
      }

      .badge-q3 { 
        background-color: #fef7e0; 
        color: #b06000; 
        border-color: #feefc3; 
      }
      body.dark-mode .badge-q3 { 
        background-color: #3e2723; 
        color: #fdd835; 
        border-color: #5d4037; 
      }

      .badge-q4 { 
        background-color: #fce8e6; 
        color: #c5221f; 
        border-color: #fad2cf; 
      }
      body.dark-mode .badge-q4 { 
        background-color: #2c1615; 
        color: #f28b82; 
        border-color: #5c2826; 
      }

      /* PALETA DOS QUARTIS — BADGES DO TOPO, ALINHADA AO HISTÓRICO */
      .badge-quartil-q1 {
        background-color: #43a047;
        color: #ffffff;
        border-color: #388e3c;
      }

      .badge-quartil-q2 {
        background-color: #c8e6c9;
        color: #245229;
        border-color: #a5d6a7;
      }

      .badge-quartil-q3 {
        background-color: #ffcdd2;
        color: #8a1c25;
        border-color: #ef9a9a;
      }

      .badge-quartil-q4 {
        background-color: #e57373;
        color: #7f0000;
        border-color: #d32f2f;
      }

      body.dark-mode .badge-quartil-q1 {
        background-color: #2e7d32;
        color: #e8f5e9;
        border-color: #43a047;
      }

      body.dark-mode .badge-quartil-q2 {
        background-color: #66bb6a;
        color: #123d17;
        border-color: #81c784;
      }

      body.dark-mode .badge-quartil-q3 {
        background-color: #c62828;
        color: #ffebee;
        border-color: #e53935;
      }

      body.dark-mode .badge-quartil-q4 {
        background-color: #b71c1c;
        color: #ffebee;
        border-color: #d32f2f;
      }

      .badge-neutral { 
        background-color: var(--bg-input); 
        color: var(--text-muted); 
        border-color: var(--border-color); 
      }

      .colab-cities-container {
        display: flex;
        align-items: center;
        flex-wrap: wrap;
        gap: 8px;
        margin-top: 12px;
        padding-top: 10px;
        border-top: 1px dashed var(--border-color);
      }

      .colab-cities-label {
        font-size: 11px;
        font-weight: 700;
        color: var(--text-muted);
        text-transform: uppercase;
        letter-spacing: 0.5px;
        display: flex;
        align-items: center;
        gap: 6px;
        margin-right: 4px;
      }

      .city-chip {
        display: inline-flex;
        align-items: center;
        gap: 6px;
        background-color: var(--bg-input);
        color: var(--text-main);
        border: 1px solid var(--border-color);
        padding: 4px 10px;
        border-radius: 6px;
        font-size: 11px;
        font-weight: 600;
        box-shadow: 0 1px 3px rgba(0,0,0,0.03);
      }

      .city-chip i {
        color: var(--primary);
        font-size: 10px;
      }

      .section-title-kpi { 
        font-size: 12px; 
        font-weight: 700; 
        text-transform: uppercase; 
        letter-spacing: 0.5px; 
        color: var(--text-muted); 
        margin-bottom: 14px; 
        display: flex; 
        align-items: center; 
        gap: 8px; 
      }

      .section-title-kpi i { 
        color: var(--primary); 
      }

      /* Separadores das categorias da Matriz de Indicadores Analíticos */
      .kpi-category-title {
        margin: 4px 0 12px;
        padding: 7px 0 6px;
        border-top: 1px solid var(--primary);
        border-bottom: 1px solid var(--primary);
        color: var(--text-main);
        font-size: 11px;
        font-weight: 800;
        text-transform: uppercase;
        letter-spacing: 0.55px;
        line-height: 1.2;
      }

      .kpi-category {
        grid-column: 1 / -1;
        min-width: 0;
      }

      .kpi-category + .kpi-category {
        margin-top: 22px;
      }

      body.tv-mode .kpi-category-title {
        display: block !important;
        margin: 4px 0 8px;
        padding: 5px 0 4px;
        font-size: 10px;
      }

      body.tv-mode .kpi-category + .kpi-category {
        margin-top: 10px;
      }

      .kpi-grid { 
        display: grid; 
        grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); 
        gap: 16px; 
        position: relative; 
      }

      .kpi-card { 
        border-radius: 14px; 
        padding: 18px 22px 14px 22px; 
        display: flex; 
        flex-direction: column; 
        justify-content: space-between; 
        min-height: 140px; 
        box-shadow: var(--shadow); 
        border: 1.5px solid transparent; 
        cursor: pointer; 
        position: relative; 
        z-index: 1; 
        overflow: visible !important; 
      }

      .kpi-card:hover { 
        transform: translateY(-2px); 
        box-shadow: 0 6px 20px rgba(0,0,0,0.08); 
        z-index: 50 !important; 
      }

      .kpi-success { 
        background-color: #e6f4ea; 
        border-color: #ceead6; 
        color: #137333; 
      }
      body.dark-mode .kpi-success { 
        background-color: #13271d; 
        border-color: #1b4332; 
        color: #81c995; 
      }

      .kpi-danger { 
        background-color: #fce8e6; 
        border-color: #fad2cf; 
        color: #c5221f; 
      }
      body.dark-mode .kpi-danger { 
        background-color: #2c1615; 
        border-color: #5c2826; 
        color: #f28b82; 
      }
      .kpi-warning { background-color: #fff8e1; border-color: #ffe082; color: #8d6e00; }
      body.dark-mode .kpi-warning { background-color: #3a2f12; border-color: #6b5818; color: #ffca28; }
      body.dark-mode .kpi-card.kpi-warning .kpi-value { color: #ffffff !important; }

      /* Estado neutro: usado quando o indicador não possui dados no contexto atual. */
      .kpi-neutral {
        background-color: #f3f4f6;
        border-color: #d9dde3;
        color: #9aa1ab;
      }
      body.dark-mode .kpi-neutral {
        background-color: #25282d;
        border-color: #3b4048;
        color: #8f97a3;
      }
      .kpi-card.kpi-neutral .kpi-info-btn { color: #9aa1ab; }
      body.dark-mode .kpi-card.kpi-neutral .kpi-info-btn { color: #8f97a3; }

      .kpi-header { 
        display: flex; 
        align-items: center; 
        justify-content: space-between; 
        position: relative; 
        width: 100%; 
      }

      .kpi-title { 
        font-size: 11px; 
        font-weight: 700; 
        text-transform: uppercase; 
        letter-spacing: 0.5px; 
        display: inline-flex;
        align-items: center;
        gap: 7px;
        min-width: 0;
      }

      /* Ícones dos indicadores: identidade visual laranja do painel. */
      .kpi-title-icon {
        width: 24px;
        height: 24px;
        min-width: 24px;
        border-radius: 7px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        background: #fff0e9;
        color: #f4511e;
        font-size: 12px;
        line-height: 1;
      }
      .kpi-title-icon i { color: #f4511e !important; }
      .kpi-title-icon.icon-status-success {
        background: #e8f5e9 !important;
        color: #2e7d32 !important;
      }
      .kpi-title-icon.icon-status-success i { color: #2e7d32 !important; }
      .kpi-title-icon.icon-status-danger {
        background: #ffebee !important;
        color: #c62828 !important;
      }
      .kpi-title-icon.icon-status-danger i { color: #c62828 !important; }
      .kpi-title-icon.icon-status-warning {
        background: #fff8e1 !important;
        color: #f9a825 !important;
      }
      .kpi-title-icon.icon-status-warning i { color: #f9a825 !important; }
      .kpi-title-icon.icon-status-neutral {
        background: #f1f3f5 !important;
        color: #8a929b !important;
      }
      .kpi-title-icon.icon-status-neutral i { color: #8a929b !important; }
      body.dark-mode .kpi-title-icon {
        background: rgba(244,81,30,0.16);
        color: #ff8a65;
      }
      body.dark-mode .kpi-title-icon i { color: #ff8a65 !important; }
      body.dark-mode .kpi-title-icon.icon-status-success {
        background: rgba(46,125,50,0.18) !important;
        color: #66bb6a !important;
      }
      body.dark-mode .kpi-title-icon.icon-status-success i { color: #66bb6a !important; }
      body.dark-mode .kpi-title-icon.icon-status-danger {
        background: rgba(198,40,40,0.18) !important;
        color: #ef5350 !important;
      }
      body.dark-mode .kpi-title-icon.icon-status-danger i { color: #ef5350 !important; }
      body.dark-mode .kpi-title-icon.icon-status-warning {
        background: rgba(249,168,37,0.18) !important;
        color: #ffca28 !important;
      }
      body.dark-mode .kpi-title-icon.icon-status-warning i { color: #ffca28 !important; }
      body.dark-mode .kpi-title-icon.icon-status-neutral {
        background: rgba(138,146,155,0.16) !important;
        color: #b0b7bf !important;
      }
      body.dark-mode .kpi-title-icon.icon-status-neutral i { color: #b0b7bf !important; }
      /* Cor do ícone acompanha sempre o status do card. */
      .kpi-card.kpi-success .kpi-title-icon { background: #e8f5e9 !important; color: #2e7d32 !important; }
      .kpi-card.kpi-success .kpi-title-icon i { color: #2e7d32 !important; }
      .kpi-card.kpi-danger .kpi-title-icon { background: #ffebee !important; color: #c62828 !important; }
      .kpi-card.kpi-danger .kpi-title-icon i { color: #c62828 !important; }
      .kpi-card.kpi-warning .kpi-title-icon { background: #fff8e1 !important; color: #f9a825 !important; }
      .kpi-card.kpi-warning .kpi-title-icon i { color: #f9a825 !important; }
      .kpi-card.kpi-neutral .kpi-title-icon { background: #f1f3f5 !important; color: #8a929b !important; }
      .kpi-card.kpi-neutral .kpi-title-icon i { color: #8a929b !important; }
      body.dark-mode .kpi-card.kpi-success .kpi-title-icon { background: rgba(46,125,50,0.18) !important; color: #66bb6a !important; }
      body.dark-mode .kpi-card.kpi-success .kpi-title-icon i { color: #66bb6a !important; }
      body.dark-mode .kpi-card.kpi-danger .kpi-title-icon { background: rgba(198,40,40,0.18) !important; color: #ef5350 !important; }
      body.dark-mode .kpi-card.kpi-danger .kpi-title-icon i { color: #ef5350 !important; }
      body.dark-mode .kpi-card.kpi-warning .kpi-title-icon { background: rgba(249,168,37,0.18) !important; color: #ffca28 !important; }
      body.dark-mode .kpi-card.kpi-warning .kpi-title-icon i { color: #ffca28 !important; }
      body.dark-mode .kpi-card.kpi-neutral .kpi-title-icon { background: rgba(138,146,155,0.16) !important; color: #b0b7bf !important; }
      body.dark-mode .kpi-card.kpi-neutral .kpi-title-icon i { color: #b0b7bf !important; }

      .kpi-title-text {
        min-width: 0;
      }

      .kpi-info-wrapper { 
        position: relative; 
        display: inline-flex; 
        align-items: center; 
        justify-content: center; 
      }

      .kpi-info-btn { 
        font-size: 14px; 
        opacity: 0.85; 
        cursor: pointer; 
        transition: opacity 0.2s ease, transform 0.2s ease; 
        padding: 2px; 
        color: #137333; 
      }
      body.dark-mode .kpi-info-btn { color: #81c995; }
      .kpi-card.kpi-danger .kpi-info-btn { color: #c5221f; }
      body.dark-mode .kpi-card.kpi-danger .kpi-info-btn { color: #f28b82; }
      .kpi-card.kpi-warning .kpi-info-btn { color: #f9a825 !important; }
      body.dark-mode .kpi-card.kpi-warning .kpi-info-btn { color: #ffca28 !important; }
      .kpi-info-btn:hover { opacity: 1; transform: scale(1.15); }

      .kpi-tooltip-box { 
        visibility: hidden; 
        opacity: 0; 
        width: 280px; 
        background-color: #ffffff; 
        color: #2b3648; 
        padding: 14px; 
        border-radius: 12px; 
        border: 1px solid #f0f0f0; 
        position: absolute; 
        top: 130%; 
        right: 0; 
        z-index: 999999 !important; 
        box-shadow: 0 10px 25px rgba(0,0,0,0.15); 
        transition: opacity 0.2s ease, visibility 0.2s ease, transform 0.2s ease; 
        transform: translateY(-5px); 
        pointer-events: none; 
        text-transform: none; 
        letter-spacing: normal; 
      }

      body.dark-mode .kpi-tooltip-box { 
        background-color: #1e1e1e; 
        color: #e2e8f0; 
        border-color: #333; 
      }

      .kpi-tooltip-box::after { 
        content: ""; 
        position: absolute; 
        bottom: 100%; 
        right: 6px; 
        border-width: 6px; 
        border-style: solid; 
        border-color: transparent transparent #ffffff transparent; 
      }
      body.dark-mode .kpi-tooltip-box::after { 
        border-color: transparent transparent #1e1e1e transparent; 
      }

      .kpi-info-wrapper:hover .kpi-tooltip-box { 
        visibility: visible; 
        opacity: 1; 
        transform: translateY(0); 
      }

      .tooltip-title { 
        font-size: 12px; 
        font-weight: 700; 
        color: #f4511e; 
        margin-bottom: 6px; 
        display: flex; 
        align-items: center; 
        gap: 6px; 
      }

      .tooltip-desc { 
        font-size: 11px; 
        line-height: 1.4; 
        color: var(--text-muted); 
        margin-bottom: 10px; 
        text-transform: uppercase; 
        font-weight: 600; 
      }

      .tooltip-divider { 
        height: 1px; 
        background-color: var(--border-color); 
        margin: 8px 0; 
      }

      .tooltip-calc-title { 
        font-size: 10px; 
        font-weight: 700; 
        text-transform: uppercase; 
        color: var(--text-muted); 
        margin-bottom: 6px; 
        letter-spacing: 0.5px; 
      }

      .tooltip-calc { 
        font-size: 11px; 
        font-weight: 700; 
        color: #f4511e; 
        background-color: #fce8e6; 
        border: 1px solid #fad2cf; 
        padding: 8px 10px; 
        border-radius: 8px; 
        text-align: center; 
      }

      body.dark-mode .tooltip-calc { 
        background-color: #3d231d; 
        border-color: #5c2826; 
        color: #ff7043; 
      }

      .kpi-body-row { 
        display: flex; 
        align-items: flex-start; 
        justify-content: space-between; 
        margin-top: 8px; 
      }

      .kpi-value-container { 
        display: flex; 
        flex-direction: column; 
        gap: 6px; 
      }

      .kpi-value { 
        font-size: 32px; 
        font-weight: 700; 
        letter-spacing: -0.5px; 
        line-height: 1; 
      }

      /* Indicador sem dado: o traço fica neutro e discreto. */
      .kpi-value.no-data {
        color: #b8bec7 !important;
      }
      body.dark-mode .kpi-value.no-data {
        color: #8b939f !important;
      }

      .kpi-trend { 
        font-size: 11px; 
        font-weight: 700; 
        display: flex; 
        align-items: center; 
        gap: 5px; 
      }

      .trend-up { color: #137333; } body.dark-mode .trend-up { color: #81c995; }
      .trend-down { color: #c5221f; } body.dark-mode .trend-down { color: #f28b82; }
      .trend-neutral { color: var(--text-muted); }

      .kpi-progress-container { 
        width: 100%; 
        height: 6px; 
        background-color: rgba(0, 0, 0, 0.08); 
        border-radius: 4px; 
        margin-top: 14px; 
        overflow: hidden; 
      }

      body.dark-mode .kpi-progress-container { 
        background-color: rgba(255, 255, 255, 0.15); 
      }

      .kpi-progress-bar { 
        height: 100%; 
        border-radius: 4px; 
        width: 0%; 
        transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1); 
      }

      .bar-success { background-color: #137333; } body.dark-mode .bar-success { background-color: #81c995; }
      .bar-danger { background-color: #c5221f; } body.dark-mode .bar-danger { background-color: #f28b82; }
      .bar-warning { background-color: #f9a825; } body.dark-mode .bar-warning { background-color: #ffca28; }
      .bar-neutral { background-color: #cfd4da !important; }
      body.dark-mode .bar-neutral { background-color: #4b515a !important; }

      .modal-overlay { 
        display: none; 
        position: fixed; 
        top: 0; 
        left: 0; 
        width: 100%; 
        height: 100%; 
        background-color: rgba(0, 0, 0, 0.6); 
        z-index: 999999; 
        justify-content: center; 
        align-items: center; 
        padding: 20px; 
      }

      .modal-container { 
        background-color: var(--bg-card); 
        width: 98%; 
        max-width: 1500px; 
        border-radius: 16px; 
        box-shadow: 0 10px 30px rgba(0,0,0,0.3); 
        display: flex; 
        flex-direction: column; 
        overflow: hidden; 
        animation: modalFadeIn 0.3s ease; 
      }

      .modal-container-tam { 
        width: 92%; 
        max-width: 1100px; 
      }

      @keyframes modalFadeIn { 
        from { opacity: 0; transform: translateY(-15px); } 
        to { opacity: 1; transform: translateY(0); } 
      }

      .modal-header { 
        padding: 20px 24px; 
        display: flex; 
        align-items: center; 
        justify-content: space-between; 
        border-bottom: 1px solid var(--border-color); 
      }

      .modal-title-area { 
        display: flex; 
        align-items: center; 
        gap: 12px; 
      }

      .modal-icon-box { 
        width: 38px; 
        height: 38px; 
        border-radius: 10px; 
        background-color: var(--accent-badge); 
        color: var(--accent-badge-text); 
        display: flex; 
        align-items: center; 
        justify-content: center; 
        font-size: 16px; 
      }

      .modal-title-text h3 { 
        font-size: 16px; 
        font-weight: 700; 
        color: var(--text-main); 
        text-transform: uppercase; 
      }

      .modal-title-text p { 
        font-size: 12px; 
        color: var(--text-muted); 
      }

      .modal-close-btn { 
        background: none; 
        border: none; 
        font-size: 18px; 
        color: var(--text-muted); 
        cursor: pointer; 
        padding: 6px; 
      }

      .modal-close-btn:hover { color: var(--primary); }

      .modal-body { 
        padding: 24px; 
        display: flex; 
        flex-direction: column; 
        gap: 20px; 
        max-height: 85vh; 
        overflow-y: auto; 
      }

      .modal-cards-row { 
        display: grid; 
        grid-template-columns: 1fr 1fr; 
        gap: 16px; 
      }

      @media (max-width: 600px) { 
        .modal-cards-row { grid-template-columns: 1fr; } 
      }

      .modal-highlight-box { 
        border-radius: 12px; 
        padding: 16px 20px; 
        display: flex; 
        flex-direction: column; 
        gap: 8px; 
        border: 1.5px solid transparent; 
        position: relative; 
      }

      .box-success { background-color: #e6f4ea; border-color: #ceead6; color: #137333; }
      body.dark-mode .box-success { background-color: #13271d; border-color: #1b4332; color: #81c995; }
      .box-danger { background-color: #fce8e6; border-color: #fad2cf; color: #c5221f; }
      body.dark-mode .box-danger { background-color: #2c1615; border-color: #5c2826; color: #f28b82; }
      .box-neutral { background-color: var(--bg-input); border-color: var(--border-color); color: var(--text-main); }

      .modal-box-header { display: flex; align-items: center; justify-content: space-between; }
      .modal-box-title { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; opacity: 0.8; }
      .modal-box-ideal-badge { font-size: 11px; font-weight: 700; padding: 4px 10px; border-radius: 6px; background-color: rgba(0,0,0,0.06); display: inline-flex; align-items: center; gap: 5px; }
      body.dark-mode .modal-box-ideal-badge { background-color: rgba(255,255,255,0.1); }
      .modal-box-row { display: flex; align-items: baseline; gap: 12px; }
      .modal-box-value { font-size: 26px; font-weight: 700; line-height: 1; }
      .modal-box-diff { font-size: 12px; font-weight: 700; padding: 2px 8px; border-radius: 4px; }
      .diff-positive { background-color: rgba(19, 115, 51, 0.15); color: #137333; } body.dark-mode .diff-positive { color: #81c995; }
      .diff-negative { background-color: rgba(197, 34, 31, 0.15); color: #c5221f; } body.dark-mode .diff-negative { color: #f28b82; }
      .modal-box-msg { font-size: 11.5px; font-weight: 500; margin-top: 2px; }
      .modal-meta-info-wrapper {
        position: relative;
        display: inline-flex;
        align-items: center;
      }
      .modal-meta-info-btn {
        width: 24px;
        height: 24px;
        border-radius: 7px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        background: #fff3e0;
        color: #f4511e;
        border: 1px solid #ffe0b2;
        font-size: 12px;
        cursor: help;
      }
      body.dark-mode .modal-meta-info-btn {
        background: #3e2723;
        color: #ff7043;
        border-color: #5d4037;
      }
      .modal-meta-tooltip {
        position: absolute;
        top: calc(100% + 8px);
        right: 0;
        width: 285px;
        padding: 12px 14px;
        background: var(--bg-card);
        color: var(--text-main);
        border: 1px solid var(--border-color);
        border-radius: 10px;
        box-shadow: 0 8px 24px rgba(0,0,0,.14);
        z-index: 1000;
        opacity: 0;
        visibility: hidden;
        transform: translateY(-4px);
        transition: opacity .16s ease, transform .16s ease, visibility .16s ease;
        pointer-events: none;
        font-size: 11px;
        line-height: 1.55;
      }
      .modal-meta-info-wrapper:hover .modal-meta-tooltip {
        opacity: 1;
        visibility: visible;
        transform: translateY(0);
      }
      .modal-meta-tooltip-title {
        display: flex;
        align-items: center;
        gap: 7px;
        font-size: 11px;
        font-weight: 800;
        color: #f4511e;
        margin-bottom: 7px;
        text-transform: uppercase;
      }
      .modal-meta-tooltip-row {
        display: block;
        padding: 2px 0;
        font-weight: 600;
      }
      .modal-meta-tooltip-row span { font-weight: 800; }
      .modal-meta-tooltip-row i { width: 18px; color: #f4511e; }
      body.dark-mode .modal-meta-tooltip-row i { color: #ff7043; }

      /* Tooltips explicativos das colunas GAP no analítico de Ponto por Cabeça */
      .th-com-tooltip { position: relative; white-space: nowrap; }
      .th-tooltip-icon {
        position: relative; display: inline-flex; align-items: center; justify-content: center;
        width: 16px; height: 16px; margin-left: 5px; border-radius: 50%;
        color: #f4511e; background: rgba(255,255,255,.92); cursor: help;
        vertical-align: middle; box-shadow: 0 1px 3px rgba(0,0,0,.18);
      }
      .th-tooltip-icon > i { font-size: 10px; line-height: 1; }
      .th-tooltip-box {
        position: fixed; z-index: 2147483647; left: 0; top: 0; transform: none;
        width: 310px; padding: 10px 12px; border-radius: 8px;
        background: #fff; color: #46505a; border: 1px solid #e2e5e8;
        box-shadow: 0 8px 22px rgba(0,0,0,.20); font-size: 11px; font-weight: 500;
        line-height: 1.45; text-align: left; white-space: normal; opacity: 0;
        visibility: hidden; pointer-events: none; transition: opacity .15s ease, visibility .15s ease;
      }
      .th-com-tooltip:hover .th-tooltip-box,
      .th-tooltip-icon:hover .th-tooltip-box,
      .th-tooltip-icon:focus .th-tooltip-box { opacity: 1; visibility: visible; }
      body.dark-mode .th-tooltip-icon { color: #ff7043; background: #252a31; }
      body.dark-mode .th-tooltip-box { background: #252a31; color: #e8eaed; border-color: #3a4048; }
      .th-tooltip-box::before {
        content: ''; position: absolute; bottom: -6px; top: auto; left: 50%; transform: translateX(-50%) rotate(45deg);
        width: 10px; height: 10px; background: inherit; border-right: 1px solid #e2e5e8; border-bottom: 1px solid #e2e5e8;
      }
      body.dark-mode .th-tooltip-box::before { border-color: #3a4048; }


      .formula-visual-container { display: flex; align-items: center; justify-content: center; gap: 8px; margin-top: 8px; width: 100%; }
      .formula-card-item { background-color: var(--bg-card); border: 1px solid var(--border-color); border-radius: 10px; padding: 8px 12px; display: flex; align-items: center; gap: 10px; flex: 1; box-shadow: 0 2px 6px rgba(0,0,0,0.03); transition: transform 0.2s ease, box-shadow 0.2s ease; }
      .formula-card-item:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,0,0,0.08); }
      .formula-icon-badge { width: 32px; height: 32px; border-radius: 8px; display: flex; align-items: center; justify-content: center; font-size: 14px; flex-shrink: 0; }
      .badge-orange { background-color: #fff3e0; color: #f4511e; } body.dark-mode .badge-orange { background-color: #3e2723; color: #ff7043; }
      .badge-blue { background-color: #e3f2fd; color: #1e88e5; } body.dark-mode .badge-blue { background-color: #0d47a1; color: #64b5f6; }
      .badge-purple { background-color: #f3e5f5; color: #8e24aa; } body.dark-mode .badge-purple { background-color: #4a148c; color: #ba68c8; }
      .badge-green { background-color: #e8f5e9; color: #2e7d32; } body.dark-mode .badge-green { background-color: #1b5e20; color: #81c995; }
      .formula-card-content { display: flex; flex-direction: column; gap: 3px; }
      .formula-card-label { font-size: 9.5px; font-weight: 700; color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.3px; display: block; }
      .formula-card-val { font-size: 16px; font-weight: 700; color: var(--text-main); line-height: 1.1; display: block; }
      .formula-circle-op { width: 22px; height: 22px; border-radius: 50%; background-color: var(--bg-card); border: 1px solid var(--border-color); color: var(--text-muted); display: flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 700; box-shadow: 0 2px 4px rgba(0,0,0,0.05); flex-shrink: 0; }

      .modal-chart-section { background-color: var(--bg-input); border: 1px solid var(--border-color); border-radius: 12px; padding: 16px; }
      .modal-chart-title { font-size: 11.5px; font-weight: 700; text-transform: uppercase; color: var(--text-muted); margin-bottom: 12px; display: flex; align-items: center; gap: 6px; }
      .modal-chart-title i { color: var(--primary); }
      .chart-container { position: relative; width: 100%; height: 240px; }
      .modal-table-section { background-color: var(--bg-input); border: 1px solid var(--border-color); border-radius: 12px; padding: 16px; }
      .table-responsive { max-height: 500px; overflow-y: auto; width: 100%; position: relative; }
      .analitica-table { width: 100%; border-collapse: separate; border-spacing: 0; font-size: 11.5px; white-space: nowrap; }
      .analitica-table th { background-color: var(--primary); color: #ffffff; padding: 10px 8px; text-align: center; font-weight: 700; position: sticky; top: 0; z-index: 10; text-transform: uppercase; cursor: pointer; user-select: none; transition: background-color 0.2s ease; border-bottom: 2px solid var(--border-color); }
      .analitica-table th:first-child { text-align: left; padding-left: 12px; } 
      .analitica-table th:hover { background-color: var(--primary-hover); }
      .analitica-table th i { margin-left: 6px; opacity: 0.4; transition: opacity 0.2s ease; font-size: 11px; }
      .analitica-table th.active-sort i { opacity: 1; }
      .analitica-table td { padding: 10px 8px; border-bottom: 1px solid var(--border-color); color: var(--text-main); text-align: center; } 
      .analitica-table td:first-child { text-align: left; padding-left: 12px; font-weight: 500; } 
      .analitica-table tbody tr:hover { background-color: rgba(0,0,0,0.04); }

      .sla-group-toggle { display:inline-flex; align-items:center; justify-content:center; gap:5px; border:0; background:transparent; color:#fff; font:inherit; font-weight:700; text-transform:uppercase; cursor:pointer; padding:0; white-space:nowrap; }
      .sla-group-toggle i { font-size:10px !important; margin-left:2px !important; opacity:1 !important; transition:transform .2s ease; }
      .sla-group-toggle[aria-expanded="true"] i { transform:rotate(180deg); }
      .sla-detail-hidden { display:none !important; }
      .sla-agrupada-table th.sla-detail-th, .sla-agrupada-table td[class*="sla-detail-"] { vertical-align:middle; }
      .sla-summary-cell { font-weight:700; text-align:center; white-space:nowrap; }
      .sla-summary-cell.td-success { background-color:#e8f5e9 !important; color:#16823b !important; }
      .sla-summary-cell.td-danger { background-color:#fde9e7 !important; color:#d93025 !important; }
      .sla-detail-th { background-color:#f4511e !important; }
      .sla-percent-cell { font-weight:700; white-space:nowrap; }
      .sla-agrupada-table { min-width: max-content; }
      .sla-agrupada-table th, .sla-agrupada-table td { white-space:nowrap; }\n      body.dark-mode .analitica-table tbody tr:hover { background-color: rgba(255,255,255,0.04); }
      .td-success { background-color: rgba(19, 115, 51, 0.12) !important; color: #137333 !important; font-weight: 700; }
      .td-danger { background-color: rgba(197, 34, 31, 0.12) !important; color: #c5221f !important; font-weight: 700; }
      body.dark-mode .td-success { background-color: rgba(129, 201, 149, 0.15) !important; color: #81c995 !important; }
      body.dark-mode .td-danger { background-color: rgba(242, 139, 130, 0.15) !important; color: #f28b82 !important; }
      .btn-link-externo { display: inline-flex; align-items: center; justify-content: center; gap: 10px; background-color: var(--primary); color: #ffffff; padding: 10px 20px; border-radius: 8px; text-decoration: none; font-weight: 700; font-size: 12px; white-space: nowrap; transition: all 0.2s ease; box-shadow: 0 4px 12px rgba(244, 81, 30, 0.25); animation: btnAnaliticoPulse 3.6s ease-in-out infinite; transform-origin: center; }
      .btn-link-externo:hover { background-color: var(--primary-hover); transform: translateY(-2px); box-shadow: 0 6px 16px rgba(244, 81, 30, 0.35); color: #ffffff; }
      .btn-link-externo:hover { animation-play-state: paused; }
      .btn-link-externo:active { transform: translateY(0) scale(0.96); box-shadow: 0 2px 7px rgba(244, 81, 30, 0.28); }
      @keyframes btnAnaliticoPulse {
        0%, 72%, 100% { transform: translateY(0) scale(1); }
        76% { transform: translateY(-1px) scale(1.015); }
        79% { transform: translateY(0) scale(0.985); }
        82% { transform: translateY(0) scale(1.01); }
        86% { transform: translateY(0) scale(1); }
      }
      @media (prefers-reduced-motion: reduce) { .btn-link-externo { animation: none; } }

      /* BADGE: DADOS DO MÊS ATUAL NOS MODAIS */
      .badge-dados-mes {
        display: inline-flex;
        align-items: center;
        gap: 5px;
        background-color: var(--accent-badge);
        color: var(--accent-badge-text);
        font-size: 10px;
        font-weight: 700;
        padding: 3px 8px;
        border-radius: 6px;
        text-transform: uppercase;
        letter-spacing: 0.5px;
        margin-left: 10px;
      }

      /* ESTILIZAÇÃO DA TABELA RAIO X GERAL */
      .raiox-table-wrapper {
        background-color: var(--bg-card);
        border-radius: 14px;
        padding: 16px;
        border: 1px solid var(--border-color);
        box-shadow: var(--shadow);
        margin-top: 28px;
        margin-bottom: 24px;
        width: 100%;
        overflow-x: hidden;
      }

      .raiox-table-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 16px;
      }

      .raiox-table-title {
        font-size: 14px;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 0.5px;
        color: var(--text-main);
        display: flex;
        align-items: center;
        gap: 10px;
      }

      .raiox-table-title i {
        color: var(--primary);
      }

      .raiox-table-wrapper .table-responsive {
        max-height: 550px;
        overflow-y: auto;
        overflow-x: hidden;
        width: 100%;
      }

      .raiox-table-wrapper .analitica-table {
        width: 100% !important;
        table-layout: auto !important;
        border-collapse: separate;
        border-spacing: 0;
      }

      .raiox-table-wrapper .analitica-table th {
        background-color: var(--primary);
        color: #ffffff;
        padding: 8px 4px !important;
        text-align: center;
        font-weight: 700;
        font-size: 12px !important;
        position: sticky;
        top: 0;
        z-index: 10;
        text-transform: uppercase;
        cursor: pointer;
        line-height: 1.1;
        vertical-align: middle;
        border-bottom: 2px solid var(--border-color);
        white-space: normal;
      }

      .raiox-table-wrapper .analitica-table th i {
        display: inline-block;
        margin-left: 3px;
        font-size: 10px !important;
        vertical-align: middle;
      }

      .raiox-table-wrapper .analitica-table td {
        padding: 6px 4px !important;
        border-bottom: 1px solid var(--border-color);
        color: var(--text-main);
        text-align: center;
        font-size: 11px !important;
        vertical-align: middle;
        line-height: 1.1;
        white-space: nowrap;
      }

      .raiox-table-wrapper .analitica-table td:first-child,
      .raiox-table-wrapper .analitica-table th:first-child {
        text-align: left;
        padding-left: 8px !important;
        padding-right: 8px !important;
        white-space: nowrap !important;
      }

      .td-warning {
        background-color: rgba(251, 192, 45, 0.2) !important;
        color: #b06000 !important;
        font-weight: 700;
      }
      body.dark-mode .td-warning {
        background-color: rgba(253, 216, 53, 0.15) !important;
        color: #fdd835 !important;
      }


      /* TOOLTIP EXECUTIVO — CRITÉRIO DE DESEMPATE */
      .ranking-tooltip {
        position: relative;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        margin-left: 5px;
        cursor: help;
        z-index: 1200;
        vertical-align: middle;
      }
      .ranking-tooltip-trigger {
        width: 18px;
        height: 18px;
        border-radius: 5px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        color: #e65100;
        background: #fff4e8;
        border: 1px solid #ffd4ad;
        font-size: 9px;
        transition: transform .2s ease, background .2s ease, color .2s ease, box-shadow .2s ease;
      }
      .ranking-tooltip:hover .ranking-tooltip-trigger,
      .ranking-tooltip:focus-within .ranking-tooltip-trigger {
        transform: scale(1.05);
        background: #f4511e;
        color: #fff;
        box-shadow: 0 3px 10px rgba(244,81,30,.22);
      }
      .ranking-tooltip-content {
        position: absolute;
        left: 0;
        top: calc(100% + 10px);
        transform: translateY(-4px);
        width: 265px;
        padding: 13px 14px 14px;
        border-radius: 10px;
        background: #ffffff;
        color: #25313b;
        box-shadow: 0 12px 30px rgba(31,45,61,.20);
        border: 1px solid #dfe4e8;
        font-size: 10px;
        font-weight: 700;
        line-height: 1.4;
        letter-spacing: .05px;
        text-transform: none;
        text-align: left;
        opacity: 0;
        visibility: hidden;
        pointer-events: none;
        transition: opacity .18s ease, transform .18s ease, visibility .18s ease;
      }
      .ranking-tooltip-content::before {
        content: '';
        position: absolute;
        top: -5px;
        left: 9px;
        width: 10px;
        height: 10px;
        background: #ffffff;
        border-left: 1px solid #dfe4e8;
        border-top: 1px solid #dfe4e8;
        transform: rotate(45deg);
      }
      .ranking-tooltip:hover .ranking-tooltip-content,
      .ranking-tooltip:focus-within .ranking-tooltip-content {
        opacity: 1;
        visibility: visible;
        transform: translateY(0);
      }
      .ranking-tooltip-title {
        display: flex;
        align-items: center;
        gap: 7px;
        margin-bottom: 5px;
        color: #26343e;
        font-size: 10px;
        font-weight: 900;
        letter-spacing: .2px;
        text-transform: uppercase;
      }
      .ranking-tooltip-title i {
        color: #f4511e;
        font-size: 11px;
      }
      .ranking-tooltip-desc {
        display: block;
        color: #60707d;
        font-size: 9px;
        font-weight: 600;
        line-height: 1.45;
        margin-bottom: 10px;
      }
      .ranking-tooltip-divider {
        height: 1px;
        background: #e7eaed;
        margin: 0 -2px 9px;
      }
      .ranking-tooltip-label {
        display: block;
        margin-bottom: 5px;
        color: #596975;
        font-size: 8px;
        font-weight: 900;
        letter-spacing: .55px;
        text-transform: uppercase;
      }
      .ranking-tooltip-calc {
        display: block;
        padding: 8px 9px;
        border-radius: 6px;
        background: #fff0ec;
        border: 1px solid #ffcfc4;
        color: #e5432a;
        font-size: 9px;
        font-weight: 900;
        line-height: 1.35;
        text-align: center;
        text-transform: uppercase;
      }
      body.dark-mode .ranking-tooltip-trigger {
        color: #ff9b63;
        background: #272c31;
        border-color: #4c555e;
      }
      body.dark-mode .ranking-tooltip:hover .ranking-tooltip-trigger,
      body.dark-mode .ranking-tooltip:focus-within .ranking-tooltip-trigger {
        background: #f4511e;
        color: #fff;
      }
      body.dark-mode .ranking-tooltip-content {
        background: #20252a !important;
        color: #eef1f3 !important;
        border-color: #414950 !important;
        box-shadow: 0 14px 32px rgba(0,0,0,.42) !important;
      }
      body.dark-mode .ranking-tooltip-content::before {
        background: #20252a !important;
        border-color: #414950 !important;
      }
      body.dark-mode .ranking-tooltip-title { color: #f5f7f8; }
      body.dark-mode .ranking-tooltip-desc { color: #b9c2c9; }
      body.dark-mode .ranking-tooltip-divider { background: #3a4249; }
      body.dark-mode .ranking-tooltip-label { color: #bfc7cd; }
      body.dark-mode .ranking-tooltip-calc {
        background: rgba(244,81,30,.13);
        border-color: rgba(244,81,30,.38);
        color: #ff8f67;
      }
      body.tv-mode .ranking-tooltip-trigger { width: 21px; height: 21px; font-size: 10px; }
      body.tv-mode .ranking-tooltip-content { width: 300px; font-size: 11px; padding: 14px 16px 15px; }
      body.tv-mode .ranking-tooltip-title { font-size: 11px; }
      body.tv-mode .ranking-tooltip-desc { font-size: 10px; }
      body.tv-mode .ranking-tooltip-label { font-size: 9px; }
      body.tv-mode .ranking-tooltip-calc { font-size: 10px; }

      /* ============================================================
         RANKING EXECUTIVO — LEADERBOARD ANIMADO
         Gráfico horizontal executivo, com destaque por ranking,
         percentil relativo e animação contínua e discreta.
         ============================================================ */
      .ranking-executive-wrapper {
        margin: 28px 0 30px;
        padding: 0;
      }
      .ranking-executive-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
        gap: 18px;
        margin-bottom: 14px;
      }
      .ranking-executive-title {
        display: flex;
        align-items: center;
        gap: 11px;
        color: var(--text-main);
        font-size: 15px;
        font-weight: 900;
        letter-spacing: .55px;
        text-transform: uppercase;
      }
      .ranking-executive-title .ranking-crown {
        width: 32px;
        height: 32px;
        border-radius: 9px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        background: linear-gradient(135deg, #ffb300, #f4511e);
        color: #fff;
        box-shadow: 0 5px 14px rgba(244,81,30,.22);
        font-size: 15px;
      }
      .ranking-executive-subtitle {
        color: var(--text-muted);
        font-size: 10px;
        font-weight: 700;
        letter-spacing: .3px;
        text-transform: uppercase;
      }
      .ranking-executive-grid {
        display: grid;
        grid-template-columns: minmax(0, 1fr) minmax(0, 1fr);
        gap: 18px;
      }
      .ranking-executive-card {
        position: relative;
        overflow: visible;
        min-width: 0;
        background: var(--bg-card);
        border: 1px solid var(--border-color);
        border-radius: 16px;
        box-shadow: var(--shadow);
        padding: 16px 18px 14px;
      }
      .ranking-executive-card::before {
        content: '';
        border-radius: 16px 16px 0 0;
        position: absolute;
        inset: 0 0 auto 0;
        height: 4px;
        background: linear-gradient(90deg, #f4511e, #ffb300, #f4511e);
      }
      .ranking-executive-card.recolhimento::before {
        background: linear-gradient(90deg, #f4511e, #ffb300, #f4511e);
      }
      .ranking-executive-card-head {
        display: flex;
        align-items: center;
        justify-content: space-between;
        gap: 12px;
        padding-bottom: 11px;
        border-bottom: 1px solid var(--border-color);
      }
      .ranking-executive-card-title {
        display: flex;
        align-items: center;
        gap: 10px;
        min-width: 0;
      }
      .ranking-executive-icon {
        width: 34px;
        height: 34px;
        flex: 0 0 34px;
        border-radius: 10px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        background: #fff3e0;
        color: #e65100;
        font-size: 15px;
      }
      .recolhimento .ranking-executive-icon {
        background: #fff3e0;
        color: #e65100;
      }
      .ranking-executive-card-title strong {
        display: block;
        color: var(--text-main);
        font-size: 13px;
        font-weight: 900;
        letter-spacing: .45px;
        line-height: 1.1;
      }
      .ranking-executive-card-title span {
        display: block;
        margin-top: 3px;
        color: var(--text-muted);
        font-size: 9px;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: .35px;
      }
      .ranking-total-badge {
        flex: 0 0 auto;
        padding: 5px 8px;
        border-radius: 999px;
        background: var(--bg-body);
        color: var(--text-muted);
        border: 1px solid var(--border-color);
        font-size: 9px;
        font-weight: 900;
        white-space: nowrap;
      }
      .ranking-board {
        display: flex;
        flex-direction: column;
        gap: 5px;
        margin-top: 13px;
      }
      .ranking-board-head {
        display: grid;
        grid-template-columns: 82px minmax(0, 1fr) 110px;
        align-items: center;
        gap: 10px;
        padding: 0 12px 6px;
        color: var(--text-muted);
        font-size: 8px;
        font-weight: 900;
        letter-spacing: .55px;
        text-transform: uppercase;
      }
      .ranking-board-head span:last-child { text-align: right; }
      .ranking-board-row {
        --rank-progress: 0%;
        position: relative;
        display: grid;
        grid-template-columns: 82px minmax(0, 1fr) 110px;
        align-items: center;
        gap: 10px;
        min-height: 38px;
        padding: 6px 12px;
        border: 1px solid transparent;
        border-radius: 10px;
        background: var(--bg-body);
        overflow: hidden;
        isolation: isolate;
        transition: transform .22s ease, border-color .22s ease, box-shadow .22s ease, background .22s ease;
      }
      .ranking-board-row::before {
        content: '';
        position: absolute;
        z-index: -2;
        left: 0;
        top: 0;
        bottom: 0;
        width: calc(var(--rank-progress) + 18px);
        background: linear-gradient(90deg, rgba(244,81,30,.10) 0%, rgba(244,81,30,.055) 68%, rgba(244,81,30,.018) 88%, transparent 100%);
        border: 0 !important;
        box-shadow: none !important;
        transition: width 1.1s cubic-bezier(.22,.8,.2,1);
      }
      .ranking-executive-card.recolhimento .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(67,160,71,.11) 0%, rgba(67,160,71,.055) 68%, rgba(67,160,71,.018) 88%, transparent 100%);
        border: 0 !important;
        box-shadow: none !important;
      }
      .ranking-board-row::after {
        content: '';
        position: absolute;
        z-index: -1;
        top: 0;
        bottom: 0;
        left: -22%;
        width: 20%;
        opacity: 0;
        transform: skewX(-18deg);
        background: linear-gradient(90deg, transparent, rgba(255,255,255,.52), transparent);
        pointer-events: none;
      }
      .ranking-board-row.ranking-pulse::after {
        animation: rankingSweep 1.8s ease-in-out;
      }
      @keyframes rankingSweep {
        0% { left: -22%; opacity: 0; }
        18% { opacity: .18; }
        52% { opacity: .72; }
        82% { opacity: .16; }
        100% { left: 108%; opacity: 0; }
      }
      .ranking-board-row:hover,
      .ranking-board-row.ranking-active {
        border-color: var(--border-color);
        transform: translateX(3px);
        box-shadow: 0 5px 14px rgba(0,0,0,.055);
      }
      .ranking-board-position {
        display: flex;
        align-items: center;
        gap: 7px;
        min-width: 0;
      }
      .ranking-board-number {
        width: 29px;
        height: 29px;
        flex: 0 0 29px;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        border-radius: 8px;
        background: #e9edf2;
        color: #475569;
        font-size: 9px;
        font-weight: 900;
      }.ranking-board-row.rank-1 .ranking-board-number {
        width: 32px;
        height: 32px;
        flex-basis: 32px;
        background: linear-gradient(135deg, #f7c948, #e9a51a);
        color: #fff;
        box-shadow: 0 3px 8px rgba(214,158,30,.25);
      }.ranking-board-row.rank-2 .ranking-board-number { background: #e6e9ee; color: #59636f; }.ranking-board-row.rank-3 .ranking-board-number { background: #f0dfd0; color: #8a5733; }
      .ranking-board-place {
        color: var(--text-muted);
        font-size: 8.5px;
        font-weight: 900;
        letter-spacing: .15px;
        white-space: nowrap;
      }
      .ranking-board-name-wrap {
        min-width: 0;
        display: flex;
        flex-direction: column;
        gap: 3px;
      }
      .ranking-board-name {
        min-width: 0;
        color: var(--text-main);
        font-size: 10.5px;
        font-weight: 850;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
      .ranking-mini-track {
        height: 3px;
        width: min(100%, 280px);
        border-radius: 99px;
        overflow: hidden;
        background: rgba(127,127,127,.12);
      }
      .ranking-mini-fill {
        height: 100%;
        width: 0;
        border-radius: inherit;
        background: linear-gradient(90deg, #f4511e, #ffb300);
        transition: width 1.15s cubic-bezier(.22,.8,.2,1);
      }
      .ranking-executive-card.recolhimento .ranking-mini-fill {
        background: linear-gradient(90deg, #43a047, #8bc34a);
      }
      .ranking-board-award {
        display: flex;
        align-items: center;
        justify-content: flex-end;
        gap: 7px;
      }
      .ranking-board-percent {
        color: var(--text-main);
        font-size: 9.5px;
        font-weight: 950;
        min-width: 39px;
        text-align: right;
      }
      .ranking-board-stars {
        color: #f5a623;
        font-size: 10px;
        line-height: 1;
        letter-spacing: 0;
        white-space: nowrap;
      }
      .ranking-board-award-label {
        color: var(--text-muted);
        font-size: 8px;
        font-weight: 800;
        text-transform: uppercase;
      }
      .ranking-empty {
        padding: 28px 12px;
        text-align: center;
        color: var(--text-muted);
        font-size: 11px;
        font-weight: 700;
      }
      .ranking-motion-note {
        display: inline-flex;
        align-items: center;
        gap: 6px;
        margin-left: auto;
        color: var(--text-muted);
        font-size: 8px;
        font-weight: 800;
        text-transform: uppercase;
        letter-spacing: .35px;
      }
      .ranking-motion-dot {
        width: 6px;
        height: 6px;
        border-radius: 50%;
        background: #f4511e;
        box-shadow: 0 0 0 0 rgba(244,81,30,.35);
        animation: rankingDot 2.2s ease-in-out infinite;
      }
      .recolhimento .ranking-motion-dot { background: #43a047; box-shadow: 0 0 0 0 rgba(67,160,71,.35); }
      @keyframes rankingDot {
        0%, 100% { box-shadow: 0 0 0 0 rgba(244,81,30,.35); opacity: .72; }
        50% { box-shadow: 0 0 0 5px rgba(244,81,30,0); opacity: 1; }
      }
      body.dark-mode .ranking-executive-card { background: #1b1b1b; border-color: #343434; }
      body.dark-mode .ranking-board-head { color: #9b9b9b; }
      body.dark-mode .ranking-board-row { background: #242424; }
      body.dark-mode .ranking-board-row::before { background: linear-gradient(90deg, rgba(244,81,30,.16), rgba(244,81,30,.035)); }
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before { background: linear-gradient(90deg, rgba(67,160,71,.17), rgba(67,160,71,.035)); }
      body.dark-mode .ranking-board-row:hover,
      body.dark-mode .ranking-board-row.ranking-active { border-color: #414141; box-shadow: 0 5px 16px rgba(0,0,0,.25); }
      body.dark-mode .ranking-board-number { background: #343434; color: #d1d1d1; }body.dark-mode .ranking-board-row.rank-1 .ranking-board-number { background: linear-gradient(135deg, #d5a92f, #a87916); color: #fff; }body.dark-mode .ranking-board-row.rank-2 .ranking-board-number { background: #3b3d40; color: #d6d8dc; }body.dark-mode .ranking-board-row.rank-3 .ranking-board-number { background: #49372b; color: #e4b994; }
      body.dark-mode .ranking-executive-icon { background: #3d281d; color: #ffab75; }
      body.dark-mode .recolhimento .ranking-executive-icon { background: #1d3823; color: #9be7a4; }
      body.dark-mode .ranking-mini-track { background: rgba(255,255,255,.10); }
      body.dark-mode .ranking-board-row::after { background: linear-gradient(90deg, transparent, rgba(255,255,255,.16), transparent); }
      /* ============================================================
         RANKING EXECUTIVO — PALETA EXECUTIVA BRISANET v4
         Mais contraste, barras mais visíveis e animação em laranja.
         ============================================================ */
      /* ============================================================
         RANKING EXECUTIVO — PALETA EXECUTIVA BRISANET v4
         Mais contraste, barras mais visíveis e animação em laranja.
         ============================================================ */
      .ranking-executive-subtitle,
      .ranking-motion-note { display: none !important; }

      .ranking-executive-card::before,
      .ranking-executive-card.recolhimento::before {
        height: 5px;
        background: linear-gradient(90deg, #e65100 0%, #f4511e 48%, #ffb300 100%) !important;
        box-shadow: 0 1px 8px rgba(244,81,30,.22);
      }

      .ranking-executive-icon,
      .recolhimento .ranking-executive-icon {
        background: linear-gradient(135deg, #fff0e5, #ffe0cf) !important;
        color: #e65100 !important;
        box-shadow: inset 0 0 0 1px rgba(244,81,30,.12), 0 3px 8px rgba(244,81,30,.08);
      }

      .ranking-board-row {
        background: linear-gradient(90deg, #f3eee9 0%, #faf9f8 58%, #f5f6f7 100%) !important;
        border-color: rgba(210, 204, 198, .9) !important;
        box-shadow: inset 0 0 0 1px rgba(255,255,255,.65);
      }

      /* Barra de desempenho mais evidente — padrão laranja para os dois rankings */
      .ranking-board-row::before,
      .ranking-executive-card.recolhimento .ranking-board-row::before {
        z-index: -2;
        width: var(--rank-progress);
        background: linear-gradient(90deg,
          rgba(230,81,0,.34) 0%,
          rgba(244,81,30,.24) 72%,
          rgba(255,179,0,.15) 100%) !important;
        border-right: 2px solid rgba(244,81,30,.42) !important;
        box-shadow: 8px 0 18px rgba(244,81,30,.06);
      }

      .ranking-board-row::after {
        z-index: 3;
        left: -28%;
        width: 18%;
        background: linear-gradient(90deg,
          transparent 0%,
          rgba(255,255,255,.10) 25%,
          rgba(255,179,0,.78) 50%,
          rgba(255,255,255,.12) 75%,
          transparent 100%) !important;
        filter: blur(.15px);
      }

      @keyframes rankingSweep {
        0% { left: -28%; opacity: 0; }
        16% { opacity: .12; }
        42% { opacity: .55; }
        58% { opacity: .72; }
        84% { opacity: .16; }
        100% { left: 112%; opacity: 0; }
      }

      .ranking-board-row:hover,
      .ranking-board-row.ranking-active {
        background: linear-gradient(90deg, #fff0e5 0%, #fff7f1 55%, #f8f8f8 100%) !important;
        border-color: #f4511e !important;
        transform: translateX(4px) !important;
        box-shadow: 0 7px 20px rgba(244,81,30,.16), inset 3px 0 0 #f4511e !important;
      }

      .ranking-board-row.ranking-active::before {
        background: linear-gradient(90deg,
          rgba(230,81,0,.48),
          rgba(244,81,30,.30),
          rgba(255,179,0,.18)) !important;
        border-right-color: rgba(244,81,30,.72) !important;
      }/* Primeiro lugar: destaque forte {
        background: linear-gradient(90deg, #ffe2c9 0%, #fff0e5 50%, #fffaf7 100%) !important;
        border-color: #f59b61 !important;
        box-shadow: 0 7px 20px rgba(244,81,30,.16), inset 4px 0 0 #e65100 !important;
      }/* 2º e 3º do Multiskill continuam diferenciados {
        background: linear-gradient(90deg, #f5eee9 0%, #faf9f8 65%, #f4f5f6 100%) !important;
        border-color: #ddd5cf !important;
      }

      .ranking-board-number {
        background: #ece7e2 !important;
        color: #5f5148 !important;
        border: 1px solid rgba(130,105,88,.12);
      }.ranking-board-row.rank-1 .ranking-board-number {
        background: linear-gradient(135deg, #ffb300, #e65100) !important;
        color: #fff !important;
        box-shadow: 0 4px 10px rgba(230,81,0,.28);
      }.ranking-board-row.rank-2 .ranking-board-number,
.ranking-board-row.rank-3 .ranking-board-number {
        background: linear-gradient(135deg, #fff0e5, #f6d9c7) !important;
        color: #b84d18 !important;
      }.ranking-board-place { color: #6d625b; }

      .ranking-board-name { color: #243044 !important; }
      .ranking-board-percent { color: #1f2937 !important; }
      .ranking-board-stars { color: #e65100 !important; text-shadow: 0 1px 0 rgba(255,255,255,.6); }

      .ranking-mini-track {
        height: 4px !important;
        background: rgba(126, 100, 82, .14) !important;
        box-shadow: inset 0 1px 2px rgba(0,0,0,.05);
      }
      .ranking-mini-fill,
      .ranking-executive-card.recolhimento .ranking-mini-fill {
        background: linear-gradient(90deg, #e65100, #f4511e, #ffb300) !important;
        box-shadow: 0 0 7px rgba(244,81,30,.18);
      }

      /* Dark mode: laranja ainda mais forte para não desaparecer na TV */
      body.dark-mode .ranking-executive-card {
        background: #171717 !important;
        border-color: #383838 !important;
      }
      body.dark-mode .ranking-board-row {
        background: linear-gradient(90deg, #292522 0%, #242424 62%, #202020 100%) !important;
        border-color: #3f3935 !important;
        box-shadow: inset 0 0 0 1px rgba(255,255,255,.025);
      }
      body.dark-mode .ranking-board-row::before,
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(244,81,30,.38), rgba(244,81,30,.18), rgba(255,179,0,.08)) !important;
        border-right-color: rgba(244,81,30,.55) !important;
      }
      body.dark-mode .ranking-board-row:hover,
      body.dark-mode .ranking-board-row.ranking-active {
        background: linear-gradient(90deg, #3a2418, #2b2521 58%, #242424) !important;
        border-color: #f4511e !important;
        box-shadow: 0 8px 22px rgba(244,81,30,.18), inset 3px 0 0 #ff6d2d !important;
      }
      body.dark-mode .ranking-board-number {
        background: #3b332e !important;
        color: #eee !important;
      }
      body.dark-mode .ranking-board-name,
      body.dark-mode .ranking-board-percent { color: #f3f4f6 !important; }
      body.dark-mode .ranking-board-place { color: #b8aaa1 !important; }
      body.dark-mode .ranking-board-stars { color: #ff9d4d !important; text-shadow: 0 0 6px rgba(244,81,30,.25); }
      body.dark-mode .ranking-mini-track { background: rgba(255,255,255,.10) !important; }
      body.dark-mode .ranking-mini-fill,
      body.dark-mode .ranking-executive-card.recolhimento .ranking-mini-fill {
        background: linear-gradient(90deg, #e65100, #ff6d2d, #ffb300) !important;
        box-shadow: 0 0 9px rgba(244,81,30,.32);
      }
      body.dark-mode .ranking-board-row::after {
        background: linear-gradient(90deg, transparent, rgba(255,179,0,.65), rgba(255,255,255,.14), transparent) !important;
      }

      /* TV: contraste adicional */
      body.tv-mode .ranking-board-row {
        min-height: 34px;
        border-radius: 9px;
      }
      body.tv-mode .ranking-board-row::before { border-right-width: 3px; }
      body.tv-mode .ranking-board-name { font-size: 9.5px; }
      body.tv-mode .ranking-board-percent { font-size: 10px; }
      body.tv-mode .ranking-board-stars { font-size: 11px; }

      @media (max-width: 900px) {
        .ranking-executive-grid { grid-template-columns: 1fr; }
      }
      @media (prefers-reduced-motion: reduce) {
        .ranking-board-row::after,
        .ranking-motion-dot { animation: none !important; }
        .ranking-board-row,
        .ranking-board-row::before,
        .ranking-mini-fill { transition: none !important; }
      }
      body.tv-mode .ranking-executive-wrapper { margin-top: 16px; margin-bottom: 18px; }
      body.tv-mode .ranking-executive-header { margin-bottom: 9px; }
      body.tv-mode .ranking-executive-title { font-size: 13px; }
      body.tv-mode .ranking-executive-card { padding: 13px 14px 11px; border-radius: 13px; }
      body.tv-mode .ranking-board { margin-top: 10px; gap: 4px; }
      body.tv-mode .ranking-board-head { grid-template-columns: 86px minmax(0,1fr) 76px; font-size: 7.5px; }
      body.tv-mode .ranking-board-row { grid-template-columns: 86px minmax(0,1fr) 76px; min-height: 30px; padding: 4px 8px; }
      body.tv-mode .ranking-board-name { font-size: 9px; }
      body.tv-mode .ranking-board-place { font-size: 7.5px; }
      body.tv-mode .ranking-board-stars { font-size: 9px; }
      @media (max-width: 800px) {
        .ranking-executive-grid { grid-template-columns: 1fr; }
      }
      @media (max-width: 520px) {
        .ranking-board-head, .ranking-board-row { grid-template-columns: 76px minmax(0,1fr) 64px; gap: 6px; }
        .ranking-board-award-label { display: none; }
        .ranking-board-place { font-size: 7.5px; }
        .ranking-board-name { font-size: 9px; }
      }

      /* ============================================================
         RANKING EXECUTIVO — ACABAMENTO PREMIUM
         Mais contraste, profundidade e animação visível sem exagero.
         ============================================================ */
      .ranking-executive-header { margin-bottom: 16px; }
      .ranking-executive-card {
        background: linear-gradient(145deg, var(--bg-card) 0%, rgba(255,255,255,.96) 100%);
        border-color: rgba(42,48,56,.14);
        box-shadow: 0 10px 28px rgba(31,38,46,.10), 0 2px 7px rgba(31,38,46,.06);
      }
      .ranking-executive-card.multiskill::before {
        height: 5px;
        background: linear-gradient(90deg, #e65100 0%, #ff7a00 45%, #ffc107 100%);
      }
      .ranking-executive-card.recolhimento::before {
        height: 5px;
        background: linear-gradient(90deg, #1b7f3a 0%, #43a047 48%, #c0ca33 100%);
      }
      .ranking-executive-icon {
        background: linear-gradient(145deg, #fff0df, #ffd6ad) !important;
        color: #d84315 !important;
        box-shadow: inset 0 0 0 1px rgba(216,67,21,.10), 0 5px 13px rgba(216,67,21,.14);
      }
      .ranking-executive-card.recolhimento .ranking-executive-icon {
        background: linear-gradient(145deg, #e2f5e7, #bfe7c8) !important;
        color: #1b7f3a !important;
        box-shadow: inset 0 0 0 1px rgba(27,127,58,.10), 0 5px 13px rgba(27,127,58,.14);
      }
      .ranking-board-row {
        background: linear-gradient(100deg, #f3f5f7 0%, #eef1f4 52%, #f8f9fa 100%);
        border-color: rgba(68,78,88,.12);
        box-shadow: inset 0 1px 0 rgba(255,255,255,.75), 0 2px 5px rgba(40,48,58,.045);
      }
      .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(230,81,0,.30) 0%, rgba(255,122,0,.18) 55%, rgba(255,193,7,.06) 100%);
        border-right: 1px solid rgba(230,81,0,.28);
      }
      .ranking-executive-card.recolhimento .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(27,127,58,.30) 0%, rgba(67,160,71,.18) 55%, rgba(192,202,51,.07) 100%);
        border-right-color: rgba(27,127,58,.27);
      }
      .ranking-board-row::after {
        width: 25%;
        left: -28%;
        background: linear-gradient(90deg, transparent, rgba(255,255,255,.20), rgba(255,255,255,.90), rgba(255,255,255,.20), transparent);
        filter: blur(.2px);
      }
      .ranking-board-row.ranking-active {
        transform: translateX(4px) scale(1.006);
        border-color: rgba(230,81,0,.42);
        box-shadow: 0 8px 20px rgba(230,81,0,.13), inset 0 0 0 1px rgba(255,255,255,.55);
      }
      .ranking-executive-card.recolhimento .ranking-board-row.ranking-active {
        border-color: rgba(27,127,58,.44);
        box-shadow: 0 8px 20px rgba(27,127,58,.13), inset 0 0 0 1px rgba(255,255,255,.55);
      }
      .ranking-board-row.ranking-active .ranking-board-name {
        font-weight: 950;
      }
      .ranking-board-row.ranking-active .ranking-board-percent,
      .ranking-board-row.ranking-active .ranking-board-stars {
        filter: brightness(1.10);
      }
      .ranking-mini-track {
        height: 4px;
        background: rgba(44,52,61,.13);
        box-shadow: inset 0 1px 2px rgba(0,0,0,.06);
      }
      .ranking-mini-fill {
        background: linear-gradient(90deg, #e65100, #ff7a00, #ffc107);
        box-shadow: 0 0 7px rgba(255,122,0,.26);
      }
      .ranking-executive-card.recolhimento .ranking-mini-fill {
        background: linear-gradient(90deg, #1b7f3a, #43a047, #c0ca33);
        box-shadow: 0 0 7px rgba(67,160,71,.25);
      }.ranking-board-row.rank-1 .ranking-board-number {
        box-shadow: 0 4px 11px rgba(214,158,30,.34), inset 0 1px 0 rgba(255,255,255,.35);
      }
      .ranking-board-stars {
        text-shadow: 0 1px 0 rgba(255,255,255,.65), 0 0 5px rgba(245,166,35,.18);
      }
      /* Mantém a animação visível: o sweep percorre a linha inteira e o pulso dura um pouco mais. */
      @keyframes rankingSweep {
        0% { left: -28%; opacity: 0; }
        14% { opacity: .15; }
        38% { opacity: .72; }
        58% { opacity: .92; }
        78% { opacity: .22; }
        100% { left: 112%; opacity: 0; }
      }
      .ranking-board-row.ranking-pulse::after { animation: rankingSweep 2.05s ease-in-out; }

      body.dark-mode .ranking-executive-card {
        background: linear-gradient(145deg, #181a1c 0%, #202327 100%);
        border-color: #343a40;
        box-shadow: 0 12px 28px rgba(0,0,0,.30), 0 2px 8px rgba(0,0,0,.24);
      }
      body.dark-mode .ranking-board-row {
        background: linear-gradient(100deg, #252a2f 0%, #2b3035 55%, #202428 100%);
        border-color: #3b4248;
        box-shadow: inset 0 1px 0 rgba(255,255,255,.035), 0 3px 7px rgba(0,0,0,.20);
      }
      body.dark-mode .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(230,81,0,.38), rgba(255,122,0,.18), rgba(255,193,7,.06));
        border-right-color: rgba(255,122,0,.30);
      }
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(27,127,58,.40), rgba(67,160,71,.20), rgba(192,202,51,.06));
        border-right-color: rgba(67,160,71,.30);
      }
      body.dark-mode .ranking-board-row::after {
        background: linear-gradient(90deg, transparent, rgba(255,255,255,.08), rgba(255,255,255,.55), rgba(255,255,255,.08), transparent);
      }
      body.dark-mode .ranking-board-row.ranking-active {
        border-color: #e66a2c;
        box-shadow: 0 9px 24px rgba(230,81,0,.18), inset 0 0 0 1px rgba(255,255,255,.05);
      }
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.ranking-active {
        border-color: #55b86a;
        box-shadow: 0 9px 24px rgba(67,160,71,.18), inset 0 0 0 1px rgba(255,255,255,.05);
      }
      body.dark-mode .ranking-mini-track { background: rgba(255,255,255,.11); }
      body.dark-mode .ranking-mini-fill { box-shadow: 0 0 9px rgba(255,122,0,.30); }
      body.dark-mode .ranking-executive-card.recolhimento .ranking-mini-fill { box-shadow: 0 0 9px rgba(67,160,71,.28); }

      /* MÓDULO: GRÁFICOS DE QUARTIS POR LIDERANÇA */
      .charts-quartil-wrapper {
        margin-top: 28px;
        margin-bottom: 28px;
        display: flex;
        flex-direction: column;
        gap: 16px;
      }

      .charts-quartil-header {
        font-size: 14px;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 0.5px;
        color: var(--text-main);
        display: flex;
        align-items: center;
        gap: 10px;
      }

      .charts-quartil-header i {
        color: var(--primary);
      }

      .charts-quartil-grid {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 20px;
      }

      .charts-quartil-grid-single {
        grid-template-columns: 1fr;
      }

      .chart-quartil-card-drilldown .chart-quartil-card-title {
        justify-content: space-between;
      }

      .quartil-drilldown-btn {
        min-height: 28px;
        padding: 5px 9px;
        border: 1px solid var(--border-color);
        border-radius: 8px;
        background: var(--bg-card);
        color: var(--primary);
        cursor: pointer;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        gap: 7px;
        font-family: Inter, sans-serif;
        font-size: 10px;
        font-weight: 700;
        white-space: nowrap;
        transition: all .2s ease;
      }

      .quartil-drilldown-btn:hover {
        background: var(--primary);
        color: #fff;
        transform: translateY(-1px);
      }

      @media (max-width: 900px) {
        .charts-quartil-grid {
          grid-template-columns: 1fr;
        }
      }

      .chart-quartil-card {
        background-color: var(--bg-card);
        border-radius: 14px;
        padding: 20px;
        border: 1px solid var(--border-color);
        box-shadow: var(--shadow);
        display: flex;
        flex-direction: column;
        gap: 12px;
      }

      .chart-quartil-card-title {
        font-size: 12px;
        font-weight: 700;
        text-transform: uppercase;
        color: var(--text-muted);
        display: flex;
        align-items: center;
        gap: 8px;
      }

      .chart-quartil-card-title i {
        color: var(--primary);
      }

      .chart-quartil-canvas-container {
        position: relative;
        width: 100%;
        height: 320px;
      }
    
      /* CORREÇÃO FINAL — GRÁFICO DE QUARTIS RESPONSIVO */
      .charts-quartil-wrapper,
      .charts-quartil-grid,
      .charts-quartil-grid-single,
      .chart-quartil-card,
      .chart-quartil-canvas-container {
        min-width: 0 !important;
        max-width: 100% !important;
        box-sizing: border-box !important;
      }

      .charts-quartil-wrapper {
        width: 100% !important;
        overflow: hidden !important;
      }

      .charts-quartil-grid,
      .charts-quartil-grid-single {
        width: 100% !important;
        overflow: hidden !important;
      }

      .chart-quartil-card {
        width: 100% !important;
        overflow: hidden !important;
      }

      .chart-quartil-card-title {
        min-width: 0 !important;
        max-width: 100% !important;
      }

      .chart-quartil-canvas-container {
        position: relative !important;
        width: 100% !important;
        height: 320px !important;
        overflow: hidden !important;
      }

      .chart-quartil-canvas-container canvas {
        display: block !important;
        width: 100% !important;
        max-width: 100% !important;
        min-width: 0 !important;
        height: 100% !important;
        box-sizing: border-box !important;
      }

      /* Evita que o botão do gráfico force uma largura maior que a tela */
      .chart-quartil-card-drilldown .chart-quartil-card-title {
        min-width: 0 !important;
        max-width: 100% !important;
        flex-wrap: wrap;
      }

      .chart-quartil-card-drilldown .quartil-drilldown-btn {
        max-width: 100%;
        box-sizing: border-box;
        overflow: hidden;
        text-overflow: ellipsis;
      }

      /* Mesmo comportamento em telas menores */
      @media (max-width: 900px) {
        .charts-quartil-wrapper,
        .charts-quartil-grid,
        .chart-quartil-card {
          width: 100% !important;
          min-width: 0 !important;
          max-width: 100% !important;
        }

        .chart-quartil-canvas-container {
          width: 100% !important;
          min-width: 0 !important;
          max-width: 100% !important;
        }
      }

      /* BARRA DE CONTROLE — MODO TV */
      .tv-control-bar {
        display: none;
        position: fixed;
        left: 16px;
        right: 16px;
        bottom: 12px;
        height: 54px;
        z-index: 9998;
        align-items: center;
        gap: 14px;
        padding: 8px 14px;
        border: 1px solid #d8dde3;
        border-radius: 12px;
        background: rgba(255,255,255,0.96);
        box-shadow: 0 8px 28px rgba(0,0,0,0.18);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        color: var(--text-main);
      }

      body.tv-mode .tv-control-bar {
        display: flex;
      }

      .tv-top-progress {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        height: 4px;
        overflow: hidden;
        border-radius: 12px 12px 0 0;
        background: rgba(216, 221, 227, 0.75);
        pointer-events: none;
      }

      .tv-top-progress-fill {
        width: 0%;
        height: 100%;
        background: var(--primary);
        border-radius: inherit;
        transition: width .9s linear;
      }

      .tv-control-left,
      .tv-control-right,
      .tv-control-nav,
      .tv-control-actions {
        display: flex;
        align-items: center;
        gap: 8px;
      }

      .tv-control-left {
        flex: 0 0 auto;
        min-width: 205px;
      }

      .tv-control-right {
        margin-left: auto;
        flex: 0 0 auto;
        gap: 14px;
      }

      .tv-control-nav {
        min-width: 170px;
        justify-content: flex-end;
      }

      .tv-control-actions {
        flex: 0 0 auto;
      }

      .tv-control-btn {
        width: 34px;
        height: 34px;
        border: 1px solid #d7dce2;
        border-radius: 9px;
        background: #ffffff;
        color: #46515f;
        cursor: pointer;
        display: inline-flex;
        align-items: center;
        justify-content: center;
        font-size: 13px;
        transition: all .18s ease;
      }

      .tv-control-btn:hover {
        background: #f4f6f8;
        border-color: #c3cad2;
        transform: translateY(-1px);
      }

      .tv-control-btn.tv-pause-btn {
        width: auto;
        padding: 0 12px;
        gap: 7px;
        font-weight: 700;
      }

      .tv-progress-dots {
        display: flex;
        align-items: center;
        gap: 5px;
        max-width: 95px;
        overflow: hidden;
      }

      .tv-progress-dot {
        width: 6px;
        height: 6px;
        min-width: 6px;
        border-radius: 50%;
        background: #b8c0c9;
        opacity: .65;
      }

      .tv-progress-dot.active {
        width: 18px;
        min-width: 18px;
        border-radius: 4px;
        background: var(--primary);
        opacity: 1;
      }

      .tv-position-label,
      .tv-next-label,
      .tv-clock-label,
      .tv-date-label {
        white-space: nowrap;
      }

      .tv-position-label {
        font-size: 12px;
        font-weight: 800;
        color: var(--text-main);
      }

      .tv-next-group {
        min-width: 190px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        gap: 4px;
      }

      .tv-next-label {
        min-width: 0;
        font-size: 12px;
        font-weight: 700;
        color: var(--text-muted);
        line-height: 1;
      }

      .tv-next-label strong {
        color: var(--primary);
        font-size: 14px;
      }

      .tv-next-progress {
        display: none;
      }

      .tv-next-progress-fill {
        display: none;
      }

      .tv-clock-label {
        font-size: 16px;
        font-weight: 800;
        letter-spacing: .3px;
      }

      .tv-date-label {
        font-size: 11px;
        color: var(--text-muted);
        font-weight: 600;
      }

      @media (max-width: 900px) {
        .tv-control-bar { left: 8px; right: 8px; gap: 8px; }
        .tv-control-left { min-width: 0; }
        .tv-control-right { gap: 8px; }
        .tv-date-label { display: none; }
        .tv-next-group { min-width: 125px; }
      }

      /* MODO ESCURO AZUL — inspirado no visual das telas de TV de referência */
      body.dark-mode {
        --bg-body: #071827;
        --bg-card: #10283a;
        --bg-header: #0b1d2b;
        --header-text: #f4f9fc;
        --bg-filter-bar: #0f2434;
        --bg-input: #162f43;
        --border-color: #29465a;
        --text-main: #e9f3f8;
        --text-muted: #9db0bf;
        --shadow: 0 5px 18px rgba(0,0,0,0.38);
        --accent-badge: #3a241c;
        --accent-badge-text: #ff956f;
      }

      body.dark-mode .header,
      body.dark-mode .filter-wrapper,
      body.dark-mode .collaborator-card,
      body.dark-mode .kpi-card,
      body.dark-mode .modal-content,
      body.dark-mode .modal-header,
      body.dark-mode .modal-section,
      body.dark-mode .chart-card,
      body.dark-mode .analitica-container {
        border-color: var(--border-color);
      }

      body.dark-mode .header {
        background-color: var(--bg-header);
      }

      body.dark-mode .filter-wrapper {
        background-color: var(--bg-filter-bar);
      }

      body.dark-mode .kpi-card,
      body.dark-mode .collaborator-card,
      body.dark-mode .chart-card,
      body.dark-mode .modal-content,
      body.dark-mode .analitica-container {
        background-color: var(--bg-card);
      }

      body.dark-mode .section-title-kpi,
      body.dark-mode .modal-title,
      body.dark-mode .modal-subtitle,
      body.dark-mode .analitica-table td,
      body.dark-mode .analitica-table th {
        color: var(--text-main);
      }

      body.dark-mode .tv-control-bar {
        background: rgba(7, 24, 39, 0.96);
        border-color: #31536a;
      }

      body.dark-mode .tv-next-progress {
        background: #284154;
      }

      body.dark-mode .tv-top-progress {
        background: rgba(40, 65, 84, 0.9);
      }

      body.dark-mode .kpi-success {
        background: linear-gradient(135deg, #103521 0%, #123f29 100%);
        border-color: #1f6b3d;
        color: #69f0ae;
      }
      body.dark-mode .kpi-danger {
        background: linear-gradient(135deg, #3a1719 0%, #421a1d 100%);
        border-color: #7e2e32;
        color: #ff7979;
      }
      body.dark-mode .kpi-neutral {
        background: linear-gradient(135deg, #20252c 0%, #262c34 100%);
        border-color: #424a55;
        color: #aab3bf;
      }
      body.dark-mode .kpi-card.kpi-neutral .kpi-info-btn { color: #aab3bf; }
      body.dark-mode .kpi-info-btn { color: #69f0ae; }
      body.dark-mode .kpi-card.kpi-danger .kpi-info-btn { color: #ff7979; }
      body.dark-mode .kpi-value.no-data { color: #aeb7c2 !important; }
      body.dark-mode .trend-up { color: #69f0ae; }
      body.dark-mode .trend-down { color: #ff7979; }
      body.dark-mode .bar-success { background-color: #22c55e; }
      body.dark-mode .bar-danger { background-color: #ef4444; }
      body.dark-mode .bar-neutral { background-color: #59616d !important; }
      body.dark-mode .kpi-progress-container { background-color: rgba(255,255,255,0.12); }

      body.dark-mode .theme-toggle-btn {
        background-color: #343a40;
        border-color: #2f3439;
        color: #ffffff;
        box-shadow: 0 2px 8px rgba(0,0,0,0.30);
      }
      body.dark-mode .theme-toggle-btn:hover {
        background-color: #232a31;
      }

      body.dark-mode .section-title-kpi,
      body.dark-mode .meta-item,
      body.dark-mode .colab-cities-label {
        color: #b8c1cc;
      }
      body.dark-mode .collaborator-name,
      body.dark-mode .meta-item strong {
        color: #f8fafc;
      }

      body.dark-mode .badge-q1 {
        background-color: #123c28;
        color: #69f0ae;
        border-color: #246b44;
      }
      body.dark-mode .badge-q2 {
        background-color: #103520;
        color: #69f0ae;
        border-color: #1f6039;
      }
      body.dark-mode .badge-q3 {
        background-color: #463719;
        color: #ffd166;
        border-color: #775d27;
      }
      body.dark-mode .badge-q4 {
        background-color: #421a1d;
        color: #ff7979;
        border-color: #7e2e32;
      }
      body.dark-mode .badge-ranking {
        background-color: #132c4f;
        color: #8ab4ff;
        border-color: #2e5d91;
      }

      /* HISTÓRICO DE QUARTIL DE PRODUÇÃO */
      /* HISTÓRICOS E CIDADES OCUPAM TODA A LARGURA DO CARD, ABAIXO DA FOTO + DADOS */
      .collaborator-card > .quartil-producao-history,
      .collaborator-card > .colab-cities-container {
        width: 100%;
        min-width: 0;
      }

      .quartil-producao-history {
        width: 100%;
        margin-top: 14px;
        padding-top: 0;
        border-top: 0;
        clear: both;
      }

      .quartil-producao-history-title {
        display: flex;
        align-items: center;
        gap: 7px;
        margin-bottom: 6px;
        color: var(--text-main);
        font-size: 13px;
        font-weight: 600;
      }

      .quartil-producao-history-title i {
        color: var(--primary);
        font-size: 13px;
      }

      .quartil-producao-history-table {
        width: 100%;
        table-layout: fixed;
        border-collapse: separate;
        border-spacing: 3px 3px;
        font-size: 10px;
      }

      .quartil-producao-history-table th {
        height: 27px;
        padding: 5px 3px;
        background: #f4511e;
        color: #ffffff;
        border: 1px solid #f4511e;
        border-radius: 3px;
        text-align: center;
        font-size: 13px;
        font-weight: 800;
        white-space: nowrap;
      }

      .quartil-producao-history-table td {
        height: 31px;
        padding: 6px 3px;
        border: 1px solid transparent;
        border-radius: 4px;
        text-align: center;
        font-size: 13px;
        font-weight: 800;
        white-space: nowrap;
        line-height: 1.15;
        color: #263238;
      }

      .quartil-producao-history-table td.q1 {
        background: #43a047;
        border-color: #388e3c;
        color: #ffffff;
      }

      .quartil-producao-history-table td.q2 {
        background: #c8e6c9;
        border-color: #a5d6a7;
        color: #245229;
      }

      .quartil-producao-history-table td.q3 {
        background: #ffcdd2;
        border-color: #ef9a9a;
        color: #8a1c25;
      }

      .quartil-producao-history-table td.q4 {
        background: #e57373;
        border-color: #d32f2f;
        color: #7f0000;
      }

      .quartil-producao-history-table td.qneutral {
        background: #eef0f2;
        border-color: #d8dde2;
        color: #7d8791;
      }

      body.dark-mode .quartil-producao-history-table th {
        background: #d84315;
        border-color: #d84315;
      }

      body.dark-mode .quartil-producao-history-table td.q1 {
        background: #2e7d32;
        border-color: #43a047;
        color: #e8f5e9;
      }

      body.dark-mode .quartil-producao-history-table td.q2 {
        background: #66bb6a;
        border-color: #81c784;
        color: #123d17;
      }

      body.dark-mode .quartil-producao-history-table td.q3 {
        background: #c62828;
        border-color: #e53935;
        color: #ffebee;
      }

      body.dark-mode .quartil-producao-history-table td.q4 {
        background: #b71c1c;
        border-color: #d32f2f;
        color: #ffebee;
      }

      body.dark-mode .quartil-producao-history-table td.qneutral {
        background: #343a40;
        border-color: #495057;
        color: #ced4da;
      }

      body.dark-mode .ranking-emphasis-card {
        background: #172b4d;
        border-color: #28426b;
        color: #8ab4f8;
      }
      body.dark-mode .cert-emphasis-card.badge-cert-good {
        background: #245b2a;
        border-color: #347a3b;
        color: #d8f3dc;
      }
      body.dark-mode .cert-emphasis-card.badge-cert-mid {
        background: #5a4617;
        border-color: #806622;
        color: #ffe8a3;
      }
      body.dark-mode .cert-emphasis-card.badge-cert-low {
        background: #7f2d35;
        border-color: #a8424b;
        color: #ffebee;
      }
      body.dark-mode .cert-emphasis-card.badge-neutral {
        background: #343a40;
        border-color: #495057;
        color: #ced4da;
      }

      /* MELHORIA — MODO TV/ESCURO: aumenta a leitura do card de Certificação */
      body.dark-mode.tv-mode .cert-emphasis-card .emphasis-card-icon,
      body.tv-mode.dark-mode .cert-emphasis-card .emphasis-card-icon {
        background: rgba(255,255,255,.88) !important;
        color: #5a4617 !important;
        box-shadow: 0 0 0 1px rgba(255,255,255,.22), 0 2px 6px rgba(0,0,0,.28);
      }

      body.dark-mode.tv-mode .cert-emphasis-card .emphasis-card-label,
      body.tv-mode.dark-mode .cert-emphasis-card .emphasis-card-label {
        opacity: 1 !important;
        color: #fff4c7 !important;
        text-shadow: 0 1px 2px rgba(0,0,0,.45);
      }

      body.dark-mode.tv-mode .cert-emphasis-card .emphasis-card-value,
      body.tv-mode.dark-mode .cert-emphasis-card .emphasis-card-value {
        color: #ffffff !important;
        font-weight: 900 !important;
        text-shadow: 0 1px 2px rgba(0,0,0,.45);
      }

      body.dark-mode.tv-mode .cert-emphasis-card.badge-cert-mid,
      body.tv-mode.dark-mode .cert-emphasis-card.badge-cert-mid {
        background: #684f13 !important;
        border-color: #a47f27 !important;
        border-left-color: #f5bd35 !important;
      }

      @media (max-width: 800px) {
        .collaborator-main-info {
          grid-template-columns: 92px minmax(0, 1fr);
          column-gap: 14px;
        }
        .colab-emphasis-card {
          grid-column: 2;
        }
        .ranking-emphasis-card {
          grid-row: 2;
        }
        .cert-emphasis-card {
          grid-row: 3;
        }
        .collaborator-avatar {
          width: 92px;
          height: 92px;
          min-width: 92px;
        }
      }

      @media (max-width: 560px) {
        .collaborator-main-info {
          grid-template-columns: 1fr;
          row-gap: 12px;
        }
        .colab-emphasis-card,
        .ranking-emphasis-card,
        .cert-emphasis-card {
          grid-column: 1;
          grid-row: auto;
        }
        .collaborator-avatar,
        body.tv-mode .collaborator-avatar {
          grid-column: 1;
          grid-row: 1;
          width: 96px !important;
          height: 96px !important;
          min-width: 96px !important;
        }
        .collaborator-details,
        body.tv-mode .collaborator-details {
          grid-column: 1;
          grid-row: 2;
        }
      }

      @media (max-width: 1100px) {
        .quartil-producao-history-table {
          font-size: 9px;
          border-spacing: 2px;
        }
        .quartil-producao-history-table th {
          font-size: 13px;
          padding: 4px 2px;
        }
        .quartil-producao-history-table td {
          font-size: 13px;
          padding: 5px 2px;
        }
      }


      /* AJUSTES FINOS — MODO TV: compactar o bloco superior e acomodar ranking com estrelas */
      @media (min-width: 801px) {
        body.tv-mode .collaborator-card {
          padding: 8px 10px 5px !important;
          margin-bottom: 5px !important;
        }

        body.tv-mode .collaborator-main-info {
          grid-template-columns: 118px minmax(0, 1fr) 250px 220px !important;
          column-gap: 14px !important;
        }

        body.tv-mode .quartil-producao-history {
          margin-top: 10px !important;
        }

        body.tv-mode .quartil-producao-history-title {
          margin-bottom: 4px !important;
        }

        body.tv-mode .ranking-emphasis-card .emphasis-card-value {
          font-size: 21px !important;
          white-space: normal !important;
          line-height: 1.05 !important;
          overflow-wrap: anywhere;
          word-break: normal;
        }
      }

      body.tv-mode .ranking-emphasis-card {
        min-width: 0 !important;
      }

      /* AJUSTE FINAL DA FOTO: mantém a parte inferior na mesma posição e traz o topo para dentro do card. */
      .collaborator-avatar {
        transform: none;
      }
      body.tv-mode .collaborator-avatar {
        height: 101px !important;
        transform: none !important;
      }

      /* AJUSTES FINAIS — MODO ESCURO E MODO TV */
      body.dark-mode .kpi-card .kpi-title-text {
        color: #ffffff !important;
      }

      body.dark-mode .kpi-card .kpi-value:not(.no-data) {
        color: #ffffff !important;
      }

      /* Mantém os indicadores sem dado em cinza, mesmo no modo escuro. */
      body.dark-mode .kpi-card .kpi-value.no-data {
        color: #8b939f !important;
      }

      /* A barra de progresso do Modo TV deve ser sempre laranja. */
      .tv-top-progress-fill {
        background: #f4511e !important;
      }

      body.dark-mode .tv-top-progress-fill {
        background: #f4511e !important;
      }

      /* Texto da contagem do Modo TV. */
      .tv-next-label strong {
        color: #f4511e !important;
      }
      body.tv-mode .ranking-executive-wrapper { margin-top: 16px; margin-bottom: 18px; }
      body.tv-mode .ranking-executive-header { margin-bottom: 9px; }
      body.tv-mode .ranking-executive-title { font-size: 13px; }
      body.tv-mode .ranking-executive-card { padding: 13px 14px 11px; border-radius: 13px; }
      body.tv-mode .ranking-board { margin-top: 10px; gap: 4px; }
      body.tv-mode .ranking-board-head { grid-template-columns: 82px minmax(0,1fr) 105px; font-size: 7.5px; }
      body.tv-mode .ranking-board-row { grid-template-columns: 82px minmax(0,1fr) 105px; min-height: 31px; padding: 4px 8px; }
      body.tv-mode .ranking-board-name { font-size: 9px; }
      body.tv-mode .ranking-board-place { font-size: 7.5px; }
      body.tv-mode .ranking-board-stars { font-size: 9px; }
      body.tv-mode .ranking-board-percent { font-size: 9px; }
      body.tv-mode .ranking-mini-track { height: 3px; }


      /* ============================================================
         RANKING EXECUTIVO V5 — VISUAL DIRETORIA
         - sem mini-barras sob o nome
         - gráfico principal com massa de cor mais forte
         - laranja Brisanet nos dois rankings
         - destaque de premiação: Multiskill 1º-3º / Recolhimento 1º
         ============================================================ */
      .ranking-executive-card::before,
      .ranking-executive-card.recolhimento::before {
        background: linear-gradient(90deg, #d94b00 0%, #f4511e 48%, #ff9d2e 100%) !important;
      }

      .ranking-executive-icon,
      .recolhimento .ranking-executive-icon {
        background: linear-gradient(145deg, #fff0e6, #ffd2b5) !important;
        color: #d94b00 !important;
        box-shadow: inset 0 0 0 1px rgba(217,75,0,.14), 0 4px 10px rgba(217,75,0,.12) !important;
      }
      .ranking-executive-icon i { font-size: 16px; }

      .ranking-board-row {
        background: #f4f5f7 !important;
        border-color: #d8dce1 !important;
      }

      /* A barra principal agora é a massa de cor do ranking. */
      .ranking-board-row::before,
      .ranking-executive-card.recolhimento .ranking-board-row::before {
        z-index: 0 !important;
        width: var(--rank-progress) !important;
        background: linear-gradient(90deg,
          rgba(217,75,0,.38) 0%,
          rgba(244,81,30,.28) 52%,
          rgba(255,157,46,.16) 100%) !important;
        border-right: 2px solid rgba(217,75,0,.62) !important;
        box-shadow: inset 4px 0 0 rgba(217,75,0,.16), inset 0 -1px 0 rgba(255,255,255,.35) !important;
      }

      .ranking-board-row > * { position: relative; z-index: 3; }
      .ranking-board-row::after {
        z-index: 4 !important;
        width: 17% !important;
        background: linear-gradient(90deg,
          transparent,
          rgba(255,255,255,.18) 30%,
          rgba(255,196,120,.82) 50%,
          rgba(255,255,255,.18) 70%,
          transparent) !important;
      }

      .ranking-board-row.ranking-active {
        border-color: #e35d16 !important;
        transform: translateX(4px) scale(1.003) !important;
        box-shadow: 0 8px 22px rgba(217,75,0,.16), inset 4px 0 0 #e85d04 !important;
      }
      .ranking-board-row.ranking-active::before {
        background: linear-gradient(90deg,
          rgba(217,75,0,.52) 0%,
          rgba(244,81,30,.38) 52%,
          rgba(255,157,46,.24) 100%) !important;
      }.ranking-board-row.rank-1 .ranking-board-number {
        background: linear-gradient(135deg, #ffb300, #e85d04) !important;
        box-shadow: 0 4px 12px rgba(217,75,0,.28) !important;
      }
      .ranking-board-stars { color: #d94b00 !important; text-shadow: none !important; }

      /* Remove qualquer trilha antiga que possa existir em versões anteriores. */
      .ranking-mini-track,
      .ranking-mini-fill { display: none !important; }

      /* Dark / TV: contraste mais alto sem perder o padrão laranja. */
      body.dark-mode .ranking-executive-card::before,
      body.dark-mode .ranking-executive-card.recolhimento::before {
        background: linear-gradient(90deg, #c93f00, #f4511e 50%, #ff9d2e) !important;
      }
      body.dark-mode .ranking-executive-icon,
      body.dark-mode .recolhimento .ranking-executive-icon {
        background: linear-gradient(145deg, #422416, #2e211b) !important;
        color: #ff8a45 !important;
        box-shadow: inset 0 0 0 1px rgba(255,138,69,.24), 0 4px 14px rgba(244,81,30,.16) !important;
      }
      body.dark-mode .ranking-board-row {
        background: #242628 !important;
        border-color: #3d4145 !important;
      }
      body.dark-mode .ranking-board-row::before,
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(217,75,0,.58), rgba(244,81,30,.36), rgba(255,157,46,.18)) !important;
        border-right-color: rgba(255,112,35,.75) !important;
      }
      body.dark-mode .ranking-board-row.ranking-active {
        border-color: #ff6d2d !important;
        box-shadow: 0 8px 24px rgba(244,81,30,.22), inset 4px 0 0 #ff6d2d !important;
      }
      body.dark-mode .ranking-board-name,
      body.dark-mode .ranking-board-percent { color: #f5f5f5 !important; }
      body.dark-mode .ranking-board-place { color: #b9bec4 !important; }
      body.dark-mode .ranking-board-stars { color: #ff8a45 !important; }

      /* TV mode: reforço visual para leitura à distância. */
      body.tv-mode .ranking-board-row,
      body.dark-mode.tv-mode .ranking-board-row,
      body.tv-mode.dark-mode .ranking-board-row {
        min-height: 42px;
      }
      body.tv-mode .ranking-board-row::before,
      body.dark-mode.tv-mode .ranking-board-row::before,
      body.tv-mode.dark-mode .ranking-board-row::before {
        background: linear-gradient(90deg, rgba(217,75,0,.48), rgba(244,81,30,.34), rgba(255,157,46,.18)) !important;
      }
      body.tv-mode .ranking-board-row.ranking-active,
      body.dark-mode.tv-mode .ranking-board-row.ranking-active,
      body.tv-mode.dark-mode .ranking-board-row.ranking-active {
        box-shadow: 0 10px 28px rgba(244,81,30,.22), inset 5px 0 0 #ff6d2d !important;
      }



/* ============================================================
   RANKING EXECUTIVO — PALETA PREMIUM v7
   Multiskill: Ouro / Prata / Bronze nos 3 primeiros.
   Recolhimento: Ouro somente no 1º.
   Demais posições: grafite executivo.
   ============================================================ */
.ranking-executive-card,
.ranking-executive-wrapper {
  overflow: visible !important;
}

/* Faixa superior: identidade única e premium */
.ranking-executive-card::before,
.ranking-executive-card.recolhimento::before {
  background: linear-gradient(90deg, #c77719 0%, #e39a2d 48%, #f0bd5b 100%) !important;
  height: 4px !important;
  box-shadow: 0 1px 10px rgba(199,119,25,.22) !important;
}

/* Ícone de gráfico: mais sóbrio e executivo */
.ranking-executive-icon,
.recolhimento .ranking-executive-icon {
  background: linear-gradient(145deg, #2b3138, #171b20) !important;
  color: #f0b64b !important;
  border: 1px solid rgba(240,182,75,.28) !important;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.08), 0 4px 10px rgba(0,0,0,.10) !important;
}

/* Tooltip: ícone e caixa reposicionados, com leitura clara */
.ranking-tooltip {
  margin-left: 6px !important;
  vertical-align: middle;
  z-index: 100 !important;
}
.ranking-tooltip-trigger {
  width: 16px !important;
  height: 16px !important;
  background: #2b3138 !important;
  color: #f3c76a !important;
  border: 1px solid rgba(240,182,75,.38) !important;
  font-size: 8px !important;
  box-shadow: 0 2px 6px rgba(0,0,0,.12);
}
.ranking-tooltip:hover .ranking-tooltip-trigger,
.ranking-tooltip:focus-within .ranking-tooltip-trigger {
  background: #c77719 !important;
  color: #fff !important;
  border-color: #e3a13c !important;
}
.ranking-tooltip-content {
  top: calc(100% + 10px) !important;
  left: 0 !important;
  transform: translateY(-4px) !important;
  width: 245px !important;
  padding: 11px 13px !important;
  background: #20262d !important;
  color: #f7f8fa !important;
  border: 1px solid rgba(240,182,75,.32) !important;
  box-shadow: 0 12px 28px rgba(0,0,0,.24) !important;
  font-size: 10px !important;
  font-weight: 800 !important;
  line-height: 1.45 !important;
  text-align: left !important;
}
.ranking-tooltip-content::before {
  left: 12px !important;
  background: #20262d !important;
  border-left-color: rgba(240,182,75,.32) !important;
  border-top-color: rgba(240,182,75,.32) !important;
}
.ranking-tooltip:hover .ranking-tooltip-content,
.ranking-tooltip:focus-within .ranking-tooltip-content {
  transform: translateY(0) !important;
}
.ranking-tooltip-label {
  color: #f0bd5b !important;
  margin-bottom: 4px !important;
}

/* Todas as linhas: base grafite elegante */
.ranking-board-row,
.ranking-executive-card.recolhimento .ranking-board-row {
  background: linear-gradient(90deg, #e8ebee 0%, #f3f4f5 58%, #e7eaed 100%) !important;
  border-color: #d0d5da !important;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.72), 0 2px 5px rgba(26,34,42,.06) !important;
}

/* Barra-base: grafite, para deixar o preenchimento realmente visível */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, #59636d 0%, #737e88 70%, #8b949d 100%) !important;
  border-right: 2px solid rgba(58,67,76,.52) !important;
  box-shadow: 8px 0 16px rgba(41,49,57,.10) !important;
}.ranking-board-row.rank-1 .ranking-board-number {
  background: linear-gradient(135deg, #ffe49a, #c68b16) !important;
  color: #4d3507 !important;
  border-color: rgba(121,82,10,.35) !important;
  box-shadow: 0 4px 10px rgba(139,96,12,.24) !important;
}.ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-number {
  background: linear-gradient(135deg, #f4f6f8, #aeb5bb) !important;
  color: #414a53 !important;
  border-color: #8f989f !important;
}.ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-number {
  background: linear-gradient(135deg, #e3b997, #96603e) !important;
  color: #4f2f1f !important;
  border-color: #8b5738 !important;
}

/* Animação: feixe dourado, visível sem ficar chamativo demais */
.ranking-board-row::after {
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,255,255,.05) 18%,
    rgba(255,210,105,.88) 50%,
    rgba(255,255,255,.10) 82%,
    transparent 100%) !important;
}
.ranking-board-row.ranking-active,
.ranking-board-row:hover {
  transform: translateX(4px) !important;
  border-color: #c78927 !important;
  box-shadow: 0 8px 20px rgba(174,119,27,.18), inset 4px 0 0 #c78927 !important;
}

/* No Recolhimento, somente o 1º recebe metal/premiação */
.ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) {
  background: linear-gradient(90deg, #e8ebee 0%, #f3f4f5 58%, #e7eaed 100%) !important;
  border-color: #d0d5da !important;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.72), 0 2px 5px rgba(26,34,42,.06) !important;
}

/* Texto das posições comuns */
.ranking-board-row:not(.rank-1) .ranking-board-name,
.ranking-board-row:not(.rank-1) .ranking-board-percent {
  color: #26313a !important;
}
.ranking-board-row:not(.rank-1) .ranking-board-place {
  color: #5c6873 !important;
}
.ranking-board-row:not(.rank-1) .ranking-board-stars {
  color: #b77719 !important;
}

/* Dark mode: mantém os três metais e deixa os demais em grafite */
body.dark-mode .ranking-board-row,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) {
  background: linear-gradient(90deg, #252b31 0%, #30363c 58%, #252b31 100%) !important;
  border-color: #414950 !important;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.04), 0 4px 12px rgba(0,0,0,.22) !important;
}
body.dark-mode .ranking-board-row::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, #59646f, #707c87) !important;
}
body.dark-mode .ranking-board-row:not(.rank-1) .ranking-board-name,
body.dark-mode .ranking-board-row:not(.rank-1) .ranking-board-percent {
  color: #f1f3f5 !important;
}
body.dark-mode .ranking-board-row:not(.rank-1) .ranking-board-place {
  color: #b9c0c7 !important;
}
body.dark-mode .ranking-board-row:not(.rank-1) .ranking-board-stars {
  color: #f0b84b !important;
}
body.dark-mode .ranking-tooltip-content { background: #171b20 !important; }
body.dark-mode .ranking-tooltip-content::before { background: #171b20 !important; }

/* TV: reforço de contraste */
body.tv-mode .ranking-board-row { min-height: 42px; }
body.tv-mode .ranking-board-name { font-size: 11px; }
body.tv-mode .ranking-board-place { font-size: 9px; }
body.tv-mode .ranking-tooltip-content { z-index: 999 !important; }


/* ============================================================
   RANKING EXECUTIVO — REFINO V9
   Tooltip claro no modo claro / escuro no modo escuro.
   Ícone de gráfico centralizado dentro do quadrado.
   Ouro mais vivo + barras em azul-grafite premium.
   ============================================================ */

/* Ícone: o gráfico fica REALMENTE dentro do quadrado */
.ranking-executive-icon,
.ranking-executive-icon i,
.recolhimento .ranking-executive-icon,
.recolhimento .ranking-executive-icon i {
  box-sizing: border-box !important;
}
.ranking-executive-icon {
  width: 40px !important;
  height: 40px !important;
  min-width: 40px !important;
  min-height: 40px !important;
  display: inline-flex !important;
  align-items: center !important;
  justify-content: center !important;
  position: relative !important;
  flex: 0 0 40px !important;
  overflow: hidden !important;
  line-height: 1 !important;
}
.ranking-executive-icon i {
  position: static !important;
  display: block !important;
  width: auto !important;
  height: auto !important;
  margin: 0 !important;
  padding: 0 !important;
  line-height: 1 !important;
  transform: none !important;
  font-size: 17px !important;
  color: #d98900 !important;
}

/* Tooltip — modo claro: igual à linguagem dos tooltips do dashboard */
.ranking-tooltip-content {
  background: #ffffff !important;
  color: #26313a !important;
  border: 1px solid #d9dde3 !important;
  box-shadow: 0 14px 30px rgba(23,35,48,.18) !important;
  border-radius: 9px !important;
}
.ranking-tooltip-content::before {
  background: #ffffff !important;
  border-left-color: #d9dde3 !important;
  border-top-color: #d9dde3 !important;
}
.ranking-tooltip-title {
  color: #26313a !important;
}
.ranking-tooltip-desc {
  color: #64717d !important;
}
.ranking-tooltip-label {
  color: #d77900 !important;
}
.ranking-tooltip-calc {
  background: #fff0ea !important;
  color: #e24e16 !important;
  border: 1px solid #ffd0c0 !important;
}
.ranking-tooltip-trigger {
  background: #ffffff !important;
  color: #d77900 !important;
  border-color: #e5c48b !important;
}
.ranking-tooltip:hover .ranking-tooltip-trigger,
.ranking-tooltip:focus-within .ranking-tooltip-trigger {
  background: #d77900 !important;
  color: #ffffff !important;
  border-color: #d77900 !important;
}.ranking-board-row.rank-1 .ranking-board-number {
  background: linear-gradient(135deg, #fff5bd, #f2ad00) !important;
  color: #543a00 !important;
  border-color: #c58b00 !important;
  box-shadow: 0 4px 12px rgba(160,108,0,.30) !important;
}

/* Demais barras: sai o cinza e entra azul-grafite do layout */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, #263d56 0%, #355675 55%, #4a6b89 100%) !important;
  border-right: 2px solid rgba(31,53,74,.68) !important;
  box-shadow: 10px 0 18px rgba(29,55,80,.15) !important;
}
.ranking-board-row,
.ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) {
  background: linear-gradient(90deg, #f7f9fb 0%, #eef2f5 58%, #e7edf2 100%) !important;
  border-color: #d1d9e0 !important;
}

/* Animação: feixe dourado mais visível sobre azul-grafite */
.ranking-board-row::after {
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,255,255,.06) 18%,
    rgba(255,211,74,.96) 50%,
    rgba(255,255,255,.12) 82%,
    transparent 100%) !important;
}
.ranking-board-row.ranking-active,
.ranking-board-row:hover {
  transform: translateX(4px) scale(1.003) !important;
  border-color: #d79a00 !important;
  box-shadow: 0 9px 24px rgba(39,67,94,.20), inset 5px 0 0 #d79a00 !important;
}

/* Modo escuro: tooltip volta ao dark e barras continuam azul-grafite */
body.dark-mode .ranking-tooltip-content,
body.tv-mode.dark-mode .ranking-tooltip-content,
body.dark-mode.tv-mode .ranking-tooltip-content {
  background: #171b20 !important;
  color: #f4f6f8 !important;
  border-color: #4a525a !important;
}
body.dark-mode .ranking-tooltip-content::before,
body.tv-mode.dark-mode .ranking-tooltip-content::before,
body.dark-mode.tv-mode .ranking-tooltip-content::before {
  background: #171b20 !important;
  border-left-color: #4a525a !important;
  border-top-color: #4a525a !important;
}
body.dark-mode .ranking-tooltip-title,
body.tv-mode.dark-mode .ranking-tooltip-title,
body.dark-mode.tv-mode .ranking-tooltip-title {
  color: #ffffff !important;
}
body.dark-mode .ranking-tooltip-desc,
body.tv-mode.dark-mode .ranking-tooltip-desc,
body.dark-mode.tv-mode .ranking-tooltip-desc {
  color: #b7c0c8 !important;
}
body.dark-mode .ranking-tooltip-label,
body.tv-mode.dark-mode .ranking-tooltip-label,
body.dark-mode.tv-mode .ranking-tooltip-label {
  color: #ffd54a !important;
}
body.dark-mode .ranking-tooltip-calc,
body.tv-mode.dark-mode .ranking-tooltip-calc,
body.dark-mode.tv-mode .ranking-tooltip-calc {
  background: #3a2420 !important;
  color: #ffd0c0 !important;
  border-color: #744638 !important;
}
body.dark-mode .ranking-executive-icon i,
body.tv-mode.dark-mode .ranking-executive-icon i,
body.dark-mode.tv-mode .ranking-executive-icon i {
  color: #ffd54a !important;
}
body.dark-mode .ranking-board-row:not(.rank-1),
body.tv-mode.dark-mode .ranking-board-row:not(.rank-1),
body.dark-mode.tv-mode .ranking-board-row:not(.rank-1) {
  background: linear-gradient(90deg, #1e2c39 0%, #263949 58%, #1e2c39 100%) !important;
  border-color: #3d5061 !important;
}
body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  background: linear-gradient(90deg, #294764, #39617f, #4b7899) !important;
}/* ============================================================
   RANKING EXECUTIVO v10 — TODAS AS LINHAS EM LARANJA
   Mantém medalhas/estrelas e deixa o ranking visualmente uniforme.
   ============================================================ */
.ranking-board-row,
.ranking-executive-card.recolhimento .ranking-board-row {
  background: linear-gradient(90deg, #f3a06f 0%, #f5ad7e 48%, #f7bc93 100%) !important;
  border-color: #dc7b3f !important;
  color: #3f2417 !important;
  box-shadow: inset 5px 0 0 #d65f18, 0 4px 12px rgba(190,85,25,.12) !important;
}/* Preenchimento proporcional: laranja vivo e claramente perceptível. */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, #d95f16 0%, #ed7b2f 52%, #f3a15e 100%) !important;
  border-right: 2px solid #c8540d !important;
  box-shadow: 12px 0 18px rgba(213,88,17,.18) !important;
}

/* A animação continua passando por cima, agora com um brilho dourado suave. */
.ranking-board-row::after {
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,255,255,.05) 18%,
    rgba(255,214,92,.95) 50%,
    rgba(255,244,194,.30) 66%,
    transparent 100%) !important;
}

.ranking-board-row.ranking-active,
.ranking-board-row:hover {
  border-color: #c95b16 !important;
  box-shadow: 0 9px 24px rgba(192,78,17,.20), inset 5px 0 0 #bf4d0b !important;
}/* Os três primeiros continuam reconhecidos pelo número/medalha {
  background: linear-gradient(90deg, #f3a06f 0%, #f5ad7e 48%, #f7bc93 100%) !important;
  border-color: #dc7b3f !important;
  box-shadow: inset 5px 0 0 #d65f18, 0 6px 16px rgba(190,85,25,.16) !important;
}/* Reforço visual do pódio do Multiskill sem voltar ao cinza/prata/bronze. */
.ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-number,
.ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-number,
.ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-number,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number {
  position: relative;
  z-index: 3;
}/* Dark mode / TV: mesma família laranja,
só com maior contraste. */
body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
  background: linear-gradient(90deg, #7d3218 0%, #963f1f 50%, #ab5128 100%) !important;
  border-color: #d96828 !important;
  color: #fff5ee !important;
  box-shadow: inset 5px 0 0 #ef6c20, 0 7px 18px rgba(0,0,0,.28) !important;
}

body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  background: linear-gradient(90deg, #b94e18 0%, #e06b22 55%, #f08c43 100%) !important;
  border-right-color: #ff9a5a !important;
}

body.dark-mode .ranking-board-row::after,
body.tv-mode.dark-mode .ranking-board-row::after,
body.dark-mode.tv-mode .ranking-board-row::after {
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,255,255,.06) 18%,
    rgba(255,211,74,.95) 50%,
    rgba(255,242,183,.26) 66%,
    transparent 100%) !important;
}



/* RANKING EXECUTIVO v11 — fundo claro + tooltip ao lado do nome */
.ranking-executive-card,
.ranking-executive-card-head,
.ranking-executive-card-title {
  background: #ffffff !important;
}
.ranking-name-line {
  display: inline-flex !important;
  align-items: center !important;
  gap: 4px !important;
  line-height: 1 !important;
  white-space: nowrap !important;
}
.ranking-name-line > strong {
  display: inline-block !important;
  line-height: 1 !important;
}
.ranking-name-line .ranking-tooltip {
  display: inline-flex !important;
  position: relative !important;
  margin-left: 1px !important;
  top: 0 !important;
  vertical-align: middle !important;
}
.ranking-name-line .ranking-tooltip-trigger {
  width: 15px !important;
  height: 15px !important;
  min-width: 15px !important;
  min-height: 15px !important;
  padding: 0 !important;
  border-radius: 50% !important;
  display: inline-flex !important;
  align-items: center !important;
  justify-content: center !important;
  background: #ffffff !important;
  color: #d95d10 !important;
  border: 1px solid #f0a06a !important;
}
.ranking-name-line .ranking-tooltip-trigger i {
  margin: 0 !important;
  line-height: 1 !important;
}
.ranking-name-line .ranking-tooltip-content {
  left: 0 !important;
  right: auto !important;
  top: calc(100% + 8px) !important;
  z-index: 5000 !important;
}
.ranking-name-line .ranking-tooltip-content::before {
  left: 8px !important;
}/* Todas as barras continuam laranja; o 1º também deixa de ser dourado. */
.ranking-board-row {
  background: linear-gradient(90deg, #e86a18 0%, #f27c2b 48%, #f6a261 100%) !important;
  border-color: #d95d10 !important;
  color: #fff8f2 !important;
  box-shadow: inset 5px 0 0 #c94b08, 0 3px 10px rgba(201,75,8,.15) !important;
}.ranking-board-row::before {
  background: linear-gradient(90deg, #b94206 0%, #e66317 50%, #f49a4f 100%) !important;
  border-right-color: #ad3d04 !important;
}
.ranking-board-row .ranking-board-name,
.ranking-board-row .ranking-board-place,
.ranking-board-row .ranking-board-percent {
  color: #fffaf6 !important;
}
.ranking-board-row .ranking-board-stars {
  color: #fff0d5 !important;
  text-shadow: 0 1px 2px rgba(116,47,6,.32) !important;
}

/* Modo escuro: cabeçalho continua branco como a referência enviada. */
body.dark-mode .ranking-executive-card,
body.dark-mode .ranking-executive-card-head,
body.dark-mode .ranking-executive-card-title {
  background: #ffffff !important;
}
body.dark-mode .ranking-name-line > strong {
  color: #26313a !important;
}
body.dark-mode .ranking-name-line .ranking-tooltip-trigger {
  background: #ffffff !important;
  color: #d95d10 !important;
}
body.dark-mode .ranking-name-line .ranking-tooltip-content {
  background: #171b20 !important;
  color: #eef1f3 !important;
}
body.dark-mode .ranking-name-line .ranking-tooltip-content::before {
  background: #171b20 !important;
}
body.tv-mode .ranking-name-line .ranking-tooltip-trigger {
  width: 18px !important;
  height: 18px !important;
  min-width: 18px !important;
  min-height: 18px !important;
}
body.tv-mode .ranking-name-line .ranking-tooltip-content {
  width: 300px !important;
}/* ============================================================
   RANKING EXECUTIVO v13 — LARANJA DA REFERÊNCIA VISUAL
   Base uniforme em laranja/pêssego,
com degradê suave.
   O destaque do pódio vem da medalha e da intensidade da borda.
   ============================================================ */
.ranking-board-row {
  background: linear-gradient(90deg, #ED9E74 0%, #F3A885 30%, #F8B297 52%, #FBD0AF 76%, #FCDABF 100%) !important;
  border-color: #D9855A !important;
  color: #4A2B1D !important;
  box-shadow: inset 5px 0 0 #E06B2D, 0 4px 12px rgba(170,91,43,.12) !important;
}/* Preenchimento proporcional — mesma linguagem de laranja da referência. */
.ranking-board-row::before {
  background: linear-gradient(90deg, #E98D60 0%, #F2A274 34%, #F7B58F 60%, #FAD0B0 100%) !important;
  border-right-color: #D86B36 !important;
  box-shadow: 10px 0 18px rgba(214,107,54,.16) !important;
}/* Multiskill — 1º {
  background: linear-gradient(90deg, #EA965F 0%, #F3A978 31%, #F8B997 54%, #FBD1B0 78%, #FCDABD 100%) !important;
  border-color: #E06B2D !important;
  box-shadow: inset 6px 0 0 #D95D1E, 0 7px 20px rgba(205,90,25,.19) !important;
}

/* A animação percorre o degradê com um brilho quente, sem estourar em branco. */
.ranking-board-row::after {
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,221,190,.06) 18%,
    rgba(255,203,143,.92) 50%,
    rgba(255,226,198,.24) 66%,
    transparent 100%) !important;
}

/* Textos com contraste executivo sobre o laranja claro. */
.ranking-board-row .ranking-board-name,
.ranking-board-row .ranking-board-place,
.ranking-board-row .ranking-board-percent {
  color: #4B2B1D !important;
}
.ranking-board-row .ranking-board-stars {
  color: #D96220 !important;
  text-shadow: 0 1px 1px rgba(92,48,24,.18) !important;
}

/* Dark / TV — mantém a mesma família cromática, com contraste suficiente. */
body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
  background: linear-gradient(90deg, #C56A43 0%, #D7835E 32%, #E59D7B 55%, #F0BDA0 100%) !important;
  border-color: #E18F69 !important;
  color: #2E1C15 !important;
  box-shadow: inset 5px 0 0 #F4511E, 0 6px 18px rgba(0,0,0,.30) !important;
}
body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  background: linear-gradient(90deg, #AF5230 0%, #CD714D 36%, #E08F6D 65%, #F0B79A 100%) !important;
  border-right-color: #FF9A5A !important;
}/* ============================================================
   RANKING EXECUTIVO v14 — PREENCHIMENTO PROPORCIONAL VISÍVEL
   Todas as linhas usam a mesma identidade laranja clara.
   A área preenchida representa a posição relativa; o restante
   permanece em laranja muito claro.
   ============================================================ */
.ranking-board-row {
  background: linear-gradient(90deg, #F8D2BE 0%, #F9D9C8 100%) !important;
  border-color: #E5A27E !important;
  color: #4A2B1D !important;
  box-shadow: inset 4px 0 0 #E8793B, 0 3px 10px rgba(196,91,42,.10) !important;
  overflow: hidden !important;
  isolation: isolate !important;
}/* A barra preenchida fica por cima do fundo claro e termina exatamente
   no percentual da linha. */
.ranking-board-row::before {
  z-index: 0 !important;
  background: linear-gradient(90deg, #E97838 0%, #F08B4E 48%, #F5A878 100%) !important;
  border-right: 2px solid #D96527 !important;
  box-shadow: 8px 0 16px rgba(211,94,36,.16) !important;
}

/* Conteúdo sempre acima da barra. */
.ranking-board-row > * {
  position: relative !important;
  z-index: 2 !important;
}/* Pódio continua sendo reconhecido por medalha/estrela {
  background: linear-gradient(90deg, #F8D2BE 0%, #F9D9C8 100%) !important;
}

/* Linha ativa: brilho sutil, sem mudar a identidade laranja. */
.ranking-board-row.ranking-active,
.ranking-board-row:hover {
  border-color: #D9672B !important;
  box-shadow: 0 8px 20px rgba(194,82,24,.17), inset 4px 0 0 #D85C1E !important;
}

/* Feixe da animação mais visível sobre a barra preenchida. */
.ranking-board-row::after {
  z-index: 3 !important;
  background: linear-gradient(90deg,
    transparent 0%,
    rgba(255,255,255,.08) 22%,
    rgba(255,235,205,.90) 50%,
    rgba(255,255,255,.16) 70%,
    transparent 100%) !important;
}

/* Texto escuro para leitura sobre o laranja claro. */
.ranking-board-row .ranking-board-name,
.ranking-board-row .ranking-board-place,
.ranking-board-row .ranking-board-percent {
  color: #4B2B1D !important;
}
.ranking-board-row .ranking-board-stars {
  color: #C95B18 !important;
  text-shadow: 0 1px 1px rgba(92,48,24,.15) !important;
}/* Modo escuro/TV: mesma lógica,
com fundo laranja escuro suave e
   preenchimento mais vivo. */
body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
  background: linear-gradient(90deg, #6F3B29 0%, #804633 100%) !important;
  border-color: #B96A45 !important;
  color: #FFF5EE !important;
  box-shadow: inset 4px 0 0 #F06B2A, 0 6px 18px rgba(0,0,0,.28) !important;
}
body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  z-index: 0 !important;
  background: linear-gradient(90deg, #D85E24 0%, #EA7A3D 48%, #F29A62 100%) !important;
  border-right-color: #FFAD78 !important;
}
body.dark-mode .ranking-board-row .ranking-board-name,
body.dark-mode .ranking-board-row .ranking-board-place,
body.dark-mode .ranking-board-row .ranking-board-percent,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-name,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-place,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-percent {
  color: #FFF5EE !important;
}
body.dark-mode .ranking-board-row .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-stars {
  color: #FFD0AD !important;
}/* ============================================================
         AJUSTE FINAL — RANKINGS MULTISKILL + RECOLHIMENTO
         1) As duas barras usam exatamente a mesma progressão proporcional.
         2) Fundo das linhas em laranja muito claro.
         3) Preenchimento proporcional em laranja claro,
do 1º ao 10º.
         ============================================================ */

      /* Fundo/base muito claro para TODAS as posições */
      .ranking-board-row,
.ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) {
        background: #FFF3EB !important;
        border-color: #F0C5AA !important;
        color: #5A3525 !important;
        box-shadow: inset 4px 0 0 #F0A06A, 0 3px 10px rgba(196,91,42,.08) !important;
      }/* Barra proporcional — MESMA para Multiskill e Recolhimento.
         O tamanho continua vindo de --rank-progress. */
      .ranking-board-row::before {
        z-index: 0 !important;
        width: var(--rank-progress) !important;
        background: linear-gradient(
          90deg,
          #F2A66F 0%,
          #F5B581 50%,
          #F8C9A5 100%
        ) !important;
        border-right: 2px solid #E79A63 !important;
        box-shadow: 7px 0 14px rgba(220,123,63,.12) !important;
      }

      /* Mantém o conteúdo acima da barra */
      .ranking-board-row > * {
        position: relative !important;
        z-index: 2 !important;
      }

      /* Texto adequado ao novo tom claro */
      .ranking-board-row .ranking-board-name,
      .ranking-board-row .ranking-board-place,
      .ranking-board-row .ranking-board-percent {
        color: #5A3525 !important;
      }

      .ranking-board-row .ranking-board-stars {
        color: #C96D32 !important;
        text-shadow: none !important;
      }

      /* Hover/linha ativa sem escurecer a barra inteira */
      .ranking-board-row.ranking-active,
      .ranking-board-row:hover {
        border-color: #E58A4D !important;
        box-shadow: 0 7px 18px rgba(196,91,42,.14), inset 4px 0 0 #E47D3D !important;
      }/* Modo escuro: mantém a mesma lógica proporcional,
com contraste adequado. */
      body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
        background: #4A3025 !important;
        border-color: #8F6047 !important;
        color: #FFF3EB !important;
      }

      body.dark-mode .ranking-board-row::before,
      body.tv-mode.dark-mode .ranking-board-row::before,
      body.dark-mode.tv-mode .ranking-board-row::before {
        width: var(--rank-progress) !important;
        background: linear-gradient(
          90deg,
          #D47A43 0%,
          #E89A63 50%,
          #F2B582 100%
        ) !important;
        border-right-color: #F2B582 !important;
      }

      body.dark-mode .ranking-board-row .ranking-board-name,
      body.dark-mode .ranking-board-row .ranking-board-place,
      body.dark-mode .ranking-board-row .ranking-board-percent,
      body.tv-mode.dark-mode .ranking-board-row .ranking-board-name,
      body.tv-mode.dark-mode .ranking-board-row .ranking-board-place,
      body.tv-mode.dark-mode .ranking-board-row .ranking-board-percent {
        color: #FFF3EB !important;
      }

      body.dark-mode .ranking-board-row .ranking-board-stars,
      body.tv-mode.dark-mode .ranking-board-row .ranking-board-stars {
        color: #FFD0AD !important;
      }/* ============================================================
         AJUSTE RECOLHIMENTO — MESMA TONALIDADE DO MULTISKILL
         O Recolhimento passa a usar exatamente a identidade de
         laranja claro do mockup,
sem os tons escuros atuais.
         ============================================================ */

      .ranking-executive-card.recolhimento .ranking-board-row {
        background: #FFF3EB !important;
        border-color: #F0C5AA !important;
        color: #5A3525 !important;
        box-shadow: inset 4px 0 0 #F0A06A, 0 3px 10px rgba(196,91,42,.08) !important;
      }/* Barra de progresso do Recolhimento com a mesma tonalidade
         clara usada no Multiskill. A largura continua proporcional. */
      .ranking-executive-card.recolhimento .ranking-board-row::before {
        z-index: 0 !important;
        width: var(--rank-progress) !important;
        background: linear-gradient(
          90deg,
          #F2A66F 0%,
          #F5B581 50%,
          #F8C9A5 100%
        ) !important;
        border-right: 2px solid #E79A63 !important;
        box-shadow: 7px 0 14px rgba(220,123,63,.12) !important;
      }

      /* Texto e estrelas no mesmo padrão claro do Multiskill */
      .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-name,
      .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-place,
      .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-percent {
        color: #5A3525 !important;
      }

      .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-stars {
        color: #C96D32 !important;
        text-shadow: none !important;
      }

      /* Hover/linha ativa */
      .ranking-executive-card.recolhimento .ranking-board-row.ranking-active,
      .ranking-executive-card.recolhimento .ranking-board-row:hover {
        border-color: #E58A4D !important;
        box-shadow: 0 7px 18px rgba(196,91,42,.14), inset 4px 0 0 #E47D3D !important;
      }

      /* Modo escuro/TV mantém o mesmo conceito de laranja claro,
         mas com contraste suficiente para o fundo escuro. */
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row,
      body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row {
        background: #4A3025 !important;
        border-color: #8F6047 !important;
        color: #FFF3EB !important;
      }

      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
      body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
        width: var(--rank-progress) !important;
        background: linear-gradient(
          90deg,
          #D47A43 0%,
          #E89A63 50%,
          #F2B582 100%
        ) !important;
        border-right-color: #F2B582 !important;
      }

      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-name,
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-place,
      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-percent,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-name,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-place,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-percent {
        color: #FFF3EB !important;
      }

      body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-stars,
      body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-stars {
        color: #FFD0AD !important;
      }


      /* ============================================================
         AJUSTE FINAL — RECOLHIMENTO
         • Quadradinho da posição branco
         • Somente 1º lugar com medalha
         • 2º ao 10º com estrela no quadradinho
         ============================================================ */

      .ranking-executive-card.recolhimento .ranking-board-number {
        background: #FFFFFF !important;
        color: #6B5144 !important;
        border: 1px solid #E9D8CC !important;
        box-shadow: 0 1px 4px rgba(80,45,25,.08) !important;
      }.ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number {
        background: linear-gradient(135deg, #FFD66B, #F2A52B) !important;
        color: #FFFFFF !important;
        border-color: #E7A72F !important;
        box-shadow: 0 4px 12px rgba(217,75,0,.20) !important;
      }

      /* Estrela dos lugares 2º ao 10º */
      .ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) .ranking-board-number {
        color: #D98245 !important;
        font-size: 16px !important;
        font-weight: 800 !important;
      }


      /* ============================================================
         AJUSTE FINAL — POSIÇÕES DOS RANKINGS
         • Multiskill: 4º ao 10º com estrela
         • Quadradinho de posição: branco nos dois rankings
         • 1º/2º/3º mantêm suas medalhas
         ============================================================ */

      .ranking-executive-card.multiskill .ranking-board-number,
      .ranking-executive-card.recolhimento .ranking-board-number {
        background: #FFFFFF !important;
        background-image: none !important;
        color: #6B5144 !important;
        border: 1px solid #E9D8CC !important;
        box-shadow: 0 1px 4px rgba(80,45,25,.08) !important;
      }

      /* O preenchimento da linha não deve "vazar" para o quadradinho. */
      .ranking-executive-card.multiskill .ranking-board-number,
      .ranking-executive-card.recolhimento .ranking-board-number {
        position: relative !important;
        z-index: 5 !important;
      }

      /* Multiskill 4º ao 10º: estrela branca no quadradinho. */
      .ranking-executive-card.multiskill .ranking-board-row:not(.rank-1):not(.rank-2):not(.rank-3) .ranking-board-number {
        color: #D98245 !important;
        font-size: 16px !important;
        font-weight: 800 !important;
      }

      /* Recolhimento 2º ao 10º: estrela no quadradinho. */
      .ranking-executive-card.recolhimento .ranking-board-row:not(.rank-1) .ranking-board-number {
        color: #D98245 !important;
        font-size: 16px !important;
        font-weight: 800 !important;
      }/* Medalhas continuam com destaque no pódio. */
      .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-number,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number {
        background: linear-gradient(135deg, #FFD66B, #F2A52B) !important;
        background-image: linear-gradient(135deg, #FFD66B, #F2A52B) !important;
        color: #FFFFFF !important;
        border-color: #E7A72F !important;
      }


      /* ============================================================
         CORREÇÃO DO ÍCONE DO CABEÇALHO — RECOLHIMENTO
         O quadrado sinalizado deve ficar BRANCO, sem o fundo verde.
         ============================================================ */
      .ranking-executive-card.recolhimento .ranking-executive-icon {
        background: #FFFFFF !important;
        background-image: none !important;
        color: #F28C28 !important;
        border: 1px solid #E8E8E8 !important;
        box-shadow: 0 3px 10px rgba(0,0,0,.08) !important;
      }

      .ranking-executive-card.recolhimento .ranking-executive-icon i {
        color: #F28C28 !important;
      }


      /* ============================================================
         CORREÇÃO FINAL — ÍCONES DOS CABEÇALHOS DOS RANKINGS
         Multiskill e Recolhimento com quadrado branco.
         ============================================================ */

      .ranking-executive-card.multiskill .ranking-executive-icon,
      .ranking-executive-card.recolhimento .ranking-executive-icon {
        background: #FFFFFF !important;
        background-image: none !important;
        color: #F28C28 !important;
        border: 1px solid #E8E8E8 !important;
        box-shadow: 0 3px 10px rgba(0,0,0,.08) !important;
      }

      .ranking-executive-card.multiskill .ranking-executive-icon i,
      .ranking-executive-card.recolhimento .ranking-executive-icon i {
        color: #F28C28 !important;
      }


      /* ============================================================
         AJUSTE TOOLTIP DO RANKING
         Abre PARA CIMA do ícone para não cobrir as primeiras linhas.
         ============================================================ */

      .ranking-executive-card {
        overflow: visible !important;
      }

      .ranking-executive-card-title,
      .ranking-name-line,
      .ranking-tooltip {
        position: relative !important;
      }

      .ranking-tooltip {
        z-index: 9999 !important;
      }

      .ranking-tooltip-content {
        top: auto !important;
        bottom: calc(100% + 10px) !important;
        left: 50% !important;
        transform: translate(-50%, 4px) !important;
        width: 280px !important;
        padding: 12px 14px 13px !important;
        border-radius: 10px !important;
        z-index: 10000 !important;
      }

      .ranking-tooltip:hover .ranking-tooltip-content,
      .ranking-tooltip:focus-within .ranking-tooltip-content {
        transform: translate(-50%, 0) !important;
      }

      /* Seta fica na parte inferior do tooltip, apontando para o ícone. */
      .ranking-tooltip-content::before {
        top: auto !important;
        bottom: -5px !important;
        left: 50% !important;
        margin-left: -5px !important;
        border-left: 1px solid #dfe4e8 !important;
        border-top: 0 !important;
        border-right: 1px solid #dfe4e8 !important;
        border-bottom: 1px solid #dfe4e8 !important;
        transform: rotate(45deg) !important;
      }

      /* Mantém o tooltip dentro da área visual do card. */
      .ranking-executive-grid {
        overflow: visible !important;
      }


      /* ANALÍTICO — 4º QUARTIL */
      .quartil4-analitico-section {
        background-color: var(--bg-input);
        border: 1px solid var(--border-color);
        border-radius: 12px;
        padding: 16px;
        margin-top: 14px;
      }
      .quartil4-analitico-title {
        display: flex;
        align-items: center;
        gap: 8px;
        margin-bottom: 10px;
        color: var(--text-muted);
        font-size: 11.5px;
        font-weight: 700;
        text-transform: uppercase;
      }
      .quartil4-analitico-title i {
        color: var(--primary);
      }
      .quartil4-table-wrap {
        max-height: 320px;
        overflow: auto;
        border: 1px solid var(--border-color);
        border-radius: 8px;
        background: var(--bg-card);
      }
      .quartil4-table {
        width: 100%;
        border-collapse: separate;
        border-spacing: 0;
        font-size: 11px;
        white-space: nowrap;
      }
      .quartil4-table th {
        position: sticky;
        top: 0;
        z-index: 12;
        background: var(--primary);
        color: #fff;
        padding: 9px 8px;
        text-align: center;
        font-weight: 700;
        text-transform: uppercase;
        border-bottom: 2px solid var(--border-color);
      }
      .quartil4-table th:first-child,
      .quartil4-table td:first-child {
        text-align: left;
        padding-left: 12px;
      }
      .quartil4-table td {
        padding: 8px;
        text-align: center;
        color: var(--text-main);
        border-bottom: 1px solid var(--border-color);
        background: var(--bg-card);
      }
      .quartil4-table tbody tr:hover td {
        background: rgba(0,0,0,.035);
      }
      .quartil4-table td.quartil4-danger {
        background: #fde9e7 !important;
        color: #c5221f !important;
        font-weight: 700;
      }
      body.dark-mode .quartil4-table td.quartil4-danger {
        background: rgba(242,139,130,.18) !important;
        color: #f28b82 !important;
      }
      .quartil4-empty {
        padding: 18px;
        text-align: center;
        color: var(--text-muted);
        font-size: 11px;
      }



/* RANKING — preenchimento único e proporcional para todas as posições */
.ranking-board-row::before {
  left: 0 !important;
  right: auto !important;
  top: 0 !important;
  bottom: 0 !important;
  width: var(--rank-progress) !important;
  min-width: 0 !important;
  max-width: 100% !important;
  background: #F7C9B5 !important;
  background-image: none !important;
  border: 0 !important;
  box-shadow: none !important;
  opacity: 1 !important;
  z-index: 0 !important;
}
</style>
  </head>
  <body>

    <!-- SPLASH SCREEN DE CARREGAMENTO -->
    <div id="splashScreenOverlay">
      <div class="splash-bg-circle-1"></div>
      <div class="splash-bg-circle-2"></div>
      <div class="splash-content-card">
        <div class="splash-logo-box">
          <img src="https://lh3.googleusercontent.com/d/13D3UFhOMHo5rUdzt8ILz6Us7g6V2HRFf" alt="Brisanet Inteligência de Dados">
        </div>
        <div class="splash-title">Painel do Supervisor</div>
        <div class="splash-subtitle">Inteligência de Dados IAT</div>
        
        <div class="splash-progress-circle-container">
          <svg viewBox="0 0 100 100">
            <circle class="splash-circle-bg" cx="50" cy="50" r="45"></circle>
            <circle class="splash-circle-bar" id="splashCircleBar" cx="50" cy="50" r="45"></circle>
          </svg>
          <span class="splash-percent-text" id="splashPercentText">0%</span>
        </div>

        <div class="splash-status-text" id="splashStatusText">Carregando indicadores do Raio X...</div>
        
        <div class="splash-progress-bar-wrapper">
          <div class="splash-progress-bar-fill" id="splashProgressBarFill"></div>
        </div>
      </div>
    </div>

    <header class="header">
      <div class="brand-section">
        <div class="logo-container">
          <img src="https://lh3.googleusercontent.com/d/13D3UFhOMHo5rUdzt8ILz6Us7g6V2HRFf" class="header-logo-img" alt="Logo">
        </div>
        <div class="brand-title"><h1>Painel do Supervisor</h1></div>
      </div>
      <div class="header-actions">
        <a href="https://drive.google.com/file/d/1QZCcEysuP4etRdtowxMXIHmcnlvD3JXJ/view?usp=sharing" target="_blank" class="btn-metas">
          <i class="fa-solid fa-chart-simple"></i> <span>METAS</span>
        </a>
        <button class="btn-refresh" id="refreshDataBtn"><i class="fa-solid fa-rotate-right"></i> Atualizar Dados</button>
        <button class="btn-clear" id="clearFiltersBtn"><i class="fa-solid fa-filter-circle-xmark"></i> Limpar Filtros</button>
        <button class="theme-toggle-btn" id="themeToggleBtn"><i class="fa-solid fa-moon" id="themeIcon"></i> <span id="themeLabel">Modo Escuro</span></button>
        <button class="tv-mode-btn" id="tvModeBtn" title="Exibir painel em modo TV"><i class="fa-solid fa-tv" id="tvModeIcon"></i> <span id="tvModeLabel">Modo TV</span></button>
      </div>
    </header>

    <div class="tv-control-bar" id="tvControlBar" aria-label="Controles do Modo TV">
      <div class="tv-top-progress" aria-hidden="true">
        <div class="tv-top-progress-fill" id="tvTopProgressFill"></div>
      </div>

      <!-- LADO ESQUERDO: HORÁRIO E DATA -->
      <div class="tv-control-left">
        <span class="tv-clock-label" id="tvClockLabel">00:00:00</span>
        <span class="tv-date-label" id="tvDateLabel">quinta-feira, 01 de outubro de 2026</span>
      </div>

      <!-- LADO DIREITO: PAUSAR, PRÓXIMA TROCA E POSIÇÃO -->
      <div class="tv-control-right">
        <div class="tv-control-actions">
          <button class="tv-control-btn tv-pause-btn" id="tvPauseBtn" title="Pausar apresentação">
            <i class="fa-solid fa-pause" id="tvPauseIcon"></i>
            <span id="tvPauseLabel">Pausar</span>
          </button>
        </div>

        <div class="tv-next-group">
          <div class="tv-next-label" id="tvNextLabel">Próxima tela em <strong>10s</strong></div>
          <div class="tv-next-progress" aria-hidden="true"><div class="tv-next-progress-fill" id="tvNextProgressFill"></div></div>
        </div>

        <div class="tv-control-nav">
          <button class="tv-control-btn" id="tvPrevBtn" title="Colaborador anterior"><i class="fa-solid fa-chevron-left"></i></button>
          <div class="tv-progress-dots" id="tvProgressDots"></div>
          <button class="tv-control-btn" id="tvNextBtn" title="Próximo colaborador"><i class="fa-solid fa-chevron-right"></i></button>
          <span class="tv-position-label" id="tvPositionLabel">01/01</span>
        </div>
      </div>
    </div>

    <main class="container">
      <section class="filter-wrapper">
        <div class="filter-grid">
          <div class="filter-group"><label><i class="fa-solid fa-user"></i> Colaborador</label><select id="filterNome"></select></div>
          <div class="filter-group"><label><i class="fa-solid fa-user-tie"></i> Coordenador</label><select id="filterCoordenador" multiple></select></div>
          <div class="filter-group"><label><i class="fa-solid fa-briefcase"></i> Gerente</label><select id="filterGerente" multiple></select></div>
          <div class="filter-group"><label><i class="fa-solid fa-calendar-days"></i> Mês</label><select id="filterMes" multiple></select></div>
          <div class="filter-group"><label><i class="fa-solid fa-sitemap"></i> Setor</label><select id="filterSetor" multiple></select></div>
          <div class="filter-group"><label><i class="fa-solid fa-chart-line"></i> Quartil Raio X</label><select id="filterQuartil" multiple></select></div>
        </div>
      </section>

      <section class="collaborator-card">
        <div class="collaborator-main-info">
          <img id="colabFoto" src="data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='150' height='150'><rect width='100%' height='100%' fill='%23f4511e'/><text x='50%' y='50%' fill='%23ffffff' font-size='16' text-anchor='middle' dy='.3em'>FOTO</text></svg>" class="collaborator-avatar" />

          <div class="collaborator-details">
            <span class="section-tag">Dados do Colaborador</span>
            <h2 id="colabNomeAbreviado" class="collaborator-name">Selecione um Colaborador</h2>

            <div class="collaborator-meta-grid">
              <div class="meta-item"><i class="fa-solid fa-id-card"></i> Matrícula: <strong id="colabMatricula">-</strong></div>
              <div class="meta-item"><i class="fa-solid fa-building"></i> Cidade Filial: <strong id="colabCidade">-</strong></div>
              <div class="meta-item"><i class="fa-solid fa-clock"></i> Tempo de Casa: <strong id="colabTempoEmpresa">-</strong></div>
              <div class="meta-item"><i class="fa-solid fa-users-gear"></i> Qtd. Equipes: <strong id="colabEquipes">-</strong></div>
              <div class="meta-item"><i class="fa-solid fa-sitemap"></i> Setor: <strong id="colabSetor">-</strong></div>
            </div></div>

          <div class="colab-emphasis-card ranking-emphasis-card" id="badgeRankingContainer">
            <div class="emphasis-card-icon"><i class="fa-solid fa-ranking-star"></i></div>
            <div class="emphasis-card-label">RANKING NORDESTE</div>
            <div class="emphasis-card-value" id="colabRanking">-</div>
          </div>

          <div class="colab-emphasis-card cert-emphasis-card badge-neutral" id="badgeCertificacaoContainer">
            <div class="emphasis-card-icon"><i class="fa-solid fa-award"></i></div>
            <div class="emphasis-card-label">CERTIFICAÇÃO</div>
            <div class="emphasis-card-value" id="colabCertificacao">-</div>
          </div>
        </div>

        <!-- HISTÓRICO RAIO X — PRIMEIRO E SEMPRE VISÍVEL -->
        <div class="quartil-raiox-visible-history" id="historicoRaioXColaborador">
          <div class="quartil-producao-history-title quartil-raiox-history-title">
            <i class="fa-solid fa-chart-line"></i> Histórico de Quartil Raio X — Últimos 12 Meses
          </div>
          <table class="quartil-producao-history-table" aria-label="Histórico de Quartil Raio X">
            <thead><tr id="historicoQuartilRaioXHead"></tr></thead>
            <tbody><tr id="historicoQuartilRaioXBody"></tr></tbody>
          </table>
        </div>

        <!-- HISTÓRICO DE PRODUÇÃO — OCULTO POR PADRÃO -->
        <div class="quartil-producao-collapsed" id="quartilProducaoCollapsed"
          onclick="toggleHistoricoQuartilProducao()" role="button" tabindex="0"
          aria-expanded="false" aria-controls="quartilProducaoContent"
          onkeydown="if(event.key==='Enter'||event.key===' '){event.preventDefault();toggleHistoricoQuartilProducao();}">
          <div class="quartil-producao-collapsed-icon"><i class="fa-solid fa-chart-column"></i></div>
          <div class="quartil-producao-collapsed-text">
            <strong>Histórico de Quartil de Produção</strong>
            <span>Clique aqui para visualizar o histórico dos últimos 12 meses.</span>
          </div>
          <div class="quartil-producao-collapsed-action">
            <i class="fa-solid fa-chevron-down"></i> Ver histórico
          </div>
        </div>

        <div class="quartil-producao-content" id="quartilProducaoContent" hidden>
          <div class="quartil-producao-history-title">
            <i class="fa-solid fa-chart-column"></i> Histórico de Quartil de Produção — Últimos 12 Meses
          </div>
          <table class="quartil-producao-history-table" aria-label="Histórico de Quartil de Produção">
            <thead><tr id="historicoQuartilProducaoHead"></tr></thead>
            <tbody><tr id="historicoQuartilProducaoBody"></tr></tbody>
          </table>
        </div>

<div class="colab-cities-container" id="containerCidadesColab">
          <span class="colab-cities-label"><i class="fa-solid fa-map-location-dot"></i> CIDADES RESPONSÁVEIS:</span>
          <div id="colabCidadesList" style="display: flex; flex-wrap: wrap; gap: 6px;">
            <span class="city-chip">-</span>
          </div>
        </div>
      </section>
      <section>
        <div class="section-title-kpi section-title-kpi-destaque"><i class="fa-solid fa-chart-pie"></i> INDICADORES DE CERTIFICAÇÃO</div>
        <div class="kpi-grid">
<!-- CARD 1: PONTO POR CABEÇA -->
          <div class="kpi-card kpi-success" id="cardPontoCabeca" onclick="abrirModalPontoCabeca()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-users"></i></span><span class="kpi-title-text">Ponto por Cabeça</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-circle-info"></i> Ponto por Cabeça</div>
                  <div class="tooltip-desc">Mede a eficiência contratada.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Pontos &divide; Equipes &divide; Dias Úteis</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valPontoCabeca">0,00</span>
                <div class="kpi-trend" id="trendPontoCabeca" style="display: flex;"><i id="trendIconPontoCabeca" class="fa-solid fa-minus"></i><span id="trendTextPontoCabeca">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 7,5</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barPontoCabeca" style="width: 0%;"></div></div>
          </div>

<!-- CARD 2: TAM -->
          <div class="kpi-card kpi-success" id="cardTam" onclick="abrirModalTam()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-users-gear"></i></span><span class="kpi-title-text">TAM</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-circle-info"></i> TAM</div>
                  <div class="tooltip-desc">Mede o percentual de equipes que atingiram a meta</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Equipes na Meta &divide; Total de Equipes</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valTam">0,00%</span>
                <div class="kpi-trend" id="trendTam" style="display: flex;"><i id="trendIconTam" class="fa-solid fa-minus"></i><span id="trendTextTam">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 80%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barTam" style="width: 0%;"></div></div>
          </div>

<!-- CARD 3: TAM ABASTECIMENTO/CLIENTE -->
          <div class="kpi-card kpi-success" id="cardAbsCliente" onclick="abrirModalAbsCliente()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-gas-pump"></i></span><span class="kpi-title-text">TAM ABASTECIMENTO/CLIENTE</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-sack-dollar"></i> TAM ABS/CLIENTE</div>
                  <div class="tooltip-desc">Valor gasto em comparação aos clientes executados.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Condutores na meta &divide; Total de condutores</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">📊 Metas por Tipo de Veículo:</div>
                  <div class="tooltip-calc">🚗 Leves: &le; R$ 6,53<br>🚐 Utilitários: &le; R$ 7,30<br>🏍️ Motocicletas: &le; R$ 1,64</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valAbsCliente">0,00%</span>
                <div class="kpi-trend" id="trendAbsCliente" style="display: flex;"><i id="trendIconAbsCliente" class="fa-solid fa-minus"></i><span id="trendTextAbsCliente">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 50%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barAbsCliente" style="width: 0%;"></div></div>
          </div>

<!-- CARD 4: TAM KM/L -->
          <div class="kpi-card kpi-success" id="cardTamKml" onclick="abrirModalTamKml()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-car-side"></i></span><span class="kpi-title-text">TAM KM/L</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-gauge-high"></i> KM/L</div>
                  <div class="tooltip-desc">TAXA DE ATINGIMENTO DA META DE AUTONOMIA POR CONDUTOR.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Condutores na meta &divide; Total de condutores</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">📊 Metas por Tipo de Veículo:</div>
                  <div class="tooltip-calc">🚗 Leves: &ge; 10 KM/L<br>🚐 Utilitários: &ge; 9 KM/L<br>🏍️ Motocicletas: &ge; 41 KM/L</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valTamKml">0,00%</span>
                <div class="kpi-trend" id="trendTamKml" style="display: flex;"><i id="trendIconTamKml" class="fa-solid fa-minus"></i><span id="trendTextTamKml">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 54%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barTamKml" style="width: 0%;"></div></div>
          </div>

<!-- CARD 5: TRATAR PONTO -->
          <div class="kpi-card kpi-success" id="cardTratarPonto" onclick="abrirModalTratarPonto()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-user-clock"></i></span><span class="kpi-title-text">TRATAR PONTO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-user-clock"></i> TRATAR PONTO</div>
                  <div class="tooltip-desc">IRREGULARIDADES NO ESPELHO DE PONTO DO COLABORADOR</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">PONTOS A TRATAR &divide; TOTAL EQUIPES</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valTratarPonto">0,00%</span>
                <div class="kpi-trend" id="trendTratarPonto" style="display: flex;"><i id="trendIconTratarPonto" class="fa-solid fa-minus"></i><span id="trendTextTratarPonto">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta = 0%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barTratarPonto" style="width: 100%;"></div></div>
          </div>

<!-- CARD 6: BH + HE POR PONTO -->
          <div class="kpi-card kpi-success" id="cardBhHe" onclick="abrirModalBhHe()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-clock"></i></span><span class="kpi-title-text">BH + HE POR PONTO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-business-time"></i> BH + HE POR PONTO</div>
                  <div class="tooltip-desc">Realização de Banco de Horas ou Hora Extra por cada ponto executado.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(Banco de Horas + HE) &divide; Pontuação Executada</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valBhHe">00:00:00</span>
                <div class="kpi-trend" id="trendBhHe" style="display: flex;"><i id="trendIconBhHe" class="fa-solid fa-minus"></i><span id="trendTextBhHe">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 00:04:00</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barBhHe" style="width: 100%;"></div></div>
          </div>

<!-- CARD 7: ALERTA GPS + PONTO -->
          <div class="kpi-card kpi-success" id="cardGpsPonto" onclick="abrirModalGpsPonto()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-location-crosshairs"></i></span><span class="kpi-title-text">ALERTA GPS + PONTO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-location-crosshairs"></i> ALERTA GPS + PONTO</div>
                  <div class="tooltip-desc">Percentual de alertas gerados em comparação com o número de equipes.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Total de Alertas &divide; Total de Equipes</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valGpsPonto">0,00</span>
                <div class="kpi-trend" id="trendGpsPonto" style="display: flex;"><i id="trendIconGpsPonto" class="fa-solid fa-minus"></i><span id="trendTextGpsPonto">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 15</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barGpsPonto" style="width: 100%;"></div></div>
          </div>

<!-- CARD 8: ACIDENTES -->
          <div class="kpi-card kpi-success" id="cardAcidentes" onclick="abrirModalAcidentes()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-triangle-exclamation"></i></span><span class="kpi-title-text">Acidentes</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-user-ninja"></i> Acidentes</div>
                  <div class="tooltip-desc">NÚMERO DE ACIDENTES COM ABERTURA DE CAT</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">Soma de acidentes registrados</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valAcidentes">0</span>
                <div class="kpi-trend" id="trendAcidentes" style="display: flex;"><i id="trendIconAcidentes" class="fa-solid fa-minus"></i><span id="trendTextAcidentes">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta = 0</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barAcidentes" style="width: 100%;"></div></div>
          </div>

<!-- CARD 9: ADERÊNCIA DE INSPEÇÃO -->
          <div class="kpi-card kpi-success" id="cardAderenciaInspecao" onclick="abrirModalAderenciaInspecao()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-clipboard-check"></i></span><span class="kpi-title-text">ADERÊNCIA DE INSPEÇÃO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clipboard-check"></i> ADERÊNCIA DE INSPEÇÃO</div>
                  <div class="tooltip-desc">ADERÊNCIA DE CONFORMIDADES NAS INSPEÇÕES DE CAMPO</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">CRITÉRIOS OK / TOTAL DE CRITÉRIOS INSPECIONADOS</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valAderenciaInspecao">0,00%</span>
                <div class="kpi-trend" id="trendAderenciaInspecao" style="display: flex;"><i id="trendIconAderenciaInspecao" class="fa-solid fa-minus"></i><span id="trendTextAderenciaInspecao">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 90%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barAderenciaInspecao" style="width: 0%;"></div></div>
          </div>

<!-- CARD 10: RECORRÊNCIA -->
          <div class="kpi-card kpi-success" id="cardRecorrencia" onclick="abrirModalRecorrencia()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-arrows-rotate"></i></span><span class="kpi-title-text">RECORRÊNCIA</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-rotate-left"></i> RECORRÊNCIA IAT</div>
                  <div class="tooltip-desc">SERVIÇOS QUE GERARAM REVISITA EM ATÉ 30 DIAS APÓS A SUA CONCLUSÃO.<br><br><strong>Nota:</strong> O indicador de recorrência IAT tem peso 2 na certificação.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">RECORRÊNCIAS &divide; SERVIÇOS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valRecorrencia">0,00%</span>
                <div class="kpi-trend" id="trendRecorrencia" style="display: flex;"><i id="trendIconRecorrencia" class="fa-solid fa-minus"></i><span id="trendTextRecorrencia">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 8%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barRecorrencia" style="width: 100%;"></div></div>
          </div>

<!-- CARD 11: CONECTOR INSTALAÇÃO -->
          <div class="kpi-card kpi-success" id="cardConectorInst" onclick="abrirModalConectorInst()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-box-open"></i></span><span class="kpi-title-text">CONECTOR INSTALAÇÃO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-plug"></i> CONECTOR INSTALAÇÃO</div>
                  <div class="tooltip-desc">MÉDIA DE USO DE CONECTOR NOS SERVIÇOS DE INSTALAÇÃO.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">CONECTORES UTILIZADOS &divide; O.S. DE INSTALAÇÃO</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valConectorInst">0,00</span>
                <div class="kpi-trend" id="trendConectorInst" style="display: flex;"><i id="trendIconConectorInst" class="fa-solid fa-minus"></i><span id="trendTextConectorInst">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 2</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barConectorInst" style="width: 100%;"></div></div>
          </div>

<!-- CARD 12: CONECTOR REPARO -->
          <div class="kpi-card kpi-success" id="cardConectorRep" onclick="abrirModalConectorRep()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-box-open"></i></span><span class="kpi-title-text">CONECTOR REPARO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-wrench"></i> CONECTOR REPARO</div>
                  <div class="tooltip-desc">MÉDIA DE USO DE CONECTOR NOS SERVIÇOS DE REPARO, MUDANÇA DE ENDEREÇO E ADICIONAL.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">CONECTORES NO REPARO &divide; O.S. DE REPARO</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valConectorRep">0,00</span>
                <div class="kpi-trend" id="trendConectorRep" style="display: flex;"><i id="trendIconConectorRep" class="fa-solid fa-minus"></i><span id="trendTextConectorRep">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 0,79</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barConectorRep" style="width: 100%;"></div></div>
          </div>

<!-- CARD 13: DROP IAT -->
          <div class="kpi-card kpi-success" id="cardDropIat" onclick="abrirModalDropIat()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-network-wired"></i></span><span class="kpi-title-text">DROP IAT</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-network-wired"></i> DROP IAT</div>
                  <div class="tooltip-desc">MÉDIA DE USO DE CABO DROP NOS SERVIÇOS DE INSTALAÇÃO E REPARO.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(CABO INST. + CABO REP.) &divide; (TOTAL INST. + TOTAL REP.)</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valDropIat">0,00</span>
                <div class="kpi-trend" id="trendDropIat" style="display: flex;"><i id="trendIconDropIat" class="fa-solid fa-minus"></i><span id="trendTextDropIat">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 80</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barDropIat" style="width: 100%;"></div></div>
          </div>

<!-- CARD 14: ÍNDICE DE VISITAS -->
          <div class="kpi-card kpi-success" id="cardIndiceVisitas" onclick="abrirModalIndiceVisitas()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">ÍNDICE DE VISITAS</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-house-user"></i> ÍNDICE DE VISITAS</div>
                  <div class="tooltip-desc">PERCENTUAL DE VISITAS DE REPARO EM COMPARAÇÃO COM A BASE DE CLIENTES ATIVOS.<br><br><strong>Nota:</strong> O indicador de índice de visitas tem peso 2 na certificação.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">VISITAS DE REPARO &divide; BASE DE CLIENTES ATIVA &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valIndiceVisitas">0,00%</span>
                <div class="kpi-trend" id="trendIndiceVisitas" style="display: flex;"><i id="trendIconIndiceVisitas" class="fa-solid fa-minus"></i><span id="trendTextIndiceVisitas">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 4%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barIndiceVisitas" style="width: 100%;"></div></div>
          </div>

<!-- CARD 15: INSTALADOS X EFETIVADOS -->
          <div class="kpi-card kpi-success" id="cardInstEfet" onclick="abrirModalInstEfet()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-column"></i></span><span class="kpi-title-text">INSTALADOS X EFETIVADOS</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-user-check"></i> INSTALADOS X EFETIVADOS</div>
                  <div class="tooltip-desc">PERCENTUAL DE CLIENTES INSTALADOS EM COMPARAÇÃO COM AS EFETIVAÇÕES REALIZADAS DENTRO DO MÊS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">TOTAL DE INSTALAÇÕES &divide; TOTAL DE EFETIVAÇÕES DO MÊS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valInstEfet">0,00%</span>
                <div class="kpi-trend" id="trendInstEfet" style="display: flex;"><i id="trendIconInstEfet" class="fa-solid fa-minus"></i><span id="trendTextInstEfet">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 90%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barInstEfet" style="width: 0%;"></div></div>
          </div>

<!-- CARD 16: SLA IAT 24 HORAS -->
          <div class="kpi-card kpi-success" id="cardSlaIat" onclick="abrirModalSlaIat()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SLA IAT 24 HORAS</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clock-rotate-left"></i> SLA IAT 24 HORAS</div>
                  <div class="tooltip-desc">CHAMADOS DE REPARO, INSTALAÇÃO, MUDANÇA DE ENDEREÇO E SERVIÇO ADICIONAL ATENDIDOS EM ATÉ 24 HORAS.<br><br><strong>Nota:</strong> O indicador de SLA IAT 24 HORAS tem peso 2 na certificação.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(INST. 24H + REP. 24H + MDE 24H + SERV ADIC. 24H) &divide; TOTAL CHAMADOS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valSlaIat">0,00%</span>
                <div class="kpi-trend" id="trendSlaIat" style="display: flex;"><i id="trendIconSlaIat" class="fa-solid fa-minus"></i><span id="trendTextSlaIat">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 65%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSlaIat" style="width: 0%;"></div></div>
          </div>

<!-- CARD 21: SAFRA -->
          <div class="kpi-card kpi-success" id="cardSafra" onclick="abrirModalSafra()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SAFRA GERAL</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-box-archive"></i> SAFRA GERAL</div>
                  <div class="tooltip-desc">APROVEITAMENTO DO RECOLHIMENTO NOS ÚLTIMOS 4 MESES.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">RECOLHIMENTO DOS ÚLTIMOS 4 MESES &divide; DEMANDA DOS ÚLTIMOS 4 MESES EXECUTADAS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valSafra">-</span>
                <div class="kpi-trend" id="trendSafra" style="display: flex;"><i id="trendIconSafra" class="fa-solid fa-minus"></i><span id="trendTextSafra">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 74%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSafra" style="width: 0%;"></div></div>
          </div>

<!-- CARD 24: ÍNDICE DE RETENÇÃO -->
          <div class="kpi-card kpi-success" id="cardIndiceRetencao" onclick="abrirModalIndiceRetencao()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">ÍNDICE DE RETENÇÃO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-user-shield"></i> ÍNDICE DE RETENÇÃO</div>
                  <div class="tooltip-desc">CLIENTES REATIVADOS EM COMPARAÇÃO COM OS RECOLHIMENTOS GERADOS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">REATIVAÇÃO FEITA PELA EQUIPE DE CAMPO &divide; RECOLHIMENTOS GERADOS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valIndiceRetencao">-</span>
                <div class="kpi-trend" id="trendIndiceRetencao" style="display: flex;"><i id="trendIconIndiceRetencao" class="fa-solid fa-minus"></i><span id="trendTextIndiceRetencao">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 15%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barIndiceRetencao" style="width: 0%;"></div></div>
          </div>

<!-- CARD 32: CERTIFICAÇÃO -->
          <div class="kpi-card kpi-success" id="cardCertificacao" onclick="abrirModalCertificacao()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">CERTIFICAÇÃO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-award"></i> CERTIFICAÇÃO</div>
                  <div class="tooltip-desc">ÍNDICE GERAL DE CONFORMIDADE E DESEMPENHO BASEADO NO ATINGIMENTO DAS METAS DOS INDICADORES ANALÍTICOS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">INDICADORES COM META ATINGIDA &divide; TOTAL DE INDICADORES APLICÁVEIS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valCertificacaoCard">0,00%</span>
                <div class="kpi-trend" id="trendCertificacao" style="display: flex;"><i id="trendIconCertificacao" class="fa-solid fa-minus"></i><span id="trendTextCertificacao">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 80%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barCertificacaoCard" style="width: 0%;"></div></div>
          </div>
        </div><!-- /kpi-grid certificação -->

        <div class="section-title-kpi section-title-kpi-destaque" style="margin-top: 24px;"><i class="fa-solid fa-chart-pie"></i> SINALIZADORES</div>
        <div class="kpi-grid">
<!-- CARD 17: SLA IAT 48 HORAS -->
          <div class="kpi-card kpi-success" id="cardSlaIat48" onclick="abrirModalSlaIat48()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SLA IAT 48 HORAS</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clock"></i> SLA IAT 48 HORAS</div>
                  <div class="tooltip-desc">PERCENTUAL DE CHAMADOS DE REPARO E INSTALAÇÃO ATENDIDOS EM ATÉ 48 HORAS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(INSTALAÇÃO 48 HORAS + REPARO 48 HORAS) &divide; (TOTAL REPARO + TOTAL INSTALAÇÃO) &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valSlaIat48">0,00%</span>
                <div class="kpi-trend" id="trendSlaIat48" style="display: flex;"><i id="trendIconSlaIat48" class="fa-solid fa-minus"></i><span id="trendTextSlaIat48">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 70%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSlaIat48" style="width: 0%;"></div></div>
          </div>

<!-- CARD 18: SLA IAT 72 HORAS -->
          <div class="kpi-card kpi-success" id="cardSlaIat72" onclick="abrirModalSlaIat72()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SLA IAT 72 HORAS</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-hourglass-half"></i> SLA IAT 72 HORAS</div>
                  <div class="tooltip-desc">PERCENTUAL DE CHAMADOS DE REPARO E INSTALAÇÃO ATENDIDOS EM ATÉ 72 HORAS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(INSTALAÇÃO 72 HORAS + REPARO 72 HORAS) &divide; (TOTAL REPARO + TOTAL INSTALAÇÃO) &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valSlaIat72">0,00%</span>
                <div class="kpi-trend" id="trendSlaIat72" style="display: flex;"><i id="trendIconSlaIat72" class="fa-solid fa-minus"></i><span id="trendTextSlaIat72">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 90%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSlaIat72" style="width: 0%;"></div></div>
          </div>

<!-- CARD 19: % EQUIPES 4º QUARTIL -->
          <div class="kpi-card kpi-success" id="cardEquipes4Quartil" onclick="abrirModalEquipes4Quartil()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-column"></i></span><span class="kpi-title-text">% EQUIPES 4º QUARTIL</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-chart-line-down"></i> % EQUIPES 4º QUARTIL</div>
                  <div class="tooltip-desc">% DE EQUIPES NO 4º QUARTIL.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">% DE EQUIPES NO 4º QUARTIL &divide; TOTAL DE EQUIPES &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valEquipes4QuartilCard">0,00%</span>
                <div class="kpi-trend" id="trendEquipes4Quartil" style="display: flex;"><i id="trendIconEquipes4Quartil" class="fa-solid fa-minus"></i><span id="trendTextEquipes4Quartil">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta = 0%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barEquipes4QuartilCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 20: ATESTADO 12 MESES -->
          <div class="kpi-card kpi-success" id="cardAtestado12m" onclick="abrirModalAtestado12m()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-file-medical"></i></span><span class="kpi-title-text">ATESTADO 12 MESES</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-notes-medical"></i> ATESTADO 12 MESES</div>
                  <div class="tooltip-desc">MÉDIA DE ATESTADOS NOS ÚLTIMOS 12 MESES POR EQUIPE.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">ATESTADOS DOS ÚLTIMOS 12 MESES &divide; EQUIPES</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valAtestado12mCard">0,00</span>
                <div class="kpi-trend" id="trendAtestado12m" style="display: flex;"><i id="trendIconAtestado12m" class="fa-solid fa-minus"></i><span id="trendTextAtestado12m">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 1</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barAtestado12mCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 22: SAFRA FTTH -->
          <div class="kpi-card kpi-success" id="cardSafraFtth" onclick="abrirModalSafraFtth()">
            <div class="kpi-header"><span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SAFRA FTTH</span></span><div class="kpi-info-wrapper" onclick="event.stopPropagation();"><i class="fa-circle-info fa-solid kpi-info-btn"></i><div class="kpi-tooltip-box"><div class="tooltip-title"><i class="fa-solid fa-network-wired"></i> SAFRA FTTH</div><div class="tooltip-desc">APROVEITAMENTO DO RECOLHIMENTO DE ONU NOS ÚLTIMOS 4 MESES.</div><div class="tooltip-divider"></div><div class="tooltip-calc-title">Métrica de Cálculo:</div><div class="tooltip-calc">TOTAL RECOLHIDO (ONU) &divide; DEMANDA RECOLHIMENTO (ONU) &times; 100</div></div></div></div>
            <div class="kpi-body-row"><div class="kpi-value-container"><span class="kpi-value" id="valSafraFtth">-</span><div class="kpi-trend" id="trendSafraFtth" style="display: flex;"><i id="trendIconSafraFtth" class="fa-solid fa-minus"></i><span id="trendTextSafraFtth">vs. mês anterior</span></div></div><div class="kpi-right-side"><span style="font-size:11.5px;color:var(--text-muted);font-weight:700;">Meta &ge; 74%</span></div></div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSafraFtth" style="width:0%;"></div></div>
          </div>

<!-- CARD 23: SAFRA FWA -->
          <div class="kpi-card kpi-success" id="cardSafraFwa" onclick="abrirModalSafraFwa()">
            <div class="kpi-header"><span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">SAFRA FWA</span></span><div class="kpi-info-wrapper" onclick="event.stopPropagation();"><i class="fa-circle-info fa-solid kpi-info-btn"></i><div class="kpi-tooltip-box"><div class="tooltip-title"><i class="fa-solid fa-router"></i> SAFRA FWA</div><div class="tooltip-desc">APROVEITAMENTO DO RECOLHIMENTO DE EQUIPAMENTOS FWA NOS ÚLTIMOS 4 MESES.</div><div class="tooltip-divider"></div><div class="tooltip-calc-title">Métrica de Cálculo:</div><div class="tooltip-calc">TOTAL RECOLHIDO (FWA) &divide; DEMANDA RECOLHIMENTO (FWA) &times; 100</div></div></div></div>
            <div class="kpi-body-row"><div class="kpi-value-container"><span class="kpi-value" id="valSafraFwa">-</span><div class="kpi-trend" id="trendSafraFwa" style="display: flex;"><i id="trendIconSafraFwa" class="fa-solid fa-minus"></i><span id="trendTextSafraFwa">vs. mês anterior</span></div></div><div class="kpi-right-side"><span style="font-size:11.5px;color:var(--text-muted);font-weight:700;">Meta &ge; 74%</span></div></div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barSafraFwa" style="width:0%;"></div></div>
          </div>

<!-- CARD 25: IMPRODUTIVA IAT -->
          <div class="kpi-card kpi-success" id="cardImprodutivaIat" onclick="abrirModalImprodutivaIat()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">IMPRODUTIVA IAT</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-ban"></i> IMPRODUTIVA IAT</div>
                  <div class="tooltip-desc">PERCENTUAL DE DESATRIBUIÇÕES SOBRE ATRIBUIÇÕES NO MÊS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(DESATRIB. INST + REP) &divide; (ATRIB. INST + REP) &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valImprodutivaIatCard">0,00%</span>
                <div class="kpi-trend" id="trendImprodutivaIat" style="display: flex;"><i id="trendIconImprodutivaIat" class="fa-solid fa-minus"></i><span id="trendTextImprodutivaIat">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 15%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barImprodutivaIatCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 26: % REGULARIZAÇÃO 15M -->
          <div class="kpi-card kpi-success" id="cardRegularizacao15m" onclick="abrirModalRegularizacao15m()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">% REGULARIZAÇÃO 15M</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-screwdriver-wrench"></i> % REGULARIZAÇÃO 15M</div>
                  <div class="tooltip-desc">% REGULARIZAÇÕES REALIZADAS NOS ÚLTIMOS 15 MESES.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">TOTAL DE REGULARIZAÇÕES REALIZADAS NOS ÚLTIMOS 15 MESES &divide; TOTAL DE CAIXAS &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valRegularizacao15mCard">0,00%</span>
                <div class="kpi-trend" id="trendRegularizacao15m" style="display: flex;"><i id="trendIconRegularizacao15m" class="fa-solid fa-minus"></i><span id="trendTextRegularizacao15m">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &ge; 50%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barRegularizacao15mCard" style="width: 0%;"></div></div>
          </div>

<!-- CARD 27: BACKLOG REPARO -->
          <div class="kpi-card kpi-success" id="cardBacklogReparo" onclick="abrirModalBacklogReparo()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">BACKLOG REPARO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clock-rotate-left"></i> BACKLOG REPARO</div>
                  <div class="tooltip-desc">Mede a quantidade de serviços de reparo em relação à capacidade de produção nos últimos 7 dias.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">DEMANDA REPARO &divide; MÉDIA 7 DIAS REPARO</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valBacklogReparoCard">0,00</span>
                <div class="kpi-trend" id="trendBacklogReparo" style="display: flex;"><i id="trendIconBacklogReparo" class="fa-solid fa-minus"></i><span id="trendTextBacklogReparo">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 2</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barBacklogReparoCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 28: BACKLOG INSTALAÇÃO -->
          <div class="kpi-card kpi-success" id="cardBacklogInstalacao" onclick="abrirModalBacklogInstalacao()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">BACKLOG INSTALAÇÃO</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clock-rotate-left"></i> BACKLOG INSTALAÇÃO</div>
                  <div class="tooltip-desc">Mede a quantidade de serviços de instalação em relação à capacidade de produção nos últimos 7 dias.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">DEMANDA INSTALAÇÃO &divide; MÉDIA 7 DIAS INSTALAÇÃO</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valBacklogInstalacaoCard">0,00</span>
                <div class="kpi-trend" id="trendBacklogInstalacao" style="display: flex;"><i id="trendIconBacklogInstalacao" class="fa-solid fa-minus"></i><span id="trendTextBacklogInstalacao">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 2</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barBacklogInstalacaoCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 29: BACKLOG IAT -->
          <div class="kpi-card kpi-success" id="cardBacklogIat" onclick="abrirModalBacklogIat()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">BACKLOG IAT</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-clock-rotate-left"></i> BACKLOG IAT</div>
                  <div class="tooltip-desc">Mede a quantidade de serviços de instalação e reparo em relação à capacidade de produção nos últimos 7 dias.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">(DEMANDA INST. + DEMANDA REP.) &divide; (MÉDIA 7D INST. + MÉDIA 7D REP.)</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valBacklogIatCard">0,00</span>
                <div class="kpi-trend" id="trendBacklogIat" style="display: flex;"><i id="trendIconBacklogIat" class="fa-solid fa-minus"></i><span id="trendTextBacklogIat">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size: 11.5px; color: var(--text-muted); font-weight: 700;">Meta &le; 2</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barBacklogIatCard" style="width: 100%;"></div></div>
          </div>

<!-- CARD 30: CHURN RATE -->
          <div class="kpi-card kpi-success" id="cardChurnRate" onclick="abrirModalChurnRate()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">CHURN RATE</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-user-minus"></i> CHURN RATE</div>
                  <div class="tooltip-desc">TAXA DE CANCELAMENTO MENSAL.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">CANCELAMENTOS &divide; TOTAL DE INSTALAÇÕES &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valChurnRate">-</span>
                <div class="kpi-trend" id="trendChurnRate" style="display: flex;"><i id="trendIconChurnRate" class="fa-solid fa-minus"></i><span id="trendTextChurnRate">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size:11.5px;color:var(--text-muted);font-weight:700;">Meta &le; 1,5%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barChurnRate" style="width:0%;"></div></div>
          </div>

<!-- CARD 31: % ISR PASTOR -->
          <div class="kpi-card kpi-success" id="cardIsrPastor" onclick="abrirModalIsrPastor()">
            <div class="kpi-header">
              <span class="kpi-title"><span class="kpi-title-icon"><i class="fa-solid fa-chart-line"></i></span><span class="kpi-title-text">% ISR PASTOR</span></span>
              <div class="kpi-info-wrapper" onclick="event.stopPropagation();">
                <i class="fa-circle-info fa-solid kpi-info-btn"></i>
                <div class="kpi-tooltip-box">
                  <div class="tooltip-title"><i class="fa-solid fa-signal"></i> % ISR PASTOR</div>
                  <div class="tooltip-desc">ÍNDICE DE SAÚDE DOS REPAROS.</div>
                  <div class="tooltip-divider"></div>
                  <div class="tooltip-calc-title">Métrica de Cálculo:</div>
                  <div class="tooltip-calc">ONU COM SINAL CRITICO &divide; TOTAL DE ONU &times; 100</div>
                </div>
              </div>
            </div>
            <div class="kpi-body-row">
              <div class="kpi-value-container">
                <span class="kpi-value" id="valIsrPastor">-</span>
                <div class="kpi-trend" id="trendIsrPastor" style="display: flex;"><i id="trendIconIsrPastor" class="fa-solid fa-minus"></i><span id="trendTextIsrPastor">vs. mês anterior</span></div>
              </div>
              <div class="kpi-right-side"><span style="font-size:11.5px;color:var(--text-muted);font-weight:700;">Meta &le; 2%</span></div>
            </div>
            <div class="kpi-progress-container"><div class="kpi-progress-bar bar-success" id="barIsrPastor" style="width:0%;"></div></div>
          </div>
        </div><!-- /kpi-grid sinalizadores -->

      <!-- TABELA ANALÍTICA DO RAIO X POR SUPERVISOR -->
      <section class="raiox-table-wrapper">
        <div class="raiox-table-header">
          <div class="raiox-table-title">
            <i class="fa-solid fa-table-list"></i> RAIO X IAT - ANALÍTICO POR SUPERVISOR
          </div>
        </div>
        <div class="table-responsive">
          <table class="analitica-table" id="tabelaRaioXGeral">
            <thead>
              <tr>
                <th onclick="sortTable('tabelaRaioXBody', 0, 'str')">SUPERVISOR <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 1, 'str')">MÊS <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 2, 'num')">PPC <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 3, 'num')">TAM <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 4, 'num')">RECORRÊNCIA IAT <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 5, 'num')">CONECTOR INST <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 6, 'num')">CONECTOR REP <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 7, 'num')">DROP IAT <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 8, 'num')">TAM KM/L <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 9, 'num')">TAM ABS/CLIENTE <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 10, 'str')">BH+HE / PT <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 11, 'num')">TRATAR PONTO <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 12, 'num')">ALERTA GPS/PONTO <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 13, 'num')">ACIDENTES <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 14, 'num')">ADERÊNCIA INSPEÇÃO <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 15, 'num')">INSTALADOS X EFETIVADOS <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 16, 'num')">ÍNDICE DE VISITAS <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 17, 'num')">SLA IAT 24 HORAS <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 18, 'num')">SAFRA <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 19, 'num')">ÍNDICE DE RETENÇÃO <i class="fa-solid fa-sort"></i></th>
                <th onclick="sortTable('tabelaRaioXBody', 20, 'num')" class="active-sort dir-desc">CERTIFICAÇÃO <i class="fa-solid fa-sort-down"></i></th>
              </tr>
            </thead>
            <tbody id="tabelaRaioXBody"></tbody>
          </table>
        </div>
      </section>

      <!-- RANKING EXECUTIVO — TOP 10 MULTISKILL + RECOLHIMENTO -->
      <section class="ranking-executive-wrapper" aria-label="Rankings executivos de performance">
        <div class="ranking-executive-header">
          <div class="ranking-executive-title">
            <span class="ranking-crown"><i class="fa-solid fa-trophy"></i></span>
            <span>RANKING</span>
          </div>
        </div>

        <div class="ranking-executive-grid">
          <article class="ranking-executive-card multiskill" aria-label="Top 10 Multiskill">
            <div class="ranking-executive-card-head">
              <div class="ranking-executive-card-title">
                <span class="ranking-executive-icon"><i class="fa-solid fa-chart-line"></i></span>
                <div>
                  <div class="ranking-name-line">
                    <strong>MULTISKILL</strong>
                    <span class="ranking-tooltip" tabindex="0" aria-label="Critério de desempate do Multiskill">
                      <span class="ranking-tooltip-trigger"><i class="fa-solid fa-circle-info"></i></span>
                      <span class="ranking-tooltip-content">
                        <span class="ranking-tooltip-title"><i class="fa-solid fa-ranking-star"></i> MULTISKILL</span>
                        <span class="ranking-tooltip-desc">Critério utilizado para definir a ordem do ranking em caso de empate entre colaboradores.</span>
                        <span class="ranking-tooltip-divider"></span>
                        <span class="ranking-tooltip-label">Critério de desempate:</span>
                        <span class="ranking-tooltip-calc">Índice de visitas</span>
                      </span>
                    </span>
                  </div>
                  <span>Top 10 do ranking</span>
                </div>
              </div>
              <span class="ranking-total-badge" id="rankingMultiskillTotal">0 colaboradores</span>
            </div>
            <div class="ranking-board" id="rankingMultiskillBoard"></div>
          </article>

          <article class="ranking-executive-card recolhimento" aria-label="Top 10 Recolhimento">
            <div class="ranking-executive-card-head">
              <div class="ranking-executive-card-title">
                <span class="ranking-executive-icon"><i class="fa-solid fa-chart-line"></i></span>
                <div>
                  <div class="ranking-name-line">
                    <strong>RECOLHIMENTO</strong>
                    <span class="ranking-tooltip" tabindex="0" aria-label="Critério de desempate do Recolhimento">
                      <span class="ranking-tooltip-trigger"><i class="fa-solid fa-circle-info"></i></span>
                      <span class="ranking-tooltip-content">
                        <span class="ranking-tooltip-title"><i class="fa-solid fa-ranking-star"></i> RECOLHIMENTO</span>
                        <span class="ranking-tooltip-desc">Critério utilizado para definir a ordem do ranking em caso de empate entre colaboradores.</span>
                        <span class="ranking-tooltip-divider"></span>
                        <span class="ranking-tooltip-label">Critério de desempate:</span>
                        <span class="ranking-tooltip-calc">Índice de clientes retidos</span>
                      </span>
                    </span>
                  </div>
                  <span>Top 10 do ranking</span>
                </div>
              </div>
              <span class="ranking-total-badge" id="rankingRecolhimentoTotal">0 colaboradores</span>
            </div>
            <div class="ranking-board" id="rankingRecolhimentoBoard"></div>
          </article>
        </div>
      </section>

      <!-- MÓDULO DE GRÁFICOS: DISTRIBUIÇÃO DE QUARTIS POR LIDERANÇA -->
      <section class="charts-quartil-wrapper">
        <div class="charts-quartil-header">
          <i class="fa-solid fa-chart-column"></i> DISTRIBUIÇÃO DE QUARTIS POR LIDERANÇA (RAIO X)
        </div>
        <div class="charts-quartil-grid charts-quartil-grid-single">
          <div class="chart-quartil-card chart-quartil-card-drilldown">
            <div class="chart-quartil-card-title">
              <span id="tituloGraficoQuartilLideranca"><i class="fa-solid fa-briefcase"></i> DISTRIBUIÇÃO DE QUARTIS POR GERENTE DE CAMPO</span>
              <button type="button" class="quartil-drilldown-btn" id="btnDrilldownQuartil" onclick="alternarGraficoQuartilLideranca()" title="Estratificar por coordenador">
                <span id="textoDrilldownQuartil">CLIQUE NA SETA AO LADO PARA ESTRATIFICAR</span>
                <i class="fa-solid fa-chevron-down"></i>
              </button>
            </div>
            <div class="chart-quartil-canvas-container">
              <canvas id="graficoQuartilLideranca"></canvas>
            </div>
          </div>
        </div>
      </section>

    </main>
    <!-- MODAL 1: PONTO POR CABEÇA -->
    <div class="modal-overlay" id="modalPontoCabeca" onclick="fecharModalPontoCabeca(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-chart-line"></i></div>
            <div class="modal-title-text">
              <h3>Ponto por Cabeça</h3>
              <p>Mede a eficiência contratada</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalPontoCabeca()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBox">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 7,5</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtual">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiff">+0,00 vs ideal</span>
              </div>
              <span class="modal-box-msg" id="modalValMsg">O valor atingiu a meta desejada.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span style="display:flex;align-items:center;gap:7px;">
                  <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
                </span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-star"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Pontos</span><span class="formula-card-val" id="calcPontos">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Equipes</span><span class="formula-card-val" id="calcEquipes">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-purple"><i class="fa-solid fa-calendar-day"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Dias Úteis</span><span class="formula-card-val" id="calcDias">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Pontos &divide; Equipes &divide; Dias Úteis</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucao"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico da Equipe
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_q77aqtpewd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaAnaliticaBody', 0, 'str')">FUNCIONÁRIO</th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 1, 'str')">SITUAÇÃO</th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 2, 'num')">DIAS DE EMPRESA</th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 3, 'num')">PONTUAÇÃO</th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 4, 'num')" id="th-ppc">PPC <i class="fa-solid fa-sort-down"></i></th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 5, 'num')">DIAS AUSENTE</th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 6, 'num')" class="th-com-tooltip">GAP ATUAL <span class="th-tooltip-icon" tabindex="0" aria-label="Explicação do Gap Atual" title="Quantidade de pontos que faltam para atingir a meta, mostrando em tempo real o quão distante o colaborador está da meta." onmouseenter="mostrarTooltipGap(this)" onmouseleave="ocultarTooltipGap(this)" onfocus="mostrarTooltipGap(this)" onblur="ocultarTooltipGap(this)"><i class="fa-solid fa-circle-info"></i><span class="th-tooltip-box">Quantidade de pontos que faltam para atingir a meta, mostrando em tempo real o quão distante o colaborador está da meta.</span></span></th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 7, 'num')" class="th-com-tooltip">GAP DIA <span class="th-tooltip-icon" tabindex="0" aria-label="Explicação do Gap Dia" title="Diferença entre os pontos que o colaborador precisa fazer por dia para alcançar a meta. Exemplo: se precisa de 8 pontos/dia e fez apenas 5, o Gap Diário é 3 pontos." onmouseenter="mostrarTooltipGap(this)" onmouseleave="ocultarTooltipGap(this)" onfocus="mostrarTooltipGap(this)" onblur="ocultarTooltipGap(this)"><i class="fa-solid fa-circle-info"></i><span class="th-tooltip-box">Diferença entre os pontos que o colaborador precisa fazer por dia para alcançar a meta. Exemplo: se precisa de 8 pontos/dia e fez apenas 5, o Gap Diário é 3 pontos.</span></span></th>
                    <th onclick="sortTable('tabelaAnaliticaBody', 8, 'num')">PONTOS / DIA (META)</th>
                  </tr>
                </thead>
                <tbody id="tabelaAnaliticaBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 2: TAM -->
    <div class="modal-overlay" id="modalTam" onclick="fecharModalTam(event)">
      <div class="modal-container modal-container-tam" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-bullseye"></i></div>
            <div class="modal-title-text">
              <h3>TAM - Taxa de Atingimento de Meta</h3>
              <p>Percentual de equipes que atingiram a meta estipulada</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalTam()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxTam">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 80%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualTam">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffTam">+0,00% vs ideal</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgTam">A meta do TAM foi atingida com sucesso.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span style="display:flex;align-items:center;gap:7px;">
                  <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
                  <span class="modal-meta-info-wrapper">
                    <span class="modal-meta-info-btn"><i class="fa-solid fa-circle-info"></i></span>
                    <span class="modal-meta-tooltip">
                      <span class="modal-meta-tooltip-title"><i class="fa-solid fa-chart-column"></i> Metas por Tipo de Veículo</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-car-side"></i> <span>Leves:</span> &ge; 10 KM/L</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-van-shuttle"></i> <span>Utilitários:</span> &ge; 9 KM/L</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-motorcycle"></i> <span>Motocicletas:</span> &ge; 41 KM/L</span>
                    </span>
                  </span>
                </span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-circle-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Bateram Meta</span><span class="formula-card-val" id="calcEquipesBateram">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users-viewfinder"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Equipes</span><span class="formula-card-val" id="calcTotalEquipesTam">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Equipes Bateram Meta &divide; Total de Equipes</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica do TAM (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoTam"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico da Equipe
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_om8anzu4wd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table analitica-table-tam">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaTamBody', 0, 'str')">FUNCIONÁRIO</th>
                    <th onclick="sortTable('tabelaTamBody', 1, 'num')">PONTOS</th>
                    <th onclick="sortTable('tabelaTamBody', 2, 'num')">META PROJEÇÃO</th>
                    <th onclick="sortTable('tabelaTamBody', 3, 'str')">ÍNDICE</th>
                  </tr>
                </thead>
                <tbody id="tabelaTamBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 3: TAM ABASTECIMENTO/CLIENTE -->
    <div class="modal-overlay" id="modalAbsCliente" onclick="fecharModalAbsCliente(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-gas-pump"></i></div>
            <div class="modal-title-text">
              <h3>TAM ABASTECIMENTO/CLIENTE</h3>
              <p>Valor gasto com abastecimento em comparação com os clientes executados</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalAbsCliente()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxAbs">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 50%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualAbs">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffAbs">+0,00% vs ideal</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgAbs">A meta do TAM ABASTECIMENTO/CLIENTE foi atingida com sucesso.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span style="display:flex;align-items:center;gap:7px;">
                  <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
                  <span class="modal-meta-info-wrapper">
                    <span class="modal-meta-info-btn" title="Metas por tipo de veículo"><i class="fa-solid fa-circle-info"></i></span>
                    <span class="modal-meta-tooltip">
                      <span class="modal-meta-tooltip-title"><i class="fa-solid fa-chart-column"></i> Metas por Tipo de Veículo</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-car-side"></i> <span>Leves:</span> &le; R$ 6,53</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-van-shuttle"></i> <span>Utilitários:</span> &le; R$ 7,30</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-motorcycle"></i> <span>Motocicletas:</span> &le; R$ 1,64</span>
                    </span>
                  </span>
                </span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-user-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Condutores Na Meta</span><span class="formula-card-val" id="calcCondutoresBateram">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Condutores</span><span class="formula-card-val" id="calcTotalCondutores">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Total de condutores dentro da meta &divide; Total de condutores</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica TAM ABASTECIMENTO/CLIENTE</div>
            <div class="chart-container"><canvas id="graficoEvolucaoAbs"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico de Condutores
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_5uudqxg2vd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaAbsBody', 0, 'str')">COLABORADOR</th>
                    <th onclick="sortTable('tabelaAbsBody', 1, 'str')">PLACA</th>
                    <th onclick="sortTable('tabelaAbsBody', 2, 'str')">TIPO VEÍCULO</th>
                    <th onclick="sortTable('tabelaAbsBody', 3, 'num')">ABASTECIMENTO (R$)</th>
                    <th onclick="sortTable('tabelaAbsBody', 4, 'num')">CLIENTES</th>
                    <th onclick="sortTable('tabelaAbsBody', 5, 'num')" class="th-com-tooltip">ABST. DESLOCAMENTO LONGO <span class="th-tooltip-icon" tabindex="0" aria-label="Explicação do deslocamento longo" title="DESLOCAMENTO LONGO REGISTRADO NA PRODUTIVIDADE EXTRA" onmouseenter="mostrarTooltipGap(this)" onmouseleave="ocultarTooltipGap(this)" onfocus="mostrarTooltipGap(this)" onblur="ocultarTooltipGap(this)"><i class="fa-solid fa-circle-info"></i><span class="th-tooltip-box">DESLOCAMENTO LONGO REGISTRADO NA PRODUTIVIDADE EXTRA</span></span></th>
                    <th onclick="sortTable('tabelaAbsBody', 6, 'num')">ABST X CLIENTE</th>
                    <th onclick="sortTable('tabelaAbsBody', 7, 'str')">ÍNDICE</th>
                  </tr>
                </thead>
                <tbody id="tabelaAbsBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 4: TAM KM/L -->
    <div class="modal-overlay" id="modalTamKml" onclick="fecharModalTamKml(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-gauge-high"></i></div>
            <div class="modal-title-text">
              <h3>TAM KM/L</h3>
              <p>TAXA DE ATINGIMENTO DA META DE AUTONOMIA POR CONDUTOR</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalTamKml()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxKml">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 54%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualKml">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffKml">+0,00% vs ideal</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgKml">A meta do TAM KM/L foi atingida com sucesso.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span style="display:flex;align-items:center;gap:7px;">
                  <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
                  <span class="modal-meta-info-wrapper">
                    <span class="modal-meta-info-btn" title="Metas por tipo de veículo"><i class="fa-solid fa-circle-info"></i></span>
                    <span class="modal-meta-tooltip">
                      <span class="modal-meta-tooltip-title"><i class="fa-solid fa-chart-column"></i> Metas por Tipo de Veículo</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-car-side"></i> <span>Leves:</span> &ge; 10 KM/L</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-van-shuttle"></i> <span>Utilitários:</span> &ge; 9 KM/L</span>
                      <span class="modal-meta-tooltip-row"><i class="fa-solid fa-motorcycle"></i> <span>Motocicletas:</span> &ge; 41 KM/L</span>
                    </span>
                  </span>
                </span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-user-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Condutores Na Meta</span><span class="formula-card-val" id="calcCondutoresBateramKml">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Condutores</span><span class="formula-card-val" id="calcTotalCondutoresKml">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Total de condutores dentro da meta &divide; Total de condutores</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica TAM KM/L</div>
            <div class="chart-container"><canvas id="graficoEvolucaoKml"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico de Condutores
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_5uudqxg2vd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaKmlBody', 0, 'str')">FUNCIONÁRIO</th>
                    <th onclick="sortTable('tabelaKmlBody', 1, 'str')">PLACA</th>
                    <th onclick="sortTable('tabelaKmlBody', 2, 'str')">TIPO VEÍCULO</th>
                    <th onclick="sortTable('tabelaKmlBody', 3, 'num')">QUANTIDADE DE ABASTECIMENTO</th>
                    <th onclick="sortTable('tabelaKmlBody', 4, 'num')">KM</th>
                    <th onclick="sortTable('tabelaKmlBody', 5, 'num')">LITROS</th>
                    <th onclick="sortTable('tabelaKmlBody', 6, 'num')">KM/L</th>
                    <th onclick="sortTable('tabelaKmlBody', 7, 'str')">ÍNDICE</th>
                  </tr>
                </thead>
                <tbody id="tabelaKmlBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 5: ACIDENTES -->
    <div class="modal-overlay" id="modalAcidentes" onclick="fecharModalAcidentes(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-user-ninja"></i></div>
            <div class="modal-title-text">
              <h3>Acidentes</h3>
              <p>NÚMERO DE ACIDENTES COM ABERTURA DE CAT</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalAcidentes()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxAcidentes">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal = 0</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualAcidentes">0</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffAcidentes">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgAcidentes">Nenhum acidente registrado no período.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item" style="flex: 100%;">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-triangle-exclamation"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total de Acidentes com CAT</span><span class="formula-card-val" id="calcTotalAcidentes">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Métrica: Soma da quantidade de acidentes com abertura de CAT</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica de Acidentes</div>
            <div class="chart-container"><canvas id="graficoEvolucaoAcidentes"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico de Acidentes
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaAcidentesBody', 0, 'str')">COLABORADOR</th>
                    <th onclick="sortTable('tabelaAcidentesBody', 1, 'str')">DATA</th>
                    <th onclick="sortTable('tabelaAcidentesBody', 2, 'str')">CAUSA</th>
                    <th onclick="sortTable('tabelaAcidentesBody', 3, 'num')">DIAS DE AFASTAMENTO</th>
                  </tr>
                </thead>
                <tbody id="tabelaAcidentesBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 6: ADERÊNCIA DE INSPEÇÃO -->
    <div class="modal-overlay" id="modalAderenciaInspecao" onclick="fecharModalAderenciaInspecao(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box"><i class="fa-solid fa-clipboard-check"></i></div>
            <div class="modal-title-text">
              <h3>ADERÊNCIA DE INSPEÇÃO</h3>
              <p>ADERÊNCIA DE CONFORMIDADES NAS INSPEÇÕES DE CAMPO</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalAderenciaInspecao()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxInspecao">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 90%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualInspecao">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffInspecao">+0,00% vs ideal</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgInspecao">A meta de inspeção foi atingida com sucesso.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Inspeções OK</span><span class="formula-card-val" id="calcInspecaoOk">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Inspeções</span><span class="formula-card-val" id="calcTotalInspecao">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Inspeções OK &divide; Total de Inspeções</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica de Aderência (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoInspecao"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; position: relative;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico de Inspeções
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://docs.google.com/spreadsheets/d/1z1MgPtzpsgYGGjHbCXXHU50XOLsi1ih5HJi01P17MlY/edit?gid=0#gid=0" target="_blank" class="btn-link-externo"><i class="fa-solid fa-up-right-from-square"></i>Clique aqui para ver o analítico</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaInspecaoBody', 0, 'str')">FUNCIONÁRIO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInspecaoBody', 1, 'num')">TOTAL DE INSPEÇÕES <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInspecaoBody', 2, 'num')">CRITÉRIOS OK <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInspecaoBody', 3, 'num')">INCONFORME <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInspecaoBody', 4, 'num')" id="th-insp-aderencia">% ADERÊNCIA INSPEÇÃO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaInspecaoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 7: TRATAR PONTO -->
    <div class="modal-overlay" id="modalTratarPonto" onclick="fecharModalTratarPonto(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-user-clock"></i></div>
            <div class="modal-title-text">
              <h3>TRATAR PONTO</h3>
              <p>IRREGULARIDADES NO ESPELHO DE PONTO DO COLABORADOR</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalTratarPonto()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxTratarPonto">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal = 0%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualTratarPonto">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffTratarPonto">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgTratarPonto">Nenhum ponto pendente de tratamento no período.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-triangle-exclamation"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Pontos a Tratar</span><span class="formula-card-val" id="calcPontosTratar">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Equipes</span><span class="formula-card-val" id="calcTotalFuncTratar">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Pontos a Tratar &divide; Total de Equipes</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoTratarPonto"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; position: relative;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base TRATAR PONTO)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_3wo5d1y4td/edit" target="_blank" class="btn-link-externo"><i class="fa-solid fa-up-right-from-square"></i>Clique aqui para ver o analítico</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaTratarPontoBody', 0, 'str')">COLABORADOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 1, 'str')">DATA <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 2, 'str')">BATIDA 1 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 3, 'str')">BATIDA 2 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 4, 'str')">BATIDA 3 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 5, 'str')">BATIDA 4 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 6, 'str')">BATIDA 5 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 7, 'str')">BATIDA 6 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 8, 'str')">BATIDA 7 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 9, 'str')">BATIDA 8 <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaTratarPontoBody', 10, 'str')">CRITÉRIO <i class="fa-solid fa-sort"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaTratarPontoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 8: BH + HE POR PONTO -->
    <div class="modal-overlay" id="modalBhHe" onclick="fecharModalBhHe(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-business-time"></i></div>
            <div class="modal-title-text">
              <h3>BH + HE POR PONTO</h3>
              <p>BANCO DE HORAS E HORA EXTRA POR PONTUAÇÃO EXECUTADA</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalBhHe()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxBhHe">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 00:04:00</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualBhHe">00:00:00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffBhHe">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgBhHe">Índice dentro da meta estipulada.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-clock"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total BH + HE</span><span class="formula-card-val" id="calcTotalBhHeTempo">00:00:00</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-star"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Pontuação</span><span class="formula-card-val" id="calcTotalPontosBhHe">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Banco de Horas + HE) &divide; Pontuação Executada</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica BH+HE por Ponto (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoBhHe"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base BH + HE)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_dvxvbarind" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaBhHeBody', 0, 'str')">COLABORADOR</th>
                    <th onclick="sortTable('tabelaBhHeBody', 1, 'str')">BH + HE / PONTUAÇÃO</th>
                    <th onclick="sortTable('tabelaBhHeBody', 2, 'str')">BANCO DE HORAS</th>
                    <th id="thBhHeGrupoHoraExtra"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoBhHe('horaextra', this)" title="Clique na seta para abrir os detalhes">HORA EXTRA TOTAL <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="bhhe-detail-th bhhe-detail-horaextra sla-detail-hidden">HORA EXTRA AGOSTO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-horaextra sla-detail-hidden">HORA EXTRA SETEMBRO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-horaextra sla-detail-hidden">HORA EXTRA OUTUBRO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-horaextra sla-detail-hidden">HORA EXTRA NOVEMBRO <i class="fa-solid fa-sort"></i></th>
                    <th id="thBhHeGrupoPontos"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoBhHe('pontos', this)" title="Clique na seta para abrir os detalhes">PONTOS TOTAL <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="bhhe-detail-th bhhe-detail-pontos sla-detail-hidden">PONTOS AGOSTO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-pontos sla-detail-hidden">PONTOS SETEMBRO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-pontos sla-detail-hidden">PONTOS OUTUBRO <i class="fa-solid fa-sort"></i></th>
                    <th class="bhhe-detail-th bhhe-detail-pontos sla-detail-hidden">PONTOS NOVEMBRO <i class="fa-solid fa-sort"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaBhHeBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 9: ALERTA GPS + PONTO -->
    <div class="modal-overlay" id="modalGpsPonto" onclick="fecharModalGpsPonto(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-location-crosshairs"></i></div>
            <div class="modal-title-text">
              <h3>ALERTA GPS + PONTO</h3>
              <p>PERCENTUAL DE ALERTAS GERADOS EM COMPARAÇÃO COM O NÚMERO DE FUNCIONÁRIOS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalGpsPonto()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxGpsPonto">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 15</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualGpsPonto">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffGpsPonto">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgGpsPonto">Índice dentro da meta estipulada.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-bell"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Alertas</span><span class="formula-card-val" id="calcTotalAlertasGps">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Equipes</span><span class="formula-card-val" id="calcTotalEquipesGps">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Total de Alertas &divide; Total de Equipes</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Alerta GPS + Ponto (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoGpsPonto"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base GPS PONTO)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_3p3ldx0byd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaGpsPontoBody', 0, 'str')">COLABORADOR</th>
                    <th onclick="sortTable('tabelaGpsPontoBody', 1, 'num')">QUANTIDADE ALERTAS</th>
                  </tr>
                </thead>
                <tbody id="tabelaGpsPontoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 10: RECORRÊNCIA IAT -->
    <div class="modal-overlay" id="modalRecorrencia" onclick="fecharModalRecorrencia(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-rotate-left"></i></div>
            <div class="modal-title-text">
              <h3>RECORRÊNCIA IAT</h3>
              <p>SERVIÇOS QUE GERARAM REVISITA EM ATÉ 30 DIAS APÓS A SUA CONCLUSÃO. (PESO 2 NA CERTIFICAÇÃO)</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalRecorrencia()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxRecorrencia">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 8%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualRecorrencia">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffRecorrencia">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgRecorrencia">Taxa de recorrência IAT dentro da meta estipulada (≤ 8%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-rotate-left"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Recorrências</span><span class="formula-card-val" id="calcTotalRecorrencias">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Serviços</span><span class="formula-card-val" id="calcTotalServicosRec">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Recorrências &divide; Serviços Executados &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Recorrência IAT (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoRecorrencia"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base RECORRÊNCIA)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_igm9qzq5pd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaRecorrenciaBody', 0, 'str')">COLABORADOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRecorrenciaBody', 1, 'num')">SERVIÇOS <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRecorrenciaBody', 2, 'num')">RECORRÊNCIAS <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRecorrenciaBody', 3, 'num')">RECORRÊNCIA IAT <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaRecorrenciaBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 11: CONECTOR DE INSTALAÇÃO -->
    <div class="modal-overlay" id="modalConectorInst" onclick="fecharModalConectorInst(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-plug"></i></div>
            <div class="modal-title-text">
              <h3>CONECTOR DE INSTALAÇÃO</h3>
              <p>MÉDIA DE USO DE CONECTOR NOS SERVIÇOS DE INSTALAÇÃO</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalConectorInst()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxConectorInst">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 2</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualConectorInst">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffConectorInst">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgConectorInst">Média de uso de conectores dentro da meta (≤ 2).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-plug"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Uso Conector</span><span class="formula-card-val" id="calcUsoConectorInst">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-house-signal"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Instalações</span><span class="formula-card-val" id="calcTotalInstalacoes">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Conectores Utilizados &divide; O.S. de Instalação</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Conector de Instalação (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoConectorInst"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base MATERIAL)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_vtis0dadqd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaConectorInstBody', 0, 'str')">COLABORADOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorInstBody', 1, 'num')">CONECTOR SC INST <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorInstBody', 2, 'num')">INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorInstBody', 3, 'num')">CONECTOR INSTALAÇÃO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaConectorInstBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 12: CONECTOR DE REPARO -->
    <div class="modal-overlay" id="modalConectorRep" onclick="fecharModalConectorRep(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-wrench"></i></div>
            <div class="modal-title-text">
              <h3>CONECTOR REPARO</h3>
              <p>MÉDIA DE USO DE CONECTOR NOS SERVIÇOS DE REPARO, MUDANÇA DE ENDEREÇO E ADICIONAL</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalConectorRep()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxConectorRep">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 0,79</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualConectorRep">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffConectorRep">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgConectorRep">Média de uso de conectores dentro da meta (≤ 0,79).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Uso Conector Rep</span><span class="formula-card-val" id="calcUsoConectorRep">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-screwdriver-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Reparos</span><span class="formula-card-val" id="calcTotalReparos">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Conectores no Reparo &divide; O.S. de Reparo</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Conector de Reparo (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoConectorRep"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base MATERIAL)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_vtis0dadqd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaConectorRepBody', 0, 'str')">COLABORADOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorRepBody', 1, 'num')">CONECTOR SC REP. <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorRepBody', 2, 'num')">REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaConectorRepBody', 3, 'num')">CONECTOR REPARO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaConectorRepBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- MODAL 13: DROP IAT -->
    <div class="modal-overlay" id="modalDropIat" onclick="fecharModalDropIat(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-network-wired"></i></div>
            <div class="modal-title-text">
              <h3>USO DROP IAT</h3>
              <p>MÉDIA DE USO DE CABO DROP NOS SERVIÇOS DE INSTALAÇÃO E REPARO</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalDropIat()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxDropIat">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 80</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualDropIat">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffDropIat">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgDropIat">Média de uso de cabo Drop dentro da meta (≤ 80).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-network-wired"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Drop (Inst+Rep)</span><span class="formula-card-val" id="calcTotalCaboDrop">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total OS (Inst+Rep)</span><span class="formula-card-val" id="calcTotalOsDrop">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Cabo Inst + Cabo Rep) &divide; (Total Inst + Total Rep)</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica DROP IAT (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoDropIat"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base MATERIAL)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_vtis0dadqd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaDropIatBody', 0, 'str')">COLABORADOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaDropIatBody', 1, 'num')">CABO DROP INST. <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaDropIatBody', 2, 'num')">CABO DROP REP. <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaDropIatBody', 3, 'num')">INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaDropIatBody', 4, 'num')">REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaDropIatBody', 5, 'num')">USO DROP IAT <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaDropIatBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 14: ÍNDICE DE VISITAS -->
    <div class="modal-overlay" id="modalIndiceVisitas" onclick="fecharModalIndiceVisitas(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-house-user"></i></div>
            <div class="modal-title-text">
              <h3>ÍNDICE DE VISITAS</h3>
              <p>PERCENTUAL DE VISITAS DE REPARO EM COMPARAÇÃO COM A BASE DE CLIENTES ATIVOS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalIndiceVisitas()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxIndiceVisitas">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 4%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualIndiceVisitas">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffIndiceVisitas">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgIndiceVisitas">Índice de visitas dentro da meta estipulada (≤ 4%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-screwdriver-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Visitas Reparo</span><span class="formula-card-val" id="calcVisitasReparo">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Base Clientes</span><span class="formula-card-val" id="calcBaseClientes">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Visitas de Reparo &divide; Base de Clientes Ativos &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Índice de Visitas (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoIndiceVisitas"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaIndiceVisitasBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceVisitasBody', 1, 'num')">VISITAS DE REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceVisitasBody', 2, 'num')">BASE DE CLIENTES <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceVisitasBody', 3, 'num')">% DE VISITAS <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaIndiceVisitasBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 15: SLA IAT 24 HORAS -->
    <div class="modal-overlay" id="modalSlaIat" onclick="fecharModalSlaIat(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-clock-rotate-left"></i></div>
            <div class="modal-title-text">
              <h3>SLA IAT 24 HORAS</h3>
              <p>CHAMADOS DE REPARO, INSTALAÇÃO, MUDANÇA DE ENDEREÇO E SERVIÇO ADICIONAL ATENDIDOS EM ATÉ 24 HORAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalSlaIat()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxSlaIat">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 65%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualSlaIat">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffSlaIat">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgSlaIat">Taxa de atendimento em até 24h dentro da meta (≥ 65%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-clock"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Serviços 24h</span><span class="formula-card-val" id="calcReparo24Horas">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Serviços</span><span class="formula-card-val" id="calcTotalReparoSla">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Inst. 24h + Rep. 24h + MDE 24h + Adic. 24h) &divide; (Total Inst. + Total Rep. + Total MDE + Total Adic.) &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica SLA IAT 24 HORAS (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoSlaIat"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_fznherre2d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table sla-agrupada-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaSlaIatBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th id="thSlaGrupoInstalacao"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIat('instalacao', this)" title="Clique na seta para abrir os detalhes">SLA INSTALAÇÃO <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla-detail-instalacao sla-detail-hidden">TOTAL INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-instalacao sla-detail-hidden">INSTALAÇÃO 24 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-instalacao sla-detail-hidden">SLA INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th id="thSlaGrupoReparo"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIat('reparo', this)" title="Clique na seta para abrir os detalhes">SLA REPARO <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla-detail-reparo sla-detail-hidden">TOTAL REPARO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-reparo sla-detail-hidden">REPARO 24 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-reparo sla-detail-hidden">SLA REPARO <i class="fa-solid fa-sort"></i></th>
                    <th id="thSlaGrupoMde"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIat('mde', this)" title="Clique na seta para abrir os detalhes">SLA MDE <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla-detail-mde sla-detail-hidden">TOTAL MDE <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-mde sla-detail-hidden">MDE 24 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-mde sla-detail-hidden">SLA MDE <i class="fa-solid fa-sort"></i></th>
                    <th id="thSlaGrupoAdic"><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIat('adic', this)" title="Clique na seta para abrir os detalhes">SLA SERVIÇO ADICIONAL <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla-detail-adic sla-detail-hidden">TOTAL SERVIÇO ADICIONAL <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-adic sla-detail-hidden">SERVIÇO ADICIONAL 24 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla-detail-adic sla-detail-hidden">SLA SERVIÇO ADICIONAL <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSlaIatBody', 16, 'num')">SLA IAT 24 HORAS <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaSlaIatBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 15A: SLA IAT 48 HORAS -->
    <div class="modal-overlay" id="modalSlaIat48" onclick="fecharModalSlaIat48(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-clock"></i></div>
            <div class="modal-title-text">
              <h3>SLA IAT 48 HORAS</h3>
              <p>CHAMADOS DE REPARO, INSTALAÇÃO, MUDANÇA DE ENDEREÇO E SERVIÇO ADICIONAL ATENDIDOS EM ATÉ 48 HORAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalSlaIat48()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxSlaIat48">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 70%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualSlaIat48">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffSlaIat48">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgSlaIat48">Taxa de atendimento em até 48h dentro da meta (≥ 70%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-circle-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Instalação 48h</span><span class="formula-card-val" id="calcInstalacao48Horas">0</span></div>
                </div>
                <div class="formula-circle-op">+</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-screwdriver-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Reparo 48h</span><span class="formula-card-val" id="calcReparo48Horas">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Reparo</span><span class="formula-card-val" id="calcTotalReparoSla48">0</span></div>
                </div>
                <div class="formula-circle-op">+</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Instalação</span><span class="formula-card-val" id="calcTotalInstalacaoSla48">0</span></div>
                </div>
                <div class="formula-circle-op">&times; 100</div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Instalação 48h + Reparo 48h) &divide; (Total Reparo + Total Instalação) &times; 100</span>
            </div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_fznherre2d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table sla-agrupada-table">
                <thead>
                  <tr>
                    <th>CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIatHoras('instalacao', this, 48)" title="Clique na seta para abrir os detalhes">SLA INSTALAÇÃO 48H <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla48-detail-instalacao sla-detail-hidden">TOTAL INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla48-detail-instalacao sla-detail-hidden">INSTALAÇÃO 48 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla48-detail-instalacao sla-detail-hidden">SLA INSTALAÇÃO 48H <i class="fa-solid fa-sort"></i></th>
                    <th><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIatHoras('reparo', this, 48)" title="Clique na seta para abrir os detalhes">SLA REPARO 48H <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla48-detail-reparo sla-detail-hidden">TOTAL REPARO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla48-detail-reparo sla-detail-hidden">REPARO 48 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla48-detail-reparo sla-detail-hidden">SLA REPARO 48H <i class="fa-solid fa-sort"></i></th>
                    <th>SLA IAT 48 HORAS <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaSlaIat48Body"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 15B: SLA IAT 72 HORAS -->
    <div class="modal-overlay" id="modalSlaIat72" onclick="fecharModalSlaIat72(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-hourglass-half"></i></div>
            <div class="modal-title-text">
              <h3>SLA IAT 72 HORAS</h3>
              <p>CHAMADOS DE REPARO, INSTALAÇÃO, MUDANÇA DE ENDEREÇO E SERVIÇO ADICIONAL ATENDIDOS EM ATÉ 72 HORAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalSlaIat72()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxSlaIat72">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 70%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualSlaIat72">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffSlaIat72">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgSlaIat72">Taxa de atendimento em até 72h. Regra de cor: verde somente acima de 90%.</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-circle-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Instalação 72h</span><span class="formula-card-val" id="calcInstalacao72Horas">0</span></div>
                </div>
                <div class="formula-circle-op">+</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-screwdriver-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Reparo 72h</span><span class="formula-card-val" id="calcReparo72Horas">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Reparo</span><span class="formula-card-val" id="calcTotalReparoSla72">0</span></div>
                </div>
                <div class="formula-circle-op">+</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Instalação</span><span class="formula-card-val" id="calcTotalInstalacaoSla72">0</span></div>
                </div>
                <div class="formula-circle-op">&times; 100</div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Instalação 72h + Reparo 72h) &divide; (Total Reparo + Total Instalação) &times; 100</span>
            </div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_fznherre2d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table sla-agrupada-table">
                <thead>
                  <tr>
                    <th>CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIatHoras('instalacao', this, 72)" title="Clique na seta para abrir os detalhes">SLA INSTALAÇÃO 72H <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla72-detail-instalacao sla-detail-hidden">TOTAL INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla72-detail-instalacao sla-detail-hidden">INSTALAÇÃO 72 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla72-detail-instalacao sla-detail-hidden">SLA INSTALAÇÃO 72H <i class="fa-solid fa-sort"></i></th>
                    <th><button type="button" class="sla-group-toggle" aria-expanded="false" onclick="alternarGrupoSlaIatHoras('reparo', this, 72)" title="Clique na seta para abrir os detalhes">SLA REPARO 72H <i class="fa-solid fa-chevron-right"></i></button></th>
                    <th class="sla-detail-th sla72-detail-reparo sla-detail-hidden">TOTAL REPARO <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla72-detail-reparo sla-detail-hidden">REPARO 72 HORAS <i class="fa-solid fa-sort"></i></th>
                    <th class="sla-detail-th sla72-detail-reparo sla-detail-hidden">SLA REPARO 72H <i class="fa-solid fa-sort"></i></th>
                    <th>SLA IAT 72 HORAS <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaSlaIat72Body"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 15C: CHURN RATE -->
    <div class="modal-overlay" id="modalChurnRate" onclick="fecharModalChurnRate(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color:#ffebee;color:#c62828;"><i class="fa-solid fa-user-minus"></i></div>
            <div class="modal-title-text">
              <h3>CHURN RATE</h3>
              <p>TAXA DE CANCELAMENTO MENSAL</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalChurnRate()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxChurnRate">
              <div class="modal-box-header"><span class="modal-box-title">Resultado</span><span class="modal-box-ideal-badge">Ideal &le; 1,5%</span></div>
              <div class="modal-box-row"><span class="modal-box-value" id="modalValAtualChurnRate">0,00%</span><span class="modal-box-diff diff-positive" id="modalValDiffChurnRate">DENTRO DA META</span></div>
              <span class="modal-box-msg" id="modalValMsgChurnRate"></span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header"><span class="modal-box-title">Composição do Cálculo</span><span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span></div>
              <div class="formula-visual-container">
                <div class="formula-card-item"><div class="formula-icon-badge badge-orange"><i class="fa-solid fa-user-xmark"></i></div><div class="formula-card-content"><span class="formula-card-label">CANCELAMENTOS</span><span class="formula-card-val" id="calcCancelamentosChurnRate">0</span></div></div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item"><div class="formula-icon-badge badge-blue"><i class="fa-solid fa-house"></i></div><div class="formula-card-content"><span class="formula-card-label">TOTAL DE INSTALAÇÕES</span><span class="formula-card-val" id="calcTotalInstalacoesChurnRate">0</span></div></div>
              </div>
              <span class="modal-box-msg" style="margin-top:6px;opacity:.8;">Fórmula: CANCELAMENTOS &divide; TOTAL DE INSTALAÇÕES &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica CHURN RATE (Mês a Mês)</div>
            <div class="chart-container" style="position:relative;height:260px;width:100%;"><canvas id="graficoEvolucaoChurnRate"></canvas></div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 15D: % ISR PASTOR -->
    <div class="modal-overlay" id="modalIsrPastor" onclick="fecharModalIsrPastor(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color:#ffebee;color:#c62828;"><i class="fa-solid fa-signal"></i></div>
            <div class="modal-title-text">
              <h3>% ISR PASTOR</h3>
              <p>ÍNDICE DE SAÚDE DOS REPAROS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalIsrPastor()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxIsrPastor">
              <div class="modal-box-header"><span class="modal-box-title">Resultado</span><span class="modal-box-ideal-badge">Ideal &le; 2%</span></div>
              <div class="modal-box-row"><span class="modal-box-value" id="modalValAtualIsrPastor">0,00%</span><span class="modal-box-diff diff-positive" id="modalValDiffIsrPastor">DENTRO DA META</span></div>
              <span class="modal-box-msg" id="modalValMsgIsrPastor"></span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header"><span class="modal-box-title">Composição do Cálculo</span><span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span></div>
              <div class="formula-visual-container">
                <div class="formula-card-item"><div class="formula-icon-badge badge-orange"><i class="fa-solid fa-signal"></i></div><div class="formula-card-content"><span class="formula-card-label">ONU COM SINAL CRITICO</span><span class="formula-card-val" id="calcOnuCriticoIsrPastor">0</span></div></div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item"><div class="formula-icon-badge badge-blue"><i class="fa-solid fa-network-wired"></i></div><div class="formula-card-content"><span class="formula-card-label">TOTAL DE ONU</span><span class="formula-card-val" id="calcTotalOnuIsrPastor">0</span></div></div>
              </div>
              <span class="modal-box-msg" style="margin-top:6px;opacity:.8;">Fórmula: ONU COM SINAL CRITICO &divide; TOTAL DE ONU &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica % ISR PASTOR (Mês a Mês)</div>
            <div class="chart-container" style="position:relative;height:260px;width:100%;"><canvas id="graficoEvolucaoIsrPastor"></canvas></div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 16: INSTALADOS X EFETIVADOS -->
    <div class="modal-overlay" id="modalInstEfet" onclick="fecharModalInstEfet(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-user-check"></i></div>
            <div class="modal-title-text">
              <h3>INSTALADOS X EFETIVADOS</h3>
              <p>PERCENTUAL DE CLIENTES INSTALADOS EM COMPARAÇÃO COM AS EFETIVAÇÕES REALIZADAS DENTRO DO MÊS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalInstEfet()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxInstEfet">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 90%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualInstEfet">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffInstEfet">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgInstEfet">Percentual de instalação sobre efetivação dentro da meta (≥ 90%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-house-signal"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Instalações Mês (Waves)</span><span class="formula-card-val" id="calcInstalacaoWaves">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-file-signature"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Efetivado Mês (Waves)</span><span class="formula-card-val" id="calcEfetivadoWaves">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Instalação Mês Atual (Waves) &divide; Efetivado Mês Atual (Waves) &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Instalados x Efetivados (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoInstEfet"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_gd7jv7xt2d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaInstEfetBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInstEfetBody', 1, 'num')">INSTALAÇÃO MÊS ATUAL (WAVES) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInstEfetBody', 2, 'num')">EFETIVADO MÊS ATUAL (WAVES) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaInstEfetBody', 3, 'num')">INSTALADOS X EFETIVADOS <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaInstEfetBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 17: SAFRA -->
    <div class="modal-overlay" id="modalSafra" onclick="fecharModalSafra(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-box-archive"></i></div>
            <div class="modal-title-text">
              <h3>SAFRA GERAL</h3>
              <p>APROVEITAMENTO DO RECOLHIMENTO NOS ÚLTIMOS 4 MESES</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalSafra()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxSafra">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 74%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualSafra">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffSafra">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgSafra">Percentual de Safra dentro da meta estipulada (≥ 74%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-boxes-packing"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Recolhido (ONU+FWA)</span><span class="formula-card-val" id="calcTotalRecolhidoSafra">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-clipboard-list"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Demanda Recolhimento (ONU+FWA)</span><span class="formula-card-val" id="calcDemandaRecolhimentoSafra">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: (Total Recolhido ONU + FWA) &divide; (Demanda Recolhimento ONU + FWA) &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica SAFRA (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoSafra"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaSafraBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSafraBody', 1, 'num')">DEMANDA RECOLHIMENTO (ONU) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSafraBody', 2, 'num')">TOTAL RECOLHIDO (ONU) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSafraBody', 3, 'num')">DEMANDA RECOLHIMENTO (FWA) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSafraBody', 4, 'num')">TOTAL RECOLHIDO (FWA) <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaSafraBody', 5, 'num')">SAFRA GERAL <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaSafraBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 17A: SAFRA FTTH -->
    <div class="modal-overlay" id="modalSafraFtth" onclick="fecharModalSafraFtth(event)"><div class="modal-container" onclick="event.stopPropagation();"><div class="modal-header"><div class="modal-title-area"><div class="modal-icon-box" style="background-color:#fff3e0;color:#f4511e;"><i class="fa-solid fa-network-wired"></i></div><div class="modal-title-text"><h3>SAFRA FTTH</h3><p>APROVEITAMENTO DO RECOLHIMENTO DE ONU NOS ÚLTIMOS 4 MESES</p></div></div><button class="modal-close-btn" onclick="fecharModalSafraFtth()"><i class="fa-solid fa-xmark"></i></button></div><div class="modal-body"><div class="modal-cards-row"><div class="modal-highlight-box box-success" id="modalHighlightBoxSafraFtth"><div class="modal-box-header"><span class="modal-box-title">Resultado</span><span class="modal-box-ideal-badge">Ideal &ge; 74%</span></div><div class="modal-box-row"><span class="modal-box-value" id="modalValAtualSafraFtth">0,00%</span><span class="modal-box-diff diff-positive" id="modalValDiffSafraFtth">DENTRO DA META</span></div><span class="modal-box-msg" id="modalValMsgSafraFtth"></span></div><div class="modal-highlight-box box-neutral"><div class="modal-box-header"><span class="modal-box-title">Composição do Cálculo</span><span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span></div><div class="formula-visual-container"><div class="formula-card-item"><div class="formula-icon-badge badge-orange"><i class="fa-solid fa-boxes-packing"></i></div><div class="formula-card-content"><span class="formula-card-label">Total Recolhido (ONU)</span><span class="formula-card-val" id="calcTotalRecolhidoSafraFtth">0</span></div></div><div class="formula-circle-op">&divide;</div><div class="formula-card-item"><div class="formula-icon-badge badge-blue"><i class="fa-solid fa-clipboard-list"></i></div><div class="formula-card-content"><span class="formula-card-label">Demanda Recolhimento (ONU)</span><span class="formula-card-val" id="calcDemandaRecolhimentoSafraFtth">0</span></div></div></div><span class="modal-box-msg" style="margin-top:6px;opacity:.8;">Fórmula: TOTAL RECOLHIDO (ONU) &divide; DEMANDA RECOLHIMENTO (ONU) &times; 100</span></div></div><div class="modal-chart-section" style="margin-top:20px;"><div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica SAFRA FTTH (Mês a Mês)</div><div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoSafraFtth"></canvas></div></div><div class="modal-table-section" style="margin-top:25px;"><div class="modal-chart-title"><i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)</div><div class="table-responsive"><table class="analitica-table"><thead><tr><th>CIDADE</th><th>TOTAL RECOLHIDO (ONU)</th><th>DEMANDA RECOLHIMENTO (ONU)</th><th>SAFRA FTTH <i class="fa-solid fa-sort-down"></i></th></tr></thead><tbody id="tabelaSafraFtthBody"></tbody></table></div></div></div></div></div>

    <!-- MODAL 17B: SAFRA FWA -->
    <div class="modal-overlay" id="modalSafraFwa" onclick="fecharModalSafraFwa(event)"><div class="modal-container" onclick="event.stopPropagation();"><div class="modal-header"><div class="modal-title-area"><div class="modal-icon-box" style="background-color:#fff3e0;color:#f4511e;"><i class="fa-solid fa-router"></i></div><div class="modal-title-text"><h3>SAFRA FWA</h3><p>APROVEITAMENTO DO RECOLHIMENTO DE EQUIPAMENTOS FWA NOS ÚLTIMOS 4 MESES</p></div></div><button class="modal-close-btn" onclick="fecharModalSafraFwa()"><i class="fa-solid fa-xmark"></i></button></div><div class="modal-body"><div class="modal-cards-row"><div class="modal-highlight-box box-success" id="modalHighlightBoxSafraFwa"><div class="modal-box-header"><span class="modal-box-title">Resultado</span><span class="modal-box-ideal-badge">Ideal &ge; 74%</span></div><div class="modal-box-row"><span class="modal-box-value" id="modalValAtualSafraFwa">0,00%</span><span class="modal-box-diff diff-positive" id="modalValDiffSafraFwa">DENTRO DA META</span></div><span class="modal-box-msg" id="modalValMsgSafraFwa"></span></div><div class="modal-highlight-box box-neutral"><div class="modal-box-header"><span class="modal-box-title">Composição do Cálculo</span><span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span></div><div class="formula-visual-container"><div class="formula-card-item"><div class="formula-icon-badge badge-orange"><i class="fa-solid fa-boxes-packing"></i></div><div class="formula-card-content"><span class="formula-card-label">Total Recolhido (FWA)</span><span class="formula-card-val" id="calcTotalRecolhidoSafraFwa">0</span></div></div><div class="formula-circle-op">&divide;</div><div class="formula-card-item"><div class="formula-icon-badge badge-blue"><i class="fa-solid fa-clipboard-list"></i></div><div class="formula-card-content"><span class="formula-card-label">Demanda Recolhimento (FWA)</span><span class="formula-card-val" id="calcDemandaRecolhimentoSafraFwa">0</span></div></div></div><span class="modal-box-msg" style="margin-top:6px;opacity:.8;">Fórmula: TOTAL RECOLHIDO (FWA) &divide; DEMANDA RECOLHIMENTO (FWA) &times; 100</span></div></div><div class="modal-chart-section" style="margin-top:20px;"><div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica SAFRA FWA (Mês a Mês)</div><div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoSafraFwa"></canvas></div></div><div class="modal-table-section" style="margin-top:25px;"><div class="modal-chart-title"><i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)</div><div class="table-responsive"><table class="analitica-table"><thead><tr><th>CIDADE</th><th>TOTAL RECOLHIDO (FWA)</th><th>DEMANDA RECOLHIMENTO (FWA)</th><th>SAFRA FWA <i class="fa-solid fa-sort-down"></i></th></tr></thead><tbody id="tabelaSafraFwaBody"></tbody></table></div></div></div></div></div>

    <!-- MODAL 18: ÍNDICE DE RETENÇÃO -->
    <div class="modal-overlay" id="modalIndiceRetencao" onclick="fecharModalIndiceRetencao(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-user-shield"></i></div>
            <div class="modal-title-text">
              <h3>ÍNDICE DE RETENÇÃO</h3>
              <p>CLIENTES REATIVADOS EM COMPARAÇÃO COM OS RECOLHIMENTOS GERADOS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalIndiceRetencao()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxIndiceRetencao">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 15%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualIndiceRetencao">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffIndiceRetencao">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgIndiceRetencao">Índice de retenção dentro da meta estipulada (≥ 15%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-user-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Reativados</span><span class="formula-card-val" id="calcTotalReativados">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-boxes-packing"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Gerados (Recolhimento + FWA)</span><span class="formula-card-val" id="calcTotalGeradosRecolhimento">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Reativados &divide; (Gerados Recolhimento + Gerados FWA) &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica ÍNDICE DE RETENÇÃO (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoIndiceRetencao"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaIndiceRetencaoBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceRetencaoBody', 1, 'num')">REATIVADOS MÊS ANTERIOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceRetencaoBody', 2, 'num')">GERADOS ONU MÊS ANTERIOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceRetencaoBody', 3, 'num')">GERADOS FWA MÊS ANTERIOR <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaIndiceRetencaoBody', 4, 'num')">% DE RETENÇÃO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaIndiceRetencaoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 19: CERTIFICAÇÃO -->
    <div class="modal-overlay" id="modalCertificacao" onclick="fecharModalCertificacao(event)">
      <div class="modal-container modal-container-tam" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-award"></i></div>
            <div class="modal-title-text">
              <h3>CERTIFICAÇÃO</h3>
              <p>ÍNDICE GERAL DE CONFORMIDADE E DESEMPENHO BASEADO NO ATINGIMENTO DAS METAS DOS INDICADORES ANALÍTICOS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalCertificacao()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxCertificacao">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 80%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualCertificacao">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffCertificacao">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgCertificacao">Índice de Certificação dentro da meta estipulada (≥ 80%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-circle-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Indicadores Bateu</span><span class="formula-card-val" id="calcBateuCertificacao">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Indicadores</span><span class="formula-card-val" id="calcTotalCertificacao">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Indicadores com Meta Atingida &divide; Total de Indicadores Aplicáveis &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica da Certificação (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoCertificacao"></canvas></div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 20: CERTIFICAÇÃO CIDADES -->
    <div class="modal-overlay" id="modalCertificacaoCidades" onclick="fecharModalCertificacaoCidades(event)">
      <div class="modal-container modal-container-tam" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-city"></i></div>
            <div class="modal-title-text">
              <h3>CERTIFICAÇÃO CIDADES</h3>
              <p>ÍNDICE GERAL DE CONFORMIDADE E DESEMPENHO BASEADO NO ATINGIMENTO DAS METAS DOS INDICADORES ANALÍTICOS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalCertificacaoCidades()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxCertificacaoCidades">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 50%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualCertificacaoCidades">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffCertificacaoCidades">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgCertificacaoCidades">Índice de Certificação Cidades dentro da meta estipulada (≥ 50%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-circle-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Bateu Ind</span><span class="formula-card-val" id="calcBateuCertificacaoCidades">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Total Ind</span><span class="formula-card-val" id="calcTotalCertificacaoCidades">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Bateu Ind &divide; Total Ind &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica da Certificação Cidades (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoCertificacaoCidades"></canvas></div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 21: % REGULARIZAÇÃO 15M -->
    <div class="modal-overlay" id="modalRegularizacao15m" onclick="fecharModalRegularizacao15m(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-screwdriver-wrench"></i></div>
            <div class="modal-title-text">
              <h3>% REGULARIZAÇÃO 15M</h3>
              <p>PERCENTUAL DE REGULARIZAÇÕES REALIZADAS NOS ÚLTIMOS 15 MESES</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalRegularizacao15m()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxRegularizacao15m">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &ge; 50%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualRegularizacao15m">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffRegularizacao15m">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgRegularizacao15m">% Regularização 15M dentro da meta estipulada (≥ 50%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-green"><i class="fa-solid fa-wrench"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Regularizado 15M</span><span class="formula-card-val" id="calcRegularizado15m">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-box"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Caixas</span><span class="formula-card-val" id="calcCaixas15m">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Regularizado 15M &divide; Caixas &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title"><i class="fa-solid fa-chart-area"></i> Evolução Histórica % Regularização 15M (Mês a Mês)</div>
            <div class="chart-container"><canvas id="graficoEvolucaoRegularizacao15m"></canvas></div>
          </div>
          <div class="modal-table-section">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
                <span class="badge-dados-mes"><i class="fa-solid fa-calendar-day"></i> Dados do Mês Atual</span>
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_qharllpood" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaRegularizacao15mBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRegularizacao15mBody', 1, 'num')">REGULARIZADO 15M <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRegularizacao15mBody', 2, 'num')">CAIXAS <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaRegularizacao15mBody', 3, 'num')" class="active-sort dir-desc">% REGULARIZAÇÃO 15M <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaRegularizacao15mBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 22: % EQUIPES 4º QUARTIL -->
    <div class="modal-overlay" id="modalEquipes4Quartil" onclick="fecharModalEquipes4Quartil(event)">
      <div class="modal-container modal-container-tam" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-chart-line-down"></i></div>
            <div class="modal-title-text">
              <h3>% EQUIPES 4º QUARTIL</h3>
              <p>PERCENTUAL DE EQUIPES NO 4º QUARTIL</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalEquipes4Quartil()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxEquipes4Quartil">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal = 0%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualEquipes4Quartil">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffEquipes4Quartil">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgEquipes4Quartil">% de Equipes no 4º Quartil dentro da meta estipulada (= 0%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-user-xmark"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Téc. 4º Quartil</span><span class="formula-card-val" id="calcTec4Quartil">0</span></div>
                </div>
                <div class="formula-circle-op">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Equipes</span><span class="formula-card-val" id="calcTotalEquipes4Q">0</span></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 6px; opacity: 0.8;">Fórmula: Téc. 4º Quartil &divide; Equipes &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section">
            <div class="modal-chart-title" style="display:flex;align-items:center;justify-content:space-between;gap:12px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica % Equipes 4º Quartil (Mês a Mês)<a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_7k68uhdu0d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a></div>
            <div class="chart-container"><canvas id="graficoEvolucaoEquipes4Quartil"></canvas></div>
          </div>

          <div class="quartil4-analitico-section">
            <div class="quartil4-analitico-title">
              <i class="fa-solid fa-table-list"></i>
              ANALÍTICO — 4º QUARTIL
            </div>
            <div class="quartil4-table-wrap">
              <table class="quartil4-table">
                <thead>
                  <tr>
                    <th>FUNCIONÁRIO</th>
                    <th>PONTOS</th>
                    <th>DIAS FINAL</th>
                    <th>MÉDIA</th>
                    <th>QUARTIL</th>
                  </tr>
                </thead>
                <tbody id="tabelaQuartil4Body"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 23: ATESTADO 12 MESES -->
    <div class="modal-overlay" id="modalAtestado12m" onclick="fecharModalAtestado12m(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-notes-medical"></i></div>
            <div class="modal-title-text">
              <h3>ATESTADO 12 MESES</h3>
              <p>MÉDIA DE ATESTADOS DOS ÚLTIMOS 12 MESES POR EQUIPE</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalAtestado12m()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxAtestado12m">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 1</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualAtestado12m">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffAtestado12m">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgAtestado12m">Média de atestados dentro da meta estipulada (&le; 1).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container" style="display:flex; align-items:center; gap:10px; margin-top:10px;">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-notes-medical"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Atestados 12M</span><br><strong class="formula-card-val" id="calcAtestado12m">0</strong></div>
                </div>
                <div class="formula-circle-op" style="font-size:18px; font-weight:bold;">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-users"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Equipes</span><br><strong class="formula-card-val" id="calcTotalEquipesAtestado">0</strong></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 8px; display:block; opacity: 0.8;">Fórmula: Atestados 12 Meses &divide; Equipes</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="display:flex;align-items:center;justify-content:space-between;gap:12px;"><span><i class="fa-solid fa-chart-area"></i> Evolução Histórica Atestado 12M (Mês a Mês)</span><a href="https://datastudio.google.com/u/0/reporting/1787f947-53f1-499d-91a8-d0fdd62b961b/page/p_52kumbzfud" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a></div>
            <div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoAtestado12m"></canvas></div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 24: IMPRODUTIVA IAT -->
    <div class="modal-overlay" id="modalImprodutivaIat" onclick="fecharModalImprodutivaIat(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-ban"></i></div>
            <div class="modal-title-text">
              <h3>IMPRODUTIVA IAT</h3>
              <p>PERCENTUAL DE DESATRIBUIÇÕES SOBRE ATRIBUIÇÕES NO MÊS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalImprodutivaIat()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxImprodutivaIat">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 15%</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualImprodutivaIat">0,00%</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffImprodutivaIat">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgImprodutivaIat">Percentual de Improdutiva IAT dentro da meta estipulada (&le; 15%).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container" style="display:flex; align-items:center; gap:10px; margin-top:10px;">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-xmark"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Desatribuições Total</span><br><strong class="formula-card-val" id="calcDesatribuicoesTotal">0</strong></div>
                </div>
                <div class="formula-circle-op" style="font-size:18px; font-weight:bold;">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-list-check"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Atribuições Total</span><br><strong class="formula-card-val" id="calcAtribuicoesTotal">0</strong></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 8px; display:block; opacity: 0.8;">Fórmula: (Desatribuições Inst + Rep) &divide; (Atribuições Inst + Rep) &times; 100</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Improdutiva IAT (Mês a Mês)</div>
            <div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoImprodutivaIat"></canvas></div>
          </div>
          <div class="modal-table-section" style="margin-top:25px;">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; margin-bottom:10px; font-weight:700;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/f895218b-8f6e-4cf4-b7b2-52e4b2ad443f/page/p_ugwniq52xd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 1, 'num')">ATRIBUIÇÕES INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 2, 'num')">DESATRIBUIÇÕES INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 3, 'num')">ATRIBUIÇÕES REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 4, 'num')">DESATRIBUIÇÕES REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaImprodutivaIatBody', 5, 'num')" class="active-sort dir-desc">IMPRODUTIVA IAT <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaImprodutivaIatBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 25: BACKLOG REPARO -->
    <div class="modal-overlay" id="modalBacklogReparo" onclick="fecharModalBacklogReparo(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-clock-rotate-left"></i></div>
            <div class="modal-title-text">
              <h3>BACKLOG REPARO</h3>
              <p>DEMANDA DE REPARO EM RELAÇÃO À CAPACIDADE PRODUZIDA NOS ÚLTIMOS 7 DIAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalBacklogReparo()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxBacklogReparo">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 2</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualBacklogReparo">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffBacklogReparo">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgBacklogReparo">Backlog de Reparo dentro da meta estipulada (&le; 2).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container" style="display:flex; align-items:center; gap:10px; margin-top:10px;">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-clipboard-list"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Demanda Reparo</span><br><strong class="formula-card-val" id="calcDemandaReparo">0</strong></div>
                </div>
                <div class="formula-circle-op" style="font-size:18px; font-weight:bold;">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-calendar-week"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Média 7 Dias Reparo</span><br><strong class="formula-card-val" id="calcMedia7DiasReparo">0</strong></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 8px; display:block; opacity: 0.8;">Fórmula: Demanda Reparo &divide; Média 7 Dias Reparo</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Backlog Reparo (Mês a Mês)</div>
            <div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoBacklogReparo"></canvas></div>
          </div>
          <div class="modal-table-section" style="margin-top:25px;">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; margin-bottom:10px; font-weight:700;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_6yoa6mziqd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaBacklogReparoBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogReparoBody', 1, 'num')">DEMANDA REPARO EM MAPA <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogReparoBody', 2, 'num')">MÉDIA FINALIZADOS 7 DIAS — REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogReparoBody', 3, 'num')" class="active-sort dir-desc">BACKLOG REPARO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaBacklogReparoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 26: BACKLOG INSTALAÇÃO -->
    <div class="modal-overlay" id="modalBacklogInstalacao" onclick="fecharModalBacklogInstalacao(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-house-signal"></i></div>
            <div class="modal-title-text">
              <h3>BACKLOG INSTALAÇÃO</h3>
              <p>DEMANDA DE INSTALAÇÃO EM RELAÇÃO À CAPACIDADE PRODUZIDA NOS ÚLTIMOS 7 DIAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalBacklogInstalacao()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxBacklogInstalacao">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 2</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualBacklogInstalacao">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffBacklogInstalacao">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgBacklogInstalacao">Backlog de Instalação dentro da meta estipulada (&le; 2).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container" style="display:flex; align-items:center; gap:10px; margin-top:10px;">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-clipboard-list"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Demanda Instalação</span><br><strong class="formula-card-val" id="calcDemandaInstalacao">0</strong></div>
                </div>
                <div class="formula-circle-op" style="font-size:18px; font-weight:bold;">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-calendar-week"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Média 7 Dias Instalação</span><br><strong class="formula-card-val" id="calcMedia7DiasInstalacao">0</strong></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 8px; display:block; opacity: 0.8;">Fórmula: Demanda Instalação &divide; Média 7 Dias Instalação</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Backlog Instalação (Mês a Mês)</div>
            <div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoBacklogInstalacao"></canvas></div>
          </div>
          <div class="modal-table-section" style="margin-top:25px;">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; margin-bottom:10px; font-weight:700;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_x02red1iqd" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaBacklogInstalacaoBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogInstalacaoBody', 1, 'num')">DEMANDA INSTALAÇÃO EM MAPA <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogInstalacaoBody', 2, 'num')">MÉDIA FINALIZADOS 7 DIAS — INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogInstalacaoBody', 3, 'num')" class="active-sort dir-desc">BACKLOG INSTALAÇÃO <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaBacklogInstalacaoBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL 27: BACKLOG IAT -->
    <div class="modal-overlay" id="modalBacklogIat" onclick="fecharModalBacklogIat(event)">
      <div class="modal-container" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div class="modal-title-area">
            <div class="modal-icon-box" style="background-color: #fff3e0; color: #f4511e;"><i class="fa-solid fa-clock-rotate-left"></i></div>
            <div class="modal-title-text">
              <h3>BACKLOG IAT</h3>
              <p>DEMANDA DE INSTALAÇÃO E REPARO EM RELAÇÃO À CAPACIDADE PRODUZIDA NOS ÚLTIMOS 7 DIAS</p>
            </div>
          </div>
          <button class="modal-close-btn" onclick="fecharModalBacklogIat()"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
          <div class="modal-cards-row">
            <div class="modal-highlight-box box-success" id="modalHighlightBoxBacklogIat">
              <div class="modal-box-header">
                <span class="modal-box-title">Realizado no Mês Vigente</span>
                <span class="modal-box-ideal-badge">Ideal &le; 2</span>
              </div>
              <div class="modal-box-row">
                <span class="modal-box-value" id="modalValAtualBacklogIat">0,00</span>
                <span class="modal-box-diff diff-positive" id="modalValDiffBacklogIat">DENTRO DA META</span>
              </div>
              <span class="modal-box-msg" id="modalValMsgBacklogIat">Backlog IAT dentro da meta estipulada (&le; 2).</span>
            </div>
            <div class="modal-highlight-box box-neutral">
              <div class="modal-box-header">
                <span class="modal-box-title">Composição do Cálculo</span>
                <span class="modal-box-ideal-badge"><i class="fa-solid fa-calculator"></i> Métrica</span>
              </div>
              <div class="formula-visual-container" style="display:flex; align-items:center; gap:10px; margin-top:10px;">
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-orange"><i class="fa-solid fa-clipboard-list"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Demanda Inst. + Rep.</span><br><strong class="formula-card-val" id="calcDemandaIat">0</strong></div>
                </div>
                <div class="formula-circle-op" style="font-size:18px; font-weight:bold;">&divide;</div>
                <div class="formula-card-item">
                  <div class="formula-icon-badge badge-blue"><i class="fa-solid fa-calendar-week"></i></div>
                  <div class="formula-card-content"><span class="formula-card-label">Média 7D Inst. + Rep.</span><br><strong class="formula-card-val" id="calcMedia7DiasIat">0</strong></div>
                </div>
              </div>
              <span class="modal-box-msg" style="margin-top: 8px; display:block; opacity: 0.8;">Fórmula: (Demanda Inst. + Demanda Rep.) &divide; (Média 7D Inst. + Média 7D Rep.)</span>
            </div>
          </div>
          <div class="modal-chart-section" style="margin-top:20px;">
            <div class="modal-chart-title" style="font-weight:700; margin-bottom:10px;"><i class="fa-solid fa-chart-area"></i> Evolução Histórica Backlog IAT (Mês a Mês)</div>
            <div class="chart-container" style="position:relative; height:260px; width:100%;"><canvas id="graficoEvolucaoBacklogIat"></canvas></div>
          </div>
          <div class="modal-table-section" style="margin-top:25px;">
            <div class="modal-chart-title" style="display: flex; align-items: center; justify-content: space-between; margin-bottom:10px; font-weight:700;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <i class="fa-solid fa-table"></i> Detalhamento Analítico (Base INDICADORES CIDADES)
              </div>
              <a href="https://datastudio.google.com/u/0/reporting/61c74141-5260-4a70-88c8-1f7cbc769e1a/page/p_v5xn2dfv0d" target="_blank" rel="noopener noreferrer" class="btn-link-externo" aria-label="Abrir analítico"><i class="fa-solid fa-up-right-from-square"></i>CLIQUE AQUI PARA VER O ANALÍTICO</a>
            </div>
            <div class="table-responsive">
              <table class="analitica-table">
                <thead>
                  <tr>
                    <th onclick="sortTable('tabelaBacklogIatBody', 0, 'str')">CIDADE <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogIatBody', 1, 'num')">DEMANDA INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogIatBody', 2, 'num')">MÉDIA 7 DIAS INSTALAÇÃO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogIatBody', 3, 'num')">DEMANDA REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogIatBody', 4, 'num')">MÉDIA 7 DIAS REPARO <i class="fa-solid fa-sort"></i></th>
                    <th onclick="sortTable('tabelaBacklogIatBody', 5, 'num')" class="active-sort dir-desc">BACKLOG IAT <i class="fa-solid fa-sort-down"></i></th>
                  </tr>
                </thead>
                <tbody id="tabelaBacklogIatBody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
    <script>
      var allData = [], pccData = [], tamData = [], abstData = [], kmlData = [], acidentesData = [], inspecaoData = [], tratarPtData = [], bhHeData = [], gpsPontoData = [], recorrenciaData = [], materialData = [], indicadoresCidadesData = [], quartil4Data = [], rankingData = { multiskill: [], recolhimento: [], totais: { multiskill: 0, recolhimento: 0 } }, selects = {};
      var meuGrafico = null, meuGraficoTam = null, meuGraficoAbs = null, meuGraficoKml = null, meuGraficoAcidentes = null, meuGraficoInspecao = null, meuGraficoTratarPonto = null, meuGraficoBhHe = null, meuGraficoGpsPonto = null, meuGraficoRecorrencia = null, meuGraficoConectorInst = null, meuGraficoConectorRep = null;
      var meuGraficoDropIat = null, meuGraficoIndiceVisitas = null, meuGraficoSlaIat = null, meuGraficoInstEfet = null, meuGraficoSafra = null, meuGraficoChurnRate = null, meuGraficoIsrPastor = null, meuGraficoIndiceRetencao = null, meuGraficoCertificacao = null, meuGraficoCertificacaoCidades = null, meuGraficoRegularizacao15m = null, meuGraficoEquipes4Quartil = null;
      var meuGraficoAtestado12m = null, meuGraficoImprodutivaIat = null, meuGraficoBacklogReparo = null, meuGraficoBacklogInstalacao = null, meuGraficoBacklogIat = null, meuGraficoSafraFtth = null, meuGraficoSafraFwa = null;
      var meuGraficoQuartilGerente = null, meuGraficoQuartilCoordenador = null;

      var metaIdeal = 7.5, metaTamIdeal = 80, metaAbsIdeal = 50, metaKmlIdeal = 54, metaInspecaoIdeal = 90, metaBhHeIdealSegundos = 240, metaGpsPontoIdeal = 15, metaRecorrenciaIdeal = 8, metaConectorInstIdeal = 2, metaConectorRepIdeal = 0.79, metaDropIatIdeal = 80, metaIndiceVisitasIdeal = 4, metaSlaIatIdeal = 65, metaInstEfetIdeal = 90, metaSafraIdeal = 74, metaSlaIat48Ideal = 70, metaSlaIat72Ideal = 90, metaSlaIat72CorIdeal = 90, metaChurnRateIdeal = 1.5, metaIsrPastorIdeal = 2, metaIndiceRetencaoIdeal = 15, metaCertificacaoIdeal = 80, metaCertificacaoCidadesIdeal = 50, metaRegularizacao15mIdeal = 50, metaEquipes4QuartilIdeal = 0, metaAtestado12mIdeal = 1, metaImprodutivaIatIdeal = 15, metaBacklogReparoIdeal = 2, metaBacklogInstalacaoIdeal = 2, metaBacklogIatIdeal = 2;

      var splashProgressInterval = null;
      var currentSplashPct = 0;

      function iniciarSmoothProgress() {
        if (splashProgressInterval) clearInterval(splashProgressInterval);
        currentSplashPct = 0;
        atualizarSplashUI(0, 'Iniciando processamento dos dados...');

        splashProgressInterval = setInterval(function() {
          if (currentSplashPct < 90) {
            var step = Math.floor(Math.random() * 4) + 2;
            currentSplashPct += step;
            if (currentSplashPct > 90) currentSplashPct = 90;

            var textoMsg = 'Carregando indicadores do sistema...';
            if (currentSplashPct > 20) textoMsg = 'Baixando base de Raio X do Supervisor...';
            if (currentSplashPct > 50) textoMsg = 'Calculando estatísticas e ranking Nordeste...';
            if (currentSplashPct > 75) textoMsg = 'Carregando indicadores do Raio X...';

            atualizarSplashUI(currentSplashPct, textoMsg);
          }
        }, 150);
      }

      function concluirSplashProgress() {
        if (splashProgressInterval) clearInterval(splashProgressInterval);
        currentSplashPct = 100;
        atualizarSplashUI(100, 'Carregamento Concluído!');

        setTimeout(function() {
          var splash = document.getElementById('splashScreenOverlay'); 
          if (splash) {
            splash.style.opacity = '0';
            splash.style.visibility = 'hidden';
          }
        }, 350);
      }

      function atualizarSplashUI(pct, statusTexto) {
        var circle = document.getElementById('splashCircleBar');
        var percentText = document.getElementById('splashPercentText');
        var statusText = document.getElementById('splashStatusText');
        var fill = document.getElementById('splashProgressBarFill');
        
        if (pct < 0) pct = 0;
        if (pct > 100) pct = 100;

        if (circle) {
          var radius = 45;
          var circumference = 2 * Math.PI * radius;
          var offset = circumference - (pct / 100) * circumference;
          circle.style.strokeDashoffset = offset;
        }
        if (percentText) percentText.textContent = Math.round(pct) + '%';
        if (fill) fill.style.width = pct + '%';
        if (statusTexto && statusText) statusText.textContent = statusTexto;
      }

      function ocultarLoadingOverlay() { 
        concluirSplashProgress();
      }

      function getRankingStars(posicaoRaw) {
        if (!posicaoRaw) return '';
        var str = String(posicaoRaw).trim();
        var match = str.match(/^\d+/);
        if (!match) return '';
        var numPos = parseInt(match[0], 10);

        if (numPos === 1) return ' ⭐⭐⭐⭐⭐';
        if (numPos === 2) return ' ⭐⭐⭐⭐';
        if (numPos === 3) return ' ⭐⭐⭐';
        if (numPos >= 4 && numPos <= 10) return ' ⭐';
        return ' 🏃';
      }

      function normalizarPosicaoRanking(valor) {
        var str = String(valor === undefined || valor === null ? '' : valor).trim();
        var match = str.match(/\d+/);
        return match ? parseInt(match[0], 10) : 999999;
      }

      function estrelasRankingExecutivo(posicao) {
        var n = normalizarPosicaoRanking(posicao);
        if (n === 1) return '★★★★★';
        if (n === 2) return '★★★★';
        if (n === 3) return '★★★';
        if (n >= 4 && n <= 10) return '★';
        return '';
      }

      function escaparHtmlRanking(valor) {
        return String(valor === undefined || valor === null ? '' : valor)
          .replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;')
          .replace(/"/g, '&quot;').replace(/'/g, '&#039;');
      }

      // A barra representa a CERTIFICAÇÃO real em uma escala fixa de 0 a 100%.
      // Ex.: 94,74% = 94,74% da barra; 100% = barra totalmente preenchida.
      function calcularPercentualRanking(certificacao) {
        if (certificacao === null || certificacao === undefined) return 0;
        var texto = String(certificacao).trim();
        if (!texto) return 0;
        var numero = parseFloat(texto.replace(/[^0-9,.-]/g, '').replace(',', '.'));
        if (!isFinite(numero)) return 0;
        if (texto.indexOf('%') === -1 && Math.abs(numero) <= 1) numero *= 100;
        return Math.max(0, Math.min(100, numero));
      }

      // A CERTIFICAÇÃO exibida no ranking vem diretamente da aba RANKING.
      // MULTISKILL = coluna B | RECOLHIMENTO = coluna I.
      function formatarCertificacaoRanking(valor) {
        if (valor === null || valor === undefined || String(valor).trim() === '') return '-';
        var texto = String(valor).trim();
        var temPercentual = texto.indexOf('%') !== -1;
        var numero = parseFloat(texto.replace(/[^0-9,.-]/g, '').replace(',', '.'));
        if (!isFinite(numero)) return texto;
        if (!temPercentual && Math.abs(numero) <= 1) numero *= 100;
        return numero.toFixed(2).replace('.', ',') + '%';
      }

      function obterFotoRanking(nome) {
        var nomeNormalizado = limparTexto(nome);
        if (!nomeNormalizado || !Array.isArray(allData)) return '';

        var registro = null;
        for (var i = 0; i < allData.length; i++) {
          if (limparTexto(getValueSafe(allData[i], ['NOME'])) === nomeNormalizado) {
            registro = allData[i];
            break;
          }
        }
        if (!registro) {
          registro = allData.find(function(d) {
            return comparaNomeFlexivel(getValueSafe(d, ['NOME']), nome);
          });
        }
        if (!registro) return '';

        return urlFotoNormalizadaTv(getValueSafe(registro, [
          'FOTO 2', 'FOTO2', 'FOTO', 'LINK FOTO', 'URL DA FOTO',
          'URL FOTO', 'LINK DA FOTO', 'IMAGEM'
        ]));
      }

      function iniciaisRanking(nome) {
        var partes = String(nome || '').trim().split(/\s+/).filter(Boolean);
        if (!partes.length) return '?';
        if (partes.length === 1) return partes[0].slice(0, 2).toUpperCase();
        return (partes[0].charAt(0) + partes[partes.length - 1].charAt(0)).toUpperCase();
      }

      function renderRankingExecutivo(tipo, dados, total) {
        var prefixo = tipo === 'multiskill' ? 'Multiskill' : 'Recolhimento';
        var board = document.getElementById('ranking' + prefixo + 'Board');
        var totalEl = document.getElementById('ranking' + prefixo + 'Total');
        if (!board) return;

        var ranking = Array.isArray(dados) ? dados.slice() : [];
        ranking.sort(function(a, b) {
          return normalizarPosicaoRanking(a.posicao) - normalizarPosicaoRanking(b.posicao);
        });
        ranking = ranking.slice(0, 10);

        var totalNum = Number(total) || ranking.length;
        if (totalEl) {
          totalEl.textContent = totalNum + (totalNum === 1 ? ' colaborador' : ' colaboradores');
        }

        if (!ranking.length) {
          board.innerHTML = '<div class="ranking-empty">Nenhum dado de ranking disponível.</div>';
          return;
        }

        var html = '';
        html += '<div class="ranking-board-head">';
        html += '<span>Posição</span><span>COLABORADOR</span><span>CERTIFICAÇÃO</span>';
        html += '</div>';

        ranking.forEach(function(item, index) {
          var pos = normalizarPosicaoRanking(item.posicao);
          var nome = item.nome || '-';
          var stars = estrelasRankingExecutivo(pos);
          var medal = tipo === 'recolhimento'
            ? (pos === 1 ? '🥇' : '★')
            : (pos === 1 ? '🥇' : (pos === 2 ? '🥈' : (pos === 3 ? '🥉' : '★')));
          var label = String(pos).padStart(2, '0') + 'º LUGAR';
          var title = escaparHtmlRanking(nome);
          var percentual = calcularPercentualRanking(item.certificacao);
          var pctTexto = formatarCertificacaoRanking(item.certificacao);
          var isRecognition = tipo === 'multiskill' ? pos <= 3 : pos === 1;
          var delay = (index * 70) + 'ms';
          var fotoUrl = obterFotoRanking(nome);
          var iniciais = escaparHtmlRanking(iniciaisRanking(nome));

          html += '<div class="ranking-board-row rank-' + pos + (isRecognition ? ' recognition-rank' : '') + '" data-ranking-index="' + index + '" style="--rank-progress:' + percentual.toFixed(2) + '%; animation-delay:' + delay + '" title="' + title + ' — posição ' + pos + ' de ' + totalNum + '">';
          html +=   '<div class="ranking-board-position">';
          html +=     '<span class="ranking-board-number">' + medal + '</span>';
          html +=     '<span class="ranking-board-place">' + label + '</span>';
          html +=   '</div>';
          html +=   '<div class="ranking-board-name-wrap">';
          if (fotoUrl) {
            html += '<img class="ranking-board-avatar" src="' + escaparHtmlRanking(fotoUrl) + '" alt="Foto de ' + title + '" loading="lazy" onerror="this.style.display=\'none\'; this.nextElementSibling.style.display=\'inline-flex\';">';
            html += '<span class="ranking-board-avatar-fallback" style="display:none">' + iniciais + '</span>';
          } else {
            html += '<span class="ranking-board-avatar-fallback">' + iniciais + '</span>';
          }
          html +=     '<div class="ranking-board-name-content">';
          html +=       '<span class="ranking-board-name">' + title + '</span>';
          html +=       '<div class="ranking-mini-track" aria-hidden="true"><div class="ranking-mini-fill" style="width:' + percentual.toFixed(2) + '%"></div></div>';
          html +=     '</div>';
          html +=   '</div>';
          html +=   '<div class="ranking-board-award">';
          html +=     '<span class="ranking-board-percent" title="Posição relativa no ranking">' + pctTexto + '</span>';
          html +=     '<span class="ranking-board-stars" aria-label="' + stars.length + ' estrelas">' + stars + '</span>';
          html +=   '</div>';
          html += '</div>';
        });

        board.innerHTML = html;
        iniciarAnimacaoRanking(board);
      }

      function iniciarAnimacaoRanking(board) {
        if (!board) return;
        if (board._rankingMotionTimer) {
          clearInterval(board._rankingMotionTimer);
          board._rankingMotionTimer = null;
        }
        if (board._rankingMouseEnter) board.removeEventListener('mouseenter', board._rankingMouseEnter);
        if (board._rankingMouseLeave) board.removeEventListener('mouseleave', board._rankingMouseLeave);

        var rows = Array.prototype.slice.call(board.querySelectorAll('.ranking-board-row[data-ranking-index]'));
        if (!rows.length || window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;

        var index = 0;
        function destacar() {
          rows.forEach(function(row) { row.classList.remove('ranking-active', 'ranking-pulse'); });
          var row = rows[index];
          if (!row) return;
          row.classList.add('ranking-active');
          void row.offsetWidth;
          row.classList.add('ranking-pulse');
          setTimeout(function() { if (row) row.classList.remove('ranking-active'); }, 1550);
          index = (index + 1) % rows.length;
        }
        function iniciarCiclo() {
          if (board._rankingMotionTimer) clearInterval(board._rankingMotionTimer);
          destacar();
          board._rankingMotionTimer = setInterval(destacar, 2300);
        }

        board._rankingMouseEnter = function() {
          if (board._rankingMotionTimer) {
            clearInterval(board._rankingMotionTimer);
            board._rankingMotionTimer = null;
          }
        };
        board._rankingMouseLeave = function() {
          index = 0;
          iniciarCiclo();
        };
        board.addEventListener('mouseenter', board._rankingMouseEnter);
        board.addEventListener('mouseleave', board._rankingMouseLeave);
        iniciarCiclo();
      }

      function renderRankingsExecutivos() {
        renderRankingExecutivo('multiskill', rankingData.multiskill, rankingData.totais && rankingData.totais.multiskill);
        renderRankingExecutivo('recolhimento', rankingData.recolhimento, rankingData.totais && rankingData.totais.recolhimento);
      }

      function limparTexto(txt) { 
        return !txt ? '' : String(txt).normalize("NFD").replace(/[\u0300-\u036f]/g, "").trim().toUpperCase(); 
      }

      function comparaNomeFlexivel(nome1, nome2) {
        if (!nome1 || !nome2) return false;
        var n1 = limparTexto(nome1), n2 = limparTexto(nome2);
        if (n1 === n2) return true;
        if (n1.length > 3 && n2.length > 3) {
          return n1.indexOf(n2) !== -1 || n2.indexOf(n1) !== -1;
        }
        return false;
      }

      function segundosParaHHMMSS(val) {
        if (val === undefined || val === null || String(val).trim() === '') return '00:00:00';
        var strVal = String(val).trim();
        if (strVal.indexOf(':') !== -1 && strVal.indexOf('-') === -1) {
          var partes = strVal.split(':');
          return partes.length === 2 ? partes[0] + ':' + partes[1] + ':00' : strVal;
        }
        var sec = Math.round(parseFloat(strVal.replace(',', '.')));
        if (isNaN(sec) || sec < 0) sec = 0;
        var h = Math.floor(sec / 3600), m = Math.floor((sec % 3600) / 60), s = sec % 60;
        return (h < 10 ? "0" + h : h) + ":" + (m < 10 ? "0" + m : m) + ":" + (s < 10 ? "0" + s : s);
      }

      function duracaoParaSegundos(val) {
        if (val === undefined || val === null || String(val).trim() === '') return 0;
        if (typeof val === 'number') {
          if (!isFinite(val) || val < 0) return 0;
          return val > 0 && val <= 1 ? Math.round(val * 86400) : Math.round(val);
        }
        var str = String(val).trim();
        if (!str) return 0;
        var match = str.match(/^(?:-)?(\d+):(\d{1,2})(?::(\d{1,2}))?$/);
        if (match) {
          var h = parseInt(match[1], 10) || 0;
          var m = parseInt(match[2], 10) || 0;
          var sec = parseInt(match[3] || '0', 10) || 0;
          return Math.max(0, h * 3600 + m * 60 + sec);
        }
        if (str.indexOf('1899') !== -1 || str.indexOf('30/12') !== -1) {
          var hora = str.match(/(\d{1,}):(\d{2})(?::(\d{2}))?/);
          if (hora) return (parseInt(hora[1],10)||0)*3600 + (parseInt(hora[2],10)||0)*60 + (parseInt(hora[3]||'0',10)||0);
        }
        var n = parseFloat(str.replace(',', '.'));
        if (!isNaN(n) && isFinite(n)) return n > 0 && n <= 1 ? Math.round(n * 86400) : Math.round(n);
        return 0;
      }

      function formatarHoraEspecial(val) {
        if (!val) return '00:00:00';
        var strVal = String(val).trim();
        if (strVal === '1899-12-30' || strVal === '30/12/1899') return '00:00:00';
        if (strVal.indexOf('1899') !== -1 || strVal.indexOf('30/12') !== -1) {
          var m = strVal.match(/\d{2}:\d{2}(:\d{2})?/);
          if (m) return m[0].length === 5 ? m[0] + ':00' : m[0];
        }
        return segundosParaHHMMSS(val);
      }

      function formatTableInteger(val) {
        if (val === undefined || val === null || String(val).trim() === '') return '-';
        var num = parseFloat(String(val).replace(',', '.'));
        return isNaN(num) ? val : Math.round(num).toString();
      }

      function formatTableNumber(val) {
        if (val === undefined || val === null || String(val).trim() === '') return '-';
        var num = parseFloat(String(val).replace(',', '.'));
        if (isNaN(num)) return val;
        return num % 1 === 0 ? num.toString() : num.toFixed(2).replace('.', ',');
      }

      function formatCurrency(val) {
        if (val === undefined || val === null || String(val).trim() === '') return 'R$ 0,00';
        var num = parseFloat(String(val).replace(',', '.'));
        return isNaN(num) ? val : 'R$ ' + num.toFixed(2).replace('.', ',');
      }
      
      function formatarDataBR(dataStr) {
        if (!dataStr) return '-';
        if (String(dataStr).indexOf('-') !== -1) {
          var p = String(dataStr).split('-');
          if(p.length === 3) return p[2] + '/' + p[1] + '/' + p[0];
        }
        return dataStr;
      }

      function formatarCertificacao(val) {
        if (val === undefined || val === null || String(val).trim() === '') return '-';
        var num = parseFloat(String(val).replace(',', '.'));
        if (isNaN(num)) return String(val).trim();
        if (num <= 1 && num > 0) num *= 100;
        return num.toFixed(2).replace('.', ',') + '%';
      }

      function getValueSafe(obj, chavesPossiveis) {
        if (!obj) return undefined;
        var keysObj = Object.keys(obj);
        for (var k = 0; k < keysObj.length; k++) {
          var keyOriginal = keysObj[k], keyLimpa = limparTexto(keyOriginal).replace(/\s+/g, ""); 
          for (var i = 0; i < chavesPossiveis.length; i++) {
            if (keyLimpa === limparTexto(chavesPossiveis[i]).replace(/\s+/g, "")) return obj[keyOriginal];
          }
        }
        for (var k = 0; k < keysObj.length; k++) {
          var keyOriginal = keysObj[k], keyLimpa = limparTexto(keyOriginal).replace(/\s+/g, ""); 
          for (var i = 0; i < chavesPossiveis.length; i++) {
            if (keyLimpa.indexOf(limparTexto(chavesPossiveis[i]).replace(/\s+/g, "")) !== -1) return obj[keyOriginal];
          }
        }
        return undefined;
      }

      // Leitura EXATA dos novos campos da página RAIO X.
      // Não usa outros indicadores nem campos alternativos.
      function getRaioXCampoExato(obj, nomeCampo) {
        if (!obj) return undefined;
        if (Object.prototype.hasOwnProperty.call(obj, nomeCampo)) return obj[nomeCampo];
        var alvo = limparTexto(nomeCampo).replace(/\s+/g, "");
        var chaves = Object.keys(obj);
        for (var i = 0; i < chaves.length; i++) {
          var limpa = limparTexto(chaves[i]).replace(/\s+/g, "");
          if (limpa === alvo) return obj[chaves[i]];
        }
        return undefined;
      }

      // Na base RAIO X existem dois campos relacionados a TRATAR PONTO.
      // O indicador principal usa EXCLUSIVAMENTE "TRATAR PONTO".
      // O segundo campo é "TRATAR PONTO 2" e não entra no cálculo principal.
      function normalizarNomeColunaRaioX(nome) {
        return limparTexto(nome || '').replace(/[^a-z0-9]/g, '');
      }

      function getRaioXTratarPonto(obj) {
        // Após a correção do cabeçalho da planilha, usa diretamente o nome
        // exclusivo da coluna "TRATAR PONTO". Não usar posição/AK evita
        // que outra coluna seja lida por engano.
        return getRaioXCampoExato(obj, 'TRATAR PONTO');
      }

      function getRaioXTratarPonto2(obj) {
        return getRaioXCampoExato(obj, 'TRATAR PONTO 2');
      }

      // TAM: usa diretamente as colunas da RAIO X.
      // Fórmula: soma de BATEU META ÷ soma de TOTAL EQUIPES × 100.
      // O mesmo cálculo é usado no card, no modal e no histórico.
      function getRaioXBateuMeta(obj) {
        // Campo oficial da aba RAIO X: BATEU META.
        // A leitura é estritamente pelo cabeçalho, sem buscar campos parecidos.
        return getRaioXCampoExato(obj, 'BATEU META');
      }

      function getRaioXTotalEquipes(obj) {
        // Aceita apenas as duas grafias do cabeçalho da RAIO X.
        // Na planilha ele pode aparecer como TOTAL EQUIPE ou TOTAL EQUIPES.
        var valor = getRaioXCampoExato(obj, 'TOTAL EQUIPES');
        if (valor === undefined) valor = getRaioXCampoExato(obj, 'TOTAL EQUIPE');
        return valor;
      }

      function calcularTamRaioXAgregado(dataContext) {
        var base = Array.isArray(dataContext) ? dataContext : [];
        if (!base.length) return { bateu: 0, equipes: 0, percentual: 0 };

        var bateuTotal = 0;
        var equipesTotal = 0;
        base.forEach(function(d) {
          bateuTotal += getNumeroLimpo(getRaioXBateuMeta(d));
          equipesTotal += getNumeroLimpo(getRaioXTotalEquipes(d));
        });

        return {
          bateu: bateuTotal,
          equipes: equipesTotal,
          percentual: equipesTotal > 0 ? (bateuTotal / equipesTotal) * 100 : 0
        };
      }

      function calcularTamRaioXAgregadoMes(dataContext, mesStr) {
        var subset = (Array.isArray(dataContext) ? dataContext : []).filter(function(d) {
          return formatarMes(getValueSafe(d, ['MÊS'])) === mesStr;
        });
        return calcularTamRaioXAgregado(subset);
      }

      // ÍNDICE DE VISITAS: usa EXCLUSIVAMENTE os campos oficiais da RAIO X.
      // Fórmula: total VISITAS DE REPARO ÷ total BASE DE CLIENTES × 100.
      function calcularIndiceVisitasRaioXAgregado(dataContext) {
        var base = Array.isArray(dataContext) ? dataContext : [];
        var visitas = 0, clientes = 0;
        base.forEach(function(d) {
          visitas += getNumeroLimpo(getRaioXCampoExato(d, 'VISITAS DE REPARO'));
          clientes += getNumeroLimpo(getRaioXCampoExato(d, 'BASE DE CLIENTES'));
        });
        return { visitas: visitas, clientes: clientes, percentual: clientes > 0 ? (visitas / clientes) * 100 : 0 };
      }

      function calcularIndiceVisitasRaioXAgregadoMes(dataContext, mesStr) {
        var subset = (Array.isArray(dataContext) ? dataContext : []).filter(function(d) {
          return formatarMes(getValueSafe(d, ['MÊS'])) === mesStr;
        });
        return calcularIndiceVisitasRaioXAgregado(subset);
      }

      // INSTALADOS X EFETIVADOS: usa EXCLUSIVAMENTE os campos oficiais da RAIO X.
      // Fórmula: Instalações Mês (Waves) ÷ Efetivado Mês (Waves) × 100.
      function calcularInstEfetRaioXAgregado(dataContext) {
        var base = Array.isArray(dataContext) ? dataContext : [];
        var instalacoes = 0, efetivados = 0;
        base.forEach(function(d) {
          instalacoes += getNumeroLimpo(getRaioXCampoExato(d, 'INSTALAÇÃO MÊS ATUAL (WAVES)'));
          efetivados += getNumeroLimpo(getRaioXCampoExato(d, 'EFETIVADO MÊS ATUAL (WAVES)'));
        });
        return { instalacoes: instalacoes, efetivados: efetivados, percentual: efetivados > 0 ? (instalacoes / efetivados) * 100 : 0 };
      }

      function calcularInstEfetRaioXAgregadoMes(dataContext, mesStr) {
        var subset = (Array.isArray(dataContext) ? dataContext : []).filter(function(d) {
          return formatarMes(getValueSafe(d, ['MÊS'])) === mesStr;
        });
        return calcularInstEfetRaioXAgregado(subset);
      }

      // TAM KM/L: usa EXCLUSIVAMENTE os campos oficiais da RAIO X.
      // Fórmula: soma de BATEU KM/L ÷ soma de TOTAL KM/L × 100.
      // O mesmo cálculo é usado no card geral, tendência e modal analítico.
      function getRaioXBateuKmL(obj) {
        return getRaioXCampoExato(obj, 'BATEU KM/L');
      }

      function getRaioXTotalKmL(obj) {
        return getRaioXCampoExato(obj, 'TOTAL KM/L');
      }

      function calcularTamKmlRaioXAgregado(dataContext) {
        var base = Array.isArray(dataContext) ? dataContext : [];
        if (!base.length) return { bateu: 0, total: 0, percentual: 0 };

        var bateuTotal = 0;
        var totalKml = 0;
        base.forEach(function(d) {
          bateuTotal += getNumeroLimpo(getRaioXBateuKmL(d));
          totalKml += getNumeroLimpo(getRaioXTotalKmL(d));
        });

        return {
          bateu: bateuTotal,
          total: totalKml,
          percentual: totalKml > 0 ? (bateuTotal / totalKml) * 100 : 0
        };
      }

      function calcularTamKmlRaioXAgregadoMes(dataContext, mesStr) {
        var subset = (Array.isArray(dataContext) ? dataContext : []).filter(function(d) {
          return formatarMes(getValueSafe(d, ['MÊS'])) === mesStr;
        });
        return calcularTamKmlRaioXAgregado(subset);
      }

      function verificarCamposChurnIsr() {
        if (!allData || allData.length === 0) return;
        var campos = ['CANCELAMENTOS (FTTH)', 'TOTAL_DE_ACESSOS (MÊS)', 'ONUS CRÍTICOS', 'TOTAL ONUS'];
        var faltantes = campos.filter(function(campo) { return getRaioXCampoExato(allData[0], campo) === undefined; });
        if (faltantes.length) {
          console.warn('Campos novos não encontrados no objeto RAIO X recebido por getDashboardData:', faltantes);
        }
      }

      function getTamPontosValue(obj) {
        if (!obj) return 0;
        var keys = Object.keys(obj);
        for (var i = 0; i < keys.length; i++) {
          var kClean = limparTexto(keys[i]);
          if (kClean === "PONTO" || kClean === "PONTOS" || kClean === "PONTUACAO" || kClean === "PONTUAÇÃO") return obj[keys[i]];
        }
        return getValueSafe(obj, ['PONTO', 'PONTOS', 'PONTUAÇÃO', 'PONTUACAO']);
      }

      function atualizarEstadoValoresKpi() {
        document.querySelectorAll('.kpi-value').forEach(function(el) {
          var semDado = el.textContent.trim() === '-';
          el.classList.toggle('no-data', semDado);
        });
      }

      function observarValoresKpi() {
        atualizarEstadoValoresKpi();
        document.querySelectorAll('.kpi-value').forEach(function(el) {
          var observer = new MutationObserver(function() {
            var semDado = el.textContent.trim() === '-';
            el.classList.toggle('no-data', semDado);
          });
          observer.observe(el, { childList: true, characterData: true, subtree: true });
        });
      }

      // ========================= MODO TV — APRESENTAÇÃO AUTOMÁTICA =========================
      var tvApresentacaoTimer = null;
      var tvContagemInterval = null;
      var tvContagemRegressiva = 10;
      var tvApresentacaoPausada = false;
      var tvApresentacaoTrocaEmAndamento = false;
      var tvApresentacaoNomes = [];
      var tvApresentacaoIndice = -1;
      var tvApresentacaoEmExecucao = false;
      // Nome do colaborador atualmente exibido no Modo TV.
      // Usado pelos analíticos quando o filtro de colaborador permanece vazio.
      var tvApresentacaoNomeAtual = '';
      var tvFotosCache = {};
      var tvFotosPrecarregando = null;

      function urlFotoNormalizadaTv(valor) {
        if (!valor || String(valor).trim() === '') return '';
        var strUrl = String(valor).trim();
        var driveMatch = strUrl.match(/[-\w]{25,}/);
        if (driveMatch) strUrl = 'https://lh3.googleusercontent.com/d/' + driveMatch[0];
        return strUrl;
      }

      function obterUrlFotoColaboradorTv(nome) {
        var nomeNormalizado = limparTexto(nome);
        if (!nomeNormalizado) return '';

        var dataAtual = null;
        var mesesSelecionados = selects['filterMes'] ? selects['filterMes'].getValue() : [];
        var mesAtualFiltro = Array.isArray(mesesSelecionados) && mesesSelecionados.length === 1
          ? mesesSelecionados[0]
          : (typeof mesesSelecionados === 'string' ? mesesSelecionados : null);

        for (var i = 0; i < allData.length; i++) {
          if (limparTexto(getValueSafe(allData[i], ['NOME'])) === nomeNormalizado) {
            if (mesAtualFiltro) {
              if (valorFiltroNormalizado(formatarMes(allData[i]['MÊS'])) === valorFiltroNormalizado(mesAtualFiltro)) {
                dataAtual = allData[i];
                break;
              }
            } else {
              dataAtual = allData[i];
              break;
            }
          }
        }

        if (!dataAtual) {
          dataAtual = allData.find(function(d) {
            return limparTexto(getValueSafe(d, ['NOME'])) === nomeNormalizado;
          });
        }
        if (!dataAtual) return '';

        return urlFotoNormalizadaTv(getValueSafe(dataAtual, ['FOTO 2', 'FOTO2', 'FOTO', 'LINK FOTO', 'URL DA FOTO', 'URL FOTO', 'LINK DA FOTO', 'IMAGEM']));
      }

      function precarregarFotosApresentacaoTv(nomes) {
        var lista = Array.isArray(nomes) ? nomes.slice() : [];
        if (!lista.length) return Promise.resolve();

        var promessas = lista.map(function(nome) {
          var chave = limparTexto(nome);
          if (!chave || tvFotosCache[chave]) return Promise.resolve();

          var url = obterUrlFotoColaboradorTv(nome);
          if (!url) {
            tvFotosCache[chave] = null;
            return Promise.resolve();
          }

          return new Promise(function(resolve) {
            var img = new Image();
            var finalizada = false;
            function concluir(imgPrecarregada) {
              if (finalizada) return;
              finalizada = true;
              tvFotosCache[chave] = imgPrecarregada || null;
              resolve();
            }
            img.onload = function() {
              // Além de baixar a imagem, força o navegador a decodificá-la
              // antes da troca visual. Isso elimina o pequeno intervalo em que
              // a foto anterior ainda fica aparecendo no Modo TV.
              if (typeof img.decode === 'function') {
                img.decode().then(function() { concluir(img); }).catch(function() { concluir(img); });
              } else {
                concluir(img);
              }
            };
            img.onerror = function() { concluir(null); };
            img.src = url;
          });
        });

        tvFotosPrecarregando = Promise.all(promessas).finally(function() {
          tvFotosPrecarregando = null;
        });
        return tvFotosPrecarregando;
      }

      function obterFiltroTvArray(id) {
        var val = selects[id] ? selects[id].getValue() : [];
        return Array.isArray(val) ? val.filter(function(v) { return String(v || '').trim() !== ''; }) : (val ? [val] : []);
      }

      function obterSupervisoresDaApresentacaoTv() {
        var nomesJaVistos = {};
        var lista = [];

        // Para a apresentação sem filtro de colaborador/coordenador/gerente,
        // não dependemos do getFilteredData(), pois ele pode refletir o contexto
        // atual dos KPIs. Aqui queremos explicitamente todos os colaboradores
        // respeitando apenas os demais filtros visuais (mês, setor e quartil).
        var nomesSelecionados = obterFiltroTvArray('filterNome');
        var coordenadoresSelecionados = obterFiltroTvArray('filterCoordenador');
        var gerentesSelecionados = obterFiltroTvArray('filterGerente');
        var mesesSelecionados = obterFiltroTvArray('filterMes');
        var setoresSelecionados = obterFiltroTvArray('filterSetor');
        var quartisSelecionados = obterFiltroTvArray('filterQuartil');

        var dataContexto = allData.filter(function(item) {
          var atendeNome = nomesSelecionados.length === 0 || registroAtendeFiltro(item, 'NOME', nomesSelecionados);
          var atendeCoord = coordenadoresSelecionados.length === 0 || registroAtendeFiltro(item, 'COORDENADOR DE CAMPO', coordenadoresSelecionados);
          var atendeGerente = gerentesSelecionados.length === 0 || registroAtendeFiltro(item, 'GERENTE DE CAMPO', gerentesSelecionados);
          var atendeMes = mesesSelecionados.length === 0 || mesesSelecionados.some(function(m) {
            return valorFiltroNormalizado(formatarMes(obterValorFiltroDoRegistro(item, 'MÊS'))) === valorFiltroNormalizado(m);
          });
          var atendeSetor = setoresSelecionados.length === 0 || registroAtendeFiltro(item, 'SETOR', setoresSelecionados);
          var atendeQuartil = quartisSelecionados.length === 0 || registroAtendeFiltro(item, 'QUARTIL RAIO X', quartisSelecionados);
          return atendeNome && atendeCoord && atendeGerente && atendeMes && atendeSetor && atendeQuartil;
        });

        dataContexto.forEach(function(item) {
          var nome = getValueSafe(item, ['NOME']);
          if (nome !== undefined && nome !== null && String(nome).trim() !== '') {
            var chave = limparTexto(nome);
            if (chave && !nomesJaVistos[chave]) {
              nomesJaVistos[chave] = true;
              lista.push(String(nome).trim());
            }
          }
        });

        return lista.sort(function(a, b) {
          return a.localeCompare(b, 'pt-BR', { sensitivity: 'base' });
        });
      }

      function pararApresentacaoTv(restaurarContexto) {
        var estavaApresentando = tvApresentacaoEmExecucao;
        if (tvApresentacaoTimer) {
          clearInterval(tvApresentacaoTimer);
          tvApresentacaoTimer = null;
        }
        if (tvContagemInterval) {
          clearInterval(tvContagemInterval);
          tvContagemInterval = null;
        }
        tvContagemRegressiva = 10;
        tvApresentacaoPausada = false;
        tvApresentacaoTrocaEmAndamento = false;
        atualizarBarraControleTv();
        tvApresentacaoNomes = [];
        tvApresentacaoIndice = -1;
        tvApresentacaoEmExecucao = false;
        tvApresentacaoNomeAtual = '';

        // Ao sair do Modo TV, volta ao contexto original do filtro (sem fixar o último supervisor apresentado).
        if (restaurarContexto && estavaApresentando) {
          var nomeAtual = obterFiltroTvArray('filterNome');
          if (nomeAtual.length === 0) {
            resetCollaboratorCard();
          }
        }
      }

      function exibirSupervisorNaApresentacaoTv(nome) {
        if (!nome || !document.body.classList.contains('tv-mode')) return;

        // Guarda quem está na tela neste momento. Os analíticos usam esse nome
        // somente no Modo TV, sem alterar o filtro real do painel.
        tvApresentacaoNomeAtual = String(nome).trim();

        // Durante a apresentação, o filtro de supervisor permanece sem seleção.
        // Isso permite que os KPIs sejam calculados pelo contexto do gerente/coordenador + mês,
        // enquanto somente a ficha do supervisor exibido muda.
        if (selects['filterNome']) selects['filterNome'].clear(true);
        updateCollaboratorCard(nome);
      }

      function iniciarApresentacaoTv() {
        if (!document.body.classList.contains('tv-mode')) return;

        // Supervisor explicitamente filtrado = tela fixa. Não há rotação.
        var nomesSelecionados = obterFiltroTvArray('filterNome');
        if (nomesSelecionados.length > 0) {
          pararApresentacaoTv(false);
          return;
        }

        var coordenadoresSelecionados = obterFiltroTvArray('filterCoordenador');
        var gerentesSelecionados = obterFiltroTvArray('filterGerente');

        // Sem colaborador, coordenador ou gerente filtrado:
        // no Modo TV, percorre todos os colaboradores disponíveis no contexto atual
        // (mantendo os demais filtros, como mês/setor/quartil).
        var novaLista = obterSupervisoresDaApresentacaoTv();
        pararApresentacaoTv(false);
        tvApresentacaoNomes = novaLista;

        // Sem nenhum filtro de pessoa/coordenação/gerência, a lista deve sempre
        // conter os colaboradores do contexto atual. Se por alguma inconsistência
        // o recorte principal vier vazio, usa todos os nomes disponíveis na base.
        if (tvApresentacaoNomes.length === 0 &&
            coordenadoresSelecionados.length === 0 &&
            gerentesSelecionados.length === 0 &&
            nomesSelecionados.length === 0) {
          var fallbackNomes = {};
          allData.forEach(function(item) {
            var nomeFallback = getValueSafe(item, ['NOME']);
            if (nomeFallback && String(nomeFallback).trim() !== '') {
              fallbackNomes[limparTexto(nomeFallback)] = String(nomeFallback).trim();
            }
          });
          tvApresentacaoNomes = Object.keys(fallbackNomes).map(function(k) { return fallbackNomes[k]; }).sort(function(a,b) {
            return a.localeCompare(b, 'pt-BR', {sensitivity:'base'});
          });
        }

        if (tvApresentacaoNomes.length === 0) return;

        tvApresentacaoEmExecucao = true;
        tvApresentacaoIndice = 0;

        // Pré-carrega as fotos antes de iniciar a apresentação.
        // Assim, na troca de supervisor, nome/dados/foto entram juntos, sem mostrar a foto anterior.
        precarregarFotosApresentacaoTv(tvApresentacaoNomes).then(function() {
          if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao) return;
          exibirSupervisorNaApresentacaoTv(tvApresentacaoNomes[tvApresentacaoIndice]);
          atualizarBarraControleTv();
          iniciarRelogioTv();

          // Se houver apenas um supervisor no recorte, mostra-o fixo.
          if (tvApresentacaoNomes.length <= 1) return;

          iniciarContagemApresentacaoTv();
        });
      }

      function atualizarBarraControleTv() {
        var bar = document.getElementById('tvControlBar');
        if (!bar) return;

        var total = tvApresentacaoNomes.length || 1;
        var atual = tvApresentacaoIndice >= 0 ? tvApresentacaoIndice + 1 : 1;
        var pos = document.getElementById('tvPositionLabel');
        if (pos) pos.textContent = String(atual).padStart(2, '0') + '/' + String(total).padStart(2, '0');

        var next = document.getElementById('tvNextLabel');
        var nextProgress = document.getElementById('tvNextProgressFill');
        var topProgress = document.getElementById('tvTopProgressFill');
        var progresso = 0;
        if (tvApresentacaoNomes.length > 1) {
          progresso = Math.max(0, Math.min(100, ((10 - Math.max(0, tvContagemRegressiva)) / 10) * 100));
        }
        if (nextProgress) {
          nextProgress.style.width = progresso + '%';
        }
        if (topProgress) {
          topProgress.style.width = progresso + '%';
        }
        if (next) {
          if (tvApresentacaoNomes.length <= 1) {
            next.innerHTML = 'Apresentação <strong>pausada</strong>';
          } else if (tvApresentacaoPausada) {
            next.innerHTML = 'Apresentação <strong>pausada</strong>';
          } else {
            next.innerHTML = 'Próxima tela em <strong>' + Math.max(0, tvContagemRegressiva) + 's</strong>';
          }
        }

        var pauseBtn = document.getElementById('tvPauseBtn');
        var pauseIcon = document.getElementById('tvPauseIcon');
        var pauseLabel = document.getElementById('tvPauseLabel');
        if (pauseBtn && pauseIcon && pauseLabel) {
          pauseIcon.className = tvApresentacaoPausada ? 'fa-solid fa-play' : 'fa-solid fa-pause';
          pauseLabel.textContent = tvApresentacaoPausada ? 'Continuar' : 'Pausar';
          pauseBtn.title = tvApresentacaoPausada ? 'Continuar apresentação' : 'Pausar apresentação';
        }

        var dots = document.getElementById('tvProgressDots');
        if (dots) {
          dots.innerHTML = '';
          var maxDots = Math.min(total, 9);
          var start = Math.max(0, Math.min(tvApresentacaoIndice - 4, total - maxDots));
          for (var i = 0; i < maxDots; i++) {
            var d = document.createElement('span');
            d.className = 'tv-progress-dot' + ((start + i) === tvApresentacaoIndice ? ' active' : '');
            dots.appendChild(d);
          }
        }
      }

      function atualizarRelogioTv() {
        var agora = new Date();
        var hora = agora.toLocaleTimeString('pt-BR', { hour12: false });
        var data = agora.toLocaleDateString('pt-BR', {
          weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'
        });
        var clock = document.getElementById('tvClockLabel');
        var date = document.getElementById('tvDateLabel');
        if (clock) clock.textContent = hora;
        if (date) date.textContent = data;
      }

      function iniciarContagemApresentacaoTv() {
        if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao || tvApresentacaoNomes.length <= 1) {
          atualizarBarraControleTv();
          return;
        }

        if (tvContagemInterval) clearInterval(tvContagemInterval);
        tvContagemRegressiva = 10;
        tvApresentacaoPausada = false;
        atualizarBarraControleTv();

        tvContagemInterval = setInterval(function() {
          if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao) {
            clearInterval(tvContagemInterval);
            tvContagemInterval = null;
            return;
          }
          if (tvApresentacaoPausada || tvApresentacaoTrocaEmAndamento) return;

          tvContagemRegressiva--;
          if (tvContagemRegressiva <= 0) {
            tvContagemRegressiva = 0;
            atualizarBarraControleTv();
            avancarApresentacaoTv();
          } else {
            atualizarBarraControleTv();
          }
        }, 1000);
      }

      function avancarApresentacaoTv(direcao) {
        if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao || tvApresentacaoTrocaEmAndamento) return;
        var listaAtualizada = obterSupervisoresDaApresentacaoTv();
        if (listaAtualizada.length === 0) {
          pararApresentacaoTv(false);
          return;
        }

        tvApresentacaoNomes = listaAtualizada;
        var total = tvApresentacaoNomes.length;
        if (direcao === 'prev') {
          tvApresentacaoIndice = (tvApresentacaoIndice - 1 + total) % total;
        } else {
          tvApresentacaoIndice = (tvApresentacaoIndice + 1) % total;
        }

        tvApresentacaoTrocaEmAndamento = true;
        precarregarFotosApresentacaoTv(tvApresentacaoNomes).then(function() {
          if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao) return;
          exibirSupervisorNaApresentacaoTv(tvApresentacaoNomes[tvApresentacaoIndice]);
        }).finally(function() {
          tvApresentacaoTrocaEmAndamento = false;
          tvContagemRegressiva = 10;
          atualizarBarraControleTv();
        });
      }

      function alternarPausaApresentacaoTv() {
        if (!document.body.classList.contains('tv-mode') || !tvApresentacaoEmExecucao || tvApresentacaoNomes.length <= 1) return;
        tvApresentacaoPausada = !tvApresentacaoPausada;
        if (tvApresentacaoPausada) {
          if (tvContagemInterval) {
            clearInterval(tvContagemInterval);
            tvContagemInterval = null;
          }
        } else {
          iniciarContagemApresentacaoTv();
        }
        atualizarBarraControleTv();
      }

      function iniciarRelogioTv() {
        atualizarRelogioTv();
        if (window.tvRelogioInterval) clearInterval(window.tvRelogioInterval);
        window.tvRelogioInterval = setInterval(atualizarRelogioTv, 1000);
      }

      function sincronizarApresentacaoTvComFiltros() {
        if (!document.body.classList.contains('tv-mode')) return;
        iniciarApresentacaoTv();
      }

      window.addEventListener('DOMContentLoaded', function() {
        iniciarSmoothProgress();
        observarValoresKpi();

        var themeBtn = document.getElementById('themeToggleBtn');
        if (themeBtn) {
          themeBtn.addEventListener('click', function() {
            var root = document.documentElement;
            root.classList.add('theme-switching');

            var isDark = document.body.classList.toggle('dark-mode');
            document.getElementById('themeIcon').className = isDark ? 'fa-solid fa-sun' : 'fa-solid fa-moon';
            document.getElementById('themeLabel').textContent = isDark ? 'Modo Claro' : 'Modo Escuro';

            ['meuGrafico','meuGraficoTam','meuGraficoAbs','meuGraficoKml','meuGraficoAcidentes','meuGraficoInspecao','meuGraficoTratarPonto','meuGraficoBhHe','meuGraficoGpsPonto','meuGraficoRecorrencia','meuGraficoConectorInst','meuGraficoConectorRep','meuGraficoDropIat','meuGraficoIndiceVisitas','meuGraficoSlaIat','meuGraficoInstEfet','meuGraficoSafra','meuGraficoSafraFtth','meuGraficoSafraFwa','meuGraficoChurnRate','meuGraficoIsrPastor','meuGraficoIndiceRetencao','meuGraficoCertificacao','meuGraficoCertificacaoCidades','meuGraficoRegularizacao15m','meuGraficoEquipes4Quartil','meuGraficoQuartilGerente','meuGraficoQuartilCoordenador','meuGraficoBacklogReparo','meuGraficoBacklogInstalacao','meuGraficoBacklogIat'].forEach(function(g) {
              if (window[g]) atualizarCoresGraficoGenerico(window[g], true);
            });

            requestAnimationFrame(function() {
              root.classList.remove('theme-switching');
            });
          });
        }


        var tvBtn = document.getElementById('tvModeBtn');
        var tvLabel = document.getElementById('tvModeLabel');
        var tvIcon = document.getElementById('tvModeIcon');

        function atualizarModoTvInterface(ativo) {
          document.body.classList.toggle('tv-mode', ativo);
          atualizarBarraControleTv();
          if (tvLabel) tvLabel.textContent = ativo ? 'Sair do Modo TV' : 'Modo TV';
          if (tvIcon) tvIcon.className = ativo ? 'fa-solid fa-compress' : 'fa-solid fa-tv';
          if (tvBtn) tvBtn.title = ativo ? 'Voltar ao modo normal' : 'Exibir painel em modo TV';

          setTimeout(function() {
            try {
              window.dispatchEvent(new Event('resize'));
              if (window.Chart) {
                Object.keys(window).forEach(function(key) {
                  try {
                    var g = window[key];
                    if (g && typeof g.resize === 'function' && g.canvas) g.resize();
                  } catch (e) {}
                });
              }
            } catch (e) {}
          }, 80);
        }

        async function alternarModoTv() {
          var ativo = document.body.classList.contains('tv-mode');
          if (ativo) {
            pararApresentacaoTv(true);
            atualizarModoTvInterface(false);
            if (document.fullscreenElement && document.exitFullscreen) {
              try { await document.exitFullscreen(); } catch (e) {}
            }
            return;
          }

          atualizarModoTvInterface(true);
          iniciarApresentacaoTv();
          if (document.documentElement.requestFullscreen) {
            try { await document.documentElement.requestFullscreen(); } catch (e) {}
          }
        }

        if (tvBtn) {
          tvBtn.addEventListener('click', alternarModoTv);
        }

        var tvPauseBtn = document.getElementById('tvPauseBtn');
        var tvPrevBtn = document.getElementById('tvPrevBtn');
        var tvNextBtn = document.getElementById('tvNextBtn');
        if (tvPauseBtn) tvPauseBtn.addEventListener('click', alternarPausaApresentacaoTv);
        if (tvPrevBtn) tvPrevBtn.addEventListener('click', function() {
          if (!tvApresentacaoPausada) tvApresentacaoPausada = true;
          if (tvContagemInterval) { clearInterval(tvContagemInterval); tvContagemInterval = null; }
          avancarApresentacaoTv('prev');
          atualizarBarraControleTv();
        });
        if (tvNextBtn) tvNextBtn.addEventListener('click', function() {
          if (!tvApresentacaoPausada) tvApresentacaoPausada = true;
          if (tvContagemInterval) { clearInterval(tvContagemInterval); tvContagemInterval = null; }
          avancarApresentacaoTv('next');
          atualizarBarraControleTv();
        });

        atualizarRelogioTv();

        document.addEventListener('fullscreenchange', function() {
          if (!document.fullscreenElement && document.body.classList.contains('tv-mode')) {
            pararApresentacaoTv(true);
            atualizarModoTvInterface(false);
          }
        });

        ['filterNome', 'filterCoordenador', 'filterGerente', 'filterMes', 'filterSetor', 'filterQuartil'].forEach(function(id) {
          var el = document.getElementById(id);
          if (el && typeof TomSelect !== 'undefined') {
            selects[id] = new TomSelect(el, { 
              plugins: ['remove_button'], 
              persist: false, 
              create: false, 
              maxOptions: null,
              maxItems: id === 'filterNome' ? 1 : null,
              placeholder: id === 'filterNome' ? 'Selecione...' : 'Todos' 
            });
            selects[id].on('change', function() {
              if (id === 'filterNome') {
                var val = selects[id].getValue(), arr = Array.isArray(val) ? val : [val];
                if (arr.length > 0 && arr[0]) {
                  updateCollaboratorCard(arr[0]);
                } else { 
                  resetCollaboratorCard(); 
                }
                applyCascadingFilters();
                sincronizarApresentacaoTvComFiltros();
              } else { 
                applyCascadingFilters();
                sincronizarApresentacaoTvComFiltros();
              }
            });
          }
        });

        var clearBtn = document.getElementById('clearFiltersBtn');
        if (clearBtn) {
          clearBtn.addEventListener('click', function() {
            Object.keys(selects).forEach(function(key) { if(selects[key]) selects[key].clear(true); });
            if (selects['filterMes']) {
              var setembro = Object.keys(selects['filterMes'].options).find(function(m) { return m.toLowerCase().indexOf('setembro/2026') !== -1; });
              if (setembro) selects['filterMes'].setValue(setembro, true);
            }
            applyCascadingFilters(); resetCollaboratorCard(); sincronizarApresentacaoTvComFiltros();
          });
        }

        var refreshBtn = document.getElementById('refreshDataBtn');
        if (refreshBtn) {
          refreshBtn.addEventListener('click', function() {
            var splash = document.getElementById('splashScreenOverlay');
            if (splash) {
              splash.style.visibility = 'visible';
              splash.style.opacity = '1';
            }
            iniciarSmoothProgress();
            carregarDadosServidor(true);
          });
        }

        carregarDadosServidor();
      });

      function carregarDadosServidor(forceRefresh) {
        if (typeof google !== 'undefined' && google.script && google.script.run) {
          google.script.run.withSuccessHandler(function(res) {
            try {
              var parsed = JSON.parse(res);
              allData = parsed.raiox || []; pccData = parsed.pcc || []; tamData = parsed.tam || []; abstData = parsed.abst || [];
              kmlData = parsed.kml || []; acidentesData = parsed.acidentes || []; inspecaoData = parsed.inspecao || [];
              tratarPtData = parsed.tratarPonto || []; bhHeData = parsed.bhHe || []; gpsPontoData = parsed.gpsPonto || [];
              recorrenciaData = parsed.recorrencia || []; materialData = parsed.material || [];
              indicadoresCidadesData = parsed.indicadoresCidades || [];
              quartil4Data = parsed.quartil4 || [];
              rankingData = parsed.ranking || { multiskill: [], recolhimento: [], totais: { multiskill: 0, recolhimento: 0 } };
              renderRankingsExecutivos();
              verificarCamposChurnIsr();
              
              if (allData.length > 0) populateInitialFilters();
              if (document.body.classList.contains('tv-mode')) sincronizarApresentacaoTvComFiltros();
            } catch(e) { console.error(e); }
            finally { ocultarLoadingOverlay(); }
          }).getDashboardData(forceRefresh === true);
        } else {
          setTimeout(function() { ocultarLoadingOverlay(); }, 800);
        }
      }

      function getNumeroLimpo(val) {
        if (val === null || val === undefined || val === '') return 0;
        if (typeof val === 'number') return val;
        var num = parseFloat(String(val).replace(/[^0-9,\.-]/g, '').replace(',', '.'));
        return isNaN(num) ? 0 : num;
      }

      function formatarMes(val) {
        if (!val) return '';
        try {
          var date = new Date(val);
          if (!isNaN(date.getTime())) {
            var meses = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
            return meses[date.getUTCMonth()] + '/' + date.getUTCFullYear();
          }
        } catch(e) {}
        return String(val);
      }

      function getMesAnterior(mesString) {
        if (!mesString) return null;
        var partes = mesString.split('/'); if (partes.length !== 2) return null;
        var mesNome = partes[0].toLowerCase(), ano = parseInt(partes[1], 10);
        var meses = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        if(mesNome === 'marco' || mesNome === 'março') mesNome = 'março';
        var index = meses.indexOf(mesNome); if (index === -1) return null;
        return index === 0 ? 'dezembro/' + (ano - 1) : meses[index - 1] + '/' + ano;
      }

      function valorFiltroNormalizado(valor) {
        return limparTexto(valor).replace(/\s+/g, ' ').trim();
      }

      function obterValorFiltroDoRegistro(item, campo) {
        if (!item) return '';
        var valor = getValueSafe(item, [campo]);
        return valor === undefined || valor === null ? '' : valor;
      }

      function registroAtendeFiltro(item, campo, selecionados) {
        if (!selecionados || selecionados.length === 0) return true;
        var valorRegistro = valorFiltroNormalizado(obterValorFiltroDoRegistro(item, campo));
        if (!valorRegistro) return false;
        return selecionados.some(function(valorSelecionado) {
          return valorFiltroNormalizado(valorSelecionado) === valorRegistro;
        });
      }

      function getUniqueValues(dataArray, key, isMonth) {
        var unique = [];
        dataArray.forEach(function(item) {
          var raw = obterValorFiltroDoRegistro(item, key);
          var val = isMonth ? formatarMes(raw) : raw;
          if (val !== undefined && val !== null && String(val).trim() !== '' && val !== 0 && val !== '0') {
            var sVal = String(val).trim();
            var jaExiste = unique.some(function(existente) {
              return valorFiltroNormalizado(existente) === valorFiltroNormalizado(sVal);
            });
            if (!jaExiste) unique.push(sVal);
          }
        });
        return unique.sort(function(a, b) {
          return String(a).localeCompare(String(b), 'pt-BR', { sensitivity: 'base' });
        });
      }

      function populateInitialFilters() {
        setDropdownOptions('filterCoordenador', getUniqueValues(allData, 'COORDENADOR DE CAMPO', false));
        setDropdownOptions('filterGerente', getUniqueValues(allData, 'GERENTE DE CAMPO', false));
        setDropdownOptions('filterMes', getUniqueValues(allData, 'MÊS', true));
        setDropdownOptions('filterSetor', getUniqueValues(allData, 'SETOR', false));
        setDropdownOptions('filterQuartil', getUniqueValues(allData, 'QUARTIL RAIO X', false));
        setDropdownOptions('filterNome', getUniqueValues(allData, 'NOME', false));

        if (selects['filterMes']) {
          var setembro = Object.keys(selects['filterMes'].options).find(function(m) { return m.toLowerCase().indexOf('setembro/2026') !== -1; });
          if (setembro) selects['filterMes'].setValue(setembro, true);
        }
        applyCascadingFilters();
      }

      function setDropdownOptions(id, arr) {
        if (!selects[id]) return;
        var tom = selects[id];
        var currentValue = tom.getValue();
        var currentValues = Array.isArray(currentValue) ? currentValue.slice() : (currentValue ? [currentValue] : []);
        tom.clearOptions();
        arr.forEach(function(val) {
          tom.addOption({ value: val, text: val });
        });
        tom.refreshOptions(false);

        // Mantém somente seleções que ainda existem nas opções disponíveis.
        var valoresValidos = currentValues.filter(function(valor) {
          return arr.some(function(opcao) {
            return valorFiltroNormalizado(opcao) === valorFiltroNormalizado(valor);
          });
        });
        if (valoresValidos.length > 0) {
          tom.setValue(id === 'filterNome' ? valoresValidos[0] : valoresValidos, true);
        } else {
          tom.clear(true);
        }
      }

      function getTamAbsContextRows(dataContext, mesFiltro) {
        var base = Array.isArray(abstData) ? abstData.slice() : [];
        if (!base.length) return [];

        // Quando o mês não é informado, respeita o(s) mês(es) atualmente selecionado(s) no painel.
        if (mesFiltro === undefined) {
          mesFiltro = selects['filterMes'] ? selects['filterMes'].getValue() : [];
        }

        // O contexto principal (RAIO X) define quais colaboradores pertencem ao filtro atual.
        var nomesContexto = {};
        (Array.isArray(dataContext) ? dataContext : []).forEach(function(d) {
          [
            getValueSafe(d, ['NOME']),
            getValueSafe(d, ['COLABORADOR']),
            getValueSafe(d, ['FUNCIONARIO', 'FUNCIONÁRIO']),
            getValueSafe(d, ['SUPERVISOR'])
          ].forEach(function(v) {
            if (v !== undefined && v !== null && String(v).trim() !== '') {
              nomesContexto[limparTexto(String(v))] = true;
            }
          });
        });

        if (Object.keys(nomesContexto).length > 0) {
          base = base.filter(function(linha) {
            var candidatos = [
              getValueSafe(linha, ['NOME']),
              getValueSafe(linha, ['COLABORADOR']),
              getValueSafe(linha, ['FUNCIONARIO', 'FUNCIONÁRIO']),
              getValueSafe(linha, ['SUPERVISOR'])
            ];
            return candidatos.some(function(v) {
              return v !== undefined && v !== null && String(v).trim() !== '' && nomesContexto[limparTexto(String(v))];
            });
          });
        }

        if (mesFiltro && ((Array.isArray(mesFiltro) && mesFiltro.length > 0) || (!Array.isArray(mesFiltro) && String(mesFiltro).trim() !== ''))) {
          var mesesFiltroArray = Array.isArray(mesFiltro) ? mesFiltro : [mesFiltro];
          base = base.filter(function(linha) {
            var mesLinha = formatarMes(getValueSafe(linha, ['MÊS']));
            return mesesFiltroArray.some(function(m) {
              return valorFiltroNormalizado(mesLinha) === valorFiltroNormalizado(m);
            });
          });
        }

        return base.filter(function(linha) {
          var idxVal = getValueSafe(linha, ['ÍNDICE', 'INDICE', 'STATUS']);
          return idxVal !== undefined && idxVal !== null && String(idxVal).trim() !== '';
        });
      }

      function calcularTamAbsAgregado(dataContext, mesFiltro) {
        // TAM ABASTECIMENTO/CLIENTE é calculado exclusivamente pelas colunas da página RAIO X:
        // BATEU ABS/CLIENTE ÷ TOTAL ABS/CLIENTE × 100.
        var base = Array.isArray(dataContext) ? dataContext.slice() : [];
        if (mesFiltro !== undefined && mesFiltro !== null && ((Array.isArray(mesFiltro) && mesFiltro.length > 0) || (!Array.isArray(mesFiltro) && String(mesFiltro).trim() !== ''))) {
          var mesesFiltroArray = Array.isArray(mesFiltro) ? mesFiltro : [mesFiltro];
          base = base.filter(function(linha) {
            var mesLinha = formatarMes(getValueSafe(linha, ['MÊS']));
            return mesesFiltroArray.some(function(m) {
              return valorFiltroNormalizado(mesLinha) === valorFiltroNormalizado(m);
            });
          });
        }

        var condutoresMeta = 0, totalCondutores = 0;
        base.forEach(function(linha) {
          condutoresMeta += getNumeroLimpo(getValueSafe(linha, ['BATEU ABS/CLIENTE']));
          totalCondutores += getNumeroLimpo(getValueSafe(linha, ['TOTAL ABS/CLIENTE']));
        });

        return {
          linhas: base,
          condutoresMeta: condutoresMeta,
          totalCondutores: totalCondutores,
          percentual: totalCondutores > 0 ? (condutoresMeta / totalCondutores) * 100 : 0
        };
      }

      function getFilteredData(ignoreMes) {
        var getSelected = function(id) {
          var val = selects[id] ? selects[id].getValue() : [];
          return Array.isArray(val) ? val : (val ? [val] : []);
        };
        var nomes = getSelected('filterNome');
        var coords = getSelected('filterCoordenador');
        var gers = getSelected('filterGerente');
        var meses = getSelected('filterMes');
        var setores = getSelected('filterSetor');
        var quartis = getSelected('filterQuartil');

        return allData.filter(function(item) {
          var mesFormatado = formatarMes(obterValorFiltroDoRegistro(item, 'MÊS'));

          return registroAtendeFiltro(item, 'NOME', nomes) &&
                 registroAtendeFiltro(item, 'COORDENADOR DE CAMPO', coords) &&
                 registroAtendeFiltro(item, 'GERENTE DE CAMPO', gers) &&
                 (ignoreMes ? true : (meses.length === 0 || meses.some(function(m) { return valorFiltroNormalizado(m) === valorFiltroNormalizado(mesFormatado); }))) &&
                 registroAtendeFiltro(item, 'SETOR', setores) &&
                 registroAtendeFiltro(item, 'QUARTIL RAIO X', quartis);
        });
      }

      function applyCascadingFilters() {
        var getSelected = function(id) {
          var val = selects[id] ? selects[id].getValue() : [];
          return Array.isArray(val) ? val : (val ? [val] : []);
        };
        var nomes = getSelected('filterNome');
        var coords = getSelected('filterCoordenador');
        var gers = getSelected('filterGerente');
        var meses = getSelected('filterMes');
        var setores = getSelected('filterSetor');
        var quartis = getSelected('filterQuartil');

        function filtrarPor(ignoreKey) {
          return allData.filter(function(item) {
            var mesFormatado = formatarMes(obterValorFiltroDoRegistro(item, 'MÊS'));

            return (ignoreKey === 'NOME' || registroAtendeFiltro(item, 'NOME', nomes)) &&
                   (ignoreKey === 'COORDENADOR DE CAMPO' || registroAtendeFiltro(item, 'COORDENADOR DE CAMPO', coords)) &&
                   (ignoreKey === 'GERENTE DE CAMPO' || registroAtendeFiltro(item, 'GERENTE DE CAMPO', gers)) &&
                   (meses.length === 0 || meses.some(function(m) { return valorFiltroNormalizado(m) === valorFiltroNormalizado(mesFormatado); })) &&
                   (ignoreKey === 'SETOR' || registroAtendeFiltro(item, 'SETOR', setores)) &&
                   (ignoreKey === 'QUARTIL RAIO X' || registroAtendeFiltro(item, 'QUARTIL RAIO X', quartis));
          });
        }

        setDropdownOptions('filterNome', getUniqueValues(filtrarPor('NOME'), 'NOME', false));
        setDropdownOptions('filterCoordenador', getUniqueValues(filtrarPor('COORDENADOR DE CAMPO'), 'COORDENADOR DE CAMPO', false));
        setDropdownOptions('filterGerente', getUniqueValues(filtrarPor('GERENTE DE CAMPO'), 'GERENTE DE CAMPO', false));
        setDropdownOptions('filterSetor', getUniqueValues(filtrarPor('SETOR'), 'SETOR', false));
        setDropdownOptions('filterQuartil', getUniqueValues(filtrarPor('QUARTIL RAIO X'), 'QUARTIL RAIO X', false));

        var currentFiltered = getFilteredData(false);
        var nomeSelecionado = selects['filterNome'] ? selects['filterNome'].getValue() : [];
        if (!nomeSelecionado || (Array.isArray(nomeSelecionado) ? nomeSelecionado.length === 0 : !nomeSelecionado)) {
          updateKpisForCurrentContext(currentFiltered, null);
        } else {
          updateCollaboratorCard(Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado);
        }
      }

      function updateKpisForCurrentContext(filteredData, selectedNomeStr) {
        var dataToCalc = filteredData || getFilteredData(false);
        if (selectedNomeStr) {
          var nomeSelecionadoNormalizado = limparTexto(selectedNomeStr);
          dataToCalc = dataToCalc.filter(function(d) {
            return limparTexto(getValueSafe(d, ['NOME'])) === nomeSelecionadoNormalizado;
          });
        }
        
        var valAtualPpc = 0, valAtualTam = 0, valAtualAbs = 0, valAtualKml = 0, valAtualAcidentes = 0, valAtualInspecao = 0, valAtualTratarPonto = 0, valAtualBhHeSegundos = 0, valAtualGpsPonto = 0, valAtualRecorrencia = 0, valAtualConectorInst = 0, valAtualConectorRep = 0, valAtualDropIat = 0, valAtualIndiceVisitas = 0, valAtualSlaIat = 0, valAtualSlaIat48 = null, valAtualSlaIat72 = null, valAtualInstEfet = 0, valAtualSafra = null, valAtualSafraFtth = null, valAtualSafraFwa = null, valAtualIndiceRetencao = null, valAtualCertificacao = 0, valAtualCertificacaoCidades = 0, valAtualRegularizacao15m = 0, valAtualEquipes4Quartil = 0, valAtualAtestado12m = 0, valAtualImprodutivaIat = 0, valAtualBacklogReparo = 0, valAtualBacklogInstalacao = 0, valAtualBacklogIat = 0, valAtualChurnRate = null, valAtualIsrPastor = null;

        var setorAtual = "";
        if (selectedNomeStr && dataToCalc.length > 0) {
          setorAtual = limparTexto(getValueSafe(dataToCalc[0], ['SETOR']));
        } else {
          var setoresSel = selects['filterSetor'] ? selects['filterSetor'].getValue() : [];
          if (Array.isArray(setoresSel) && setoresSel.length === 1) setorAtual = limparTexto(setoresSel[0]);
        }

        if (dataToCalc.length > 0) {
          var totalPontosPpc=0, totalEquipesPpc=0, diasUteisPpc=0, sTam=0, cTam=0, sAbs=0, cAbs=0, sKml=0, cKml=0, sInsp=0, cInsp=0, totalInspecaoOkCard=0, totalInspecoesCard=0, sTratar=0, cTratar=0, totalBhHeSegundosCard=0, totalPtTotalBhHeCard=0, sGps=0, totalEquipesGpsCard=0, cGps=0, sRec=0, cRec=0, sCI=0, cCI=0, sCR=0, cCR=0, sDI=0, cDI=0, sIV=0, cIV=0, sSLA=0, cSLA=0, sIE=0, cIE=0, sSafra=0, cSafra=0, sRet=0, cRet=0, sCert=0, cCert=0, sAtestado12m=0, sEquipesAtestado=0, sDesatribIat=0, sAtribIat=0; 
          var sBateuIndCid=0, sTotalIndCid=0, sReg15m=0, sCaixas15m=0, sTec4Q=0, sEquipes4Q=0;
          var sDemandaReparoCard=0, sMedia7DiasCard=0, sDemandaInstalacaoCard=0, sMedia7DiasInstalacaoCard=0;
          var totalRecolhidoSafraFtth=0, demandaRecolhimentoSafraFtth=0, totalRecolhidoSafraFwa=0, demandaRecolhimentoSafraFwa=0;
          var totalBateuMetaTam=0, totalEquipesTamRaioX=0;
          var totalPontosTratarRaioX=0, totalEquipesTratarRaioX=0;
          var totalInstalacaoSla48=0, instalacao48Horas=0, totalReparoSla48=0, reparo48Horas=0, totalInstalacaoSla72=0, instalacao72Horas=0, totalReparoSla72=0, reparo72Horas=0;
          var totalCancelamentosChurn=0, totalInstalacoesChurn=0, totalOnusCriticosIsr=0, totalOnusIsr=0;
          var totalServicosRecorrencia=0, totalRecorrenciasRecorrencia=0;

          dataToCalc.forEach(function(d) {
            totalPontosPpc += getNumeroLimpo(getValueSafe(d, ['PONTOS', 'PONTUACAO', 'PONTUAÇÃO']));
            totalEquipesPpc += getNumeroLimpo(getValueSafe(d, ['EQUIPE', 'EQUIPES']));
            totalPontosTratarRaioX += getNumeroLimpo(getRaioXTratarPonto(d));
            totalEquipesTratarRaioX += getNumeroLimpo(getRaioXCampoExato(d, 'EQUIPES'));
            if (diasUteisPpc <= 0) {
              var diasLinhaPpc = getNumeroLimpo(getValueSafe(d, ['DIAS', 'DIA', 'DIAS UTEIS', 'DIAS ÚTEIS']));
              if (diasLinhaPpc > 0) diasUteisPpc = diasLinhaPpc;
            }
            var rT = getValueSafe(d, ['TAM']); if (rT !== undefined && rT !== null && rT !== '') { var nT = getNumeroLimpo(rT); if (nT <= 1 && nT > 0) nT *= 100; sTam += nT; cTam++; }
            var rA = getValueSafe(d, ['TAM ABS/CLIENTE', 'TAM ABS CLIENTE', 'ABS/CLIENTE']); if (rA !== undefined && rA !== null && rA !== '') { var nA = getNumeroLimpo(rA); if (nA <= 1 && nA > 0) nA *= 100; sAbs += nA; cAbs++; }
            var rK = getValueSafe(d, ['TAM KM/L', 'TAM KML', 'KM/L']); if (rK !== undefined && rK !== null && rK !== '') { var nK = getNumeroLimpo(rK); if (nK <= 1 && nK > 0) nK *= 100; sKml += nK; cKml++; }
            valAtualAcidentes += getNumeroLimpo(getValueSafe(d, ['ACIDENTES', 'ACIDENTE']));
            totalInspecaoOkCard += getNumeroLimpo(getValueSafe(d, ['INSPEÇÕES OK', 'INSPECOES OK']));
            totalInspecoesCard += getNumeroLimpo(getValueSafe(d, ['TOTAL INSPEÇÕES', 'TOTAL INSPECOES']));
            var rTP = getRaioXTratarPonto(d); if (rTP !== undefined && rTP !== null && rTP !== '') { var nTP = getNumeroLimpo(rTP); if (nTP <= 1 && nTP > 0) nTP *= 100; sTratar += nTP; cTratar++; }
            
            totalBhHeSegundosCard += duracaoParaSegundos(getValueSafe(d, ['BH + HE', 'BH+HE']));
            totalPtTotalBhHeCard += getNumeroLimpo(getValueSafe(d, ['PT TOTAL', 'PONTUAÇÃO TOTAL', 'PONTOS TOTAL', 'PONTUAÇÃO']));
            sGps += getNumeroLimpo(getRaioXCampoExato(d, 'ALERTAS'));
            totalEquipesGpsCard += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL EQUIPES'));
            var rREC = getValueSafe(d, ['RECORRÊNCIA', 'RECORRENCIA', 'RECORRÊNCIA IAT', 'RECORRENCIA IAT']); if (rREC !== undefined && rREC !== null && rREC !== '') { var nR = getNumeroLimpo(rREC); if (nR <= 1 && nR > 0) nR *= 100; sRec += nR; cRec++; }
            totalServicosRecorrencia += getNumeroLimpo(getValueSafe(d, ['SERVIÇO', 'SERVICO', 'SERVIÇOS', 'SERVICOS', 'REPAROS EXECUTADOS']));
            totalRecorrenciasRecorrencia += getNumeroLimpo(getValueSafe(d, ['RECORRÊNCIAS', 'RECORRENCIAS', 'REVISITAS']));
            
            var rCI = getValueSafe(d, ['CONECTOR INSTALAÇÃO', 'CONECTOR INSTALACAO', 'CONECTOR INST', 'CONECTOR DE INSTALAÇÃO', 'CONECTOR DE INSTALACAO']); if (rCI !== undefined && rCI !== null && rCI !== '') { sCI += getNumeroLimpo(rCI); cCI++; }
            var rCR = getValueSafe(d, ['CONECTOR REPARO', 'CONECTOR REPAROS', 'CONECTOR REP', 'CONECTOR DE REPARO', 'CONECTOR DE REPAROS']); if (rCR !== undefined && rCR !== null && rCR !== '') { sCR += getNumeroLimpo(rCR); cCR++; }
            var rDI = getValueSafe(d, ['USO DROP IAT', 'DROP IAT', 'DROP']); if (rDI !== undefined && rDI !== null && rDI !== '') { sDI += getNumeroLimpo(rDI); cDI++; }
            
            var rIV = getValueSafe(d, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS', 'VISITAS DE REPARO / BASE']);
            if (rIV !== undefined && rIV !== null && rIV !== '') { var nIV = getNumeroLimpo(rIV); if (nIV <= 1 && nIV > 0) nIV *= 100; sIV += nIV; cIV++; }

            var rSLA = getValueSafe(d, ['SLA IAT', 'SLA_IAT', 'SLA']);
            if (rSLA !== undefined && rSLA !== null && rSLA !== '') { var nSLA = getNumeroLimpo(rSLA); if (nSLA <= 1 && nSLA > 0) nSLA *= 100; sSLA += nSLA; cSLA++; }

            var rIE = getValueSafe(d, ['INSTALADOS X EFETIVADOS', 'INSTALADOS X EFETIVADO', 'INSTALADO X EFETIVADO']);
            if (rIE !== undefined && rIE !== null && rIE !== '') { var nIE = getNumeroLimpo(rIE); if (nIE <= 1 && nIE > 0) nIE *= 100; sIE += nIE; cIE++; }

            var rCert = getValueSafe(d, ['CERTIFICAÇÃO', 'CERTIFICACAO']);
            if (rCert !== undefined && rCert !== null && rCert !== '') { var nCert = getNumeroLimpo(rCert); if (nCert <= 1 && nCert > 0) nCert *= 100; sCert += nCert; cCert++; }

            sBateuIndCid += getNumeroLimpo(getValueSafe(d, ['BATEU IND', 'BATEU_IND']));
            sTotalIndCid += getNumeroLimpo(getValueSafe(d, ['TOTAL IND', 'TOTAL_IND']));

            sReg15m += getNumeroLimpo(getValueSafe(d, ['REGULARIZADO 15M', 'REGULARIZADO_15M']));
            sCaixas15m += getNumeroLimpo(getValueSafe(d, ['CAIXAS', 'TOTAL CAIXAS']));

            sTec4Q += getNumeroLimpo(getValueSafe(d, ['TÉC. 4º QUARTIL', 'TEC. 4º QUARTIL', 'TEC 4 QUARTIL']));
            sEquipes4Q += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));

            sAtestado12m += getNumeroLimpo(getValueSafe(d, ['ATESTADO 12 MESES', 'ATESTADOS DOS ÚLTIMOS 12 MESES', 'ATESTADOS 12 MESES']));
            sEquipesAtestado += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));

            var desatInst = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO']));
            var desatRep = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']));
            var atribInst = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO']));
            var atribRep = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']));
            sDesatribIat += (desatInst + desatRep);
            sAtribIat += (atribInst + atribRep);

            sDemandaReparoCard += getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
            sMedia7DiasCard += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));

            sDemandaInstalacaoCard += getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
            sMedia7DiasInstalacaoCard += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));

            totalRecolhidoSafraFtth += getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (ONU)', 'TOTAL RECOLHIDO ONU']));
            demandaRecolhimentoSafraFtth += getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (ONU)', 'DEMANDA RECOLHIMENTO ONU']));
            totalRecolhidoSafraFwa += getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (FWA)', 'TOTAL RECOLHIDO FWA']));
            demandaRecolhimentoSafraFwa += getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (FWA)', 'DEMANDA RECOLHIMENTO FWA']));

            instalacao48Horas += getNumeroLimpo(getValueSafe(d, ['INSTALAÇÃO 48 HORAS']));
            reparo48Horas += getNumeroLimpo(getValueSafe(d, ['REPARO 48 HORAS']));
            totalReparoSla48 += getNumeroLimpo(getValueSafe(d, ['TOTAL REPARO']));
            totalInstalacaoSla48 += getNumeroLimpo(getValueSafe(d, ['TOTAL INSTALAÇÃO']));

            instalacao72Horas += getNumeroLimpo(getValueSafe(d, ['INSTALAÇÃO 72 HORAS']));
            reparo72Horas += getNumeroLimpo(getValueSafe(d, ['REPARO 72 HORAS']));
            totalReparoSla72 += getNumeroLimpo(getValueSafe(d, ['TOTAL REPARO']));
            totalInstalacaoSla72 += getNumeroLimpo(getValueSafe(d, ['TOTAL INSTALAÇÃO']));

            totalCancelamentosChurn += getNumeroLimpo(getRaioXCampoExato(d, 'CANCELAMENTOS (FTTH)'));
            totalInstalacoesChurn += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL_DE_ACESSOS (MÊS)'));
            totalOnusCriticosIsr += getNumeroLimpo(getRaioXCampoExato(d, 'ONUS CRÍTICOS'));
            totalOnusIsr += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL ONUS'));

            var rowSetor = limparTexto(getValueSafe(d, ['SETOR']));
            var isSetorRec = (rowSetor.indexOf('RECOLHIMENTO') !== -1 || setorAtual.indexOf('RECOLHIMENTO') !== -1);

            var rSafra = getValueSafe(d, ['SAFRA']);
            if (rSafra !== undefined && rSafra !== null && rSafra !== '') {
              var nSafraDirect = getNumeroLimpo(rSafra); if (nSafraDirect <= 1 && nSafraDirect > 0) nSafraDirect *= 100;
              sSafra += nSafraDirect; cSafra++;
            } else if (isSetorRec) {
              var recOnu = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (ONU)', 'TOTAL RECOLHIDO ONU']));
              var recFwa = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (FWA)', 'TOTAL RECOLHIDO FWA']));
              var demOnu = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (ONU)', 'DEMANDA RECOLHIMENTO ONU']));
              var demFwa = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (FWA)', 'DEMANDA RECOLHIMENTO FWA']));
              if ((demOnu + demFwa) > 0) {
                sSafra += (((recOnu + recFwa) / (demOnu + demFwa)) * 100);
                cSafra++;
              }
            }

            var rRet = getValueSafe(d, ['ÍNDICE DE RETENÇÃO', 'INDICE DE RETENCAO', 'RETENÇÃO', 'RETENCAO']);
            if (rRet !== undefined && rRet !== null && rRet !== '') {
              var nRetDirect = getNumeroLimpo(rRet); if (nRetDirect <= 1 && nRetDirect > 0) nRetDirect *= 100;
              sRet += nRetDirect; cRet++;
            } else if (isSetorRec) {
              var reat = getNumeroLimpo(getValueSafe(d, ['REATIVADOS', 'REATIVADO']));
              var gerRec = getNumeroLimpo(getValueSafe(d, ['GERADOS RECOLHIMENTO', 'GERADOS RECOLHIMENTOS']));
              var gerFwa = getNumeroLimpo(getValueSafe(d, ['GERADOS FWA']));
              if ((gerRec + gerFwa) > 0) {
                sRet += ((reat / (gerRec + gerFwa)) * 100);
                cRet++;
              }
            }
          });

          valAtualPpc = (totalPontosPpc > 0 && totalEquipesPpc > 0 && diasUteisPpc > 0) ? (totalPontosPpc / totalEquipesPpc / diasUteisPpc) : 0;
          var tamRaioXAtual = calcularTamRaioXAgregado(dataToCalc);
          totalBateuMetaTam = tamRaioXAtual.bateu;
          totalEquipesTamRaioX = tamRaioXAtual.equipes;
          valAtualTam = tamRaioXAtual.percentual;
          valAtualTratarPonto = totalEquipesTratarRaioX > 0 ? ((totalPontosTratarRaioX / totalEquipesTratarRaioX) * 100) : 0;
          var tamAbsAgregadoAtual = calcularTamAbsAgregado(dataToCalc);
          valAtualAbs = tamAbsAgregadoAtual.percentual;
          var tamKmlRaioXAtual = calcularTamKmlRaioXAgregado(dataToCalc);
          valAtualKml = tamKmlRaioXAtual.percentual;
          valAtualInspecao = totalInspecoesCard > 0 ? ((totalInspecaoOkCard / totalInspecoesCard) * 100) : 0;
          valAtualTratarPonto = totalEquipesTratarRaioX > 0 ? ((totalPontosTratarRaioX / totalEquipesTratarRaioX) * 100) : 0;
          valAtualBhHeSegundos = totalPtTotalBhHeCard > 0 ? (totalBhHeSegundosCard / totalPtTotalBhHeCard) : 0;
          valAtualGpsPonto = totalEquipesGpsCard > 0 ? (sGps / totalEquipesGpsCard) : 0;
          valAtualRecorrencia = totalServicosRecorrencia > 0 ? ((totalRecorrenciasRecorrencia / totalServicosRecorrencia) * 100) : 0;
          valAtualConectorInst = cCI > 0 ? (sCI / cCI) : 0;
          valAtualConectorRep = cCR > 0 ? (sCR / cCR) : 0;
          valAtualDropIat = cDI > 0 ? (sDI / cDI) : 0;
          var indiceVisitasRaioXAtual = calcularIndiceVisitasRaioXAgregado(dataToCalc);
          valAtualIndiceVisitas = indiceVisitasRaioXAtual.percentual;
          valAtualSlaIat = cSLA > 0 ? (sSLA / cSLA) : 0;
          var instEfetRaioXAtual = calcularInstEfetRaioXAgregado(dataToCalc);
          valAtualInstEfet = instEfetRaioXAtual.percentual;
          valAtualSafra = cSafra > 0 ? (sSafra / cSafra) : null;
          valAtualSafraFtth = demandaRecolhimentoSafraFtth > 0 ? ((totalRecolhidoSafraFtth / demandaRecolhimentoSafraFtth) * 100) : null;
          valAtualSafraFwa = demandaRecolhimentoSafraFwa > 0 ? ((totalRecolhidoSafraFwa / demandaRecolhimentoSafraFwa) * 100) : null;
          var totalBaseSla48 = totalReparoSla48 + totalInstalacaoSla48;
          var totalBaseSla72 = totalReparoSla72 + totalInstalacaoSla72;
          valAtualSlaIat48 = totalBaseSla48 > 0 ? (((instalacao48Horas + reparo48Horas) / totalBaseSla48) * 100) : null;
          valAtualSlaIat72 = totalBaseSla72 > 0 ? (((instalacao72Horas + reparo72Horas) / totalBaseSla72) * 100) : null;
          valAtualChurnRate = totalInstalacoesChurn > 0 ? ((totalCancelamentosChurn / totalInstalacoesChurn) * 100) : null;
          valAtualIsrPastor = totalOnusIsr > 0 ? ((totalOnusCriticosIsr / totalOnusIsr) * 100) : null;
          valAtualIndiceRetencao = cRet > 0 ? (sRet / cRet) : null;
          valAtualCertificacao = cCert > 0 ? (sCert / cCert) : 0;
          
          valAtualCertificacaoCidades = sTotalIndCid > 0 ? ((sBateuIndCid / sTotalIndCid) * 100) : 0;
          valAtualRegularizacao15m = sCaixas15m > 0 ? ((sReg15m / sCaixas15m) * 100) : 0;
          valAtualEquipes4Quartil = sEquipes4Q > 0 ? ((sTec4Q / sEquipes4Q) * 100) : 0;
          valAtualAtestado12m = sEquipesAtestado > 0 ? (sAtestado12m / sEquipesAtestado) : 0;
          valAtualImprodutivaIat = sAtribIat > 0 ? ((sDesatribIat / sAtribIat) * 100) : 0;
          valAtualBacklogReparo = sMedia7DiasCard > 0 ? (sDemandaReparoCard / sMedia7DiasCard) : 0;
          valAtualBacklogInstalacao = sMedia7DiasInstalacaoCard > 0 ? (sDemandaInstalacaoCard / sMedia7DiasInstalacaoCard) : 0;
          
          var demIatTotal = sDemandaInstalacaoCard + sDemandaReparoCard;
          var medIatTotal = sMedia7DiasInstalacaoCard + sMedia7DiasCard;
          valAtualBacklogIat = medIatTotal > 0 ? (demIatTotal / medIatTotal) : 0;
        }

        var mesesSelecionados = selects['filterMes'] ? selects['filterMes'].getValue() : [];
        var mesAtualFiltro = Array.isArray(mesesSelecionados) && mesesSelecionados.length === 1 ? mesesSelecionados[0] : (typeof mesesSelecionados === 'string' ? mesesSelecionados : null);
        var mesAnteriorStr = getMesAnterior(mesAtualFiltro);

        if (mesAnteriorStr) {
          var dataPrev = getFilteredData(true).filter(function(d) { return formatarMes(d['MÊS']) === mesAnteriorStr; });
          if (selectedNomeStr) dataPrev = dataPrev.filter(function(d) { return d['NOME'] === selectedNomeStr; });

          if (dataPrev.length > 0) {
            var pPpc=0, pTotalPontosPpc=0, pTotalEquipesPpc=0, pDiasUteisPpc=0, pTam=0, cTamP=0, pAbs=0, cAbsP=0, pKml=0, cKmlP=0, pAcd=0, pInsp=0, cInspP=0, pTotalInspecaoOk=0, pTotalInspecoes=0, pTrat=0, cTratP=0, pBh=0, cBhP=0, pBhHeSegundos=0, pPtTotalBhHe=0, pGps=0, pTotalEquipesGps=0, cGpsP=0, pRec=0, cRecP=0, pCI=0, cCIP=0, pCR=0, cCRP=0, pDI=0, cDIP=0, pIV=0, cIVP=0, pSLA=0, cSLAP=0, pIE=0, cIEP=0, pCert=0, cCertP=0, pSafra=0, cSafraP=0;
            var pBateuIndCid=0, pTotalIndCid=0, pReg15m=0, pCaixas15m=0, pTec4Q=0, pEquipes4Q=0;
            var pDemandaReparoCard=0, pMedia7DiasCard=0, pDemandaInstalacaoCard=0, pMedia7DiasInstalacaoCard=0;
            var pTotalRecolhidoSafraFtth=0, pDemandaRecolhimentoSafraFtth=0, pTotalRecolhidoSafraFwa=0, pDemandaRecolhimentoSafraFwa=0;
            var pBateuMetaTam=0, pTotalEquipesTamRaioX=0;
            var pTotalPontosTratarRaioX=0, pTotalEquipesTratarRaioX=0;
            var pRet=0, cRetP=0;
            var pTotalInstalacaoSla48=0, pInstalacao48Horas=0, pTotalReparoSla48=0, pReparo48Horas=0, pTotalInstalacaoSla72=0, pInstalacao72Horas=0, pTotalReparoSla72=0, pReparo72Horas=0;
            var pTotalCancelamentosChurn=0, pTotalInstalacoesChurn=0, pTotalOnusCriticosIsr=0, pTotalOnusIsr=0;
            var pTotalServicosRecorrencia=0, pTotalRecorrenciasRecorrencia=0;

            dataPrev.forEach(function(d) {
              pTotalPontosPpc += getNumeroLimpo(getValueSafe(d, ['PONTOS', 'PONTUACAO', 'PONTUAÇÃO']));
              pTotalEquipesPpc += getNumeroLimpo(getValueSafe(d, ['EQUIPE', 'EQUIPES']));
              pTotalPontosTratarRaioX += getNumeroLimpo(getRaioXTratarPonto(d));
              pTotalEquipesTratarRaioX += getNumeroLimpo(getRaioXCampoExato(d, 'EQUIPES'));
              if (pDiasUteisPpc <= 0) {
                var pDiasLinhaPpc = getNumeroLimpo(getValueSafe(d, ['DIAS', 'DIA', 'DIAS UTEIS', 'DIAS ÚTEIS']));
                if (pDiasLinhaPpc > 0) pDiasUteisPpc = pDiasLinhaPpc;
              }
              var rT = getValueSafe(d, ['TAM']); if (rT !== undefined && rT !== null && rT !== '') { var n = getNumeroLimpo(rT); if (n<=1 && n>0) n*=100; pTam += n; cTamP++; }
              var rA = getValueSafe(d, ['TAM ABS/CLIENTE', 'TAM ABS CLIENTE', 'ABS/CLIENTE']); if (rA !== undefined && rA !== null && rA !== '') { var n = getNumeroLimpo(rA); if (n<=1 && n>0) n*=100; pAbs += n; cAbsP++; }
              var rK = getValueSafe(d, ['TAM KM/L', 'TAM KML', 'KM/L']); if (rK !== undefined && rK !== null && rK !== '') { var n = getNumeroLimpo(rK); if (n<=1 && n>0) n*=100; pKml += n; cKmlP++; }
              pAcd += getNumeroLimpo(getValueSafe(d, ['ACIDENTES', 'ACIDENTE']));
              pTotalInspecaoOk += getNumeroLimpo(getValueSafe(d, ['INSPEÇÕES OK', 'INSPECOES OK']));
              pTotalInspecoes += getNumeroLimpo(getValueSafe(d, ['TOTAL INSPEÇÕES', 'TOTAL INSPECOES']));
              var rTP = getRaioXTratarPonto(d); if (rTP !== undefined && rTP !== null && rTP !== '') { var n = getNumeroLimpo(rTP); if (n<=1 && n>0) n*=100; pTrat += n; cTratP++; }
              pBhHeSegundos += duracaoParaSegundos(getValueSafe(d, ['BH + HE', 'BH+HE']));
              pPtTotalBhHe += getNumeroLimpo(getValueSafe(d, ['PT TOTAL', 'PONTUAÇÃO TOTAL', 'PONTOS TOTAL', 'PONTUAÇÃO']));
              pGps += getNumeroLimpo(getRaioXCampoExato(d, 'ALERTAS'));
              pTotalEquipesGps += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL EQUIPES'));
              var rREC = getValueSafe(d, ['RECORRÊNCIA', 'RECORRENCIA', 'RECORRÊNCIA IAT', 'RECORRENCIA IAT']); if (rREC !== undefined && rREC !== null && rREC !== '') { var n = getNumeroLimpo(rREC); if (n<=1 && n>0) n*=100; pRec += n; cRecP++; }
              var rRetPrev = getValueSafe(d, ['ÍNDICE DE RETENÇÃO', 'INDICE DE RETENCAO', 'RETENÇÃO', 'RETENCAO']);
              if (rRetPrev !== undefined && rRetPrev !== null && rRetPrev !== '') { var nRetPrev = getNumeroLimpo(rRetPrev); if (nRetPrev <= 1 && nRetPrev > 0) nRetPrev *= 100; pRet += nRetPrev; cRetP++; }
              pTotalServicosRecorrencia += getNumeroLimpo(getValueSafe(d, ['SERVIÇO', 'SERVICO', 'SERVIÇOS', 'SERVICOS', 'REPAROS EXECUTADOS']));
              pTotalRecorrenciasRecorrencia += getNumeroLimpo(getValueSafe(d, ['RECORRÊNCIAS', 'RECORRENCIAS', 'REVISITAS']));
              var rCI = getValueSafe(d, ['CONECTOR INSTALAÇÃO', 'CONECTOR INSTALACAO', 'CONECTOR INST', 'CONECTOR DE INSTALAÇÃO', 'CONECTOR DE INSTALACAO']); if (rCI !== undefined && rCI !== null && rCI !== '') { pCI += getNumeroLimpo(rCI); cCIP++; }
              var rCR = getValueSafe(d, ['CONECTOR REPARO', 'CONECTOR REPAROS', 'CONECTOR REP', 'CONECTOR DE REPARO', 'CONECTOR DE REPAROS']); if (rCR !== undefined && rCR !== null && rCR !== '') { pCR += getNumeroLimpo(rCR); cCRP++; }
              var rDI = getValueSafe(d, ['USO DROP IAT', 'DROP IAT', 'DROP']); if (rDI !== undefined && rDI !== null && rDI !== '') { pDI += getNumeroLimpo(rDI); cDIP++; }
              var rIV = getValueSafe(d, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS']); if (rIV !== undefined && rIV !== null && rIV !== '') { var n = getNumeroLimpo(rIV); if (n<=1 && n>0) n*=100; pIV += n; cIVP++; }
              var rSLA = getValueSafe(d, ['SLA IAT', 'SLA_IAT', 'SLA']); if (rSLA !== undefined && rSLA !== null && rSLA !== '') { var n = getNumeroLimpo(rSLA); if (n<=1 && n>0) n*=100; pSLA += n; cSLAP++; }
              var rIE = getValueSafe(d, ['INSTALADOS X EFETIVADOS', 'INSTALADOS X EFETIVADO']); if (rIE !== undefined && rIE !== null && rIE !== '') { var n = getNumeroLimpo(rIE); if (n<=1 && n>0) n*=100; pIE += n; cIEP++; }
              var rCert = getValueSafe(d, ['CERTIFICAÇÃO', 'CERTIFICACAO']); if (rCert !== undefined && rCert !== null && rCert !== '') { var n = getNumeroLimpo(rCert); if (n<=1 && n>0) n*=100; pCert += n; cCertP++; }

              var rowSetorPrev = limparTexto(getValueSafe(d, ['SETOR']));
              var isSetorRecPrev = (rowSetorPrev.indexOf('RECOLHIMENTO') !== -1 || setorAtual.indexOf('RECOLHIMENTO') !== -1);
              var rSafraPrev = getValueSafe(d, ['SAFRA']);
              if (rSafraPrev !== undefined && rSafraPrev !== null && rSafraPrev !== '') {
                var nSafraPrev = getNumeroLimpo(rSafraPrev); if (nSafraPrev <= 1 && nSafraPrev > 0) nSafraPrev *= 100;
                pSafra += nSafraPrev; cSafraP++;
              } else if (isSetorRecPrev) {
                var recOnuPrev = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (ONU)', 'TOTAL RECOLHIDO ONU']));
                var recFwaPrev = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (FWA)', 'TOTAL RECOLHIDO FWA']));
                var demOnuPrev = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (ONU)', 'DEMANDA RECOLHIMENTO ONU']));
                var demFwaPrev = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (FWA)', 'DEMANDA RECOLHIMENTO FWA']));
                if ((demOnuPrev + demFwaPrev) > 0) {
                  pSafra += (((recOnuPrev + recFwaPrev) / (demOnuPrev + demFwaPrev)) * 100); cSafraP++;
                }
              }

              pBateuIndCid += getNumeroLimpo(getValueSafe(d, ['BATEU IND', 'BATEU_IND']));
              pTotalIndCid += getNumeroLimpo(getValueSafe(d, ['TOTAL IND', 'TOTAL_IND']));
              pReg15m += getNumeroLimpo(getValueSafe(d, ['REGULARIZADO 15M', 'REGULARIZADO_15M']));
              pCaixas15m += getNumeroLimpo(getValueSafe(d, ['CAIXAS', 'TOTAL CAIXAS']));
              pTec4Q += getNumeroLimpo(getValueSafe(d, ['TÉC. 4º QUARTIL', 'TEC. 4º QUARTIL', 'TEC 4 QUARTIL']));
              pEquipes4Q += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));

              pDemandaReparoCard += getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
              pMedia7DiasCard += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));

              pDemandaInstalacaoCard += getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
              pMedia7DiasInstalacaoCard += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));

              pTotalRecolhidoSafraFtth += getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (ONU)', 'TOTAL RECOLHIDO ONU']));
              pDemandaRecolhimentoSafraFtth += getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (ONU)', 'DEMANDA RECOLHIMENTO ONU']));
              pTotalRecolhidoSafraFwa += getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (FWA)', 'TOTAL RECOLHIDO FWA']));
              pDemandaRecolhimentoSafraFwa += getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (FWA)', 'DEMANDA RECOLHIMENTO FWA']));

              pInstalacao48Horas += getNumeroLimpo(getValueSafe(d, ['INSTALAÇÃO 48 HORAS']));
              pReparo48Horas += getNumeroLimpo(getValueSafe(d, ['REPARO 48 HORAS']));
              pTotalReparoSla48 += getNumeroLimpo(getValueSafe(d, ['TOTAL REPARO']));
              pTotalInstalacaoSla48 += getNumeroLimpo(getValueSafe(d, ['TOTAL INSTALAÇÃO']));

              pInstalacao72Horas += getNumeroLimpo(getValueSafe(d, ['INSTALAÇÃO 72 HORAS']));
              pReparo72Horas += getNumeroLimpo(getValueSafe(d, ['REPARO 72 HORAS']));
              pTotalReparoSla72 += getNumeroLimpo(getValueSafe(d, ['TOTAL REPARO']));
              pTotalInstalacaoSla72 += getNumeroLimpo(getValueSafe(d, ['TOTAL INSTALAÇÃO']));

              pTotalCancelamentosChurn += getNumeroLimpo(getRaioXCampoExato(d, 'CANCELAMENTOS (FTTH)'));
              pTotalInstalacoesChurn += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL_DE_ACESSOS (MÊS)'));
              pTotalOnusCriticosIsr += getNumeroLimpo(getRaioXCampoExato(d, 'ONUS CRÍTICOS'));
              pTotalOnusIsr += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL ONUS'));
            });

            pPpc = (pTotalPontosPpc > 0 && pTotalEquipesPpc > 0 && pDiasUteisPpc > 0) ? (pTotalPontosPpc / pTotalEquipesPpc / pDiasUteisPpc) : 0;
            renderTrend('trendPontoCabeca', 'trendIconPontoCabeca', 'trendTextPontoCabeca', valAtualPpc, pPpc, false, false);
            var prevTam = calcularTamRaioXAgregado(dataPrev).percentual;
            renderTrend('trendTam', 'trendIconTam', 'trendTextTam', valAtualTam, prevTam, true, false);
            var tamAbsAgregadoPrev = calcularTamAbsAgregado(dataPrev, mesAnteriorStr);
            var prevAbsCliente = tamAbsAgregadoPrev.percentual;
            renderTrend('trendAbsCliente', 'trendIconAbsCliente', 'trendTextAbsCliente', valAtualAbs, prevAbsCliente, true, false);
            var prevTamKmlRaioX = calcularTamKmlRaioXAgregado(dataPrev).percentual;
            renderTrend('trendTamKml', 'trendIconTamKml', 'trendTextTamKml', valAtualKml, prevTamKmlRaioX, true, false);
            renderTrend('trendAcidentes', 'trendIconAcidentes', 'trendTextAcidentes', valAtualAcidentes, pAcd, false, true);
            renderTrend('trendAderenciaInspecao', 'trendIconAderenciaInspecao', 'trendTextAderenciaInspecao', valAtualInspecao, pTotalInspecoes > 0 ? ((pTotalInspecaoOk / pTotalInspecoes) * 100) : 0, true, false);
            var prevTratarPonto = pTotalEquipesTratarRaioX > 0 ? ((pTotalPontosTratarRaioX / pTotalEquipesTratarRaioX) * 100) : null;
            renderTrend('trendTratarPonto', 'trendIconTratarPonto', 'trendTextTratarPonto', valAtualTratarPonto, prevTratarPonto, true, true);
            renderTrend('trendBhHe', 'trendIconBhHe', 'trendTextBhHe', valAtualBhHeSegundos, pPtTotalBhHe > 0 ? pBhHeSegundos / pPtTotalBhHe : 0, false, true);
            renderTrend('trendGpsPonto', 'trendIconGpsPonto', 'trendTextGpsPonto', valAtualGpsPonto, pTotalEquipesGps > 0 ? pGps / pTotalEquipesGps : 0, false, true);
            var prevRecorrencia = pTotalServicosRecorrencia > 0 ? ((pTotalRecorrenciasRecorrencia / pTotalServicosRecorrencia) * 100) : 0;
            renderTrend('trendRecorrencia', 'trendIconRecorrencia', 'trendTextRecorrencia', valAtualRecorrencia, prevRecorrencia, true, true);
            renderTrend('trendConectorInst', 'trendIconConectorInst', 'trendTextConectorInst', valAtualConectorInst, cCIP > 0 ? pCI / cCIP : 0, false, true);
            renderTrend('trendConectorRep', 'trendIconConectorRep', 'trendTextConectorRep', valAtualConectorRep, cCRP > 0 ? pCR / cCRP : 0, false, true);
            renderTrend('trendDropIat', 'trendIconDropIat', 'trendTextDropIat', valAtualDropIat, cDIP > 0 ? pDI / cDIP : 0, false, true);
            var prevIndiceVisitasRaioX = calcularIndiceVisitasRaioXAgregado(dataPrev).percentual;
            renderTrend('trendIndiceVisitas', 'trendIconIndiceVisitas', 'trendTextIndiceVisitas', valAtualIndiceVisitas, prevIndiceVisitasRaioX, true, true);
            renderTrend('trendSlaIat', 'trendIconSlaIat', 'trendTextSlaIat', valAtualSlaIat, cSLAP > 0 ? pSLA / cSLAP : 0, true, false);
            var prevInstEfetRaioX = calcularInstEfetRaioXAgregado(dataPrev).percentual;
            renderTrend('trendInstEfet', 'trendIconInstEfet', 'trendTextInstEfet', valAtualInstEfet, prevInstEfetRaioX, true, false);

            var prevSafraGeral = cSafraP > 0 ? (pSafra / cSafraP) : null;
            renderTrend('trendSafra', 'trendIconSafra', 'trendTextSafra', valAtualSafra, prevSafraGeral, true, false);

            var prevSafraFtth = pDemandaRecolhimentoSafraFtth > 0 ? ((pTotalRecolhidoSafraFtth / pDemandaRecolhimentoSafraFtth) * 100) : null;
            var prevSafraFwa = pDemandaRecolhimentoSafraFwa > 0 ? ((pTotalRecolhidoSafraFwa / pDemandaRecolhimentoSafraFwa) * 100) : null;
            renderTrend('trendSafraFtth', 'trendIconSafraFtth', 'trendTextSafraFtth', valAtualSafraFtth, prevSafraFtth, true, false);
            renderTrend('trendSafraFwa', 'trendIconSafraFwa', 'trendTextSafraFwa', valAtualSafraFwa, prevSafraFwa, true, false);

            var prevSlaIat48 = (pTotalReparoSla48 + pTotalInstalacaoSla48) > 0 ? (((pInstalacao48Horas + pReparo48Horas) / (pTotalReparoSla48 + pTotalInstalacaoSla48)) * 100) : null;
            var prevSlaIat72 = (pTotalReparoSla72 + pTotalInstalacaoSla72) > 0 ? (((pInstalacao72Horas + pReparo72Horas) / (pTotalReparoSla72 + pTotalInstalacaoSla72)) * 100) : null;
            renderTrend('trendSlaIat48', 'trendIconSlaIat48', 'trendTextSlaIat48', valAtualSlaIat48, prevSlaIat48, true, false);
            renderTrend('trendSlaIat72', 'trendIconSlaIat72', 'trendTextSlaIat72', valAtualSlaIat72, prevSlaIat72, true, false);

            var prevChurnRate = pTotalInstalacoesChurn > 0 ? ((pTotalCancelamentosChurn / pTotalInstalacoesChurn) * 100) : null;
            var prevIsrPastor = pTotalOnusIsr > 0 ? ((pTotalOnusCriticosIsr / pTotalOnusIsr) * 100) : null;
            renderTrend('trendChurnRate', 'trendIconChurnRate', 'trendTextChurnRate', valAtualChurnRate, prevChurnRate, true, true);
            renderTrend('trendIsrPastor', 'trendIconIsrPastor', 'trendTextIsrPastor', valAtualIsrPastor, prevIsrPastor, true, true);
            var prevIndiceRetencao = cRetP > 0 ? (pRet / cRetP) : null;
            renderTrend('trendIndiceRetencao', 'trendIconIndiceRetencao', 'trendTextIndiceRetencao', valAtualIndiceRetencao, prevIndiceRetencao, true, false);

            renderTrend('trendCertificacao', 'trendIconCertificacao', 'trendTextCertificacao', valAtualCertificacao, cCertP > 0 ? pCert / cCertP : 0, true, false);
            renderTrend('trendCertificacaoCidades', 'trendIconCertificacaoCidades', 'trendTextCertificacaoCidades', valAtualCertificacaoCidades, pTotalIndCid > 0 ? (pBateuIndCid / pTotalIndCid) * 100 : 0, true, false);
            renderTrend('trendRegularizacao15m', 'trendIconRegularizacao15m', 'trendTextRegularizacao15m', valAtualRegularizacao15m, pCaixas15m > 0 ? (pReg15m / pCaixas15m) * 100 : 0, true, false);
            renderTrend('trendEquipes4Quartil', 'trendIconEquipes4Quartil', 'trendTextEquipes4Quartil', valAtualEquipes4Quartil, pEquipes4Q > 0 ? (pTec4Q / pEquipes4Q) * 100 : 0, true, true);
            
            var prevBacklogReparo = pMedia7DiasCard > 0 ? (pDemandaReparoCard / pMedia7DiasCard) : 0;
            renderTrend('trendBacklogReparo', 'trendIconBacklogReparo', 'trendTextBacklogReparo', valAtualBacklogReparo, prevBacklogReparo, false, true);

            var prevBacklogInstalacao = pMedia7DiasInstalacaoCard > 0 ? (pDemandaInstalacaoCard / pMedia7DiasInstalacaoCard) : 0;
            renderTrend('trendBacklogInstalacao', 'trendIconBacklogInstalacao', 'trendTextBacklogInstalacao', valAtualBacklogInstalacao, prevBacklogInstalacao, false, true);

            var pDemIatTotal = pDemandaInstalacaoCard + pDemandaReparoCard;
            var pMedIatTotal = pMedia7DiasInstalacaoCard + pMedia7DiasCard;
            var prevBacklogIat = pMedIatTotal > 0 ? (pDemIatTotal / pMedIatTotal) : 0;
            renderTrend('trendBacklogIat', 'trendIconBacklogIat', 'trendTextBacklogIat', valAtualBacklogIat, prevBacklogIat, false, true);
          }
        }

        renderCardStyle('cardPontoCabeca', 'valPontoCabeca', 'barPontoCabeca', valAtualPpc.toFixed(2).replace('.', ','), valAtualPpc >= metaIdeal, Math.min((valAtualPpc / metaIdeal) * 100, 100));
        renderCardStyle('cardTam', 'valTam', 'barTam', valAtualTam.toFixed(2).replace('.', ',') + '%', valAtualTam >= metaTamIdeal, Math.min((valAtualTam / metaTamIdeal) * 100, 100));
        renderCardStyle('cardAbsCliente', 'valAbsCliente', 'barAbsCliente', valAtualAbs.toFixed(2).replace('.', ',') + '%', valAtualAbs >= metaAbsIdeal, Math.min((valAtualAbs / metaAbsIdeal) * 100, 100));
        renderCardStyle('cardTamKml', 'valTamKml', 'barTamKml', valAtualKml.toFixed(2).replace('.', ',') + '%', valAtualKml >= metaKmlIdeal, Math.min((valAtualKml / metaKmlIdeal) * 100, 100));
        renderCardStyle('cardAcidentes', 'valAcidentes', 'barAcidentes', valAtualAcidentes.toString(), valAtualAcidentes === 0, 100);
        renderCardStyle('cardAderenciaInspecao', 'valAderenciaInspecao', 'barAderenciaInspecao', valAtualInspecao.toFixed(2).replace('.', ',') + '%', valAtualInspecao >= metaInspecaoIdeal, Math.min((valAtualInspecao / metaInspecaoIdeal) * 100, 100));
        renderCardStyle('cardTratarPonto', 'valTratarPonto', 'barTratarPonto', valAtualTratarPonto.toFixed(2).replace('.', ',') + '%', valAtualTratarPonto === 0, valAtualTratarPonto === 0 ? 100 : Math.min(valAtualTratarPonto, 100));
        renderCardStyle('cardBhHe', 'valBhHe', 'barBhHe', segundosParaHHMMSS(valAtualBhHeSegundos), valAtualBhHeSegundos <= metaBhHeIdealSegundos, 100);
        renderCardStyle('cardGpsPonto', 'valGpsPonto', 'barGpsPonto', formatTableNumber(valAtualGpsPonto), valAtualGpsPonto <= metaGpsPontoIdeal, 100);
        renderCardStyle('cardRecorrencia', 'valRecorrencia', 'barRecorrencia', valAtualRecorrencia.toFixed(2).replace('.', ',') + '%', valAtualRecorrencia <= metaRecorrenciaIdeal, 100);
        renderCardStyle('cardConectorInst', 'valConectorInst', 'barConectorInst', valAtualConectorInst.toFixed(2).replace('.', ','), valAtualConectorInst <= metaConectorInstIdeal, 100);
        renderCardStyle('cardConectorRep', 'valConectorRep', 'barConectorRep', valAtualConectorRep.toFixed(2).replace('.', ','), valAtualConectorRep <= metaConectorRepIdeal, 100);
        renderCardStyle('cardDropIat', 'valDropIat', 'barDropIat', valAtualDropIat.toFixed(2).replace('.', ','), valAtualDropIat <= metaDropIatIdeal, 100);
        renderCardStyle('cardIndiceVisitas', 'valIndiceVisitas', 'barIndiceVisitas', valAtualIndiceVisitas.toFixed(2).replace('.', ',') + '%', valAtualIndiceVisitas <= metaIndiceVisitasIdeal, 100);
        renderCardStyle('cardSlaIat', 'valSlaIat', 'barSlaIat', valAtualSlaIat.toFixed(2).replace('.', ',') + '%', valAtualSlaIat >= metaSlaIatIdeal, Math.min((valAtualSlaIat / metaSlaIatIdeal) * 100, 100));
        renderCardStyle('cardInstEfet', 'valInstEfet', 'barInstEfet', valAtualInstEfet.toFixed(2).replace('.', ',') + '%', valAtualInstEfet >= metaInstEfetIdeal, Math.min((valAtualInstEfet / metaInstEfetIdeal) * 100, 100));
        if (valAtualSlaIat48 === null) { renderCardStyle('cardSlaIat48','valSlaIat48','barSlaIat48','-',true,0); } else { renderCardStyle('cardSlaIat48','valSlaIat48','barSlaIat48',valAtualSlaIat48.toFixed(2).replace('.',',')+'%',valAtualSlaIat48>=metaSlaIat48Ideal,Math.min(valAtualSlaIat48,100)); }
        if (valAtualSlaIat72 === null) { renderCardStyle('cardSlaIat72','valSlaIat72','barSlaIat72','-',true,0); } else { renderCardStyle('cardSlaIat72','valSlaIat72','barSlaIat72',valAtualSlaIat72.toFixed(2).replace('.',',')+'%',valAtualSlaIat72>=metaSlaIat72CorIdeal,Math.min(valAtualSlaIat72,100)); }
        if (valAtualChurnRate === null) { renderCardStyle('cardChurnRate','valChurnRate','barChurnRate','-',true,0); } else { renderCardStyle('cardChurnRate','valChurnRate','barChurnRate',valAtualChurnRate.toFixed(2).replace('.',',')+'%',valAtualChurnRate<=metaChurnRateIdeal,100); }
        if (valAtualIsrPastor === null) { renderCardStyle('cardIsrPastor','valIsrPastor','barIsrPastor','-',true,0); } else { renderCardStyle('cardIsrPastor','valIsrPastor','barIsrPastor',valAtualIsrPastor.toFixed(2).replace('.',',')+'%',valAtualIsrPastor<=metaIsrPastorIdeal,100); }

        if (valAtualSafra === null) {
          renderCardStyle('cardSafra', 'valSafra', 'barSafra', '-', true, 0);
        } else {
          renderCardStyle('cardSafra', 'valSafra', 'barSafra', valAtualSafra.toFixed(2).replace('.', ',') + '%', valAtualSafra >= metaSafraIdeal, Math.min((valAtualSafra / metaSafraIdeal) * 100, 100));
        }

        if (valAtualSafraFtth === null) {
          renderCardStyle('cardSafraFtth','valSafraFtth','barSafraFtth','-',true,0);
        } else {
          renderCardStyle('cardSafraFtth','valSafraFtth','barSafraFtth',valAtualSafraFtth.toFixed(2).replace('.',',')+'%',valAtualSafraFtth>=metaSafraIdeal,Math.min((valAtualSafraFtth/metaSafraIdeal)*100,100));
        }
        if (valAtualSafraFwa === null) {
          renderCardStyle('cardSafraFwa','valSafraFwa','barSafraFwa','-',true,0);
        } else {
          renderCardStyle('cardSafraFwa','valSafraFwa','barSafraFwa',valAtualSafraFwa.toFixed(2).replace('.',',')+'%',valAtualSafraFwa>=metaSafraIdeal,Math.min((valAtualSafraFwa/metaSafraIdeal)*100,100));
        }

        if (valAtualIndiceRetencao === null) {
          renderCardStyle('cardIndiceRetencao', 'valIndiceRetencao', 'barIndiceRetencao', '-', true, 0);
        } else {
          renderCardStyle('cardIndiceRetencao', 'valIndiceRetencao', 'barIndiceRetencao', valAtualIndiceRetencao.toFixed(2).replace('.', ',') + '%', valAtualIndiceRetencao >= metaIndiceRetencaoIdeal, Math.min((valAtualIndiceRetencao / metaIndiceRetencaoIdeal) * 100, 100));
        }

        renderCardStyle('cardCertificacao', 'valCertificacaoCard', 'barCertificacaoCard', valAtualCertificacao.toFixed(2).replace('.', ',') + '%', valAtualCertificacao >= metaCertificacaoIdeal, Math.min((valAtualCertificacao / metaCertificacaoIdeal) * 100, 100));
        renderCardStyle('cardCertificacaoCidades', 'valCertificacaoCidadesCard', 'barCertificacaoCidadesCard', valAtualCertificacaoCidades.toFixed(2).replace('.', ',') + '%', valAtualCertificacaoCidades >= metaCertificacaoCidadesIdeal, Math.min((valAtualCertificacaoCidades / metaCertificacaoCidadesIdeal) * 100, 100));
        renderCardStyle('cardRegularizacao15m', 'valRegularizacao15mCard', 'barRegularizacao15mCard', valAtualRegularizacao15m.toFixed(2).replace('.', ',') + '%', valAtualRegularizacao15m >= metaRegularizacao15mIdeal, Math.min((valAtualRegularizacao15m / metaRegularizacao15mIdeal) * 100, 100));
        renderCardStyle('cardEquipes4Quartil', 'valEquipes4QuartilCard', 'barEquipes4QuartilCard', valAtualEquipes4Quartil.toFixed(2).replace('.', ',') + '%', valAtualEquipes4Quartil === metaEquipes4QuartilIdeal, valAtualEquipes4Quartil === 0 ? 100 : Math.min(valAtualEquipes4Quartil, 100));
        renderCardStyle('cardAtestado12m', 'valAtestado12mCard', 'barAtestado12mCard', valAtualAtestado12m.toFixed(2).replace('.', ','), valAtualAtestado12m <= metaAtestado12mIdeal, 100);
        renderCardStyle('cardImprodutivaIat', 'valImprodutivaIatCard', 'barImprodutivaIatCard', valAtualImprodutivaIat.toFixed(2).replace('.', ',') + '%', valAtualImprodutivaIat <= metaImprodutivaIatIdeal, 100);
        renderCardStyle('cardBacklogReparo', 'valBacklogReparoCard', 'barBacklogReparoCard', valAtualBacklogReparo.toFixed(2).replace('.', ','), valAtualBacklogReparo <= metaBacklogReparoIdeal, 100);
        renderCardStyle('cardBacklogInstalacao', 'valBacklogInstalacaoCard', 'barBacklogInstalacaoCard', valAtualBacklogInstalacao.toFixed(2).replace('.', ','), valAtualBacklogInstalacao <= metaBacklogInstalacaoIdeal, 100);
        renderCardStyle('cardBacklogIat', 'valBacklogIatCard', 'barBacklogIatCard', valAtualBacklogIat.toFixed(2).replace('.', ','), valAtualBacklogIat <= metaBacklogIatIdeal, 100);
        sincronizarIconesKpiComStatus();
        aplicarStatusKpiComFaixaDeAtencao();

        renderTabelaRaioXGeral(dataToCalc);
        renderizarGraficosQuartilLideranca(dataToCalc);
      }
      var nivelGraficoQuartilLideranca = 'gerente';
      var ultimoDataGraficoQuartilLideranca = null;

      function renderizarGraficosQuartilLideranca(dataContext) {
        var sourceData = dataContext || getFilteredData(false);
        ultimoDataGraficoQuartilLideranca = sourceData;
        renderizarNivelGraficoQuartilLideranca(sourceData);
      }

      function renderizarNivelGraficoQuartilLideranca(dataContext) {
        var sourceData = dataContext || ultimoDataGraficoQuartilLideranca || getFilteredData(false);
        var ehGerente = nivelGraficoQuartilLideranca === 'gerente';
        var campo = ehGerente ? 'GERENTE DE CAMPO' : 'COORDENADOR DE CAMPO';
        var dados = processarQuartisPorCampo(sourceData, campo);
        var titulo = document.getElementById('tituloGraficoQuartilLideranca');
        var botao = document.getElementById('btnDrilldownQuartil');

        if (titulo) {
          titulo.innerHTML = '<i class="fa-solid ' + (ehGerente ? 'fa-briefcase' : 'fa-user-tie') + '"></i> DISTRIBUIÇÃO DE QUARTIS POR ' + (ehGerente ? 'GERENTE' : 'COORDENADOR') + ' DE CAMPO';
        }
        if (botao) {
          botao.innerHTML = ehGerente
            ? '<span>CLIQUE NA SETA AO LADO PARA ESTRATIFICAR</span><i class="fa-solid fa-chevron-down"></i>'
            : '<span>CLIQUE NA SETA AO LADO PARA VOLTAR</span><i class="fa-solid fa-chevron-up"></i>';
          botao.title = ehGerente ? 'Estratificar por coordenador' : 'Voltar para gerente';
        }

        plotarGraficoEmpilhado('graficoQuartilLideranca', 'meuGraficoQuartilLideranca', dados);
      }

      function alternarGraficoQuartilLideranca() {
        nivelGraficoQuartilLideranca = nivelGraficoQuartilLideranca === 'gerente' ? 'coordenador' : 'gerente';
        renderizarNivelGraficoQuartilLideranca();
      }

      function processarQuartisPorCampo(dataList, campoChave) {
        var grupos = {};
        var ignorarNomes = ['MPC', 'N/A', 'SEM GERENTE', 'SEM COORDENADOR', 'UNDEFINED', '-'];

        dataList.forEach(function(item) {
          var nomeLider = getValueSafe(item, [campoChave]);
          if (!nomeLider || String(nomeLider).trim() === '' || String(nomeLider).trim() === '0') return;

          nomeLider = String(nomeLider).trim();
          var nomeLiderClean = limparTexto(nomeLider);

          if (ignorarNomes.indexOf(nomeLiderClean) !== -1) return;

          if (!grupos[nomeLider]) {
            grupos[nomeLider] = { nome: nomeLider, eq3: 0, q1: 0, q2: 0, q3: 0, q4: 0 };
          }

          var eq3 = getNumeroLimpo(getValueSafe(item, ['EQUIPES 3', 'EQUIPES3', 'EQUIPE 3']));
          var q1 = getNumeroLimpo(getValueSafe(item, ['1º QUARTIL', '1 QUARTIL', 'QUARTIL 1', 'Q1']));
          var q2 = getNumeroLimpo(getValueSafe(item, ['2º QUARTIL', '2 QUARTIL', 'QUARTIL 2', 'Q2']));
          var q3 = getNumeroLimpo(getValueSafe(item, ['3º QUARTIL', '3 QUARTIL', 'QUARTIL 3', 'Q3']));
          var q4 = getNumeroLimpo(getValueSafe(item, ['4º QUARTIL', '4 QUARTIL', 'QUARTIL 4', 'Q4']));

          grupos[nomeLider].eq3 += eq3;
          grupos[nomeLider].q1 += q1;
          grupos[nomeLider].q2 += q2;
          grupos[nomeLider].q3 += q3;
          grupos[nomeLider].q4 += q4;
        });

        var listaResultados = [];
        Object.keys(grupos).forEach(function(k) {
          var g = grupos[k];
          var total = g.eq3 > 0 ? g.eq3 : (g.q1 + g.q2 + g.q3 + g.q4);
          
          if (total > 0) {
            var pctQ1 = Number(((g.q1 / total) * 100).toFixed(2));
            var pctQ2 = Number(((g.q2 / total) * 100).toFixed(2));
            var pctQ3 = Number(((g.q3 / total) * 100).toFixed(2));
            var pctQ4 = Number(((g.q4 / total) * 100).toFixed(2));

            listaResultados.push({
              nome: g.nome,
              pctQ1: pctQ1,
              pctQ2: pctQ2,
              pctQ3: pctQ3,
              pctQ4: pctQ4
            });
          }
        });

        listaResultados.sort(function(a, b) {
          return b.pctQ1 - a.pctQ1;
        });

        return listaResultados;
      }

      function plotarGraficoEmpilhado(canvasId, globalVarName, dadosAgrupados) {
        var ctx = document.getElementById(canvasId); if (!ctx) return;
        var isDark = document.body.classList.contains('dark-mode');
        var textColor = isDark ? '#9ca3af' : '#6c757d';
        var gridColor = isDark ? 'rgba(255,255,255,0.08)' : 'rgba(0,0,0,0.06)';

        if (window[globalVarName]) window[globalVarName].destroy();

        var labels = dadosAgrupados.map(function(d) { return d.nome; });
        var dataQ1 = dadosAgrupados.map(function(d) { return d.pctQ1; });
        var dataQ2 = dadosAgrupados.map(function(d) { return d.pctQ2; });
        var dataQ3 = dadosAgrupados.map(function(d) { return d.pctQ3; });
        var dataQ4 = dadosAgrupados.map(function(d) { return d.pctQ4; });

        window[globalVarName] = new Chart(ctx, {
          type: 'bar',
          data: {
            labels: labels,
            datasets: [
              { label: '1º QUARTIL', data: dataQ1, backgroundColor: '#2e7d32', stack: 'Stack 0' },
              { label: '2º QUARTIL', data: dataQ2, backgroundColor: '#81c995', stack: 'Stack 0' },
              { label: '3º QUARTIL', data: dataQ3, backgroundColor: '#e57373', stack: 'Stack 0' },
              { label: '4º QUARTIL', data: dataQ4, backgroundColor: '#d32f2f', stack: 'Stack 0' }
            ]
          },
          plugins: [ChartDataLabels],
          options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
              legend: {
                display: true,
                position: 'top',
                labels: {
                  color: isDark ? '#e2e8f0' : '#2b3648',
                  font: { family: 'Inter', size: 11, weight: 'bold' },
                  boxWidth: 14
                }
              },
              datalabels: {
                color: '#ffffff',
                font: { family: 'Inter', size: 10, weight: 'bold' },
                formatter: function(value) {
                  return value > 0 ? value.toFixed(2).replace('.', ',') + '%' : '';
                }
              }
            },
            scales: {
              x: {
                stacked: true,
                grid: { display: false },
                ticks: { color: textColor, font: { family: 'Inter', size: 10, weight: 'bold' } }
              },
              y: {
                stacked: true,
                beginAtZero: true,
                max: 100,
                grid: { color: gridColor },
                ticks: {
                  color: textColor,
                  font: { family: 'Inter', size: 10 },
                  callback: function(v) { return v + '%'; }
                }
              }
            }
          }
        });
      }

      function renderTrend(containerId, iconId, textId, curVal, prevVal, isPct, invertGood) {
        var container = document.getElementById(containerId), icon = document.getElementById(iconId), text = document.getElementById(textId);
        if (!container || !icon || !text) return;
        if (prevVal === undefined || prevVal === null) { container.style.display = 'none'; return; }
        container.style.display = 'flex';
        var diff = curVal - prevVal;
        if (Math.abs(diff) < 0.001) {
          container.className = 'kpi-trend trend-neutral'; icon.className = 'fa-solid fa-minus'; text.textContent = 'vs. mês anterior'; return;
        }
        var isUp = diff > 0;
        var isGood = invertGood ? !isUp : isUp;
        container.className = isGood ? 'kpi-trend trend-up' : 'kpi-trend trend-down';
        icon.className = isUp ? 'fa-solid fa-arrow-up' : 'fa-solid fa-arrow-down';
        var txtDiff = isPct ? Math.abs(diff).toFixed(2).replace('.', ',') + '%' : Math.abs(diff).toFixed(2).replace('.', ',');
        text.textContent = (isUp ? '+' : '-') + txtDiff + ' vs. mês anterior';
      }

      function renderCardStyle(cardId, valId, barId, textValue, isSuccess, barPct) {
        var card = document.getElementById(cardId), valEl = document.getElementById(valId), barEl = document.getElementById(barId);
        if (!card || !valEl || !barEl) return;

        var semDado = String(textValue).trim() === '-';
        valEl.textContent = textValue;
        valEl.classList.toggle('no-data', semDado);

        var iconBox = card.querySelector('.kpi-title-icon');
        if (iconBox) {
          iconBox.classList.remove('icon-status-success','icon-status-danger','icon-status-warning','icon-status-neutral');
        }

        if (semDado) {
          card.className = 'kpi-card kpi-neutral';
          barEl.style.width = '0%';
          barEl.className = 'kpi-progress-bar bar-neutral';
          if (iconBox) iconBox.classList.add('icon-status-neutral');
          return;
        }

        barEl.style.width = barPct + '%';
        card.className = isSuccess ? 'kpi-card kpi-success' : 'kpi-card kpi-danger';
        barEl.className = isSuccess ? 'kpi-progress-bar bar-success' : 'kpi-progress-bar bar-danger';
        if (iconBox) iconBox.classList.add(isSuccess ? 'icon-status-success' : 'icon-status-danger');
      }

      function aplicarStatusKpiComFaixaDeAtencao() {
        var configs = {
          cardPontoCabeca:{valueId:'valPontoCabeca',meta:7.5,direction:'higher'}, cardTam:{valueId:'valTam',meta:80,direction:'higher'}, cardAbsCliente:{valueId:'valAbsCliente',meta:50,direction:'higher'}, cardTamKml:{valueId:'valTamKml',meta:54,direction:'higher'},
          cardAcidentes:{valueId:'valAcidentes',meta:0,direction:'lower'}, cardAderenciaInspecao:{valueId:'valAderenciaInspecao',meta:90,direction:'higher'}, cardTratarPonto:{valueId:'valTratarPonto',meta:0,direction:'lower'}, cardBhHe:{valueId:'valBhHe',meta:240,direction:'lower',type:'time'},
          cardGpsPonto:{valueId:'valGpsPonto',meta:15,direction:'lower'}, cardRecorrencia:{valueId:'valRecorrencia',meta:8,direction:'lower'}, cardConectorInst:{valueId:'valConectorInst',meta:2,direction:'lower'}, cardConectorRep:{valueId:'valConectorRep',meta:0.79,direction:'lower'}, cardDropIat:{valueId:'valDropIat',meta:80,direction:'lower'}, cardIndiceVisitas:{valueId:'valIndiceVisitas',meta:4,direction:'lower'},
          cardSlaIat:{valueId:'valSlaIat',meta:65,direction:'higher'}, cardInstEfet:{valueId:'valInstEfet',meta:90,direction:'higher'}, cardSlaIat48:{valueId:'valSlaIat48',meta:70,direction:'higher'}, cardSlaIat72:{valueId:'valSlaIat72',meta:90,direction:'higher'},
          cardChurnRate:{valueId:'valChurnRate',meta:1.5,direction:'lower'}, cardIsrPastor:{valueId:'valIsrPastor',meta:2,direction:'lower'}, cardSafra:{valueId:'valSafra',meta:74,direction:'higher'}, cardSafraFtth:{valueId:'valSafraFtth',meta:74,direction:'higher'}, cardSafraFwa:{valueId:'valSafraFwa',meta:74,direction:'higher'}, cardIndiceRetencao:{valueId:'valIndiceRetencao',meta:15,direction:'higher'},
          cardCertificacao:{valueId:'valCertificacaoCard',meta:80,direction:'higher'}, cardCertificacaoCidades:{valueId:'valCertificacaoCidadesCard',meta:50,direction:'higher'}, cardRegularizacao15m:{valueId:'valRegularizacao15mCard',meta:50,direction:'higher'}, cardEquipes4Quartil:{valueId:'valEquipes4QuartilCard',meta:0,direction:'lower'}, cardAtestado12m:{valueId:'valAtestado12mCard',meta:1,direction:'lower'}, cardImprodutivaIat:{valueId:'valImprodutivaIatCard',meta:15,direction:'lower'}, cardBacklogReparo:{valueId:'valBacklogReparoCard',meta:2,direction:'lower'}, cardBacklogInstalacao:{valueId:'valBacklogInstalacaoCard',meta:2,direction:'lower'}, cardBacklogIat:{valueId:'valBacklogIatCard',meta:2,direction:'lower'}
        };
        function parseValor(el,type){ if(!el)return null; var raw=String(el.textContent||'').trim(); if(!raw||raw==='-'||raw==='—')return null; if(type==='time'){var p=raw.split(':').map(Number); if(p.length!==3||p.some(function(n){return isNaN(n);}))return null; return p[0]*3600+p[1]*60+p[2];} var normalizado=raw.replace(/%/g,'').trim(); if(normalizado.indexOf(',')>=0){ normalizado=normalizado.replace(/\./g,'').replace(',','.'); } var num=parseFloat(normalizado); return isNaN(num)?null:num; }
        Object.keys(configs).forEach(function(cardId){ var cfg=configs[cardId], card=document.getElementById(cardId), valueEl=document.getElementById(cfg.valueId); if(!card||!valueEl)return; var valor=parseValor(valueEl,cfg.type); if(valor===null){card.className='kpi-card kpi-neutral'; var bn=card.querySelector('.kpi-progress-bar'); if(bn)bn.className='kpi-progress-bar bar-neutral'; return;} var status='danger'; if(cfg.direction==='higher'){if(valor>=cfg.meta)status='success';else if(valor>=cfg.meta*0.90)status='warning';}else{if(valor<=cfg.meta)status='success';else if(cfg.meta>0&&valor<=cfg.meta/0.90)status='warning';} card.className='kpi-card kpi-'+status; var bar=card.querySelector('.kpi-progress-bar'); if(bar)bar.className='kpi-progress-bar bar-'+status; });
        sincronizarIconesKpiComStatus();
      }

      function sincronizarIconesKpiComStatus() {
        document.querySelectorAll('.kpi-card .kpi-title-icon').forEach(function(iconBox) {
          var card = iconBox.closest('.kpi-card');
          if (!card) return;
          iconBox.classList.remove('icon-status-success','icon-status-danger','icon-status-warning','icon-status-neutral');
          if (card.classList.contains('kpi-success')) iconBox.classList.add('icon-status-success');
          else if (card.classList.contains('kpi-danger')) iconBox.classList.add('icon-status-danger');
          else if (card.classList.contains('kpi-warning')) iconBox.classList.add('icon-status-warning');
          else iconBox.classList.add('icon-status-neutral');
        });
      }

      function renderTabelaRaioXGeral(dataList) {
        var tbody = document.getElementById('tabelaRaioXBody');
        if (!tbody) return;
        tbody.innerHTML = '';

        if (!dataList || dataList.length === 0) {
          tbody.innerHTML = '<tr><td colspan="21" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro encontrado.</td></tr>';
          return;
        }

        var listaOrdenada = dataList.slice().sort(function(a, b) {
          var certA = getNumeroLimpo(getValueSafe(a, ['CERTIFICAÇÃO', 'CERTIFICACAO']));
          if (certA <= 1 && certA > 0) certA *= 100;
          var certB = getNumeroLimpo(getValueSafe(b, ['CERTIFICAÇÃO', 'CERTIFICACAO']));
          if (certB <= 1 && certB > 0) certB *= 100;

          // 1º critério: certificação (maior para menor).
          var diferencaCert = certB - certA;
          if (Math.abs(diferencaCert) > 0.000001) return diferencaCert;

          // 2º critério: ÍNDICE DE VISITAS (menor é melhor).
          var visitasA = getNumeroLimpo(getValueSafe(a, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS']));
          var visitasB = getNumeroLimpo(getValueSafe(b, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS']));
          if (visitasA <= 1 && visitasA > 0) visitasA *= 100;
          if (visitasB <= 1 && visitasB > 0) visitasB *= 100;
          if (!isFinite(visitasA) || visitasA < 0) visitasA = 999999999;
          if (!isFinite(visitasB) || visitasB < 0) visitasB = 999999999;
          if (Math.abs(visitasA - visitasB) > 0.000001) return visitasA - visitasB;

          // Desempate final para manter a ordem determinística.
          var nomeA = String(getValueSafe(a, ['NOME_ABREVIADO', 'NOME ABREVIADO', 'NOME']) || '');
          var nomeB = String(getValueSafe(b, ['NOME_ABREVIADO', 'NOME ABREVIADO', 'NOME']) || '');
          return nomeA.localeCompare(nomeB, 'pt-BR');
        });

        listaOrdenada.forEach(function(d) {
          var tr = document.createElement('tr');
          var indiceVisitasOrdenacao = getNumeroLimpo(getValueSafe(d, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS']));
          if (indiceVisitasOrdenacao <= 1 && indiceVisitasOrdenacao > 0) indiceVisitasOrdenacao *= 100;
          tr.dataset.indiceVisitasOrdenacao = (isFinite(indiceVisitasOrdenacao) && indiceVisitasOrdenacao >= 0) ? String(indiceVisitasOrdenacao) : '999999999';

          var sup = getValueSafe(d, ['NOME_ABREVIADO', 'NOME ABREVIADO', 'NOME ABREVIADO DO GESTOR', 'NOME_ABREVIADO_GESTOR']) || getValueSafe(d, ['NOME']) || '-';
          var mes = formatarMes(getValueSafe(d, ['MÊS'])) || '-';

          var numPpc = getNumeroLimpo(getValueSafe(d, ['PPC', 'PONTO POR CABEÇA', 'PONTO POR CABECA']));
          var numTam = getNumeroLimpo(getValueSafe(d, ['TAM'])); if (numTam <= 1 && numTam > 0) numTam *= 100;
          var numRec = getNumeroLimpo(getValueSafe(d, ['RECORRÊNCIA', 'RECORRENCIA', 'RECORRÊNCIA IAT'])); if (numRec <= 1 && numRec > 0) numRec *= 100;
          var numCI = getNumeroLimpo(getValueSafe(d, ['CONECTOR INSTALAÇÃO', 'CONECTOR INSTALACAO', 'CONECTOR INST']));
          var numCR = getNumeroLimpo(getValueSafe(d, ['CONECTOR REPARO', 'CONECTOR REPAROS', 'CONECTOR REP']));
          var numDI = getNumeroLimpo(getValueSafe(d, ['USO DROP IAT', 'DROP IAT', 'DROP']));
          var numKml = getNumeroLimpo(getValueSafe(d, ['TAM KM/L', 'TAM KML', 'KM/L'])); if (numKml <= 1 && numKml > 0) numKml *= 100;
          var numAbs = getNumeroLimpo(getValueSafe(d, ['TAM ABS/CLIENTE', 'TAM ABS CLIENTE', 'ABS/CLIENTE'])); if (numAbs <= 1 && numAbs > 0) numAbs *= 100;
          
          var rawBh = getValueSafe(d, ['BH+HE / PT', 'BH+HE/PT', 'BH + HE / PT']);
          var segBh = getNumeroLimpo(rawBh);
          var txtBh = segundosParaHHMMSS(segBh);

          var numTratar = getNumeroLimpo(getRaioXTratarPonto(d)); if (numTratar <= 1 && numTratar > 0) numTratar *= 100;
          var numGps = getNumeroLimpo(getValueSafe(d, ['ALERTA GPS', 'ALERTAS GPS', 'ALERTA GPS + PONTO']));
          var numAcd = getNumeroLimpo(getValueSafe(d, ['ACIDENTES', 'ACIDENTE']));
          var numInsp = getNumeroLimpo(getValueSafe(d, ['ADERÊNCIA INSPEÇÃO', 'ADERENCIA INSPECAO'])); if (numInsp <= 1 && numInsp > 0) numInsp *= 100;
          var numIE = getNumeroLimpo(getValueSafe(d, ['INSTALADOS X EFETIVADOS', 'INSTALADOS X EFETIVADO'])); if (numIE <= 1 && numIE > 0) numIE *= 100;
          var numIV = getNumeroLimpo(getValueSafe(d, ['ÍNDICE DE VISITAS', 'INDICE DE VISITAS'])); if (numIV <= 1 && numIV > 0) numIV *= 100;
          var numSLA = getNumeroLimpo(getValueSafe(d, ['SLA IAT', 'SLA_IAT', 'SLA'])); if (numSLA <= 1 && numSLA > 0) numSLA *= 100;

          var rowSetor = limparTexto(getValueSafe(d, ['SETOR']));
          var txtSafra = '-', isSafraOk = true;
          var txtRet = '-', isRetOk = true;

          var rSafraRow = getValueSafe(d, ['SAFRA']);
          if (rSafraRow !== undefined && rSafraRow !== null && rSafraRow !== '') {
            var nSafraDirect = getNumeroLimpo(rSafraRow); if (nSafraDirect <= 1 && nSafraDirect > 0) nSafraDirect *= 100;
            txtSafra = nSafraDirect.toFixed(2).replace('.', ',') + '%';
            isSafraOk = nSafraDirect >= metaSafraIdeal;
          } else if (rowSetor.indexOf('RECOLHIMENTO') !== -1) {
            var recOnu = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (ONU)']));
            var recFwa = getNumeroLimpo(getValueSafe(d, ['TOTAL RECOLHIDO (FWA)']));
            var demOnu = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (ONU)']));
            var demFwa = getNumeroLimpo(getValueSafe(d, ['DEMANDA RECOLHIMENTO (FWA)']));
            var nSafra = (demOnu + demFwa) > 0 ? (((recOnu + recFwa) / (demOnu + demFwa)) * 100) : 0;
            txtSafra = nSafra.toFixed(2).replace('.', ',') + '%';
            isSafraOk = nSafra >= metaSafraIdeal;
          }

          var rRetRow = getValueSafe(d, ['ÍNDICE DE RETENÇÃO', 'INDICE DE RETENCAO', 'RETENÇÃO', 'RETENCAO']);
          if (rRetRow !== undefined && rRetRow !== null && rRetRow !== '') {
            var nRetDirect = getNumeroLimpo(rRetRow); if (nRetDirect <= 1 && nRetDirect > 0) nRetDirect *= 100;
            txtRet = nRetDirect.toFixed(2).replace('.', ',') + '%';
            isRetOk = nRetDirect >= metaIndiceRetencaoIdeal;
          } else if (rowSetor.indexOf('RECOLHIMENTO') !== -1) {
            var reat = getNumeroLimpo(getValueSafe(d, ['REATIVADOS']));
            var gerRec = getNumeroLimpo(getValueSafe(d, ['GERADOS RECOLHIMENTO']));
            var gerFwa = getNumeroLimpo(getValueSafe(d, ['GERADOS FWA']));
            var nRet = (gerRec + gerFwa) > 0 ? ((reat / (gerRec + gerFwa)) * 100) : 0;
            txtRet = nRet.toFixed(2).replace('.', ',') + '%';
            isRetOk = nRet >= metaIndiceRetencaoIdeal;
          }

          var numCert = getNumeroLimpo(getValueSafe(d, ['CERTIFICAÇÃO', 'CERTIFICACAO'])); if (numCert <= 1 && numCert > 0) numCert *= 100;

          var cols = [
            { val: sup, isOk: null },
            { val: mes, isOk: null },
            { val: numPpc.toFixed(2).replace('.', ','), isOk: numPpc >= metaIdeal },
            { val: numTam.toFixed(2).replace('.', ',') + '%', isOk: numTam >= metaTamIdeal },
            { val: numRec.toFixed(2).replace('.', ',') + '%', isOk: numRec <= metaRecorrenciaIdeal },
            { val: numCI.toFixed(2).replace('.', ','), isOk: numCI <= metaConectorInstIdeal },
            { val: numCR.toFixed(2).replace('.', ','), isOk: numCR <= metaConectorRepIdeal },
            { val: numDI.toFixed(2).replace('.', ','), isOk: numDI <= metaDropIatIdeal },
            { val: numKml.toFixed(2).replace('.', ',') + '%', isOk: numKml >= metaKmlIdeal },
            { val: numAbs.toFixed(2).replace('.', ',') + '%', isOk: numAbs >= metaAbsIdeal },
            { val: txtBh, isOk: segBh <= metaBhHeIdealSegundos },
            { val: numTratar.toFixed(2).replace('.', ',') + '%', isOk: numTratar === 0 },
            { val: formatTableNumber(numGps), isOk: numGps <= metaGpsPontoIdeal },
            { val: numAcd.toString(), isOk: numAcd === 0 },
            { val: numInsp.toFixed(2).replace('.', ',') + '%', isOk: numInsp >= metaInspecaoIdeal },
            { val: numIE.toFixed(2).replace('.', ',') + '%', isOk: numIE >= metaInstEfetIdeal },
            { val: numIV.toFixed(2).replace('.', ',') + '%', isOk: numIV <= metaIndiceVisitasIdeal },
            { val: numSLA.toFixed(2).replace('.', ',') + '%', isOk: numSLA >= metaSlaIatIdeal },
            { val: txtSafra, isOk: txtSafra === '-' ? null : isSafraOk },
            { val: txtRet, isOk: txtRet === '-' ? null : isRetOk },
            { 
              val: numCert.toFixed(2).replace('.', ',') + '%', 
              classCor: numCert >= 80 ? 'td-success' : (numCert >= 40 ? 'td-warning' : 'td-danger')
            }
          ];

          cols.forEach(function(col) {
            var td = document.createElement('td');
            td.textContent = col.val;

            if (col.classCor) {
              td.className = col.classCor;
            } else if (col.isOk !== null && col.val !== '-') {
              td.className = col.isOk ? 'td-success' : 'td-danger';
            }

            tr.appendChild(td);
          });

          tbody.appendChild(tr);
        });
      }

      function updateCollaboratorCard(selectedNome) {
        if (!selectedNome) { resetCollaboratorCard(); return; }

        var mesesSelecionados = selects['filterMes'] ? selects['filterMes'].getValue() : [];
        var mesAtualFiltro = Array.isArray(mesesSelecionados) && mesesSelecionados.length === 1 ? mesesSelecionados[0] : (typeof mesesSelecionados === 'string' ? mesesSelecionados : null);

        var dataAtual = null;
        var nomeSelecionadoNormalizado = limparTexto(selectedNome);
        for (var i = 0; i < allData.length; i++) {
          if (limparTexto(getValueSafe(allData[i], ['NOME'])) === nomeSelecionadoNormalizado) {
            if (mesAtualFiltro) {
              if (valorFiltroNormalizado(formatarMes(allData[i]['MÊS'])) === valorFiltroNormalizado(mesAtualFiltro)) { dataAtual = allData[i]; break; }
            } else { dataAtual = allData[i]; break; }
          }
        }

        if (!dataAtual) dataAtual = allData.find(function(d) { return limparTexto(getValueSafe(d, ['NOME'])) === nomeSelecionadoNormalizado; });
        if (!dataAtual) return;

        var urlFoto = getValueSafe(dataAtual, ['FOTO 2', 'FOTO2', 'FOTO', 'LINK FOTO', 'URL DA FOTO', 'URL FOTO', 'LINK DA FOTO', 'IMAGEM']);
        var chaveFotoTv = limparTexto(selectedNome);
        var fotoPrecarregadaTv = (document.body.classList.contains('tv-mode') && Object.prototype.hasOwnProperty.call(tvFotosCache, chaveFotoTv))
          ? tvFotosCache[chaveFotoTv]
          : null;
        var urlFotoTv = fotoPrecarregadaTv && fotoPrecarregadaTv.src
          ? fotoPrecarregadaTv.src
          : urlFotoNormalizadaTv(urlFoto);
        var imgEl = document.getElementById('colabFoto');
        if (imgEl) {
          var fallbackFoto = "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='150' height='150'><rect width='100%' height='100%' fill='%23f4511e'/><text x='50%' y='50%' fill='%23ffffff' font-size='16' text-anchor='middle' dy='.3em'>FOTO</text></svg>";
          imgEl.onerror = function() { this.src = fallbackFoto; };

          // No Modo TV, só troca a foto depois que ela já estiver carregada e
          // decodificada. Assim nunca exibimos a foto do supervisor anterior
          // enquanto a nova ainda está sendo processada.
          if (fotoPrecarregadaTv && fotoPrecarregadaTv.complete) {
            imgEl.src = fotoPrecarregadaTv.src;
          } else {
            imgEl.src = urlFotoTv || fallbackFoto;
          }
        }

        document.getElementById('colabNomeAbreviado').textContent = dataAtual['NOME'] || 'N/A';
        document.getElementById('colabMatricula').textContent = dataAtual['CÓDIGO'] || '-';
        document.getElementById('colabCidade').textContent = dataAtual['CIDADE FILIAL'] || '-';
        document.getElementById('colabTempoEmpresa').textContent = dataAtual['TEMPO EMPRESA'] || dataAtual['TEMPO DE CASA'] || '-';
        document.getElementById('colabEquipes').textContent = dataAtual['EQUIPES'] || '0';
        document.getElementById('colabSetor').textContent = getValueSafe(dataAtual, ['SETOR']) || '-';

        var valRankingRaw = getValueSafe(dataAtual, ['POSIÇÃO 2', 'POSICAO 2']) || dataAtual['POSIÇÃO 2'] || dataAtual['POSICAO 2'];
        var rankingEl = document.getElementById('colabRanking');
        if (rankingEl) {
          rankingEl.innerHTML = '';
          var rankingRawTexto = (valRankingRaw !== undefined && valRankingRaw !== null) ? String(valRankingRaw).trim() : '';
          if (rankingRawTexto) {
            var matchRanking = rankingRawTexto.match(/^\d+/);
            var numRanking = matchRanking ? parseInt(matchRanking[0], 10) : NaN;
            var posSpan = document.createElement('span');
            posSpan.className = 'ranking-position-text';
            posSpan.textContent = rankingRawTexto;
            rankingEl.appendChild(posSpan);

            var qtdEstrelas = 0;
            if (numRanking === 1) qtdEstrelas = 5;
            else if (numRanking === 2) qtdEstrelas = 4;
            else if (numRanking === 3) qtdEstrelas = 3;
            else if (numRanking >= 4 && numRanking <= 10) qtdEstrelas = 1;

            if (qtdEstrelas > 0) {
              var starsSpan = document.createElement('span');
              starsSpan.className = 'ranking-stars';
              for (var si = 0; si < qtdEstrelas; si++) {
                var starSpan = document.createElement('span');
                starSpan.className = 'ranking-star';
                starSpan.textContent = '⭐';
                starsSpan.appendChild(starSpan);
              }
              rankingEl.appendChild(starsSpan);
            } else if (!isNaN(numRanking) && numRanking > 10) {
              var runnerSpan = document.createElement('span');
              runnerSpan.className = 'ranking-runner';
              runnerSpan.textContent = '🏃';
              rankingEl.appendChild(runnerSpan);
            }
          } else {
            rankingEl.textContent = '-';
          }
        }

        var valCertificacaoRaw = getValueSafe(dataAtual, ['CERTIFICAÇÃO', 'CERTIFICACAO']);
        var certFormatted = formatarCertificacao(valCertificacaoRaw);
        document.getElementById('colabCertificacao').textContent = certFormatted;

        var certBox = document.getElementById('badgeCertificacaoContainer');
        if (certBox) {
          var numCert = getNumeroLimpo(valCertificacaoRaw);
          if (numCert <= 1 && numCert > 0) numCert *= 100;
          certBox.className = 'colab-emphasis-card cert-emphasis-card';
          if (certFormatted === '-') {
            certBox.classList.add('badge-neutral');
          } else if (numCert > 80) {
            certBox.classList.add('badge-cert-good');
          } else if (numCert >= 40) {
            certBox.classList.add('badge-cert-mid');
          } else {
            certBox.classList.add('badge-cert-low');
          }
        }

        var valQProd = getValueSafe(dataAtual, ['QUARTIL PRODUÇÃO', 'QUARTIL PRODUCAO', 'QUARTIL PROD']);
        var strQProd = (valQProd !== undefined && valQProd !== null && String(valQProd).trim() !== '') ? String(valQProd).trim() : '-';
        var valQRaioX = getValueSafe(dataAtual, ['QUARTIL RAIO X', 'QUARTIL RAIOX', 'QUARTIL']);
        var strQRaioX = (valQRaioX !== undefined && valQRaioX !== null && String(valQRaioX).trim() !== '') ? String(valQRaioX).trim() : '-';
        var rawCidades = getValueSafe(dataAtual, ['CIDADES', 'CIDADES DE ATUAÇÃO', 'CIDADES ATUAÇÃO']) || getValueSafe(dataAtual, ['CIDADE']);
        var listEl = document.getElementById('colabCidadesList');
        
        if (listEl) {
          listEl.innerHTML = '';
          if (rawCidades && String(rawCidades).trim() !== '' && String(rawCidades).trim() !== '-') {
            var listaCidades = String(rawCidades).split(/[,;]+/);
            listaCidades.forEach(function(cid) {
              var nomeCid = cid.trim();
              if (nomeCid !== '') {
                var chip = document.createElement('span');
                chip.className = 'city-chip';
                chip.innerHTML = '<i class="fa-solid fa-location-dot"></i> ' + nomeCid.toUpperCase();
                listEl.appendChild(chip);
              }
            });
          } else {
            listEl.innerHTML = '<span class="city-chip">-</span>';
          }
        }

        renderHistoricoQuartilProducao(selectedNome);
        renderHistoricoQuartilRaioX(selectedNome);

        var filteredData = getFilteredData(false).filter(function(d) {
          return limparTexto(getValueSafe(d, ['NOME'])) === nomeSelecionadoNormalizado;
        });
        updateKpisForCurrentContext(filteredData, selectedNome);
      }


      function renderHistoricoQuartil(nomeSelecionado, campoQuartil, headId, bodyId, tituloVazio) {
        var head = document.getElementById(headId);
        var body = document.getElementById(bodyId);
        if (!head || !body) return;

        head.innerHTML = '';
        body.innerHTML = '';

        var nomeNorm = valorFiltroNormalizado(nomeSelecionado || '');
        if (!nomeNorm) {
          var thVazio = document.createElement('th');
          thVazio.colSpan = 12;
          thVazio.textContent = 'SELECIONE UM COLABORADOR';
          head.appendChild(thVazio);
          var tdVazio = document.createElement('td');
          tdVazio.colSpan = 12;
          tdVazio.className = 'qneutral';
          tdVazio.textContent = '—';
          body.appendChild(tdVazio);
          return;
        }

        var mesesMap = {};
        (Array.isArray(allData) ? allData : []).forEach(function(reg) {
          var nomeReg = valorFiltroNormalizado(getValueSafe(reg, ['NOME']));
          if (nomeReg !== nomeNorm) return;
          var mes = formatarMes(getValueSafe(reg, ['MÊS']));
          if (!mes) return;
          var q = getValueSafe(reg, campoQuartil);
          if (!mesesMap[mes] || (String(mesesMap[mes]).trim() === '-' && q)) mesesMap[mes] = q;
        });

        var mesesOrdenados = Object.keys(mesesMap).sort(function(a, b) {
          function chave(m) {
            var partes = String(m).split('/');
            var nomes = ['janeiro','fevereiro','março','abril','maio','junho','julho','agosto','setembro','outubro','novembro','dezembro'];
            var idx = nomes.indexOf(String(partes[0]).toLowerCase());
            return (parseInt(partes[1], 10) || 0) * 12 + idx;
          }
          return chave(a) - chave(b);
        }).slice(-12);

        if (!mesesOrdenados.length) {
          var thSem = document.createElement('th');
          thSem.colSpan = 12;
          thSem.textContent = tituloVazio || 'SEM HISTÓRICO';
          head.appendChild(thSem);
          var tdSem = document.createElement('td');
          tdSem.colSpan = 12;
          tdSem.className = 'qneutral';
          tdSem.textContent = '—';
          body.appendChild(tdSem);
          return;
        }

        var abreviacoes = {janeiro:'JAN', fevereiro:'FEV', março:'MAR', abril:'ABR', maio:'MAI', junho:'JUN', julho:'JUL', agosto:'AGO', setembro:'SET', outubro:'OUT', novembro:'NOV', dezembro:'DEZ'};
        while (mesesOrdenados.length < 12) mesesOrdenados.unshift(null);

        mesesOrdenados.forEach(function(mes) {
          var th = document.createElement('th');
          if (mes) {
            var partes = mes.split('/');
            th.textContent = (abreviacoes[String(partes[0]).toLowerCase()] || partes[0]).toUpperCase() + '/' + String(partes[1]).slice(-2);
          } else {
            th.textContent = '—';
          }
          head.appendChild(th);
        });

        mesesOrdenados.forEach(function(mes) {
          var td = document.createElement('td');
          if (!mes) {
            td.className = 'qneutral';
            td.textContent = '—';
          } else {
            var raw = mesesMap[mes];
            var texto = raw !== undefined && raw !== null && String(raw).trim() !== '' ? String(raw).trim() : '—';
            var clean = limparTexto(texto);
            if (clean.indexOf('1') !== -1) td.className = 'q1';
            else if (clean.indexOf('2') !== -1) td.className = 'q2';
            else if (clean.indexOf('3') !== -1) td.className = 'q3';
            else if (clean.indexOf('4') !== -1) td.className = 'q4';
            else td.className = 'qneutral';
            if (texto !== '—' && !/QUARTIL/i.test(texto)) texto = texto.replace(/^([1-4])(?:º|°)?/i, '$1º QUARTIL');
            td.textContent = texto.toUpperCase();
          }
          body.appendChild(td);
        });
      }

      function renderHistoricoQuartilProducao(nomeSelecionado) {
        renderHistoricoQuartil(
          nomeSelecionado,
          ['QUARTIL PRODUÇÃO', 'QUARTIL PRODUCAO', 'QUARTIL PROD'],
          'historicoQuartilProducaoHead',
          'historicoQuartilProducaoBody',
          'SEM HISTÓRICO DE QUARTIL DE PRODUÇÃO'
        );
      }

      function renderHistoricoQuartilRaioX(nomeSelecionado) {
        renderHistoricoQuartil(
          nomeSelecionado,
          ['QUARTIL RAIO X', 'QUARTIL RAIOX', 'QUARTIL'],
          'historicoQuartilRaioXHead',
          'historicoQuartilRaioXBody',
          'SEM HISTÓRICO DE QUARTIL RAIO X'
        );
      }

      function resetCollaboratorCard() {
        if (selects['filterNome']) selects['filterNome'].clear(true);
        
        var imgEl = document.getElementById('colabFoto');
        if (imgEl) {
          imgEl.src = "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='150' height='150'><rect width='100%' height='100%' fill='%23f4511e'/><text x='50%' y='50%' fill='%23ffffff' font-size='16' text-anchor='middle' dy='.3em'>FOTO</text></svg>";
        }

        document.getElementById('colabNomeAbreviado').textContent = 'Selecione um Colaborador';
        document.getElementById('colabMatricula').textContent = '-';
        document.getElementById('colabCidade').textContent = '-';
        document.getElementById('colabTempoEmpresa').textContent = '-';
        document.getElementById('colabEquipes').textContent = '-';
        document.getElementById('colabSetor').textContent = '-';
        document.getElementById('colabRanking').textContent = '-';
        document.getElementById('colabCertificacao').textContent = '-';
        document.getElementById('colabCidadesList').innerHTML = '<span class="city-chip">-</span>';
        renderHistoricoQuartilProducao(null);
        renderHistoricoQuartilRaioX(null);
        
        updateKpisForCurrentContext(getFilteredData(false), null);
      }

      function mostrarTooltipGap(el) {
        var box = el ? el.querySelector('.th-tooltip-box') : null;
        if (!box) return;
        var r = el.getBoundingClientRect();
        box.style.left = Math.max(8, Math.min(window.innerWidth - 326, r.left + (r.width / 2) - 155)) + 'px';
        var top = r.top - 10;
        box.style.top = Math.max(8, top - box.offsetHeight) + 'px';
        box.style.opacity = '1';
        box.style.visibility = 'visible';
      }
      function ocultarTooltipGap(el) {
        var box = el ? el.querySelector('.th-tooltip-box') : null;
        if (!box) return;
        box.style.opacity = '0';
        box.style.visibility = 'hidden';
      }

      function abrirModalPontoCabeca() { document.getElementById('modalPontoCabeca').style.display = 'flex'; renderizarConteudoModal(); }
      function fecharModalPontoCabeca() { document.getElementById('modalPontoCabeca').style.display = 'none'; }
      function abrirModalTam() { document.getElementById('modalTam').style.display = 'flex'; renderizarConteudoModalTam(); }
      function fecharModalTam() { document.getElementById('modalTam').style.display = 'none'; }
      function abrirModalAbsCliente() { document.getElementById('modalAbsCliente').style.display = 'flex'; renderizarConteudoModalAbs(); }
      function fecharModalAbsCliente() { document.getElementById('modalAbsCliente').style.display = 'none'; }
      function abrirModalTamKml() { document.getElementById('modalTamKml').style.display = 'flex'; renderizarConteudoModalKml(); }
      function fecharModalTamKml() { document.getElementById('modalTamKml').style.display = 'none'; }
      function abrirModalAcidentes() { document.getElementById('modalAcidentes').style.display = 'flex'; renderizarConteudoModalAcidentes(); }
      function fecharModalAcidentes() { document.getElementById('modalAcidentes').style.display = 'none'; }
      function abrirModalAderenciaInspecao() { document.getElementById('modalAderenciaInspecao').style.display = 'flex'; renderizarConteudoModalInspecao(); }
      function fecharModalAderenciaInspecao() { document.getElementById('modalAderenciaInspecao').style.display = 'none'; }
      function abrirModalTratarPonto() { document.getElementById('modalTratarPonto').style.display = 'flex'; renderizarConteudoModalTratarPonto(); }
      function fecharModalTratarPonto() { document.getElementById('modalTratarPonto').style.display = 'none'; }
      function alternarGrupoBhHe(grupo, botao) {
        var config = { pontos: '.bhhe-detail-pontos', horaextra: '.bhhe-detail-horaextra' };
        var classe = config[grupo];
        if (!classe) return;
        var modal = document.getElementById('modalBhHe');
        if (!modal) return;
        var aberto = botao.getAttribute('aria-expanded') === 'true';
        var novoAberto = !aberto;
        botao.setAttribute('aria-expanded', novoAberto ? 'true' : 'false');
        var icon = botao.querySelector('i');
        if (icon) icon.className = novoAberto ? 'fa-solid fa-chevron-left' : 'fa-solid fa-chevron-right';
        modal.querySelectorAll(classe).forEach(function(el) {
          el.classList.toggle('sla-detail-hidden', !novoAberto);
        });
      }

      function resetarGruposBhHe() {
        var modal = document.getElementById('modalBhHe');
        if (!modal) return;
        modal.querySelectorAll('.sla-group-toggle').forEach(function(btn) {
          btn.setAttribute('aria-expanded', 'false');
          var icon = btn.querySelector('i');
          if (icon) icon.className = 'fa-solid fa-chevron-right';
        });
        modal.querySelectorAll('.bhhe-detail-pontos, .bhhe-detail-horaextra').forEach(function(el) {
          el.classList.add('sla-detail-hidden');
        });
      }

      function abrirModalBhHe() { document.getElementById('modalBhHe').style.display = 'flex'; resetarGruposBhHe(); renderizarConteudoModalBhHe(); }
      function fecharModalBhHe() { document.getElementById('modalBhHe').style.display = 'none'; }
      function abrirModalGpsPonto() { document.getElementById('modalGpsPonto').style.display = 'flex'; renderizarConteudoModalGpsPonto(); }
      function fecharModalGpsPonto() { document.getElementById('modalGpsPonto').style.display = 'none'; }
      function abrirModalRecorrencia() { document.getElementById('modalRecorrencia').style.display = 'flex'; renderizarConteudoModalRecorrencia(); }
      function fecharModalRecorrencia() { document.getElementById('modalRecorrencia').style.display = 'none'; }
      function abrirModalConectorInst() { document.getElementById('modalConectorInst').style.display = 'flex'; renderizarConteudoModalConectorInst(); }
      function fecharModalConectorInst() { document.getElementById('modalConectorInst').style.display = 'none'; }
      function abrirModalConectorRep() { document.getElementById('modalConectorRep').style.display = 'flex'; renderizarConteudoModalConectorRep(); }
      function fecharModalConectorRep() { document.getElementById('modalConectorRep').style.display = 'none'; }
      function abrirModalDropIat() { document.getElementById('modalDropIat').style.display = 'flex'; renderizarConteudoModalDropIat(); }
      function fecharModalDropIat() { document.getElementById('modalDropIat').style.display = 'none'; }
      function abrirModalIndiceVisitas() { document.getElementById('modalIndiceVisitas').style.display = 'flex'; renderizarConteudoModalIndiceVisitas(); }
      function fecharModalIndiceVisitas() { document.getElementById('modalIndiceVisitas').style.display = 'none'; }
      function abrirModalSlaIat() { document.getElementById('modalSlaIat').style.display = 'flex'; renderizarConteudoModalSlaIat(); }
      function fecharModalSlaIat() { document.getElementById('modalSlaIat').style.display = 'none'; }
      function abrirModalSlaIat48() { document.getElementById('modalSlaIat48').style.display = 'flex'; renderizarConteudoModalSlaIat48(); }
      function fecharModalSlaIat48() { document.getElementById('modalSlaIat48').style.display = 'none'; }
      function abrirModalSlaIat72() { document.getElementById('modalSlaIat72').style.display = 'flex'; renderizarConteudoModalSlaIat72(); }
      function fecharModalSlaIat72() { document.getElementById('modalSlaIat72').style.display = 'none'; }
      function abrirModalChurnRate() { document.getElementById('modalChurnRate').style.display = 'flex'; renderizarConteudoModalChurnRate(); }
      function fecharModalChurnRate() { document.getElementById('modalChurnRate').style.display = 'none'; }
      function abrirModalIsrPastor() { document.getElementById('modalIsrPastor').style.display = 'flex'; renderizarConteudoModalIsrPastor(); }
      function fecharModalIsrPastor() { document.getElementById('modalIsrPastor').style.display = 'none'; }
      function abrirModalInstEfet() { document.getElementById('modalInstEfet').style.display = 'flex'; renderizarConteudoModalInstEfet(); }
      function fecharModalInstEfet() { document.getElementById('modalInstEfet').style.display = 'none'; }
      function abrirModalSafraFtth() { document.getElementById('modalSafraFtth').style.display='flex'; renderizarConteudoModalSafraFtth(); }
      function fecharModalSafraFtth(e) { if (!e || e.target===e.currentTarget || e.target.closest('.modal-close-btn')) document.getElementById('modalSafraFtth').style.display='none'; }
      function abrirModalSafraFwa() { document.getElementById('modalSafraFwa').style.display='flex'; renderizarConteudoModalSafraFwa(); }
      function fecharModalSafraFwa(e) { if (!e || e.target===e.currentTarget || e.target.closest('.modal-close-btn')) document.getElementById('modalSafraFwa').style.display='none'; }
      function abrirModalSafra() { document.getElementById('modalSafra').style.display = 'flex'; renderizarConteudoModalSafra(); }
      function fecharModalSafra() { document.getElementById('modalSafra').style.display = 'none'; }
      function abrirModalIndiceRetencao() { document.getElementById('modalIndiceRetencao').style.display = 'flex'; renderizarConteudoModalIndiceRetencao(); }
      function fecharModalIndiceRetencao() { document.getElementById('modalIndiceRetencao').style.display = 'none'; }
      function abrirModalCertificacao() { document.getElementById('modalCertificacao').style.display = 'flex'; renderizarConteudoModalCertificacao(); }
      function fecharModalCertificacao() { document.getElementById('modalCertificacao').style.display = 'none'; }
      function abrirModalCertificacaoCidades() { document.getElementById('modalCertificacaoCidades').style.display = 'flex'; renderizarConteudoModalCertificacaoCidades(); }
      function fecharModalCertificacaoCidades() { document.getElementById('modalCertificacaoCidades').style.display = 'none'; }
      function abrirModalRegularizacao15m() { document.getElementById('modalRegularizacao15m').style.display = 'flex'; renderizarConteudoModalRegularizacao15m(); }
      function fecharModalRegularizacao15m() { document.getElementById('modalRegularizacao15m').style.display = 'none'; }
      function abrirModalEquipes4Quartil() { document.getElementById('modalEquipes4Quartil').style.display = 'flex'; renderizarConteudoModalEquipes4Quartil(); }
      function fecharModalEquipes4Quartil() { 
        var el = document.getElementById('modalEquipes4Quartil');
        if (el) { el.style.display = 'none'; }
      }

      function abrirModalAtestado12m() {
        var el = document.getElementById('modalAtestado12m');
        if (el) { el.style.display = 'flex'; }
        renderizarConteudoModalAtestado12m();
      }
      
      function fecharModalAtestado12m(e) {
        if (!e || e.target === e.currentTarget || e.target.classList.contains('modal-close-btn') || e.target.closest('.modal-close-btn')) {
          var el = document.getElementById('modalAtestado12m');
          if (el) { el.style.display = 'none'; }
        }
      }

      function abrirModalImprodutivaIat() {
        var el = document.getElementById('modalImprodutivaIat');
        if (el) { el.style.display = 'flex'; }
        renderizarConteudoModalImprodutivaIat();
      }

      function fecharModalImprodutivaIat(e) {
        if (!e || e.target === e.currentTarget || e.target.classList.contains('modal-close-btn') || e.target.closest('.modal-close-btn')) {
          var el = document.getElementById('modalImprodutivaIat');
          if (el) { el.style.display = 'none'; }
        }
      }

      function abrirModalBacklogReparo() {
        var el = document.getElementById('modalBacklogReparo');
        if (el) { el.style.display = 'flex'; }
        renderizarConteudoModalBacklogReparo();
      }

      function fecharModalBacklogReparo(e) {
        if (!e || e.target === e.currentTarget || e.target.classList.contains('modal-close-btn') || e.target.closest('.modal-close-btn')) {
          var el = document.getElementById('modalBacklogReparo');
          if (el) { el.style.display = 'none'; }
        }
      }

      function abrirModalBacklogInstalacao() {
        var el = document.getElementById('modalBacklogInstalacao');
        if (el) { el.style.display = 'flex'; }
        renderizarConteudoModalBacklogInstalacao();
      }

      function fecharModalBacklogInstalacao(e) {
        if (!e || e.target === e.currentTarget || e.target.classList.contains('modal-close-btn') || e.target.closest('.modal-close-btn')) {
          var el = document.getElementById('modalBacklogInstalacao');
          if (el) { el.style.display = 'none'; }
        }
      }

      function abrirModalBacklogIat() {
        var el = document.getElementById('modalBacklogIat');
        if (el) { el.style.display = 'flex'; }
        renderizarConteudoModalBacklogIat();
      }

      function fecharModalBacklogIat(e) {
        if (!e || e.target === e.currentTarget || e.target.classList.contains('modal-close-btn') || e.target.closest('.modal-close-btn')) {
          var el = document.getElementById('modalBacklogIat');
          if (el) { el.style.display = 'none'; }
        }
      }

      function calcularPontoCabecaConsolidado(data) {
        var totalPontos = 0;
        var totalEquipes = 0;
        var diasUteis = 0;

        (data || []).forEach(function(d) {
          totalPontos += getNumeroLimpo(getValueSafe(d, ['PONTOS', 'PONTUACAO', 'PONTUAÇÃO']));
          totalEquipes += getNumeroLimpo(getValueSafe(d, ['EQUIPE', 'EQUIPES']));

          // DIAS ÚTEIS é um valor do período, não deve ser somado linha a linha.
          if (diasUteis <= 0) {
            var diasLinha = getNumeroLimpo(getValueSafe(d, ['DIAS', 'DIA', 'DIAS UTEIS', 'DIAS ÚTEIS']));
            if (diasLinha > 0) diasUteis = diasLinha;
          }
        });

        return {
          totalPontos: totalPontos,
          totalEquipes: totalEquipes,
          diasUteis: diasUteis,
          valor: totalPontos > 0 && totalEquipes > 0 && diasUteis > 0
            ? (totalPontos / totalEquipes / diasUteis)
            : 0
        };
      }

      function fitHistoricData(labels, dados) {
        if (!labels || !dados || labels.length === 0) return { labels: [], dados: [] };
        var firstNonZeroIndex = -1;
        for (var i = 0; i < dados.length; i++) {
          if (dados[i] !== undefined && dados[i] !== null && dados[i] > 0) {
            firstNonZeroIndex = i;
            break;
          }
        }
        if (firstNonZeroIndex === -1) {
          return { labels: labels.slice(-1), dados: dados.slice(-1) };
        }
        return {
          labels: labels.slice(firstNonZeroIndex),
          dados: dados.slice(firstNonZeroIndex)
        };
      }

      // Histórico especial: para ATESTADOS, TRATAR PONTO e ACIDENTES,
      // zero é um valor válido e deve aparecer. Mês realmente em branco
      // continua sendo omitido.
      function fitHistoricDataComZeroValido(labels, dados, mesesComDado) {
        if (!labels || !dados || labels.length === 0) return { labels: [], dados: [] };
        var labelsFiltrados = [], dadosFiltrados = [];
        for (var i = 0; i < labels.length; i++) {
          if (mesesComDado && mesesComDado[i]) {
            labelsFiltrados.push(labels[i]);
            dadosFiltrados.push(dados[i]);
          }
        }
        return { labels: labelsFiltrados, dados: dadosFiltrados };
      }

      function valorHistoricoExiste(valor) {
        if (valor === undefined || valor === null) return false;
        var texto = String(valor).trim();
        return texto !== '' && texto !== '-';
      }

            function fitHistoricDataComZeros(labels, dados) {
        if (!labels || !dados || labels.length === 0) return { labels: [], dados: [] };
        var firstValidIndex = -1;
        for (var i = 0; i < dados.length; i++) {
          if (dados[i] !== undefined && dados[i] !== null && dados[i] !== '') {
            firstValidIndex = i;
            break;
          }
        }
        if (firstValidIndex === -1) return { labels: [], dados: [] };
        return {
          labels: labels.slice(firstValidIndex),
          dados: dados.slice(firstValidIndex)
        };
      }

      // No Modo TV, o filtro de colaborador fica vazio para permitir a rotação.
      // Ao abrir um analítico, porém, o contexto deve ser somente do colaborador
      // que está sendo exibido naquele instante. Fora do Modo TV, mantém o filtro normal.
      function obterNomeSelecionadoParaAnalitico() {
        var nomeSelecionado = selects['filterNome'] ? selects['filterNome'].getValue() : [];
        var lista = Array.isArray(nomeSelecionado) ? nomeSelecionado.slice() : (nomeSelecionado ? [nomeSelecionado] : []);
        if (document.body.classList.contains('tv-mode') && lista.length === 0 && tvApresentacaoNomeAtual) {
          lista = [tvApresentacaoNomeAtual];
        }
        return lista;
      }

      function renderizarConteudoModal() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricos = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; });
          var calcHistorico = calcularPontoCabecaConsolidado(subset);
          valoresHistoricos.push(Number(calcHistorico.valor.toFixed(2)));
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var calcVigente = calcularPontoCabecaConsolidado(subsetVigente);
        var valVigente = calcVigente.valor, totalPontos = calcVigente.totalPontos, totalEquipes = calcVigente.totalEquipes, totalDias = calcVigente.diasUteis;
        var box = document.getElementById('modalHighlightBox'), valEl = document.getElementById('modalValAtual'), diffEl = document.getElementById('modalValDiff'), msgEl = document.getElementById('modalValMsg');
        valEl.textContent = valVigente.toFixed(2).replace('.', ',');
        var diff = valVigente - metaIdeal; diffEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(2).replace('.', ',') + ' vs ideal';
        if (valVigente >= metaIdeal) { box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; msgEl.textContent = 'O valor atingiu a meta desejada.'; }
        else { box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; msgEl.textContent = 'Atenção! Ficou abaixo da meta ideal (' + metaIdeal + ').'; }
        document.getElementById('calcPontos').textContent = totalPontos > 0 ? totalPontos.toFixed(0) : '-';
        document.getElementById('calcEquipes').textContent = totalEquipes > 0 ? totalEquipes.toFixed(0) : '-';
        document.getElementById('calcDias').textContent = totalDias > 0 ? totalDias.toFixed(0) : '-';
        var tbody = document.getElementById('tabelaAnaliticaBody'); tbody.innerHTML = '';
        var dadosTabela = nomeColab ? pccData.filter(function(linhaPPC) { return limparTexto(getValueSafe(linhaPPC, ['NOME'])) === limparTexto(nomeColab); }) : pccData;
        if (!dadosTabela || dadosTabela.length === 0) {
          tbody.innerHTML = '<tr><td colspan="9" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro encontrado.</td></tr>';
        } else {
          dadosTabela.sort(function(a, b) { return getNumeroLimpo(getValueSafe(b, ['PPC'])) - getNumeroLimpo(getValueSafe(a, ['PPC'])); });
          dadosTabela.forEach(function(l) {
            var tr = document.createElement('tr');
            var ppcBruto = getValueSafe(l, ['PPC']), numPPC = getNumeroLimpo(ppcBruto);
            [getValueSafe(l, ['FUNCIONARIO', 'FUNCIONÁRIO']), getValueSafe(l, ['SITUAÇÃO', 'SITUACAO']), formatTableInteger(getValueSafe(l, ['DIAS DE EMPRESA'])), formatTableNumber(getValueSafe(l, ['PONTUAÇÃO'])), formatTableNumber(ppcBruto), formatTableInteger(getValueSafe(l, ['DIAS AUSENTE'])), formatTableNumber(getValueSafe(l, ['GAP ATUAL'])), formatTableNumber(getValueSafe(l, ['GAP DIA'])), formatTableNumber(getValueSafe(l, ['PONTOS / DIA (META)']))].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = val || '-';
              if (index === 4 && val !== '-') td.className = numPPC >= metaIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        resetSortIcons();
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricos);
        renderizarGraficoHistoricoGenerico('graficoEvolucao', 'meuGrafico', 'Ponto por Cabeça', fitted.labels, fitted.dados, metaIdeal);
      }

      function renderizarConteudoModalTam() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosTam = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; });
          var agregadoTamMes = calcularTamRaioXAgregado(subset);
          valoresHistoricosTam.push(Number(agregadoTamMes.percentual.toFixed(2)));
        });
        var dadosRaioXTamAtual = getFilteredData(false);
        if (nomeColab) dadosRaioXTamAtual = dadosRaioXTamAtual.filter(function(d) { return limparTexto(getValueSafe(d, ['NOME'])) === limparTexto(nomeColab); });
        var agregadoTamAtual = calcularTamRaioXAgregado(dadosRaioXTamAtual);
        var totalEquipesBateram = agregadoTamAtual.bateu;
        var totalEquipesGeral = agregadoTamAtual.equipes;

        var tbody = document.getElementById('tabelaTamBody'); tbody.innerHTML = '';
        var dadosTabelaTam = nomeColab ? tamData.filter(function(linhaTam) { return limparTexto(getValueSafe(linhaTam, ['NOME', 'SUPERVISOR'])) === limparTexto(nomeColab); }) : tamData;
        if (!dadosTabelaTam || dadosTabelaTam.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro encontrado.</td></tr>';
        } else {
          dadosTabelaTam.sort(function(a, b) { return getNumeroLimpo(getTamPontosValue(b)) - getNumeroLimpo(getTamPontosValue(a)); });
          dadosTabelaTam.forEach(function(l) {
            var colIndice = String(getValueSafe(l, ['ÍNDICE', 'INDICE', 'STATUS']) || '-').trim().toUpperCase();
            var tr = document.createElement('tr');
            [getValueSafe(l, ['FUNCIONARIO', 'FUNCIONÁRIO']), formatTableNumber(getTamPontosValue(l)), formatTableNumber(getValueSafe(l, ['META PROJEÇÃO', 'PROJEÇÃO'])), colIndice].forEach(function(val, idx) {
              var td = document.createElement('td'); td.textContent = val || '-';
              if (idx === 3 && val !== '-') td.className = (val.indexOf('BATEU') !== -1 && val.indexOf('NÃO') === -1) ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var valVigenteTam = totalEquipesGeral > 0 ? ((totalEquipesBateram / totalEquipesGeral) * 100) : 0;
        var box = document.getElementById('modalHighlightBoxTam'), valEl = document.getElementById('modalValAtualTam'), diffEl = document.getElementById('modalValDiffTam'), msgEl = document.getElementById('modalValMsgTam');
        valEl.textContent = valVigenteTam.toFixed(2).replace('.', ',') + '%';
        var diff = valVigenteTam - metaTamIdeal; diffEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(2).replace('.', ',') + '% vs ideal';
        box.className = valVigenteTam >= metaTamIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteTam >= metaTamIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        msgEl.textContent = valVigenteTam >= metaTamIdeal ? 'A meta do TAM foi atingida com sucesso.' : 'Atenção! O TAM ficou abaixo do indicador ideal (' + metaTamIdeal + '%).';
        document.getElementById('calcEquipesBateram').textContent = totalEquipesBateram;
        document.getElementById('calcTotalEquipesTam').textContent = totalEquipesGeral;
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosTam);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoTam', 'meuGraficoTam', 'TAM (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalAbs() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosAbs = [];
        mesesUnicos.forEach(function(mesStr) {
          var agregadoMes = calcularTamAbsAgregado(dataSemMes, mesStr);
          valoresHistoricosAbs.push(Number(agregadoMes.percentual.toFixed(2)));
        });
        var tbody = document.getElementById('tabelaAbsBody'); tbody.innerHTML = '';
        var dataAtualAbs = getFilteredData(false);
        if (nomeColab) dataAtualAbs = dataAtualAbs.filter(function(d) { return d['NOME'] === nomeColab; });
        var agregadoAtualAbs = calcularTamAbsAgregado(dataAtualAbs);
        var dadosTabelaAbst = nomeColab ? abstData.filter(function(linha) {
          var candidatosNome = [
            getValueSafe(linha, ['SUPERVISOR']),
            getValueSafe(linha, ['NOME']),
            getValueSafe(linha, ['COLABORADOR']),
            getValueSafe(linha, ['FUNCIONARIO', 'FUNCIONÁRIO'])
          ];
          return candidatosNome.some(function(valorNome) { return comparaNomeFlexivel(valorNome, nomeColab); });
        }) : abstData.slice();
        var totalCondutoresBateram = 0, totalCondutoresGeral = 0;
        if (!dadosTabelaAbst || dadosTabelaAbst.length === 0) {
          tbody.innerHTML = '<tr><td colspan="8" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum condutor encontrado na base TAM ABASTECIMENTO/CLIENTE.</td></tr>';
        } else {
          dadosTabelaAbst.forEach(function(linha) {
            if (getNumeroLimpo(getValueSafe(linha, ['BATEU', 'BATEU META'])) === 1) totalCondutoresBateram++;
            var totTecVal = getNumeroLimpo(getValueSafe(linha, ['TOTAL TÉC.', 'TOTAL TEC.'])); totalCondutoresGeral += (totTecVal > 0 ? totTecVal : 1);
            var colIndice = String(getValueSafe(linha, ['ÍNDICE', 'INDICE', 'STATUS']) || '-').trim().toUpperCase();
            var tr = document.createElement('tr');
            var rawAbastecimento = getValueSafe(linha, ['R$', 'ABASTECIMENTO', 'ABASTECIMENTO (R$)', 'VALOR ABASTECIMENTO']);
            var rawClientes = getValueSafe(linha, ['CLIENTES', 'TOTAL CLIENTES']);
            var rawDeslocamentoLongo = getValueSafe(linha, [
              'ABST. DESLOCAMENTO LONGO',
              'ABST DESLOCAMENTO LONGO',
              'ABASTECIMENTO DESLOCAMENTO LONGO',
              'ABASTECIMENTO DESLOCAMENTO LONGO (R$)',
              'ABASTECIMENTO DESLOCAMENTO',
              'DESLOCAMENTO LONGO',
              'R$ DESLOCAMENTO LONGO',
              'ABST LONGO',
              'DESLOCAMENTO'
            ]);
            var rawAbstXCliente = getValueSafe(linha, [
              'ABST X CLIENTE',
              'ABST X CLIENTE (R$)',
              'ABASTECIMENTO X CLIENTE',
              'ABASTECIMENTO X CLIENTE (R$)',
              'ABASTECIMENTO/CLIENTE',
              'ABST X CLIENTES',
              'ABST/CLIENTE'
            ]);
            // Caso a coluna calculada ABST X CLIENTE não venha da fonte, calcula R$ abastecimento / clientes.
            if ((rawAbstXCliente === undefined || rawAbstXCliente === null || String(rawAbstXCliente).trim() === '') && getNumeroLimpo(rawClientes) > 0) {
              rawAbstXCliente = getNumeroLimpo(rawAbastecimento) / getNumeroLimpo(rawClientes);
            }

            [
              getValueSafe(linha, ['COLABORADOR', 'FUNCIONARIO', 'FUNCIONÁRIO', 'NOME']),
              getValueSafe(linha, ['PLACA']),
              getValueSafe(linha, ['TIPO VEÍCULO', 'TIPO VEICULO']),
              formatCurrency(rawAbastecimento),
              formatTableInteger(rawClientes),
              formatCurrency(rawDeslocamentoLongo),
              formatCurrency(rawAbstXCliente),
              colIndice
            ].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 7 && val !== '-') td.className = (val.indexOf('BATEU') !== -1 && val.indexOf('NÃO') === -1) ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        // O destaque e a composição usam os mesmos dados da página RAIO X.
        var valVigenteAbs = agregadoAtualAbs.percentual;
        var totalMetaAbsAtual = agregadoAtualAbs.condutoresMeta;
        var totalCondutoresAbsAtual = agregadoAtualAbs.totalCondutores;
        var box = document.getElementById('modalHighlightBoxAbs'), valEl = document.getElementById('modalValAtualAbs'), diffEl = document.getElementById('modalValDiffAbs'), msgEl = document.getElementById('modalValMsgAbs');
        valEl.textContent = valVigenteAbs.toFixed(2).replace('.', ',') + '%';
        var diff = valVigenteAbs - metaAbsIdeal; diffEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(2).replace('.', ',') + '% vs ideal';
        box.className = valVigenteAbs >= metaAbsIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteAbs >= metaAbsIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        msgEl.textContent = valVigenteAbs >= metaAbsIdeal ? 'A meta do TAM ABASTECIMENTO/CLIENTE foi atingida com sucesso.' : 'Atenção! Ficou abaixo da meta ideal (' + metaAbsIdeal + '%).';
        document.getElementById('calcCondutoresBateram').textContent = totalMetaAbsAtual;
        document.getElementById('calcTotalCondutores').textContent = totalCondutoresAbsAtual;
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosAbs);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoAbs', 'meuGraficoAbs', 'TAM ABS/CLIENTE (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalKml() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosKml = [];
        mesesUnicos.forEach(function(mesStr) {
          var agregadoKmlMes = calcularTamKmlRaioXAgregadoMes(dataSemMes, mesStr);
          valoresHistoricosKml.push(Number(agregadoKmlMes.percentual.toFixed(2)));
        });
        var tbody = document.getElementById('tabelaKmlBody'); tbody.innerHTML = '';
        var dadosTabelaKml = nomeColab ? kmlData.filter(function(linhaKml) { return limparTexto(getValueSafe(linhaKml, ['SUPERVISOR', 'NOME'])) === limparTexto(nomeColab); }) : kmlData;
        dadosTabelaKml = dadosTabelaKml.filter(function(linhaKml) { var idxVal = getValueSafe(linhaKml, ['ÍNDICE', 'INDICE', 'STATUS']); return idxVal !== undefined && idxVal !== null && String(idxVal).trim() !== ''; });
        var totalCondutoresBateram = 0, totalCondutoresGeral = 0;
        if (!dadosTabelaKml || dadosTabelaKml.length === 0) {
          tbody.innerHTML = '<tr><td colspan="8" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum condutor encontrado na base TAM KM/L.</td></tr>';
        } else {
          dadosTabelaKml.forEach(function(linhaKml) {
            if (getNumeroLimpo(getValueSafe(linhaKml, ['BATEU', 'BATEU META'])) === 1) totalCondutoresBateram++;
            var totTecVal = getNumeroLimpo(getValueSafe(linhaKml, ['TOTAL TÉC.', 'TOTAL TEC.'])); totalCondutoresGeral += (totTecVal > 0 ? totTecVal : 1);
            var colIndice = String(getValueSafe(linhaKml, ['ÍNDICE', 'INDICE', 'STATUS']) || '-').trim().toUpperCase();
            var tr = document.createElement('tr');
            [getValueSafe(linhaKml, ['FUNCIONARIO', 'COLABORADOR', 'NOME']), getValueSafe(linhaKml, ['PLACA']), getValueSafe(linhaKml, ['TIPO VEÍCULO', 'TIPO VEICULO']), formatTableInteger(getValueSafe(linhaKml, ['QUANTIDADE DE ABASTECIMENTO'])), formatTableNumber(getValueSafe(linhaKml, ['KM'])), formatTableNumber(getValueSafe(linhaKml, ['LITROS'])), formatTableNumber(getValueSafe(linhaKml, ['KM/L', 'KML'])), colIndice].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 7 && val !== '-') td.className = (val.indexOf('BATEU') !== -1 && val.indexOf('NÃO') === -1) ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        // A composição do modal usa a mesma fonte e a mesma fórmula do card:
        // RAIO X -> BATEU KM/L ÷ TOTAL KM/L × 100.
        var dadosRaioXKmlAtual = getFilteredData(false);
        if (nomeColab) {
          dadosRaioXKmlAtual = dadosRaioXKmlAtual.filter(function(d) {
            return limparTexto(getValueSafe(d, ['NOME'])) === limparTexto(nomeColab);
          });
        }
        var agregadoKmlAtual = calcularTamKmlRaioXAgregado(dadosRaioXKmlAtual);
        totalCondutoresBateram = agregadoKmlAtual.bateu;
        totalCondutoresGeral = agregadoKmlAtual.total;
        var valVigenteKml = agregadoKmlAtual.percentual;
        var box = document.getElementById('modalHighlightBoxKml'), valEl = document.getElementById('modalValAtualKml'), diffEl = document.getElementById('modalValDiffKml'), msgEl = document.getElementById('modalValMsgKml');
        valEl.textContent = valVigenteKml.toFixed(2).replace('.', ',') + '%';
        var diff = valVigenteKml - metaKmlIdeal; diffEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(2).replace('.', ',') + '% vs ideal';
        box.className = valVigenteKml >= metaKmlIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteKml >= metaKmlIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        msgEl.textContent = valVigenteKml >= metaKmlIdeal ? 'A meta do TAM KM/L foi atingida com sucesso.' : 'Atenção! Ficou abaixo da meta ideal (' + metaKmlIdeal + '%).';
        document.getElementById('calcCondutoresBateramKml').textContent = totalCondutoresBateram;
        document.getElementById('calcTotalCondutoresKml').textContent = totalCondutoresGeral;
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosKml);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoKml', 'meuGraficoKml', 'TAM KM/L (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalAcidentes() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosAcidentes = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), somaAcd = 0, temDadoAcidente = false;
          subset.forEach(function(d) {
            var rawAcidente = getValueSafe(d, ['ACIDENTES', 'ACIDENTE']);
            if (rawAcidente !== undefined && rawAcidente !== null && String(rawAcidente).trim() !== '') temDadoAcidente = true;
            somaAcd += getNumeroLimpo(rawAcidente);
          });
          valoresHistoricosAcidentes.push(temDadoAcidente ? somaAcd : null);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var valVigenteAcd = 0; subsetVigente.forEach(function(d) { valVigenteAcd += getNumeroLimpo(getValueSafe(d, ['ACIDENTES', 'ACIDENTE'])); });
        var box = document.getElementById('modalHighlightBoxAcidentes'), valEl = document.getElementById('modalValAtualAcidentes'), diffEl = document.getElementById('modalValDiffAcidentes'), msgEl = document.getElementById('modalValMsgAcidentes');
        valEl.textContent = valVigenteAcd.toString();
        box.className = valVigenteAcd === 0 ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteAcd === 0 ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteAcd === 0 ? 'DENTRO DA META' : '+' + valVigenteAcd + ' ACIDENTE(S)';
        msgEl.textContent = valVigenteAcd === 0 ? 'Nenhum acidente registrado no período.' : 'Atenção! Houve registro de acidente com CAT no período.';
        document.getElementById('calcTotalAcidentes').textContent = valVigenteAcd.toString();
        var tbody = document.getElementById('tabelaAcidentesBody'); tbody.innerHTML = '';
        var dadosTabelaAcidentes = nomeColab ? acidentesData.filter(function(l) { return limparTexto(getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO', 'NOME'])) === limparTexto(nomeColab); }) : acidentesData;
        if (!dadosTabelaAcidentes || dadosTabelaAcidentes.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de acidente encontrado.</td></tr>';
        } else {
          dadosTabelaAcidentes.forEach(function(l) {
            var tr = document.createElement('tr');
            [getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO', 'NOME']), getValueSafe(l, ['DATA']), getValueSafe(l, ['CAUSA', 'MOTIVO']), formatTableInteger(getValueSafe(l, ['DIAS DE AFASTAMENTO']))].forEach(function(val) {
              var td = document.createElement('td'); td.textContent = val || '-'; tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricDataComZeros(mesesUnicos, valoresHistoricosAcidentes);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoAcidentes', 'meuGraficoAcidentes', 'Qtd. Acidentes', fitted.labels, fitted.dados, 5, undefined, true);
      }

      function renderizarConteudoModalInspecao() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosInspecao = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          var okMes = 0, totalMes = 0;
          subset.forEach(function(d) {
            okMes += getNumeroLimpo(getValueSafe(d, ['INSPEÇÕES OK', 'INSPECOES OK']));
            totalMes += getNumeroLimpo(getValueSafe(d, ['TOTAL INSPEÇÕES', 'TOTAL INSPECOES']));
          });
          valoresHistoricosInspecao.push(totalMes > 0 ? Number(((okMes / totalMes) * 100).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalInspecaoOk = 0, totalInspecaoGeral = 0;
        subsetVigente.forEach(function(d) {
          totalInspecaoOk += getNumeroLimpo(getValueSafe(d, ['INSPEÇÕES OK', 'INSPECOES OK']));
          totalInspecaoGeral += getNumeroLimpo(getValueSafe(d, ['TOTAL INSPEÇÕES', 'TOTAL INSPECOES']));
        });
        var valVigenteInspecao = totalInspecaoGeral > 0 ? ((totalInspecaoOk / totalInspecaoGeral) * 100) : 0;
        var box = document.getElementById('modalHighlightBoxInspecao'), valEl = document.getElementById('modalValAtualInspecao'), diffEl = document.getElementById('modalValDiffInspecao'), msgEl = document.getElementById('modalValMsgInspecao');
        valEl.textContent = valVigenteInspecao.toFixed(2).replace('.', ',') + '%';
        var diff = valVigenteInspecao - metaInspecaoIdeal; diffEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(2).replace('.', ',') + '% vs ideal';
        box.className = valVigenteInspecao >= metaInspecaoIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteInspecao >= metaInspecaoIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        msgEl.textContent = valVigenteInspecao >= metaInspecaoIdeal ? 'A meta de inspeção foi atingida com sucesso.' : 'Atenção! Ficou abaixo da meta ideal (' + metaInspecaoIdeal + '%).';
        document.getElementById('calcInspecaoOk').textContent = totalInspecaoOk.toString();
        document.getElementById('calcTotalInspecao').textContent = totalInspecaoGeral.toString();
        var tbody = document.getElementById('tabelaInspecaoBody'); tbody.innerHTML = '';
        var dadosTabelaInspecao = inspecaoData;
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaInspecao = inspecaoData.filter(function(l) {
            var sup = limparTexto(getValueSafe(l, ['SUPERVISOR', 'GESTOR', 'COORDENADOR', 'NOME'])), func = limparTexto(getValueSafe(l, ['FUNCIONARIO', 'COLABORADOR']));
            return (sup && (sup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(sup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaInspecao || dadosTabelaInspecao.length === 0) {
          tbody.innerHTML = '<tr><td colspan="5" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de inspeção encontrado.</td></tr>';
        } else {
          dadosTabelaInspecao.sort(function(a, b) { return getNumeroLimpo(getValueSafe(b, ['% ADERÊNCIA INSPEÇÃO', 'ADERENCIA'])) - getNumeroLimpo(getValueSafe(a, ['% ADERÊNCIA INSPEÇÃO', 'ADERENCIA'])); });
          dadosTabelaInspecao.forEach(function(l) {
            var tr = document.createElement('tr');
            var rawAderencia = getValueSafe(l, ['% ADERÊNCIA INSPEÇÃO', 'ADERÊNCIA INSPEÇÃO', 'ADERENCIA INSPECAO']);
            var numAderencia = getNumeroLimpo(rawAderencia); if (numAderencia <= 1 && numAderencia > 0) numAderencia *= 100;
            [getValueSafe(l, ['FUNCIONARIO', 'COLABORADOR']), formatTableInteger(getValueSafe(l, ['TOTAL'])), formatTableInteger(getValueSafe(l, ['BATEU'])), formatTableInteger(getValueSafe(l, ['NÃO BATEU'])), numAderencia.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = val || '-';
              if (index === 4 && val !== '-') td.className = numAderencia >= metaInspecaoIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosInspecao);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoInspecao', 'meuGraficoInspecao', 'Aderência (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalTratarPonto() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        var mesesSelecionados = selects['filterMes'] ? selects['filterMes'].getValue() : [];
        var mesAtualFiltro = Array.isArray(mesesSelecionados) && mesesSelecionados.length === 1 ? mesesSelecionados[0] : (typeof mesesSelecionados === 'string' ? mesesSelecionados : null);
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var empToSup = {}; pccData.forEach(function(d) { var sup = limparTexto(getValueSafe(d, ['NOME', 'SUPERVISOR'])), emp = limparTexto(getValueSafe(d, ['FUNCIONARIO', 'COLABORADOR'])); if (sup && emp) empToSup[emp] = sup; });
        var valoresHistoricosTratarPonto = [];
        mesesUnicos.forEach(function(mesStr) {
          var subsetDataMes = dataSemMes.filter(function(l) { return formatarMes(getValueSafe(l, ['MÊS'])) === mesStr; });
          var pontosTratarMes = 0, equipesMes = 0, temDadoTratarPonto = false;
          subsetDataMes.forEach(function(d) {
            var rawTratarPonto = getRaioXTratarPonto(d);
            if (rawTratarPonto !== undefined && rawTratarPonto !== null && String(rawTratarPonto).trim() !== '') temDadoTratarPonto = true;
            pontosTratarMes += getNumeroLimpo(rawTratarPonto);
            equipesMes += getNumeroLimpo(getRaioXCampoExato(d, 'EQUIPES'));
          });
          valoresHistoricosTratarPonto.push(!temDadoTratarPonto ? null : (equipesMes > 0 ? Number(((pontosTratarMes / equipesMes) * 100).toFixed(2)) : 0));
        });
        var subsetVigente = getFilteredData(false);
        if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return limparTexto(getValueSafe(d, ['NOME'])) === limparTexto(nomeColab); });
        var totalPontosTratarVigentes = 0, totalFuncionariosVigentes = 0;
        subsetVigente.forEach(function(d) {
          totalPontosTratarVigentes += getNumeroLimpo(getRaioXTratarPonto(d));
          totalFuncionariosVigentes += getNumeroLimpo(getRaioXCampoExato(d, 'EQUIPES'));
        });
        var valVigenteTratarPonto = totalFuncionariosVigentes > 0 ? ((totalPontosTratarVigentes / totalFuncionariosVigentes) * 100) : 0;
        var pontosATratarVigentes = 0, sNome = limparTexto(nomeColab);
        if (tratarPtData.length > 0) {
          tratarPtData.forEach(function(l) {
            var f = limparTexto(getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO'])), m = formatarMes(getValueSafe(l, ['DATA'])), crit = String(getValueSafe(l, ['CRITÉRIO', 'CRITERIO']) || '').toUpperCase();
            var supInRow = limparTexto(getValueSafe(l, ['SUPERVISOR', 'GESTOR'])), empInRow = limparTexto(getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO']));
            var isMyTeam = !nomeColab ? true : ((supInRow && (supInRow.indexOf(sNome) !== -1 || sNome.indexOf(supInRow) !== -1)) || (empInRow && empToSup[empInRow] && empToSup[empInRow].indexOf(sNome) !== -1));
            if ((mesAtualFiltro ? (m === mesAtualFiltro) : true) && isMyTeam && crit.indexOf('TRATAR PONTO') !== -1) pontosATratarVigentes++;
          });
        }
        var box = document.getElementById('modalHighlightBoxTratarPonto'), valEl = document.getElementById('modalValAtualTratarPonto'), diffEl = document.getElementById('modalValDiffTratarPonto'), msgEl = document.getElementById('modalValMsgTratarPonto');
        valEl.textContent = valVigenteTratarPonto.toFixed(2).replace('.', ',') + '%';
        box.className = valVigenteTratarPonto === 0 ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteTratarPonto === 0 ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteTratarPonto === 0 ? 'DENTRO DA META' : '+' + valVigenteTratarPonto.toFixed(2).replace('.', ',') + '% acima da meta';
        msgEl.textContent = valVigenteTratarPonto === 0 ? 'Nenhum ponto pendente de tratamento no período.' : 'Atenção! Existem registros que precisam de tratamento.';
        document.getElementById('calcPontosTratar').textContent = totalPontosTratarVigentes.toString();
        document.getElementById('calcTotalFuncTratar').textContent = totalFuncionariosVigentes.toString();
        var tbody = document.getElementById('tabelaTratarPontoBody'); tbody.innerHTML = '';
        var dadosTabelaPonto = tratarPtData;
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaPonto = tratarPtData.filter(function(l) {
            var sup = limparTexto(getValueSafe(l, ['SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO'])), m = formatarMes(getValueSafe(l, ['DATA']));
            return (mesAtualFiltro ? (m === mesAtualFiltro) : true) && ((sup && (sup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(sup) !== -1)) || (func && empToSup[func] && empToSup[func].indexOf(colabAlvo) !== -1));
          });
        } else {
          dadosTabelaPonto = tratarPtData.filter(function(l) { return mesAtualFiltro ? (formatarMes(getValueSafe(l, ['DATA'])) === mesAtualFiltro) : true; });
        }
        if (!dadosTabelaPonto || dadosTabelaPonto.length === 0) {
          tbody.innerHTML = '<tr><td colspan="11" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhuma pendência de ponto encontrada.</td></tr>';
        } else {
          dadosTabelaPonto.forEach(function(l) {
            var tr = document.createElement('tr');
            [getValueSafe(l, ['COLABORADOR', 'FUNCIONARIO']), formatarDataBR(getValueSafe(l, ['DATA'])), getValueSafe(l, ['BATIDA 1']), getValueSafe(l, ['BATIDA 2']), getValueSafe(l, ['BATIDA 3']), getValueSafe(l, ['BATIDA 4']), getValueSafe(l, ['BATIDA 5']), getValueSafe(l, ['BATIDA 6']), getValueSafe(l, ['BATIDA 7']), getValueSafe(l, ['BATIDA 8']), getValueSafe(l, ['CRITÉRIO', 'CRITERIO'])].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 10 && val && String(val).toUpperCase().indexOf('TRATAR PONTO') !== -1) td.className = 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricDataComZeros(mesesUnicos, valoresHistoricosTratarPonto);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoTratarPonto', 'meuGraficoTratarPonto', 'Tratar Ponto (%)', fitted.labels, fitted.dados, 10, undefined, true);
      }

      function renderizarConteudoModalBhHe() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosBhHe = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), somaBhHe = 0, somaPt = 0;
          subset.forEach(function(d) {
            somaBhHe += duracaoParaSegundos(getValueSafe(d, ['BH + HE', 'BH+HE']));
            somaPt += getNumeroLimpo(getValueSafe(d, ['PT TOTAL', 'PONTUAÇÃO TOTAL', 'PONTOS TOTAL', 'PONTUAÇÃO']));
          });
          valoresHistoricosBhHe.push(somaPt > 0 ? Number((somaBhHe / somaPt).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var valVigenteBhHe = 0, totalPontosExec = 0, totalBhHeSegundos = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalPontosExec += getNumeroLimpo(getValueSafe(d, ['PT TOTAL', 'PONTUAÇÃO TOTAL', 'PONTOS TOTAL', 'PONTUAÇÃO']));
            totalBhHeSegundos += duracaoParaSegundos(getValueSafe(d, ['BH + HE', 'BH+HE']));
          });
          valVigenteBhHe = totalPontosExec > 0 ? (totalBhHeSegundos / totalPontosExec) : 0;
        }
        var box = document.getElementById('modalHighlightBoxBhHe'), valEl = document.getElementById('modalValAtualBhHe'), diffEl = document.getElementById('modalValDiffBhHe'), msgEl = document.getElementById('modalValMsgBhHe');
        valEl.textContent = segundosParaHHMMSS(valVigenteBhHe);
        box.className = valVigenteBhHe <= metaBhHeIdealSegundos ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteBhHe <= metaBhHeIdealSegundos ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteBhHe <= metaBhHeIdealSegundos ? 'DENTRO DA META' : 'ACIMA DA META';
        msgEl.textContent = valVigenteBhHe <= metaBhHeIdealSegundos ? 'Índice dentro da meta estipulada (≤ 00:04:00).' : 'Atenção! O índice ultrapassou o limite ideal de 00:04:00.';
        document.getElementById('calcTotalBhHeTempo').textContent = segundosParaHHMMSS(totalBhHeSegundos);
        document.getElementById('calcTotalPontosBhHe').textContent = formatTableInteger(totalPontosExec);
        var tbody = document.getElementById('tabelaBhHeBody'); tbody.innerHTML = '';
        var dadosTabelaBhHe = bhHeData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaBhHe = bhHeData.filter(function(l) {
            var nomeSup = limparTexto(getValueSafe(l, ['NOME', 'SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(l, ['CHAVE', 'COLABORADOR', 'FUNCIONARIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaBhHe || dadosTabelaBhHe.length === 0) {
          tbody.innerHTML = '<tr><td colspan="13" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de BH + HE encontrado.</td></tr>';
        } else {
          dadosTabelaBhHe.forEach(function(l) {
            var tr = document.createElement('tr');
            var rawBhHePt = getValueSafe(l, ['BH + HE / PONTUAÇÃO', 'BH+HE / PONTUAÇÃO', 'BH+HE / PT', 'BH + HE PT']);
            var colBhHePt = formatarHoraEspecial(rawBhHePt);
            var segs = getNumeroLimpo(rawBhHePt);
            if (colBhHePt && colBhHePt.indexOf(':') !== -1) {
              var p = colBhHePt.split(':'); segs = (parseInt(p[0]||0,10)*3600) + (parseInt(p[1]||0,10)*60) + parseInt(p[2]||0,10);
            }

            var pontosMes = [
              getNumeroLimpo(getValueSafe(l, ['PT AGOSTO'])),
              getNumeroLimpo(getValueSafe(l, ['PT SETEMBRO'])),
              getNumeroLimpo(getValueSafe(l, ['PT OUTUBRO'])),
              getNumeroLimpo(getValueSafe(l, ['PT NOVEMBRO']))
            ];
            var pontosTotal = pontosMes.reduce(function(acc, val) { return acc + (isFinite(val) ? val : 0); }, 0);

            var horasExtraMes = [
              getValueSafe(l, ['HORA EXTRA AGOSTO']),
              getValueSafe(l, ['HORA EXTRA SETEMBRO']),
              getValueSafe(l, ['HORA EXTRA OUTUBRO']),
              getValueSafe(l, ['HORA EXTRA NOVEMBRO'])
            ];
            var horasExtraTotalSeg = horasExtraMes.reduce(function(acc, val) { return acc + duracaoParaSegundos(val); }, 0);

            var valores = [
              getValueSafe(l, ['CHAVE', 'COLABORADOR', 'NOME']),
              colBhHePt,
              formatarHoraEspecial(getValueSafe(l, ['BANCO DE HORAS'])),
              segundosParaHHMMSS(horasExtraTotalSeg),
              formatarHoraEspecial(horasExtraMes[0]),
              formatarHoraEspecial(horasExtraMes[1]),
              formatarHoraEspecial(horasExtraMes[2]),
              formatarHoraEspecial(horasExtraMes[3]),
              formatTableNumber(pontosTotal),
              formatTableNumber(pontosMes[0]),
              formatTableNumber(pontosMes[1]),
              formatTableNumber(pontosMes[2]),
              formatTableNumber(pontosMes[3])
            ];

            valores.forEach(function(val, idx) {
              var td = document.createElement('td');
              td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (idx >= 4 && idx <= 7) td.classList.add('bhhe-detail-td', 'bhhe-detail-horaextra', 'sla-detail-hidden');
              if (idx >= 9 && idx <= 12) td.classList.add('bhhe-detail-td', 'bhhe-detail-pontos', 'sla-detail-hidden');
              if (idx === 1 && val !== '-') td.className = segs <= metaBhHeIdealSegundos ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosBhHe);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoBhHe', 'meuGraficoBhHe', 'BH+HE/PT', fitted.labels, fitted.dados, 300, 'duracao');
      }

      function renderizarConteudoModalGpsPonto() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosGpsPonto = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), somaAlertas = 0, somaEquipes = 0;
          subset.forEach(function(d) {
            somaAlertas += getNumeroLimpo(getRaioXCampoExato(d, 'ALERTAS'));
            somaEquipes += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL EQUIPES'));
          });
          valoresHistoricosGpsPonto.push(somaEquipes > 0 ? Number((somaAlertas / somaEquipes).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalAlertas = 0, totalEquipesGps = 0, valVigenteGpsPonto = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalAlertas += getNumeroLimpo(getRaioXCampoExato(d, 'ALERTAS'));
            totalEquipesGps += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL EQUIPES'));
          });
          valVigenteGpsPonto = totalEquipesGps > 0 ? (totalAlertas / totalEquipesGps) : 0;
        }
        var box = document.getElementById('modalHighlightBoxGpsPonto'), valEl = document.getElementById('modalValAtualGpsPonto'), diffEl = document.getElementById('modalValDiffGpsPonto'), msgEl = document.getElementById('modalValMsgGpsPonto');
        valEl.textContent = formatTableNumber(valVigenteGpsPonto);
        box.className = valVigenteGpsPonto <= metaGpsPontoIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteGpsPonto <= metaGpsPontoIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteGpsPonto <= metaGpsPontoIdeal ? 'DENTRO DA META' : 'ACIMA DA META';
        msgEl.textContent = valVigenteGpsPonto <= metaGpsPontoIdeal ? 'Índice de alertas por equipe dentro da meta (≤ 15).' : 'Atenção! O índice de alertas por equipe ultrapassou o limite ideal de 15.';
        document.getElementById('calcTotalAlertasGps').textContent = formatTableInteger(totalAlertas);
        document.getElementById('calcTotalEquipesGps').textContent = formatTableInteger(totalEquipesGps);
        var tbody = document.getElementById('tabelaGpsPontoBody'); tbody.innerHTML = '';
        var dadosTabelaGpsPonto = gpsPontoData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaGpsPonto = gpsPontoData.filter(function(l) {
            var nomeSup = limparTexto(getValueSafe(l, ['NOME', 'SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(l, ['COLABORADOR', 'CHAVE', 'FUNCIONARIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaGpsPonto || dadosTabelaGpsPonto.length === 0) {
          tbody.innerHTML = '<tr><td colspan="2" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de alerta GPS encontrado.</td></tr>';
        } else {
          dadosTabelaGpsPonto.sort(function(a, b) {
            return getNumeroLimpo(getValueSafe(b, ['QUANTIDADE ALERTAS', 'ALERTAS'])) - getNumeroLimpo(getValueSafe(a, ['QUANTIDADE ALERTAS', 'ALERTAS']));
          });
          dadosTabelaGpsPonto.forEach(function(l) {
            var tr = document.createElement('tr');
            var rawA = getValueSafe(l, ['QUANTIDADE ALERTAS', 'ALERTAS']), numA = getNumeroLimpo(rawA);
            [getValueSafe(l, ['COLABORADOR', 'CHAVE', 'NOME']), formatTableInteger(rawA)].forEach(function(val, idx) {
              var td = document.createElement('td'); td.textContent = val || '-';
              if (idx === 1 && val !== '-') td.className = numA <= metaGpsPontoIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosGpsPonto);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoGpsPonto', 'meuGraficoGpsPonto', 'Alertas GPS', fitted.labels, fitted.dados, 20);
      }

      function renderizarConteudoModalRecorrencia() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosRecorrencia = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; });
          var totalServicosMes = 0, totalRecorrenciasMes = 0;
          subset.forEach(function(d) {
            totalServicosMes += getNumeroLimpo(getValueSafe(d, ['SERVIÇO', 'SERVICO', 'SERVIÇOS', 'SERVICOS', 'REPAROS EXECUTADOS']));
            totalRecorrenciasMes += getNumeroLimpo(getValueSafe(d, ['RECORRÊNCIAS', 'RECORRENCIAS', 'REVISITAS']));
          });
          valoresHistoricosRecorrencia.push(totalServicosMes > 0 ? Number(((totalRecorrenciasMes / totalServicosMes) * 100).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalServicos = 0, totalRecorrencias = 0, valVigenteRecorrencia = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalServicos += getNumeroLimpo(getValueSafe(d, ['SERVIÇO', 'SERVICO', 'SERVIÇOS', 'SERVICOS', 'REPAROS EXECUTADOS']));
            totalRecorrencias += getNumeroLimpo(getValueSafe(d, ['RECORRÊNCIAS', 'RECORRENCIAS', 'REVISITAS']));
          });
          valVigenteRecorrencia = totalServicos > 0 ? ((totalRecorrencias / totalServicos) * 100) : 0;
        }
        var box = document.getElementById('modalHighlightBoxRecorrencia'), valEl = document.getElementById('modalValAtualRecorrencia'), diffEl = document.getElementById('modalValDiffRecorrencia'), msgEl = document.getElementById('modalValMsgRecorrencia');
        valEl.textContent = valVigenteRecorrencia.toFixed(2).replace('.', ',') + '%';
        if (valVigenteRecorrencia <= metaRecorrenciaIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Taxa de recorrência IAT dentro da meta estipulada (≤ 8%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ACIMA DA META'; msgEl.textContent = 'Atenção! A taxa de recorrência IAT ultrapassou o limite ideal de 8%.';
        }
        document.getElementById('calcTotalServicosRec').textContent = formatTableInteger(totalServicos);
        document.getElementById('calcTotalRecorrencias').textContent = formatTableInteger(totalRecorrencias);
        var tbody = document.getElementById('tabelaRecorrenciaBody'); tbody.innerHTML = '';
        var dadosTabelaRecorrencia = recorrenciaData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaRecorrencia = recorrenciaData.filter(function(linha) {
            var nomeSup = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COORDENADOR'])), func = limparTexto(getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'FUNCIONARIO', 'FUNCIONÁRIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaRecorrencia || dadosTabelaRecorrencia.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de recorrência IAT encontrado.</td></tr>';
        } else {
          dadosTabelaRecorrencia.sort(function(a, b) {
            var servA = getNumeroLimpo(getValueSafe(a, ['SERVIÇOS', 'SERVICOS'])), recA = getNumeroLimpo(getValueSafe(a, ['RECORRÊNCIAS', 'RECORRENCIAS']));
            var servB = getNumeroLimpo(getValueSafe(b, ['SERVIÇOS', 'SERVICOS'])), recB = getNumeroLimpo(getValueSafe(b, ['RECORRÊNCIAS', 'RECORRENCIAS']));
            /* A coluna RECORRÊNCIA IAT da base não participa do cálculo desta tabela.
               O percentual deve sempre ser calculado pela própria linha: RECORRÊNCIAS ÷ SERVIÇOS × 100. */
            var numIatA = servA > 0 ? (recA / servA) * 100 : 0;
            var numIatB = servB > 0 ? (recB / servB) * 100 : 0;
            return numIatB - numIatA;
          });
          dadosTabelaRecorrencia.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colColaborador = getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'NOME', 'FUNCIONARIO']);
            var rawServicos = getValueSafe(linha, ['SERVIÇOS', 'SERVICOS']), numServicos = getNumeroLimpo(rawServicos);
            var rawRecorrencias = getValueSafe(linha, ['RECORRÊNCIAS', 'RECORRENCIAS']), numRecorrencias = getNumeroLimpo(rawRecorrencias);
            /* O percentual da linha deve ser derivado dos dois números exibidos na tabela. */
            var numIat = numServicos > 0 ? ((numRecorrencias / numServicos) * 100) : 0;
            [colColaborador, formatTableInteger(rawServicos), formatTableInteger(rawRecorrencias), numIat.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numIat <= metaRecorrenciaIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosRecorrencia);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoRecorrencia', 'meuGraficoRecorrencia', 'Recorrência IAT (%)', fitted.labels, fitted.dados, 15);
      }

      function renderizarConteudoModalConectorInst() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosConectorInst = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['CONECTOR INSTALAÇÃO', 'CONECTOR INSTALACAO', 'CONECTOR INST']);
            if (r !== undefined && r !== null && r !== '') { soma += getNumeroLimpo(r); count++; }
          });
          valoresHistoricosConectorInst.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalUsoConector = 0, totalInstalacoesOS = 0, valVigenteConectorInst = 0;
        if (subsetVigente.length > 0) {
          var sCI = 0, cCI = 0;
          subsetVigente.forEach(function(d) {
            var rVal = getValueSafe(d, ['CONECTOR INSTALAÇÃO', 'CONECTOR INSTALACAO', 'CONECTOR INST']);
            if(rVal !== undefined && rVal !== null && rVal !== '') { sCI += getNumeroLimpo(rVal); cCI++; }
            totalUsoConector += getNumeroLimpo(getValueSafe(d, ['USO CONECTOR INST', 'CONECTOR SC INST', 'CONECTORES INST']));
            totalInstalacoesOS += getNumeroLimpo(getValueSafe(d, ['TOTAL INST', 'INSTALACAO', 'INSTALAÇÃO']));
          });
          valVigenteConectorInst = cCI > 0 ? (sCI / cCI) : 0;
        }
        var box = document.getElementById('modalHighlightBoxConectorInst'), valEl = document.getElementById('modalValAtualConectorInst'), diffEl = document.getElementById('modalValDiffConectorInst'), msgEl = document.getElementById('modalValMsgConectorInst');
        valEl.textContent = valVigenteConectorInst.toFixed(2).replace('.', ',');
        box.className = valVigenteConectorInst <= metaConectorInstIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteConectorInst <= metaConectorInstIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteConectorInst <= metaConectorInstIdeal ? 'DENTRO DA META' : 'ACIMA DA META';
        msgEl.textContent = valVigenteConectorInst <= metaConectorInstIdeal ? 'Média de uso de conectores dentro da meta (≤ 2).' : 'Atenção! A média de uso de conectores ultrapassou o limite ideal de 2.';
        document.getElementById('calcUsoConectorInst').textContent = formatTableInteger(totalUsoConector);
        document.getElementById('calcTotalInstalacoes').textContent = formatTableInteger(totalInstalacoesOS);
        var tbody = document.getElementById('tabelaConectorInstBody'); tbody.innerHTML = '';
        var dadosTabelaMaterial = materialData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaMaterial = materialData.filter(function(linha) {
            var nomeSup = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'FUNCIONARIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaMaterial || dadosTabelaMaterial.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de material/conector encontrado.</td></tr>';
        } else {
          dadosTabelaMaterial.sort(function(a, b) {
            var scA = getNumeroLimpo(getValueSafe(a, ['CONECTOR SC INST', 'USO CONECTOR INST'])), instA = getNumeroLimpo(getValueSafe(a, ['INSTALAÇÃO', 'INSTALACAO']));
            var scB = getNumeroLimpo(getValueSafe(b, ['CONECTOR SC INST', 'USO CONECTOR INST'])), instB = getNumeroLimpo(getValueSafe(b, ['INSTALAÇÃO', 'INSTALACAO']));
            return (instB > 0 ? (scB / instB) : 0) - (instA > 0 ? (scA / instA) : 0);
          });
          dadosTabelaMaterial.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colColaborador = getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'NOME', 'FUNCIONARIO']);
            var rawConectorSc = getValueSafe(linha, ['CONECTOR SC INST', 'USO CONECTOR INST']), numConectorSc = getNumeroLimpo(rawConectorSc);
            var rawInstalacao = getValueSafe(linha, ['INSTALAÇÃO', 'INSTALACAO']), numInstalacao = getNumeroLimpo(rawInstalacao);
            var numCalcInst = numInstalacao > 0 ? (numConectorSc / numInstalacao) : 0;
            [colColaborador, formatTableInteger(rawConectorSc), formatTableInteger(rawInstalacao), numCalcInst.toFixed(2).replace('.', ',')].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numCalcInst <= metaConectorInstIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosConectorInst);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoConectorInst', 'meuGraficoConectorInst', 'Conector Instalação', fitted.labels, fitted.dados, 5);
      }

      function renderizarConteudoModalConectorRep() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosConectorRep = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['CONECTOR REPARO', 'CONECTOR REPAROS', 'CONECTOR REP']);
            if (r !== undefined && r !== null && r !== '') { soma += getNumeroLimpo(r); count++; }
          });
          valoresHistoricosConectorRep.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalUsoConectorRep = 0, totalReparosOS = 0, valVigenteConectorRep = 0;
        if (subsetVigente.length > 0) {
          var sCR = 0, cCR = 0;
          subsetVigente.forEach(function(d) {
            var rVal = getValueSafe(d, ['CONECTOR REPARO', 'CONECTOR REPAROS', 'CONECTOR REP']);
            if(rVal !== undefined && rVal !== null && rVal !== '') { sCR += getNumeroLimpo(rVal); cCR++; }
            totalUsoConectorRep += getNumeroLimpo(getValueSafe(d, ['USO CONECTOR REP', 'CONECTOR SC REP.', 'CONECTOR SC REP']));
            totalReparosOS += getNumeroLimpo(getValueSafe(d, ['TOTAL REP', 'REPARO', 'REPAROS']));
          });
          valVigenteConectorRep = cCR > 0 ? (sCR / cCR) : 0;
        }
        var box = document.getElementById('modalHighlightBoxConectorRep'), valEl = document.getElementById('modalValAtualConectorRep'), diffEl = document.getElementById('modalValDiffConectorRep'), msgEl = document.getElementById('modalValMsgConectorRep');
        valEl.textContent = valVigenteConectorRep.toFixed(2).replace('.', ',');
        box.className = valVigenteConectorRep <= metaConectorRepIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteConectorRep <= metaConectorRepIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteConectorRep <= metaConectorRepIdeal ? 'DENTRO DA META' : 'ACIMA DA META';
        msgEl.textContent = valVigenteConectorRep <= metaConectorRepIdeal ? 'Média de uso de conectores em reparo dentro da meta (≤ 0,79).' : 'Atenção! A média de uso de conectores em reparo ultrapassou a meta de 0,79.';
        document.getElementById('calcUsoConectorRep').textContent = formatTableInteger(totalUsoConectorRep);
        document.getElementById('calcTotalReparos').textContent = formatTableInteger(totalReparosOS);
        var tbody = document.getElementById('tabelaConectorRepBody'); tbody.innerHTML = '';
        var dadosTabelaMaterial = materialData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaMaterial = materialData.filter(function(linha) {
            var nomeSup = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'FUNCIONARIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaMaterial || dadosTabelaMaterial.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de conector de reparo encontrado.</td></tr>';
        } else {
          dadosTabelaMaterial.sort(function(a, b) {
            var scA = getNumeroLimpo(getValueSafe(a, ['CONECTOR SC REP.', 'CONECTOR SC REP'])), repA = getNumeroLimpo(getValueSafe(a, ['REPARO', 'REPAROS']));
            var scB = getNumeroLimpo(getValueSafe(b, ['CONECTOR SC REP.', 'CONECTOR SC REP'])), repB = getNumeroLimpo(getValueSafe(b, ['REPARO', 'REPAROS']));
            return (repB > 0 ? (scB / repB) : 0) - (repA > 0 ? (scA / repA) : 0);
          });
          dadosTabelaMaterial.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colColaborador = getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'NOME', 'FUNCIONARIO']);
            var rawConectorScRep = getValueSafe(linha, ['CONECTOR SC REP.', 'CONECTOR SC REP']), numConectorScRep = getNumeroLimpo(rawConectorScRep);
            var rawReparo = getValueSafe(linha, ['REPARO', 'REPAROS']), numReparo = getNumeroLimpo(rawReparo);
            var numCalcRep = numReparo > 0 ? (numConectorScRep / numReparo) : 0;
            [colColaborador, formatTableInteger(rawConectorScRep), formatTableInteger(rawReparo), numCalcRep.toFixed(2).replace('.', ',')].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numCalcRep <= metaConectorRepIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosConectorRep);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoConectorRep', 'meuGraficoConectorRep', 'Conector Reparo', fitted.labels, fitted.dados, 2);
      }

      function renderizarConteudoModalDropIat() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosDropIat = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['USO DROP IAT', 'DROP IAT', 'DROP']);
            if (r !== undefined && r !== null && r !== '') { soma += getNumeroLimpo(r); count++; }
          });
          valoresHistoricosDropIat.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalCaboDrop = 0, totalOsDrop = 0, valVigenteDropIat = 0;
        if (subsetVigente.length > 0) {
          var sDI = 0, cDI = 0;
          subsetVigente.forEach(function(d) {
            var rVal = getValueSafe(d, ['USO DROP IAT', 'DROP IAT', 'DROP']);
            if(rVal !== undefined && rVal !== null && rVal !== '') { sDI += getNumeroLimpo(rVal); cDI++; }
            var caboInst = getNumeroLimpo(getValueSafe(d, ['USO CABO DROP INST', 'CABO DROP INST.']));
            var caboRep = getNumeroLimpo(getValueSafe(d, ['USO CABO DROP REP.', 'CABO DROP REP.']));
            totalCaboDrop += (caboInst + caboRep);
            var totInst = getNumeroLimpo(getValueSafe(d, ['TOTAL INST', 'INSTALACAO', 'INSTALAÇÃO']));
            var totRep = getNumeroLimpo(getValueSafe(d, ['TOTAL REP', 'REPARO', 'REPAROS']));
            totalOsDrop += (totInst + totRep);
          });
          valVigenteDropIat = cDI > 0 ? (sDI / cDI) : 0;
        }
        var box = document.getElementById('modalHighlightBoxDropIat'), valEl = document.getElementById('modalValAtualDropIat'), diffEl = document.getElementById('modalValDiffDropIat'), msgEl = document.getElementById('modalValMsgDropIat');
        valEl.textContent = valVigenteDropIat.toFixed(2).replace('.', ',');
        box.className = valVigenteDropIat <= metaDropIatIdeal ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diffEl.className = valVigenteDropIat <= metaDropIatIdeal ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diffEl.textContent = valVigenteDropIat <= metaDropIatIdeal ? 'DENTRO DA META' : 'ACIMA DA META';
        msgEl.textContent = valVigenteDropIat <= metaDropIatIdeal ? 'Média de uso de cabo Drop dentro da meta (≤ 80).' : 'Atenção! A média de uso de cabo Drop ultrapassou a meta de 80.';
        document.getElementById('calcTotalCaboDrop').textContent = formatTableInteger(totalCaboDrop);
        document.getElementById('calcTotalOsDrop').textContent = formatTableInteger(totalOsDrop);
        var tbody = document.getElementById('tabelaDropIatBody'); tbody.innerHTML = '';
        var dadosTabelaMaterial = materialData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaMaterial = materialData.filter(function(linha) {
            var nomeSup = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR'])), func = limparTexto(getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'FUNCIONARIO']));
            return (nomeSup && (nomeSup.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeSup) !== -1)) || (func && (func.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(func) !== -1));
          });
        }
        if (!dadosTabelaMaterial || dadosTabelaMaterial.length === 0) {
          tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de uso de cabo Drop encontrado.</td></tr>';
        } else {
          dadosTabelaMaterial.sort(function(a, b) {
            var caboInstA = getNumeroLimpo(getValueSafe(a, ['CABO DROP INST.', 'CABO DROP INST'])), caboRepA = getNumeroLimpo(getValueSafe(a, ['CABO DROP REP.', 'CABO DROP REP']));
            var instA = getNumeroLimpo(getValueSafe(a, ['INSTALAÇÃO', 'INSTALACAO'])), repA = getNumeroLimpo(getValueSafe(a, ['REPARO', 'REPAROS']));
            var caboInstB = getNumeroLimpo(getValueSafe(b, ['CABO DROP INST.', 'CABO DROP INST'])), caboRepB = getNumeroLimpo(getValueSafe(b, ['CABO DROP REP.', 'CABO DROP REP']));
            var instB = getNumeroLimpo(getValueSafe(b, ['INSTALAÇÃO', 'INSTALACAO'])), repB = getNumeroLimpo(getValueSafe(b, ['REPARO', 'REPAROS']));
            return ((instB + repB) > 0 ? ((caboInstB + caboRepB) / (instB + repB)) : 0) - ((instA + repA) > 0 ? ((caboInstA + caboRepA) / (instA + repA)) : 0);
          });
          dadosTabelaMaterial.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colColaborador = getValueSafe(linha, ['COLABORADOR', 'CHAVE', 'NOME', 'FUNCIONARIO']);
            var rawCaboInst = getValueSafe(linha, ['CABO DROP INST.', 'CABO DROP INST']), numCaboInst = getNumeroLimpo(rawCaboInst);
            var rawCaboRep = getValueSafe(linha, ['CABO DROP REP.', 'CABO DROP REP']), numCaboRep = getNumeroLimpo(rawCaboRep);
            var rawInstalacao = getValueSafe(linha, ['INSTALAÇÃO', 'INSTALACAO']), numInstalacao = getNumeroLimpo(rawInstalacao);
            var rawReparo = getValueSafe(linha, ['REPARO', 'REPAROS']), numReparo = getNumeroLimpo(rawReparo);
            var totalOs = numInstalacao + numReparo;
            var numCalcDropIat = totalOs > 0 ? ((numCaboInst + numCaboRep) / totalOs) : 0;
            [colColaborador, formatTableInteger(rawCaboInst), formatTableInteger(rawCaboRep), formatTableInteger(rawInstalacao), formatTableInteger(rawReparo), numCalcDropIat.toFixed(2).replace('.', ',')].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 5 && val !== '-') td.className = numCalcDropIat <= metaDropIatIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosDropIat);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoDropIat', 'meuGraficoDropIat', 'DROP IAT', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalIndiceVisitas() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosIndiceVisitas = [];
        mesesUnicos.forEach(function(mesStr) {
          var calcMes = calcularIndiceVisitasRaioXAgregadoMes(dataSemMes, mesStr);
          valoresHistoricosIndiceVisitas.push(Number(calcMes.percentual.toFixed(2)));
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var calcIndiceVigente = calcularIndiceVisitasRaioXAgregado(subsetVigente);
        var totalVisitasReparo = calcIndiceVigente.visitas;
        var totalBaseClientes = calcIndiceVigente.clientes;
        var valVigenteIndiceVisitas = calcIndiceVigente.percentual;
        var box = document.getElementById('modalHighlightBoxIndiceVisitas'), valEl = document.getElementById('modalValAtualIndiceVisitas'), diffEl = document.getElementById('modalValDiffIndiceVisitas'), msgEl = document.getElementById('modalValMsgIndiceVisitas');
        valEl.textContent = valVigenteIndiceVisitas.toFixed(2).replace('.', ',') + '%';
        if (valVigenteIndiceVisitas <= metaIndiceVisitasIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Índice de visitas dentro da meta estipulada (≤ 4%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ACIMA DA META'; msgEl.textContent = 'Atenção! O índice de visitas ultrapassou o limite ideal de 4%.';
        }
        document.getElementById('calcVisitasReparo').textContent = formatTableInteger(totalVisitasReparo);
        document.getElementById('calcBaseClientes').textContent = formatTableInteger(totalBaseClientes);
        var tbody = document.getElementById('tabelaIndiceVisitasBody'); tbody.innerHTML = '';
        var dadosTabelaCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaCidades = dadosTabelaCidades.filter(function(linha) {
            var nomeResp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return nomeResp && (nomeResp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeResp) !== -1);
          });
        }
        if (!dadosTabelaCidades || dadosTabelaCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de índice de visitas encontrado.</td></tr>';
        } else {
          dadosTabelaCidades.sort(function(a, b) {
            var visA = getNumeroLimpo(getValueSafe(a, ['VISITAS DE REPARO'])), baseA = getNumeroLimpo(getValueSafe(a, ['BASE DE CLIENTES']));
            var rawPctA = getValueSafe(a, ['% DE VISITAS', 'ÍNDICE DE VISITAS']), numPctA = (rawPctA !== undefined && rawPctA !== null && rawPctA !== '') ? getNumeroLimpo(rawPctA) : (baseA > 0 ? (visA / baseA) * 100 : 0);
            if (numPctA <= 1 && numPctA > 0) numPctA *= 100;
            var visB = getNumeroLimpo(getValueSafe(b, ['VISITAS DE REPARO'])), baseB = getNumeroLimpo(getValueSafe(b, ['BASE DE CLIENTES']));
            var rawPctB = getValueSafe(b, ['% DE VISITAS', 'ÍNDICE DE VISITAS']), numPctB = (rawPctB !== undefined && rawPctB !== null && rawPctB !== '') ? getNumeroLimpo(rawPctB) : (baseB > 0 ? (visB / baseB) * 100 : 0);
            if (numPctB <= 1 && numPctB > 0) numPctB *= 100;
            return numPctB - numPctA;
          });
          dadosTabelaCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var rawVisitasReparo = getValueSafe(linha, ['VISITAS DE REPARO']), numVisitasReparo = getNumeroLimpo(rawVisitasReparo);
            var rawBaseClientes = getValueSafe(linha, ['BASE DE CLIENTES']), numBaseClientes = getNumeroLimpo(rawBaseClientes);
            var rawPctVisitas = getValueSafe(linha, ['% DE VISITAS', 'ÍNDICE DE VISITAS']);
            var numPctVisitas = (rawPctVisitas !== undefined && rawPctVisitas !== null && rawPctVisitas !== '') ? getNumeroLimpo(rawPctVisitas) : (numBaseClientes > 0 ? ((numVisitasReparo / numBaseClientes) * 100) : 0);
            if (numPctVisitas <= 1 && numPctVisitas > 0) numPctVisitas *= 100;
            [colCidade, formatTableInteger(rawVisitasReparo), formatTableInteger(rawBaseClientes), numPctVisitas.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numPctVisitas <= metaIndiceVisitasIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosIndiceVisitas);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoIndiceVisitas', 'meuGraficoIndiceVisitas', 'Índice de Visitas (%)', fitted.labels, fitted.dados, 10);
      }

      function calcularSlaIatHoras(data, horas) {
        var inst48 = horas === 48 ? 'INSTALAÇÃO 48 HORAS' : 'INSTALAÇÃO 72 HORAS';
        var rep48 = horas === 48 ? 'REPARO 48 HORAS' : 'REPARO 72 HORAS';
        var totalInst = 0, totalRep = 0, instHoras = 0, repHoras = 0;
        (data || []).forEach(function(d) {
          totalInst += getNumeroLimpo(getValueSafe(d, ['TOTAL INSTALAÇÃO']));
          totalRep += getNumeroLimpo(getValueSafe(d, ['TOTAL REPARO']));
          instHoras += getNumeroLimpo(getValueSafe(d, [inst48]));
          repHoras += getNumeroLimpo(getValueSafe(d, [rep48]));
        });
        var totalBase = totalRep + totalInst;
        var valor = totalBase > 0 ? ((instHoras + repHoras) / totalBase) * 100 : 0;
        return { totalInstalacao: totalInst, totalReparo: totalRep, instalacaoHoras: instHoras, reparoHoras: repHoras, valor: valor };
      }

      function obterDadosTabelaSlaIatHoras(horas) {
        var dados = (indicadoresCidadesData || []).slice();
        var nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dados = dados.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return resp && (resp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(resp) !== -1);
          });
        }
        return dados;
      }

      function aplicarStatusSlaIatHoras(prefix, valor, horas) {
        var box = document.getElementById('modalHighlightBox' + prefix);
        var diff = document.getElementById('modalValDiff' + prefix);
        var msg = document.getElementById('modalValMsg' + prefix);
        var ok = horas === 48 ? valor >= metaSlaIat48Ideal : valor >= metaSlaIat72CorIdeal;
        box.className = ok ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diff.className = ok ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diff.textContent = ok ? 'DENTRO DA META' : 'ABAIXO DA META';
        msg.textContent = horas === 48
          ? (ok ? 'Taxa de atendimento em até 48h dentro da meta (≥ 70%).' : 'Atenção! O SLA IAT 48 HORAS ficou abaixo da meta de 70%.')
          : (ok ? 'SLA IAT 72 HORAS dentro da meta de 90%.' : 'Atenção! O SLA IAT 72 HORAS ficou abaixo da meta de 90%.');
      }

      function alternarGrupoSlaIatHoras(grupo, botao, horas) {
        var modalId = horas === 48 ? 'modalSlaIat48' : 'modalSlaIat72';
        var classe = '.sla' + horas + '-detail-' + grupo;
        var modal = document.getElementById(modalId);
        if (!modal) return;
        var aberto = botao.getAttribute('aria-expanded') === 'true';
        var novoAberto = !aberto;
        botao.setAttribute('aria-expanded', novoAberto ? 'true' : 'false');
        var icon = botao.querySelector('i');
        if (icon) icon.className = novoAberto ? 'fa-solid fa-chevron-left' : 'fa-solid fa-chevron-right';
        modal.querySelectorAll(classe).forEach(function(el) {
          el.classList.toggle('sla-detail-hidden', !novoAberto);
        });
      }

      function resetarGruposSlaIatHoras(horas) {
        var modalId = horas === 48 ? 'modalSlaIat48' : 'modalSlaIat72';
        var modal = document.getElementById(modalId);
        if (!modal) return;
        modal.querySelectorAll('.sla-group-toggle').forEach(function(btn) {
          btn.setAttribute('aria-expanded', 'false');
          var icon = btn.querySelector('i');
          if (icon) icon.className = 'fa-solid fa-chevron-right';
        });
        modal.querySelectorAll('.sla' + horas + '-detail-instalacao, .sla' + horas + '-detail-reparo').forEach(function(el) {
          el.classList.add('sla-detail-hidden');
        });
      }

      function obterDadosTabelaSlaIatHoras(horas) {
        var dados = (indicadoresCidadesData || []).slice();
        var nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dados = dados.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return resp && (resp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(resp) !== -1);
          });
        }
        return dados;
      }

      function aplicarStatusSlaIatHoras(prefix, valor, horas) {
        var box = document.getElementById('modalHighlightBox' + prefix);
        var diff = document.getElementById('modalValDiff' + prefix);
        var msg = document.getElementById('modalValMsg' + prefix);
        var ok = horas === 48 ? valor >= metaSlaIat48Ideal : valor >= metaSlaIat72CorIdeal;
        box.className = ok ? 'modal-highlight-box box-success' : 'modal-highlight-box box-danger';
        diff.className = ok ? 'modal-box-diff diff-positive' : 'modal-box-diff diff-negative';
        diff.textContent = ok ? 'DENTRO DA META' : 'ABAIXO DA META';
        msg.textContent = horas === 48
          ? (ok ? 'Taxa de atendimento em até 48h dentro da meta (≥ 70%).' : 'Atenção! O SLA IAT 48 HORAS ficou abaixo da meta de 70%.')
          : (ok ? 'SLA IAT 72 HORAS dentro da meta de 90%.' : 'Atenção! O SLA IAT 72 HORAS ficou abaixo da meta de 90%.');
      }

      function renderizarTabelaSlaIatHoras(horas) {
        var tbody = document.getElementById('tabelaSlaIat' + horas + 'Body');
        if (!tbody) return;
        var dados = obterDadosTabelaSlaIatHoras(horas);
        tbody.innerHTML = '';
        resetarGruposSlaIatHoras(horas);
        if (!dados.length) {
          tbody.innerHTML = '<tr><td colspan="10" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de SLA IAT ' + horas + ' HORAS encontrado.</td></tr>';
          return;
        }
        var instKey = 'INSTALAÇÃO ' + horas + ' HORAS';
        var repKey = 'REPARO ' + horas + ' HORAS';
        var meta = horas === 48 ? metaSlaIat48Ideal : metaSlaIat72CorIdeal;
        dados.sort(function(a, b) {
          var instA = getNumeroLimpo(getValueSafe(a, [instKey])), repA = getNumeroLimpo(getValueSafe(a, [repKey]));
          var totInstA = getNumeroLimpo(getValueSafe(a, ['TOTAL INSTALAÇÃO'])), totRepA = getNumeroLimpo(getValueSafe(a, ['TOTAL REPARO']));
          var instB = getNumeroLimpo(getValueSafe(b, [instKey])), repB = getNumeroLimpo(getValueSafe(b, [repKey]));
          var totInstB = getNumeroLimpo(getValueSafe(b, ['TOTAL INSTALAÇÃO'])), totRepB = getNumeroLimpo(getValueSafe(b, ['TOTAL REPARO']));
          var baseA = totInstA + totRepA, baseB = totInstB + totRepB;
          var vA = baseA > 0 ? ((instA + repA) / baseA) * 100 : 0;
          var vB = baseB > 0 ? ((instB + repB) / baseB) * 100 : 0;
          return vB - vA;
        });
        dados.forEach(function(linha) {
          var tr = document.createElement('tr');
          var cidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
          var totalRep = getNumeroLimpo(getValueSafe(linha, ['TOTAL REPARO']));
          var repHoras = getNumeroLimpo(getValueSafe(linha, [repKey]));
          var totalInst = getNumeroLimpo(getValueSafe(linha, ['TOTAL INSTALAÇÃO']));
          var instHoras = getNumeroLimpo(getValueSafe(linha, [instKey]));
          var slaInst = calcularPercentualSlaDetalhe(instHoras, totalInst);
          var slaRep = calcularPercentualSlaDetalhe(repHoras, totalRep);
          var base = totalRep + totalInst;
          var valor = base > 0 ? ((instHoras + repHoras) / base) * 100 : 0;
          function addTd(value, classes) {
            var td = document.createElement('td');
            td.textContent = (value !== undefined && value !== null && value !== '') ? value : '-';
            if (classes) td.className = classes;
            tr.appendChild(td);
          }
          addTd(cidade);
          addTd(slaInst.toFixed(2).replace('.', ',') + '%', 'sla-summary-cell ' + (slaInst >= meta ? 'td-success' : 'td-danger'));
          addTd(formatTableInteger(totalInst), 'sla' + horas + '-detail-instalacao sla-detail-hidden');
          addTd(formatTableInteger(instHoras), 'sla' + horas + '-detail-instalacao sla-detail-hidden');
          addTd(slaInst.toFixed(2).replace('.', ',') + '%', 'sla' + horas + '-detail-instalacao sla-detail-hidden sla-percent-cell ' + (slaInst >= meta ? 'td-success' : 'td-danger'));
          addTd(slaRep.toFixed(2).replace('.', ',') + '%', 'sla-summary-cell ' + (slaRep >= meta ? 'td-success' : 'td-danger'));
          addTd(formatTableInteger(totalRep), 'sla' + horas + '-detail-reparo sla-detail-hidden');
          addTd(formatTableInteger(repHoras), 'sla' + horas + '-detail-reparo sla-detail-hidden');
          addTd(slaRep.toFixed(2).replace('.', ',') + '%', 'sla' + horas + '-detail-reparo sla-detail-hidden sla-percent-cell ' + (slaRep >= meta ? 'td-success' : 'td-danger'));
          addTd(valor.toFixed(2).replace('.', ',') + '%', 'sla-percent-cell ' + (valor >= meta ? 'td-success' : 'td-danger'));
          tbody.appendChild(tr);
        });
      }

      function renderizarConteudoModalSlaIat48() {
        var calc = calcularSlaIatHoras(getFilteredData(false) || [], 48);
        document.getElementById('modalValAtualSlaIat48').textContent = calc.valor.toFixed(2).replace('.', ',') + '%';
        document.getElementById('calcInstalacao48Horas').textContent = formatTableInteger(calc.instalacaoHoras);
        document.getElementById('calcReparo48Horas').textContent = formatTableInteger(calc.reparoHoras);
        document.getElementById('calcTotalReparoSla48').textContent = formatTableInteger(calc.totalReparo);
        document.getElementById('calcTotalInstalacaoSla48').textContent = formatTableInteger(calc.totalInstalacao);
        aplicarStatusSlaIatHoras('SlaIat48', calc.valor, 48);
        renderizarTabelaSlaIatHoras(48);
      }

      function renderizarConteudoModalSlaIat72() {
        var calc = calcularSlaIatHoras(getFilteredData(false) || [], 72);
        document.getElementById('modalValAtualSlaIat72').textContent = calc.valor.toFixed(2).replace('.', ',') + '%';
        document.getElementById('calcInstalacao72Horas').textContent = formatTableInteger(calc.instalacaoHoras);
        document.getElementById('calcReparo72Horas').textContent = formatTableInteger(calc.reparoHoras);
        document.getElementById('calcTotalReparoSla72').textContent = formatTableInteger(calc.totalReparo);
        document.getElementById('calcTotalInstalacaoSla72').textContent = formatTableInteger(calc.totalInstalacao);
        aplicarStatusSlaIatHoras('SlaIat72', calc.valor, 72);
        renderizarTabelaSlaIatHoras(72);
      }

      function calcularChurnRate(data) {
        var cancelamentos = 0, totalInstalacoes = 0;
        (data || []).forEach(function(d) {
          cancelamentos += getNumeroLimpo(getRaioXCampoExato(d, 'CANCELAMENTOS (FTTH)'));
          totalInstalacoes += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL_DE_ACESSOS (MÊS)'));
        });
        return { cancelamentos: cancelamentos, totalInstalacoes: totalInstalacoes, valor: totalInstalacoes > 0 ? (cancelamentos / totalInstalacoes) * 100 : null };
      }

      function calcularIsrPastor(data) {
        var onusCriticos = 0, totalOnu = 0;
        (data || []).forEach(function(d) {
          onusCriticos += getNumeroLimpo(getRaioXCampoExato(d, 'ONUS CRÍTICOS'));
          totalOnu += getNumeroLimpo(getRaioXCampoExato(d, 'TOTAL ONUS'));
        });
        return { onusCriticos: onusCriticos, totalOnu: totalOnu, valor: totalOnu > 0 ? (onusCriticos / totalOnu) * 100 : null };
      }

      function renderizarHistoricoIndicadorNovo(tipo) {
        var dataSemMes = getFilteredData(true) || [], mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro','fevereiro','março','abril','maio','junho','julho','agosto','setembro','outubro','novembro','dezembro'];
        mesesUnicos.sort(function(a,b) {
          var pA=a.split('/'), pB=b.split('/');
          return (parseInt(pA[1],10)!==parseInt(pB[1],10)) ? parseInt(pA[1],10)-parseInt(pB[1],10) : mesesOrdem.indexOf(pA[0].toLowerCase())-mesesOrdem.indexOf(pB[0].toLowerCase());
        });

        var valoresHistoricos = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; });
          var calc = tipo === 'CHURN' ? calcularChurnRate(subset) : calcularIsrPastor(subset);
          valoresHistoricos.push(calc.valor === null ? 0 : Number(calc.valor.toFixed(2)));
        });

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricos);
        if (tipo === 'CHURN') {
          renderizarGraficoHistoricoGenerico('graficoEvolucaoChurnRate','meuGraficoChurnRate','CHURN RATE (%)',fitted.labels,fitted.dados,Math.max(3, Math.ceil(Math.max.apply(null, valoresHistoricos.concat([1])) + 1)));
        } else {
          renderizarGraficoHistoricoGenerico('graficoEvolucaoIsrPastor','meuGraficoIsrPastor','% ISR PASTOR (%)',fitted.labels,fitted.dados,Math.max(4, Math.ceil(Math.max.apply(null, valoresHistoricos.concat([1])) + 1)));
        }
      }

      function aplicarStatusIndicadorNovo(prefix, valor, tipo) {
        var box = document.getElementById('modalHighlightBox' + prefix);
        var diff = document.getElementById('modalValDiff' + prefix);
        var msg = document.getElementById('modalValMsg' + prefix);
        if (!box || !diff || !msg) return;
        var meta = tipo === 'CHURN' ? metaChurnRateIdeal : metaIsrPastorIdeal;
        var ok = valor !== null && valor <= meta;
        box.className = 'modal-highlight-box ' + (ok ? 'box-success' : 'box-danger');
        diff.className = 'modal-box-diff ' + (ok ? 'diff-positive' : 'diff-negative');
        diff.textContent = valor === null ? 'SEM DADOS' : (ok ? 'DENTRO DA META' : 'ACIMA DA META');
        msg.textContent = tipo === 'CHURN'
          ? (ok ? 'Churn Rate dentro da meta de até 1,5%.' : 'Atenção! O Churn Rate ficou acima da meta de 1,5%.')
          : (ok ? '% ISR PASTOR dentro da meta de até 2%.' : 'Atenção! % ISR PASTOR ficou acima da meta de 2%.');
      }

      function renderizarConteudoModalChurnRate() {
        var calc = calcularChurnRate(getFilteredData(false) || []);
        document.getElementById('modalValAtualChurnRate').textContent = calc.valor === null ? '-' : calc.valor.toFixed(2).replace('.', ',') + '%';
        document.getElementById('calcCancelamentosChurnRate').textContent = formatTableInteger(calc.cancelamentos);
        document.getElementById('calcTotalInstalacoesChurnRate').textContent = formatTableInteger(calc.totalInstalacoes);
        aplicarStatusIndicadorNovo('ChurnRate', calc.valor, 'CHURN');
        renderizarHistoricoIndicadorNovo('CHURN');
      }

      function renderizarConteudoModalIsrPastor() {
        var calc = calcularIsrPastor(getFilteredData(false) || []);
        document.getElementById('modalValAtualIsrPastor').textContent = calc.valor === null ? '-' : calc.valor.toFixed(2).replace('.', ',') + '%';
        document.getElementById('calcOnuCriticoIsrPastor').textContent = formatTableInteger(calc.onusCriticos);
        document.getElementById('calcTotalOnuIsrPastor').textContent = formatTableInteger(calc.totalOnu);
        aplicarStatusIndicadorNovo('IsrPastor', calc.valor, 'ISR');
        renderizarHistoricoIndicadorNovo('ISR');
      }

      function alternarGrupoSlaIat(grupo, botao) {
        var config = {
          instalacao: '.sla-detail-instalacao',
          reparo: '.sla-detail-reparo',
          mde: '.sla-detail-mde',
          adic: '.sla-detail-adic'
        };
        var classe = config[grupo];
        if (!classe) return;
        var aberto = botao.getAttribute('aria-expanded') === 'true';
        var novoAberto = !aberto;
        botao.setAttribute('aria-expanded', novoAberto ? 'true' : 'false');
        botao.querySelector('i').className = novoAberto ? 'fa-solid fa-chevron-left' : 'fa-solid fa-chevron-right';
        document.querySelectorAll('#modalSlaIat ' + classe).forEach(function(el) {
          el.classList.toggle('sla-detail-hidden', !novoAberto);
        });
      }

      function calcularPercentualSlaDetalhe(atendido, total) {
        return total > 0 ? (atendido / total) * 100 : 0;
      }

      function classeSlaIat24(percentual) {
        return percentual >= metaSlaIatIdeal ? 'td-success' : 'td-danger';
      }

      function renderizarConteudoModalSlaIat() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosSlaIat = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['SLA IAT', 'SLA_IAT', 'SLA']);
            if (r !== undefined && r !== null && r !== '') { var nVal = getNumeroLimpo(r); if (nVal <= 1 && nVal > 0) nVal *= 100; soma += nVal; count++; }
          });
          valoresHistoricosSlaIat.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var valVigenteSlaIat = 0;
        if (subsetVigente.length > 0) {
          var sSLA = 0, cSLA = 0;
          subsetVigente.forEach(function(d) {
            var rVal = getValueSafe(d, ['SLA IAT', 'SLA_IAT', 'SLA']);
            if (rVal !== undefined && rVal !== null && rVal !== '') { var nVal = getNumeroLimpo(rVal); if (nVal <= 1 && nVal > 0) nVal *= 100; sSLA += nVal; cSLA++; }
          });
          valVigenteSlaIat = cSLA > 0 ? (sSLA / cSLA) : 0;
        }
        var box = document.getElementById('modalHighlightBoxSlaIat'), valEl = document.getElementById('modalValAtualSlaIat'), diffEl = document.getElementById('modalValDiffSlaIat'), msgEl = document.getElementById('modalValMsgSlaIat');
        valEl.textContent = valVigenteSlaIat.toFixed(2).replace('.', ',') + '%';
        if (valVigenteSlaIat >= metaSlaIatIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Taxa de atendimento em até 24h dentro da meta (≥ 65%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O SLA IAT 24 HORAS ficou abaixo da meta de 65%.';
        }

        var tbody = document.getElementById('tabelaSlaIatBody'); tbody.innerHTML = '';
        // Sempre que o modal for reaberto, os detalhes começam recolhidos para manter a tabela compacta.
        document.querySelectorAll('.sla-group-toggle').forEach(function(btn) { btn.setAttribute('aria-expanded', 'false'); });
        document.querySelectorAll('.sla-detail-instalacao, .sla-detail-reparo, .sla-detail-mde, .sla-detail-adic').forEach(function(el) { el.classList.add('sla-detail-hidden'); });
        document.querySelectorAll('.sla-summary-instalacao, .sla-summary-reparo, .sla-summary-mde, .sla-summary-adic').forEach(function(el) { el.classList.remove('sla-detail-hidden'); });

        var dadosTabelaCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaCidades = dadosTabelaCidades.filter(function(linha) {
            var nomeResp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return nomeResp && (nomeResp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeResp) !== -1);
          });
        }
        var totalServicos24h = 0, totalServicosGeral = 0;
        dadosTabelaCidades.forEach(function(linha) {
          var inst24 = getNumeroLimpo(getValueSafe(linha, ['INSTALAÇÃO 24 HORAS']));
          var rep24 = getNumeroLimpo(getValueSafe(linha, ['REPARO 24 HORAS']));
          var mde24 = getNumeroLimpo(getValueSafe(linha, ['MDE 24 HORAS']));
          var adic24 = getNumeroLimpo(getValueSafe(linha, ['SERVIÇO ADICIONAL 24 HORAS']));
          totalServicos24h += (inst24 + rep24 + mde24 + adic24);
          var totInst = getNumeroLimpo(getValueSafe(linha, ['TOTAL INSTALAÇÃO']));
          var totRep = getNumeroLimpo(getValueSafe(linha, ['TOTAL REPARO']));
          var totMde = getNumeroLimpo(getValueSafe(linha, ['TOTAL MDE']));
          var totAdic = getNumeroLimpo(getValueSafe(linha, ['TOTAL SERVIÇO ADICIONAL']));
          totalServicosGeral += (totInst + totRep + totMde + totAdic);
        });
        document.getElementById('calcReparo24Horas').textContent = formatTableInteger(totalServicos24h);
        document.getElementById('calcTotalReparoSla').textContent = formatTableInteger(totalServicosGeral);

        if (!dadosTabelaCidades || dadosTabelaCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="14" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de SLA IAT 24 HORAS encontrado.</td></tr>';
        } else {
          dadosTabelaCidades.sort(function(a, b) {
            var s24A = getNumeroLimpo(getValueSafe(a, ['INSTALAÇÃO 24 HORAS'])) + getNumeroLimpo(getValueSafe(a, ['REPARO 24 HORAS'])) + getNumeroLimpo(getValueSafe(a, ['MDE 24 HORAS'])) + getNumeroLimpo(getValueSafe(a, ['SERVIÇO ADICIONAL 24 HORAS']));
            var totA = getNumeroLimpo(getValueSafe(a, ['TOTAL INSTALAÇÃO'])) + getNumeroLimpo(getValueSafe(a, ['TOTAL MDE'])) + getNumeroLimpo(getValueSafe(a, ['TOTAL REPARO'])) + getNumeroLimpo(getValueSafe(a, ['TOTAL SERVIÇO ADICIONAL']));
            var rawSlaA = getValueSafe(a, ['SLA IAT', 'SLA']); var numSlaA = (rawSlaA !== undefined && rawSlaA !== null && rawSlaA !== '') ? getNumeroLimpo(rawSlaA) : (totA > 0 ? (s24A / totA) * 100 : 0); if (numSlaA <= 1 && numSlaA > 0) numSlaA *= 100;
            var s24B = getNumeroLimpo(getValueSafe(b, ['INSTALAÇÃO 24 HORAS'])) + getNumeroLimpo(getValueSafe(b, ['REPARO 24 HORAS'])) + getNumeroLimpo(getValueSafe(b, ['MDE 24 HORAS'])) + getNumeroLimpo(getValueSafe(b, ['SERVIÇO ADICIONAL 24 HORAS']));
            var totB = getNumeroLimpo(getValueSafe(b, ['TOTAL INSTALAÇÃO'])) + getNumeroLimpo(getValueSafe(b, ['TOTAL MDE'])) + getNumeroLimpo(getValueSafe(b, ['TOTAL REPARO'])) + getNumeroLimpo(getValueSafe(b, ['TOTAL SERVIÇO ADICIONAL']));
            var rawSlaB = getValueSafe(b, ['SLA IAT', 'SLA']); var numSlaB = (rawSlaB !== undefined && rawSlaB !== null && rawSlaB !== '') ? getNumeroLimpo(rawSlaB) : (totB > 0 ? (s24B / totB) * 100 : 0); if (numSlaB <= 1 && numSlaB > 0) numSlaB *= 100;
            return numSlaB - numSlaA;
          });
          dadosTabelaCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var totInst = getNumeroLimpo(getValueSafe(linha, ['TOTAL INSTALAÇÃO']));
            var inst24 = getNumeroLimpo(getValueSafe(linha, ['INSTALAÇÃO 24 HORAS']));
            var totRep = getNumeroLimpo(getValueSafe(linha, ['TOTAL REPARO']));
            var rep24 = getNumeroLimpo(getValueSafe(linha, ['REPARO 24 HORAS']));
            var totMde = getNumeroLimpo(getValueSafe(linha, ['TOTAL MDE']));
            var mde24 = getNumeroLimpo(getValueSafe(linha, ['MDE 24 HORAS']));
            var totAdic = getNumeroLimpo(getValueSafe(linha, ['TOTAL SERVIÇO ADICIONAL']));
            var adic24 = getNumeroLimpo(getValueSafe(linha, ['SERVIÇO ADICIONAL 24 HORAS']));
            var slaInst = calcularPercentualSlaDetalhe(inst24, totInst);
            var slaRep = calcularPercentualSlaDetalhe(rep24, totRep);
            var slaMde = calcularPercentualSlaDetalhe(mde24, totMde);
            var slaAdic = calcularPercentualSlaDetalhe(adic24, totAdic);
            var numS24 = inst24 + rep24 + mde24 + adic24;
            var numTotGeral = totInst + totRep + totMde + totAdic;
            var rawSlaIat = getValueSafe(linha, ['SLA IAT', 'SLA']), numSlaIat = 0;
            if (rawSlaIat !== undefined && rawSlaIat !== null && rawSlaIat !== '') { numSlaIat = getNumeroLimpo(rawSlaIat); if (numSlaIat <= 1 && numSlaIat > 0) numSlaIat *= 100; }
            else { numSlaIat = numTotGeral > 0 ? ((numS24 / numTotGeral) * 100) : 0; }
            function addTd(value, classes) { var td=document.createElement('td'); td.textContent=(value!==undefined && value!==null && value!=='')?value:'-'; if(classes) td.className=classes; tr.appendChild(td); }
            addTd(colCidade);
            addTd(slaInst.toFixed(2).replace('.', ',')+'%', 'sla-summary-cell sla-summary-instalacao '+classeSlaIat24(slaInst));
            addTd(formatTableInteger(totInst), 'sla-detail-instalacao sla-detail-hidden');
            addTd(formatTableInteger(inst24), 'sla-detail-instalacao sla-detail-hidden');
            addTd(slaInst.toFixed(2).replace('.', ',')+'%', 'sla-detail-instalacao sla-detail-hidden sla-percent-cell '+classeSlaIat24(slaInst));
            addTd(slaRep.toFixed(2).replace('.', ',')+'%', 'sla-summary-cell sla-summary-reparo '+classeSlaIat24(slaRep));
            addTd(formatTableInteger(totRep), 'sla-detail-reparo sla-detail-hidden');
            addTd(formatTableInteger(rep24), 'sla-detail-reparo sla-detail-hidden');
            addTd(slaRep.toFixed(2).replace('.', ',')+'%', 'sla-detail-reparo sla-detail-hidden sla-percent-cell '+classeSlaIat24(slaRep));
            addTd(slaMde.toFixed(2).replace('.', ',')+'%', 'sla-summary-cell sla-summary-mde '+classeSlaIat24(slaMde));
            addTd(formatTableInteger(totMde), 'sla-detail-mde sla-detail-hidden');
            addTd(formatTableInteger(mde24), 'sla-detail-mde sla-detail-hidden');
            addTd(slaMde.toFixed(2).replace('.', ',')+'%', 'sla-detail-mde sla-detail-hidden sla-percent-cell '+classeSlaIat24(slaMde));
            addTd(slaAdic.toFixed(2).replace('.', ',')+'%', 'sla-summary-cell sla-summary-adic '+classeSlaIat24(slaAdic));
            addTd(formatTableInteger(totAdic), 'sla-detail-adic sla-detail-hidden');
            addTd(formatTableInteger(adic24), 'sla-detail-adic sla-detail-hidden');
            addTd(slaAdic.toFixed(2).replace('.', ',')+'%', 'sla-detail-adic sla-detail-hidden sla-percent-cell '+classeSlaIat24(slaAdic));
            addTd(numSlaIat.toFixed(2).replace('.', ',')+'%', 'sla-percent-cell '+classeSlaIat24(numSlaIat));
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosSlaIat);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoSlaIat', 'meuGraficoSlaIat', 'SLA IAT 24 HORAS (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalInstEfet() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = []; dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        var valoresHistoricosInstEfet = [];
        mesesUnicos.forEach(function(mesStr) {
          var calcMes = calcularInstEfetRaioXAgregadoMes(dataSemMes, mesStr);
          valoresHistoricosInstEfet.push(Number(calcMes.percentual.toFixed(2)));
        });
        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var calcInstEfetVigente = calcularInstEfetRaioXAgregado(subsetVigente);
        var valVigenteInstEfet = calcInstEfetVigente.percentual;
        var box = document.getElementById('modalHighlightBoxInstEfet'), valEl = document.getElementById('modalValAtualInstEfet'), diffEl = document.getElementById('modalValDiffInstEfet'), msgEl = document.getElementById('modalValMsgInstEfet');
        valEl.textContent = valVigenteInstEfet.toFixed(2).replace('.', ',') + '%';
        if (valVigenteInstEfet >= metaInstEfetIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Percentual de instalação sobre efetivação dentro da meta (≥ 90%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O indicador ficou abaixo do valor ideal de 90%.';
        }
        var tbody = document.getElementById('tabelaInstEfetBody'); tbody.innerHTML = '';
        var dadosTabelaCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosTabelaCidades = dadosTabelaCidades.filter(function(linha) {
            var nomeResp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return nomeResp && (nomeResp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(nomeResp) !== -1);
          });
        }
        var totalInstalacaoWaves = calcInstEfetVigente.instalacoes;
        var totalEfetivadoWaves = calcInstEfetVigente.efetivados;
        document.getElementById('calcInstalacaoWaves').textContent = formatTableInteger(totalInstalacaoWaves);
        document.getElementById('calcEfetivadoWaves').textContent = formatTableInteger(totalEfetivadoWaves);
        if (!dadosTabelaCidades || dadosTabelaCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Instalados x Efetivados encontrado.</td></tr>';
        } else {
          dadosTabelaCidades.sort(function(a, b) {
            var instA = getNumeroLimpo(getValueSafe(a, ['INSTALAÇÃO MÊS ATUAL (WAVES)'])), efetA = getNumeroLimpo(getValueSafe(a, ['EFETIVADO MÊS ATUAL (WAVES)']));
            var rawIeA = getValueSafe(a, ['INSTALADOS X EFETIVADOS']), numIeA = (rawIeA !== undefined && rawIeA !== null && rawIeA !== '') ? getNumeroLimpo(rawIeA) : (efetA > 0 ? (instA / efetA) * 100 : 0);
            if (numIeA <= 1 && numIeA > 0) numIeA *= 100;
            var instB = getNumeroLimpo(getValueSafe(b, ['INSTALAÇÃO MÊS ATUAL (WAVES)'])), efetB = getNumeroLimpo(getValueSafe(b, ['EFETIVADO MÊS ATUAL (WAVES)']));
            var rawIeB = getValueSafe(b, ['INSTALADOS X EFETIVADOS']), numIeB = (rawIeB !== undefined && rawIeB !== null && rawIeB !== '') ? getNumeroLimpo(rawIeB) : (efetB > 0 ? (instB / efetB) * 100 : 0);
            if (numIeB <= 1 && numIeB > 0) numIeB *= 100;
            return numIeB - numIeA;
          });
          dadosTabelaCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var rawInstWaves = getValueSafe(linha, ['INSTALAÇÃO MÊS ATUAL (WAVES)']), rawEfetWaves = getValueSafe(linha, ['EFETIVADO MÊS ATUAL (WAVES)']);
            var rawInstEfet = getValueSafe(linha, ['INSTALADOS X EFETIVADOS']), numInstEfet = 0;
            if (rawInstEfet !== undefined && rawInstEfet !== null && rawInstEfet !== '') {
              numInstEfet = getNumeroLimpo(rawInstEfet); if (numInstEfet <= 1 && numInstEfet > 0) numInstEfet *= 100;
            } else { var numEfetWaves = getNumeroLimpo(rawEfetWaves), numInstWaves = getNumeroLimpo(rawInstWaves); numInstEfet = numEfetWaves > 0 ? ((numInstWaves / numEfetWaves) * 100) : 0; }
            [colCidade, formatTableInteger(rawInstWaves), formatTableInteger(rawEfetWaves), numInstEfet.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numInstEfet >= metaInstEfetIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }
        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosInstEfet);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoInstEfet', 'meuGraficoInstEfet', 'Instalados x Efetivados (%)', fitted.labels, fitted.dados, 100);
      }

      function obterDadosCidadesSafraNovos() {
        var sel = obterNomeSelecionadoParaAnalitico();
        var nome = Array.isArray(sel) ? sel[0] : sel;
        var dados = (indicadoresCidadesData || []).slice();
        if (nome) {
          var alvo = limparTexto(nome);
          dados = dados.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME','SUPERVISOR','GESTOR','COLABORADOR']));
            return resp && (resp.indexOf(alvo)!==-1 || alvo.indexOf(resp)!==-1);
          });
        }
        return dados;
      }

      function aplicarStatusSafraNovo(prefix, valor, tipo) {
        var box=document.getElementById('modalHighlightBox'+prefix), diff=document.getElementById('modalValDiff'+prefix), msg=document.getElementById('modalValMsg'+prefix);
        var ok=valor>=metaSafraIdeal;
        box.className=ok?'modal-highlight-box box-success':'modal-highlight-box box-danger';
        diff.className=ok?'modal-box-diff diff-positive':'modal-box-diff diff-negative';
        diff.textContent=ok?'DENTRO DA META':'ABAIXO DA META';
        msg.textContent=ok?'Safra '+tipo+' dentro da meta estipulada (≥ 74%).':'Atenção! A Safra '+tipo+' ficou abaixo da meta de 74%.';
      }

      function renderizarTabelaSafraNovo(tipo) {
        var tbody=document.getElementById(tipo==='FTTH'?'tabelaSafraFtthBody':'tabelaSafraFwaBody'); if(!tbody)return;
        var dados=obterDadosCidadesSafraNovos(); tbody.innerHTML='';
        var recKey=tipo==='FTTH'?'TOTAL RECOLHIDO (ONU)':'TOTAL RECOLHIDO (FWA)';
        var demKey=tipo==='FTTH'?'DEMANDA RECOLHIMENTO (ONU)':'DEMANDA RECOLHIMENTO (FWA)';
        if(!dados.length){ tbody.innerHTML='<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro encontrado para este indicador.</td></tr>'; return; }
        dados.sort(function(a,b){var da=getNumeroLimpo(getValueSafe(a,[demKey])),ra=getNumeroLimpo(getValueSafe(a,[recKey])),db=getNumeroLimpo(getValueSafe(b,[demKey])),rb=getNumeroLimpo(getValueSafe(b,[recKey]));return (db>0?rb/db*100:0)-(da>0?ra/da*100:0);});
        dados.forEach(function(l){var tr=document.createElement('tr'),cidade=getValueSafe(l,['CIDADE','CIDADE FILIAL']),rec=getNumeroLimpo(getValueSafe(l,[recKey])),dem=getNumeroLimpo(getValueSafe(l,[demKey])),v=dem>0?rec/dem*100:0;[cidade,formatTableInteger(rec),formatTableInteger(dem),v.toFixed(2).replace('.',',')+'%'].forEach(function(x,i){var td=document.createElement('td');td.textContent=x||'-';if(i===3)td.className=v>=metaSafraIdeal?'td-success':'td-danger';tr.appendChild(td);});tbody.appendChild(tr);});
      }

      function renderizarHistoricoSafraNovo(tipo) {
        var nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        var dataSemMes = getFilteredData(true) || [];
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });

        var mesesUnicos = [];
        dataSemMes.forEach(function(d) {
          var m = formatarMes(d['MÊS']);
          if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m);
        });

        var mesesOrdem = ['janeiro','fevereiro','março','abril','maio','junho','julho','agosto','setembro','outubro','novembro','dezembro'];
        mesesUnicos.sort(function(a,b) {
          var pA=a.split('/'), pB=b.split('/');
          return (parseInt(pA[1],10)!==parseInt(pB[1],10))
            ? parseInt(pA[1],10)-parseInt(pB[1],10)
            : mesesOrdem.indexOf(pA[0].toLowerCase())-mesesOrdem.indexOf(pB[0].toLowerCase());
        });

        var recKey = tipo === 'FTTH' ? ['TOTAL RECOLHIDO (ONU)','TOTAL RECOLHIDO ONU'] : ['TOTAL RECOLHIDO (FWA)','TOTAL RECOLHIDO FWA'];
        var demKey = tipo === 'FTTH' ? ['DEMANDA RECOLHIMENTO (ONU)','DEMANDA RECOLHIMENTO ONU'] : ['DEMANDA RECOLHIMENTO (FWA)','DEMANDA RECOLHIMENTO FWA'];
        var valoresHistoricos = [];

        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; });
          var rec=0, dem=0;
          subset.forEach(function(d) {
            rec += getNumeroLimpo(getValueSafe(d, recKey));
            dem += getNumeroLimpo(getValueSafe(d, demKey));
          });
          valoresHistoricos.push(dem > 0 ? Number(((rec/dem)*100).toFixed(2)) : 0);
        });

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricos);
        renderizarGraficoHistoricoGenerico(
          tipo === 'FTTH' ? 'graficoEvolucaoSafraFtth' : 'graficoEvolucaoSafraFwa',
          tipo === 'FTTH' ? 'meuGraficoSafraFtth' : 'meuGraficoSafraFwa',
          tipo === 'FTTH' ? 'SAFRA FTTH (%)' : 'SAFRA FWA (%)',
          fitted.labels, fitted.dados, 100
        );
      }

      function renderizarConteudoModalSafraFtth() {
        var data=getFilteredData(false)||[],rec=0,dem=0; data.forEach(function(d){rec+=getNumeroLimpo(getValueSafe(d,['TOTAL RECOLHIDO (ONU)','TOTAL RECOLHIDO ONU']));dem+=getNumeroLimpo(getValueSafe(d,['DEMANDA RECOLHIMENTO (ONU)','DEMANDA RECOLHIMENTO ONU']));});
        var v=dem>0?rec/dem*100:0; document.getElementById('modalValAtualSafraFtth').textContent=v.toFixed(2).replace('.',',')+'%'; document.getElementById('calcTotalRecolhidoSafraFtth').textContent=formatTableInteger(rec); document.getElementById('calcDemandaRecolhimentoSafraFtth').textContent=formatTableInteger(dem); aplicarStatusSafraNovo('SafraFtth',v,'FTTH'); renderizarHistoricoSafraNovo('FTTH'); renderizarTabelaSafraNovo('FTTH');
      }
      function renderizarConteudoModalSafraFwa() {
        var data=getFilteredData(false)||[],rec=0,dem=0; data.forEach(function(d){rec+=getNumeroLimpo(getValueSafe(d,['TOTAL RECOLHIDO (FWA)','TOTAL RECOLHIDO FWA']));dem+=getNumeroLimpo(getValueSafe(d,['DEMANDA RECOLHIMENTO (FWA)','DEMANDA RECOLHIMENTO FWA']));});
        var v=dem>0?rec/dem*100:0; document.getElementById('modalValAtualSafraFwa').textContent=v.toFixed(2).replace('.',',')+'%'; document.getElementById('calcTotalRecolhidoSafraFwa').textContent=formatTableInteger(rec); document.getElementById('calcDemandaRecolhimentoSafraFwa').textContent=formatTableInteger(dem); aplicarStatusSafraNovo('SafraFwa',v,'FWA'); renderizarHistoricoSafraNovo('FWA'); renderizarTabelaSafraNovo('FWA');
      }

      function renderizarConteudoModalSafra() {
        var nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        var dataVigente = getFilteredData(false);

        var box = document.getElementById('modalHighlightBoxSafra');
        var valEl = document.getElementById('modalValAtualSafra');
        var diffEl = document.getElementById('modalValDiffSafra');
        var msgEl = document.getElementById('modalValMsgSafra');
        var tbody = document.getElementById('tabelaSafraBody'); tbody.innerHTML = '';

        var dadosCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosCidades = dadosCidades.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return resp && (resp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(resp) !== -1);
          });
        }

        var totDemOnu = 0, totRecOnu = 0, totDemFwa = 0, totRecFwa = 0;
        dadosCidades.forEach(function(l) {
          totDemOnu += getNumeroLimpo(getValueSafe(l, ['DEMANDA RECOLHIMENTO (ONU)']));
          totRecOnu += getNumeroLimpo(getValueSafe(l, ['TOTAL RECOLHIDO (ONU)']));
          totDemFwa += getNumeroLimpo(getValueSafe(l, ['DEMANDA RECOLHIMENTO (FWA)']));
          totRecFwa += getNumeroLimpo(getValueSafe(l, ['TOTAL RECOLHIDO (FWA)']));
        });

        var totDem = totDemOnu + totDemFwa;
        var totRec = totRecOnu + totRecFwa;
        var valSafraGeral = totDem > 0 ? ((totRec / totDem) * 100) : 0;

        if (valSafraGeral === 0 && dataVigente.length > 0) {
          var sumSafraRaioX = 0, cntSafraRaioX = 0;
          dataVigente.forEach(function(d) {
            var rS = getValueSafe(d, ['SAFRA']);
            if (rS !== undefined && rS !== null && rS !== '') {
              var nS = getNumeroLimpo(rS); if (nS <= 1 && nS > 0) nS *= 100;
              sumSafraRaioX += nS; cntSafraRaioX++;
            }
          });
          if (cntSafraRaioX > 0) valSafraGeral = sumSafraRaioX / cntSafraRaioX;
        }

        valEl.textContent = valSafraGeral.toFixed(2).replace('.', ',') + '%';
        if (valSafraGeral >= metaSafraIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Percentual de Safra dentro da meta estipulada (≥ 74%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O percentual de Safra ficou abaixo da meta de 74%.';
        }

        document.getElementById('calcTotalRecolhidoSafra').textContent = formatTableInteger(totRec);
        document.getElementById('calcDemandaRecolhimentoSafra').textContent = formatTableInteger(totDem);

        if (!dadosCidades || dadosCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Safra encontrado para as cidades deste supervisor.</td></tr>';
        } else {
          dadosCidades.sort(function(a, b) {
            var demA = getNumeroLimpo(getValueSafe(a, ['DEMANDA RECOLHIMENTO (ONU)'])) + getNumeroLimpo(getValueSafe(a, ['DEMANDA RECOLHIMENTO (FWA)']));
            var recA = getNumeroLimpo(getValueSafe(a, ['TOTAL RECOLHIDO (ONU)'])) + getNumeroLimpo(getValueSafe(a, ['TOTAL RECOLHIDO (FWA)']));
            var demB = getNumeroLimpo(getValueSafe(b, ['DEMANDA RECOLHIMENTO (ONU)'])) + getNumeroLimpo(getValueSafe(b, ['DEMANDA RECOLHIMENTO (FWA)']));
            var recB = getNumeroLimpo(getValueSafe(b, ['TOTAL RECOLHIDO (ONU)'])) + getNumeroLimpo(getValueSafe(b, ['TOTAL RECOLHIDO (FWA)']));
            return (demB > 0 ? (recB / demB) * 100 : 0) - (demA > 0 ? (recA / demA) * 100 : 0);
          });

          dadosCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var dOnu = getNumeroLimpo(getValueSafe(linha, ['DEMANDA RECOLHIMENTO (ONU)']));
            var rOnu = getNumeroLimpo(getValueSafe(linha, ['TOTAL RECOLHIDO (ONU)']));
            var dFwa = getNumeroLimpo(getValueSafe(linha, ['DEMANDA RECOLHIMENTO (FWA)']));
            var rFwa = getNumeroLimpo(getValueSafe(linha, ['TOTAL RECOLHIDO (FWA)']));
            var totalD = dOnu + dFwa, totalR = rOnu + rFwa;
            var numSafraRow = totalD > 0 ? ((totalR / totalD) * 100) : 0;
            [colCidade, formatTableInteger(dOnu), formatTableInteger(rOnu), formatTableInteger(dFwa), formatTableInteger(rFwa), numSafraRow.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 5 && val !== '-') td.className = numSafraRow >= metaSafraIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }

        var dataSemMes = getFilteredData(true);
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosSafra = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['SAFRA']);
            if (r !== undefined && r !== null && r !== '') { var nVal = getNumeroLimpo(r); if (nVal <= 1 && nVal > 0) nVal *= 100; soma += nVal; count++; }
          });
          valoresHistoricosSafra.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosSafra);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoSafra', 'meuGraficoSafra', 'SAFRA (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalIndiceRetencao() {
        var nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        var dataVigente = getFilteredData(false);

        var box = document.getElementById('modalHighlightBoxIndiceRetencao');
        var valEl = document.getElementById('modalValAtualIndiceRetencao');
        var diffEl = document.getElementById('modalValDiffIndiceRetencao');
        var msgEl = document.getElementById('modalValMsgIndiceRetencao');
        var tbody = document.getElementById('tabelaIndiceRetencaoBody'); tbody.innerHTML = '';

        var dadosCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosCidades = dadosCidades.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return resp && (resp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(resp) !== -1);
          });
        }

        var totReativados = 0, totGerRec = 0, totGerFwa = 0;
        dadosCidades.forEach(function(l) {
          totReativados += getNumeroLimpo(getValueSafe(l, ['REATIVADOS']));
          totGerRec += getNumeroLimpo(getValueSafe(l, ['GERADOS RECOLHIMENTO']));
          totGerFwa += getNumeroLimpo(getValueSafe(l, ['GERADOS FWA']));
        });

        var totGerados = totGerRec + totGerFwa;
        var valRetencaoGeral = totGerados > 0 ? ((totReativados / totGerados) * 100) : 0;

        if (valRetencaoGeral === 0 && dataVigente.length > 0) {
          var sumRetRaioX = 0, cntRetRaioX = 0;
          dataVigente.forEach(function(d) {
            var rR = getValueSafe(d, ['ÍNDICE DE RETENÇÃO', 'INDICE DE RETENCAO', 'RETENÇÃO', 'RETENCAO']);
            if (rR !== undefined && rR !== null && rR !== '') {
              var nR = getNumeroLimpo(rR); if (nR <= 1 && nR > 0) nR *= 100;
              sumRetRaioX += nR; cntRetRaioX++;
            }
          });
          if (cntRetRaioX > 0) valRetencaoGeral = sumRetRaioX / cntRetRaioX;
        }

        valEl.textContent = valRetencaoGeral.toFixed(2).replace('.', ',') + '%';
        if (valRetencaoGeral >= metaIndiceRetencaoIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Índice de retenção dentro da meta estipulada (≥ 15%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O índice de retenção ficou abaixo do valor ideal de 15%.';
        }

        document.getElementById('calcTotalReativados').textContent = formatTableInteger(totReativados);
        document.getElementById('calcTotalGeradosRecolhimento').textContent = formatTableInteger(totGerados);

        if (!dadosCidades || dadosCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="5" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de retenção encontrado para as cidades deste supervisor.</td></tr>';
        } else {
          dadosCidades.sort(function(a, b) {
            var reatA = getNumeroLimpo(getValueSafe(a, ['REATIVADOS'])), gerA = getNumeroLimpo(getValueSafe(a, ['GERADOS RECOLHIMENTO'])) + getNumeroLimpo(getValueSafe(a, ['GERADOS FWA']));
            var reatB = getNumeroLimpo(getValueSafe(b, ['REATIVADOS'])), gerB = getNumeroLimpo(getValueSafe(b, ['GERADOS RECOLHIMENTO'])) + getNumeroLimpo(getValueSafe(b, ['GERADOS FWA']));
            return (gerB > 0 ? (reatB / gerB) * 100 : 0) - (gerA > 0 ? (reatA / gerA) * 100 : 0);
          });

          dadosCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var rReat = getNumeroLimpo(getValueSafe(linha, ['REATIVADOS']));
            var rGerRec = getNumeroLimpo(getValueSafe(linha, ['GERADOS RECOLHIMENTO']));
            var rGerFwa = getNumeroLimpo(getValueSafe(linha, ['GERADOS FWA']));
            var totalG = rGerRec + rGerFwa;
            var numRetRow = totalG > 0 ? ((rReat / totalG) * 100) : 0;
            [colCidade, formatTableInteger(rReat), formatTableInteger(rGerRec), formatTableInteger(rGerFwa), numRetRow.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 4 && val !== '-') td.className = numRetRow >= metaIndiceRetencaoIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }

        var dataSemMes = getFilteredData(true);
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosRet = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['ÍNDICE DE RETENÇÃO', 'INDICE DE RETENCAO', 'RETENÇÃO', 'RETENCAO']);
            if (r !== undefined && r !== null && r !== '') { var nVal = getNumeroLimpo(r); if (nVal <= 1 && nVal > 0) nVal *= 100; soma += nVal; count++; }
          });
          valoresHistoricosRet.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosRet);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoIndiceRetencao', 'meuGraficoIndiceRetencao', 'ÍNDICE DE RETENÇÃO (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalCertificacao() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosCert = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), soma = 0, count = 0;
          subset.forEach(function(d) {
            var r = getValueSafe(d, ['CERTIFICAÇÃO', 'CERTIFICACAO']);
            if (r !== undefined && r !== null && r !== '') { var nVal = getNumeroLimpo(r); if (nVal <= 1 && nVal > 0) nVal *= 100; soma += nVal; count++; }
          });
          valoresHistoricosCert.push(count > 0 ? Number((soma / count).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalBateu = 0, totalMetas = 0, valVigenteCertificacao = 0;
        if (subsetVigente.length > 0) {
          var sCert = 0, cCert = 0;
          subsetVigente.forEach(function(d) {
            totalBateu += getNumeroLimpo(getValueSafe(d, ['BATEU', 'INDICADORES BATEU']));
            totalMetas += getNumeroLimpo(getValueSafe(d, ['TOTAL', 'TOTAL INDICADORES']));
            var rVal = getValueSafe(d, ['CERTIFICAÇÃO', 'CERTIFICACAO']);
            if (rVal !== undefined && rVal !== null && rVal !== '') { var nVal = getNumeroLimpo(rVal); if (nVal <= 1 && nVal > 0) nVal *= 100; sCert += nVal; cCert++; }
          });
          valVigenteCertificacao = cCert > 0 ? (sCert / cCert) : 0;
        }

        var box = document.getElementById('modalHighlightBoxCertificacao'), valEl = document.getElementById('modalValAtualCertificacao'), diffEl = document.getElementById('modalValDiffCertificacao'), msgEl = document.getElementById('modalValMsgCertificacao');
        valEl.textContent = valVigenteCertificacao.toFixed(2).replace('.', ',') + '%';
        if (valVigenteCertificacao >= metaCertificacaoIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Índice de Certificação dentro da meta estipulada (≥ 80%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! A Certificação ficou abaixo da meta ideal de 80%.';
        }

        document.getElementById('calcBateuCertificacao').textContent = formatTableInteger(totalBateu);
        document.getElementById('calcTotalCertificacao').textContent = formatTableInteger(totalMetas);

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosCert);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoCertificacao', 'meuGraficoCertificacao', 'Certificação (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalCertificacaoCidades() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosCertCidades = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumBateu = 0, sumTotal = 0;
          subset.forEach(function(d) {
            sumBateu += getNumeroLimpo(getValueSafe(d, ['BATEU IND', 'BATEU_IND']));
            sumTotal += getNumeroLimpo(getValueSafe(d, ['TOTAL IND', 'TOTAL_IND']));
          });
          valoresHistoricosCertCidades.push(sumTotal > 0 ? Number(((sumBateu / sumTotal) * 100).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalBateuInd = 0, totalInd = 0, valVigenteCertCidades = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalBateuInd += getNumeroLimpo(getValueSafe(d, ['BATEU IND', 'BATEU_IND']));
            totalInd += getNumeroLimpo(getValueSafe(d, ['TOTAL IND', 'TOTAL_IND']));
          });
          valVigenteCertCidades = totalInd > 0 ? ((totalBateuInd / totalInd) * 100) : 0;
        }

        var box = document.getElementById('modalHighlightBoxCertificacaoCidades'), valEl = document.getElementById('modalValAtualCertificacaoCidades'), diffEl = document.getElementById('modalValDiffCertificacaoCidades'), msgEl = document.getElementById('modalValMsgCertificacaoCidades');
        valEl.textContent = valVigenteCertCidades.toFixed(2).replace('.', ',') + '%';
        if (valVigenteCertCidades >= metaCertificacaoCidadesIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Índice de Certificação Cidades dentro da meta estipulada (≥ 50%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! A Certificação Cidades ficou abaixo da meta ideal de 50%.';
        }

        document.getElementById('calcBateuCertificacaoCidades').textContent = formatTableInteger(totalBateuInd);
        document.getElementById('calcTotalCertificacaoCidades').textContent = formatTableInteger(totalInd);

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosCertCidades);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoCertificacaoCidades', 'meuGraficoCertificacaoCidades', 'Certificação Cidades (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalRegularizacao15m() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosReg15m = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumReg = 0, sumCaixas = 0;
          subset.forEach(function(d) {
            sumReg += getNumeroLimpo(getValueSafe(d, ['REGULARIZADO 15M', 'REGULARIZADO_15M']));
            sumCaixas += getNumeroLimpo(getValueSafe(d, ['CAIXAS', 'TOTAL CAIXAS']));
          });
          valoresHistoricosReg15m.push(sumCaixas > 0 ? Number(((sumReg / sumCaixas) * 100).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalReg15m = 0, totalCaixas = 0, valVigenteReg15m = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalReg15m += getNumeroLimpo(getValueSafe(d, ['REGULARIZADO 15M', 'REGULARIZADO_15M']));
            totalCaixas += getNumeroLimpo(getValueSafe(d, ['CAIXAS', 'TOTAL CAIXAS']));
          });
          valVigenteReg15m = totalCaixas > 0 ? ((totalReg15m / totalCaixas) * 100) : 0;
        }

        var box = document.getElementById('modalHighlightBoxRegularizacao15m'), valEl = document.getElementById('modalValAtualRegularizacao15m'), diffEl = document.getElementById('modalValDiffRegularizacao15m'), msgEl = document.getElementById('modalValMsgRegularizacao15m');
        valEl.textContent = valVigenteReg15m.toFixed(2).replace('.', ',') + '%';
        if (valVigenteReg15m >= metaRegularizacao15mIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = '% Regularização 15M dentro da meta estipulada (≥ 50%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O percentual de regularização ficou abaixo da meta ideal de 50%.';
        }

        document.getElementById('calcRegularizado15m').textContent = formatTableInteger(totalReg15m);
        document.getElementById('calcCaixas15m').textContent = formatTableInteger(totalCaixas);

        var tbody = document.getElementById('tabelaRegularizacao15mBody'); tbody.innerHTML = '';
        var dadosCidades = indicadoresCidadesData || [];
        if (nomeColab) {
          var colabAlvo = limparTexto(nomeColab);
          dadosCidades = dadosCidades.filter(function(linha) {
            var resp = limparTexto(getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']));
            return resp && (resp.indexOf(colabAlvo) !== -1 || colabAlvo.indexOf(resp) !== -1);
          });
        }

        if (!dadosCidades || dadosCidades.length === 0) {
          tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Regularização 15M encontrado.</td></tr>';
        } else {
          dadosCidades.sort(function(a, b) {
            var regA = getNumeroLimpo(getValueSafe(a, ['REGULARIZADO 15M', 'REGULARIZADO_15M'])), caixasA = getNumeroLimpo(getValueSafe(a, ['CAIXAS', 'TOTAL CAIXAS']));
            var pctA = caixasA > 0 ? (regA / caixasA) * 100 : 0;
            var regB = getNumeroLimpo(getValueSafe(b, ['REGULARIZADO 15M', 'REGULARIZADO_15M'])), caixasB = getNumeroLimpo(getValueSafe(b, ['CAIXAS', 'TOTAL CAIXAS']));
            var pctB = caixasB > 0 ? (regB / caixasB) * 100 : 0;
            return pctB - pctA;
          });

          dadosCidades.forEach(function(linha) {
            var tr = document.createElement('tr');
            var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
            var rawReg = getValueSafe(linha, ['REGULARIZADO 15M', 'REGULARIZADO_15M']), numReg = getNumeroLimpo(rawReg);
            var rawCaixas = getValueSafe(linha, ['CAIXAS', 'TOTAL CAIXAS']), numCaixas = getNumeroLimpo(rawCaixas);
            var numPctReg = numCaixas > 0 ? ((numReg / numCaixas) * 100) : 0;

            [colCidade, formatTableInteger(rawReg), formatTableInteger(rawCaixas), numPctReg.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
              var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
              if (index === 3 && val !== '-') td.className = numPctReg >= metaRegularizacao15mIdeal ? 'td-success' : 'td-danger';
              tr.appendChild(td);
            });
            tbody.appendChild(tr);
          });
        }

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosReg15m);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoRegularizacao15m', 'meuGraficoRegularizacao15m', '% Regularização 15M', fitted.labels, fitted.dados, 100);
      }


      function renderTabelaQuartil4() {
        var tbody = document.getElementById('tabelaQuartil4Body');
        if (!tbody) return;

        tbody.innerHTML = '';

        var dados = Array.isArray(quartil4Data) ? quartil4Data.slice() : [];

        /*
         * FILTRO DO ANALÍTICO:
         * A coluna F da aba "4º QUARTIL" é a coluna NOME e representa
         * o supervisor. Portanto, quando houver supervisor selecionado
         * no filtro do painel (filterNome), a tabela deve trazer somente
         * os colaboradores cujo NOME da aba 4º QUARTIL seja exatamente
         * aquele supervisor.
         */
        var supervisoresSelecionados = [];
        if (selects['filterNome']) {
          var valorSupervisor = selects['filterNome'].getValue();
          supervisoresSelecionados = Array.isArray(valorSupervisor)
            ? valorSupervisor.filter(function(v) { return String(v || '').trim() !== ''; })
            : (valorSupervisor ? [valorSupervisor] : []);
        }

        if (supervisoresSelecionados.length > 0) {
          dados = dados.filter(function(linha) {
            var supervisorDaLinha = getValueSafe(linha, ['NOME']);
            return supervisoresSelecionados.some(function(supervisorSelecionado) {
              return valorFiltroNormalizado(supervisorDaLinha) === valorFiltroNormalizado(supervisorSelecionado);
            });
          });
        }

        if (!dados.length) {
          var trVazio = document.createElement('tr');
          var tdVazio = document.createElement('td');
          tdVazio.colSpan = 5;
          tdVazio.className = 'quartil4-empty';
          tdVazio.textContent = supervisoresSelecionados.length > 0
            ? 'Nenhum colaborador do 4º Quartil encontrado para o supervisor selecionado.'
            : 'Nenhum dado encontrado na aba 4º QUARTIL.';
          trVazio.appendChild(tdVazio);
          tbody.appendChild(trVazio);
          return;
        }

        dados.forEach(function(linha) {
          var funcionario = getValueSafe(linha, ['FUNCIONÁRIO', 'FUNCIONARIO']);
          var pontos = getValueSafe(linha, ['PONTOS']);
          var diasFinal = getValueSafe(linha, ['DIAS FINAL', 'DIAS FINAIS']);
          var media = getValueSafe(linha, ['MÉDIA', 'MEDIA']);
          var quartil = getValueSafe(linha, ['QUARTIL']);

          var tr = document.createElement('tr');

          var valores = [
            funcionario,
            pontos,
            diasFinal,
            media,
            quartil
          ];

          valores.forEach(function(valor, index) {
            var td = document.createElement('td');
            var texto = (valor !== undefined && valor !== null && String(valor).trim() !== '')
              ? String(valor).trim()
              : '-';

            if (index === 1 || index === 2 || index === 3) {
              var numero = getNumeroLimpo(valor);
              texto = (valor !== undefined && valor !== null && String(valor).trim() !== '')
                ? numero.toLocaleString('pt-BR', {
                    minimumFractionDigits: index === 2 ? 0 : 2,
                    maximumFractionDigits: 2
                  })
                : '-';
            }

            td.textContent = texto;

            if (index === 4 && texto !== '-' && /quartil/i.test(texto)) {
              td.classList.add('quartil4-danger');
            }

            tr.appendChild(td);
          });

          tbody.appendChild(tr);
        });
      }


      function renderizarConteudoModalEquipes4Quartil() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return d['NOME'] === nomeColab; });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });
        
        var valoresHistoricosEquipes4Q = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumTec4Q = 0, sumEquipes = 0;
          subset.forEach(function(d) {
            sumTec4Q += getNumeroLimpo(getValueSafe(d, ['TÉC. 4º QUARTIL', 'TEC. 4º QUARTIL', 'TEC 4 QUARTIL']));
            sumEquipes += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));
          });
          valoresHistoricosEquipes4Q.push(sumEquipes > 0 ? Number(((sumTec4Q / sumEquipes) * 100).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return d['NOME'] === nomeColab; });
        var totalTec4Q = 0, totalEquipes = 0, valVigenteEquipes4Q = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalTec4Q += getNumeroLimpo(getValueSafe(d, ['TÉC. 4º QUARTIL', 'TEC. 4º QUARTIL', 'TEC 4 QUARTIL']));
            totalEquipes += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));
          });
          valVigenteEquipes4Q = totalEquipes > 0 ? ((totalTec4Q / totalEquipes) * 100) : 0;
        }

        var box = document.getElementById('modalHighlightBoxEquipes4Quartil'), valEl = document.getElementById('modalValAtualEquipes4Quartil'), diffEl = document.getElementById('modalValDiffEquipes4Quartil'), msgEl = document.getElementById('modalValMsgEquipes4Quartil');
        valEl.textContent = valVigenteEquipes4Q.toFixed(2).replace('.', ',') + '%';
        if (valVigenteEquipes4Q === metaEquipes4QuartilIdeal) {
          box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = '% de Equipes no 4º Quartil dentro da meta estipulada (= 0%).';
        } else {
          box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! Existem equipes no 4º Quartil.';
        }

        document.getElementById('calcTec4Quartil').textContent = formatTableInteger(totalTec4Q);
        document.getElementById('calcTotalEquipes4Q').textContent = formatTableInteger(totalEquipes);

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosEquipes4Q);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoEquipes4Quartil', 'meuGraficoEquipes4Quartil', '% Equipes 4º Quartil', fitted.labels, fitted.dados, 100);

        /* Analítico fixo do modal — dados da aba 4º QUARTIL. */
        renderTabelaQuartil4();
      }

      function renderizarConteudoModalAtestado12m() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });

        var valoresHistoricosAtestado12m = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumAtestado = 0, sumEquipes = 0, temDadoAtestado = false;
          subset.forEach(function(d) {
            var rawAtestado = getValueSafe(d, ['ATESTADO 12 MESES', 'ATESTADOS DOS ÚLTIMOS 12 MESES', 'ATESTADOS 12 MESES']);
            if (rawAtestado !== undefined && rawAtestado !== null && String(rawAtestado).trim() !== '') temDadoAtestado = true;
            sumAtestado += getNumeroLimpo(rawAtestado);
            sumEquipes += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));
          });
          valoresHistoricosAtestado12m.push(!temDadoAtestado ? null : (sumEquipes > 0 ? Number((sumAtestado / sumEquipes).toFixed(2)) : 0));
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var totalAtestado12m = 0, totalEquipesAtestado = 0, valVigenteAtestado12m = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalAtestado12m += getNumeroLimpo(getValueSafe(d, ['ATESTADO 12 MESES', 'ATESTADOS DOS ÚLTIMOS 12 MESES', 'ATESTADOS 12 MESES']));
            totalEquipesAtestado += getNumeroLimpo(getValueSafe(d, ['EQUIPES', 'EQUIPE']));
          });
          valVigenteAtestado12m = totalEquipesAtestado > 0 ? (totalAtestado12m / totalEquipesAtestado) : 0;
        }

        var box = document.getElementById('modalHighlightBoxAtestado12m'), valEl = document.getElementById('modalValAtualAtestado12m'), diffEl = document.getElementById('modalValDiffAtestado12m'), msgEl = document.getElementById('modalValMsgAtestado12m');
        if (valEl) valEl.textContent = valVigenteAtestado12m.toFixed(2).replace('.', ',');
        if (box && diffEl && msgEl) {
          if (valVigenteAtestado12m <= metaAtestado12mIdeal) {
            box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Média de atestados dentro da meta estipulada (≤ 1).';
          } else {
            box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! A média de atestados ultrapassou a meta de 1.';
          }
        }

        var elCalcAt = document.getElementById('calcAtestado12m'); if (elCalcAt) elCalcAt.textContent = formatTableInteger(totalAtestado12m);
        var elCalcEq = document.getElementById('calcTotalEquipesAtestado'); if (elCalcEq) elCalcEq.textContent = formatTableInteger(totalEquipesAtestado);

        var fitted = fitHistoricDataComZeros(mesesUnicos, valoresHistoricosAtestado12m);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoAtestado12m', 'meuGraficoAtestado12m', 'Atestado 12M', fitted.labels, fitted.dados, 5, undefined, true);
      }

      function renderizarConteudoModalImprodutivaIat() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = obterNomeSelecionadoParaAnalitico();
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });

        var valoresHistoricosImprodutivaIat = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumDesatrib = 0, sumAtrib = 0;
          subset.forEach(function(d) {
            var desatInst = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO']));
            var desatRep = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']));
            var atribInst = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO']));
            var atribRep = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']));
            sumDesatrib += (desatInst + desatRep);
            sumAtrib += (atribInst + atribRep);
          });
          valoresHistoricosImprodutivaIat.push(sumAtrib > 0 ? Number(((sumDesatrib / sumAtrib) * 100).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var totalDesatrib = 0, totalAtrib = 0, valVigenteImprodutivaIat = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            var desatInst = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO']));
            var desatRep = getNumeroLimpo(getValueSafe(d, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']));
            var atribInst = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO']));
            var atribRep = getNumeroLimpo(getValueSafe(d, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']));
            totalDesatrib += (desatInst + desatRep);
            totalAtrib += (atribInst + atribRep);
          });
          valVigenteImprodutivaIat = totalAtrib > 0 ? ((totalDesatrib / totalAtrib) * 100) : 0;
        }

        var box = document.getElementById('modalHighlightBoxImprodutivaIat'), valEl = document.getElementById('modalValAtualImprodutivaIat'), diffEl = document.getElementById('modalValDiffImprodutivaIat'), msgEl = document.getElementById('modalValMsgImprodutivaIat');
        if (valEl) valEl.textContent = valVigenteImprodutivaIat.toFixed(2).replace('.', ',') + '%';
        if (box && diffEl && msgEl) {
          if (valVigenteImprodutivaIat <= metaImprodutivaIatIdeal) {
            box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Percentual de Improdutiva IAT dentro da meta estipulada (≤ 15%).';
          } else {
            box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O percentual de improdutiva IAT ultrapassou a meta de 15%.';
          }
        }

        var elCalcDes = document.getElementById('calcDesatribuicoesTotal'); if (elCalcDes) elCalcDes.textContent = formatTableInteger(totalDesatrib);
        var elCalcAtr = document.getElementById('calcAtribuicoesTotal'); if (elCalcAtr) elCalcAtr.textContent = formatTableInteger(totalAtrib);

        var tbody = document.getElementById('tabelaImprodutivaIatBody');
        if (tbody) {
          tbody.innerHTML = '';
          var dadosCidades = indicadoresCidadesData || [];
          if (nomeColab) {
            dadosCidades = dadosCidades.filter(function(linha) {
              var resp = getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']);
              return comparaNomeFlexivel(resp, nomeColab);
            });
          }

          if (!dadosCidades || dadosCidades.length === 0) {
            tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Improdutiva IAT encontrado.</td></tr>';
          } else {
            dadosCidades.sort(function(a, b) {
              var desA = getNumeroLimpo(getValueSafe(a, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO'])) + getNumeroLimpo(getValueSafe(a, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']));
              var atrA = getNumeroLimpo(getValueSafe(a, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO'])) + getNumeroLimpo(getValueSafe(a, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']));
              var pctA = atrA > 0 ? (desA / atrA) * 100 : 0;

              var desB = getNumeroLimpo(getValueSafe(b, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO'])) + getNumeroLimpo(getValueSafe(b, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']));
              var atrB = getNumeroLimpo(getValueSafe(b, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO'])) + getNumeroLimpo(getValueSafe(b, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']));
              var pctB = atrB > 0 ? (desB / atrB) * 100 : 0;

              return pctB - pctA;
            });

            dadosCidades.forEach(function(linha) {
              var tr = document.createElement('tr');
              var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
              var rawAtrInst = getValueSafe(linha, ['ATRIBUIÇÕES INSTALAÇÃO', 'ATRIBUICOES INSTALACAO']), numAtrInst = getNumeroLimpo(rawAtrInst);
              var rawDesInst = getValueSafe(linha, ['DESATRIBUIÇÕES INSTALAÇÃO', 'DESATRIBUICOES INSTALACAO']), numDesInst = getNumeroLimpo(rawDesInst);
              var rawAtrRep = getValueSafe(linha, ['ATRIBUIÇÕES REPARO', 'ATRIBUICOES REPARO']), numAtrRep = getNumeroLimpo(rawAtrRep);
              var rawDesRep = getValueSafe(linha, ['DESATRIBUIÇÕES REPARO', 'DESATRIBUICOES REPARO']), numDesRep = getNumeroLimpo(rawDesRep);

              var totalDes = numDesInst + numDesRep;
              var totalAtr = numAtrInst + numAtrRep;
              var numPctImprod = totalAtr > 0 ? ((totalDes / totalAtr) * 100) : 0;

              [colCidade, formatTableInteger(rawAtrInst), formatTableInteger(rawDesInst), formatTableInteger(rawAtrRep), formatTableInteger(rawDesRep), numPctImprod.toFixed(2).replace('.', ',') + '%'].forEach(function(val, index) {
                var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
                if (index === 5 && val !== '-') td.className = numPctImprod <= metaImprodutivaIatIdeal ? 'td-success' : 'td-danger';
                tr.appendChild(td);
              });
              tbody.appendChild(tr);
            });
          }
        }

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosImprodutivaIat);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoImprodutivaIat', 'meuGraficoImprodutivaIat', 'Improdutiva IAT (%)', fitted.labels, fitted.dados, 100);
      }

      function renderizarConteudoModalBacklogReparo() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = selects['filterNome'] ? selects['filterNome'].getValue() : [];
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });

        var valoresHistoricosBacklogReparo = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumDemanda = 0, sumMedia7 = 0;
          subset.forEach(function(d) {
            sumDemanda += getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
            sumMedia7 += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO']));
          });
          valoresHistoricosBacklogReparo.push(sumMedia7 > 0 ? Number((sumDemanda / sumMedia7).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var totalDemandaVigente = 0, totalMedia7Vigente = 0, valVigenteBacklogReparo = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalDemandaVigente += getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
            totalMedia7Vigente += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO']));
          });
          valVigenteBacklogReparo = totalMedia7Vigente > 0 ? (totalDemandaVigente / totalMedia7Vigente) : 0;
        }

        var box = document.getElementById('modalHighlightBoxBacklogReparo'), valEl = document.getElementById('modalValAtualBacklogReparo'), diffEl = document.getElementById('modalValDiffBacklogReparo'), msgEl = document.getElementById('modalValMsgBacklogReparo');
        if (valEl) valEl.textContent = valVigenteBacklogReparo.toFixed(2).replace('.', ',');
        if (box && diffEl && msgEl) {
          if (valVigenteBacklogReparo <= metaBacklogReparoIdeal) {
            box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Backlog de Reparo dentro da meta estipulada (≤ 2).';
          } else {
            box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O Backlog de Reparo ultrapassou a meta ideal de 2.';
          }
        }

        var elDem = document.getElementById('calcDemandaReparo'); if (elDem) elDem.textContent = formatTableInteger(totalDemandaVigente);
        var elMed = document.getElementById('calcMedia7DiasReparo'); if (elMed) elMed.textContent = formatTableNumber(totalMedia7Vigente);

        var tbody = document.getElementById('tabelaBacklogReparoBody');
        if (tbody) {
          tbody.innerHTML = '';
          var dadosCidades = indicadoresCidadesData || [];
          if (nomeColab) {
            dadosCidades = dadosCidades.filter(function(linha) {
              var resp = getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']);
              return comparaNomeFlexivel(resp, nomeColab);
            });
          }

          if (!dadosCidades || dadosCidades.length === 0) {
            tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Backlog Reparo encontrado.</td></tr>';
          } else {
            dadosCidades.sort(function(a, b) {
              var demA = getNumeroLimpo(getValueSafe(a, ['DEMANDA REPARO', 'DEMANDA_REPARO'])), medA = getNumeroLimpo(getValueSafe(a, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO']));
              var backlogA = medA > 0 ? (demA / medA) : 0;
              var demB = getNumeroLimpo(getValueSafe(b, ['DEMANDA REPARO', 'DEMANDA_REPARO'])), medB = getNumeroLimpo(getValueSafe(b, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO']));
              var backlogB = medB > 0 ? (demB / medB) : 0;
              return backlogB - backlogA;
            });

            dadosCidades.forEach(function(linha) {
              var tr = document.createElement('tr');
              var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
              var rawDemanda = getValueSafe(linha, ['DEMANDA REPARO', 'DEMANDA_REPARO']), numDemanda = getNumeroLimpo(rawDemanda);
              var rawMedia7 = getValueSafe(linha, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO']), numMedia7 = getNumeroLimpo(rawMedia7);
              var calcBacklog = numMedia7 > 0 ? (numDemanda / numMedia7) : 0;

              [colCidade, formatTableInteger(rawDemanda), formatTableNumber(rawMedia7), calcBacklog.toFixed(2).replace('.', ',')].forEach(function(val, index) {
                var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
                if (index === 3 && val !== '-') td.className = calcBacklog <= metaBacklogReparoIdeal ? 'td-success' : 'td-danger';
                tr.appendChild(td);
              });
              tbody.appendChild(tr);
            });
          }
        }

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosBacklogReparo);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoBacklogReparo', 'meuGraficoBacklogReparo', 'Backlog Reparo', fitted.labels, fitted.dados, 5);
      }

      function renderizarConteudoModalBacklogInstalacao() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = selects['filterNome'] ? selects['filterNome'].getValue() : [];
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });

        var valoresHistoricosBacklogInstalacao = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumDemanda = 0, sumMedia7 = 0;
          subset.forEach(function(d) {
            sumDemanda += getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
            sumMedia7 += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
          });
          valoresHistoricosBacklogInstalacao.push(sumMedia7 > 0 ? Number((sumDemanda / sumMedia7).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var totalDemandaVigente = 0, totalMedia7Vigente = 0, valVigenteBacklogInstalacao = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            totalDemandaVigente += getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
            totalMedia7Vigente += getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
          });
          valVigenteBacklogInstalacao = totalMedia7Vigente > 0 ? (totalDemandaVigente / totalMedia7Vigente) : 0;
        }

        var box = document.getElementById('modalHighlightBoxBacklogInstalacao'), valEl = document.getElementById('modalValAtualBacklogInstalacao'), diffEl = document.getElementById('modalValDiffBacklogInstalacao'), msgEl = document.getElementById('modalValMsgBacklogInstalacao');
        if (valEl) valEl.textContent = valVigenteBacklogInstalacao.toFixed(2).replace('.', ',');
        if (box && diffEl && msgEl) {
          if (valVigenteBacklogInstalacao <= metaBacklogInstalacaoIdeal) {
            box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Backlog de Instalação dentro da meta estipulada (≤ 2).';
          } else {
            box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O Backlog de Instalação ultrapassou a meta ideal de 2.';
          }
        }

        var elDem = document.getElementById('calcDemandaInstalacao'); if (elDem) elDem.textContent = formatTableInteger(totalDemandaVigente);
        var elMed = document.getElementById('calcMedia7DiasInstalacao'); if (elMed) elMed.textContent = formatTableNumber(totalMedia7Vigente);

        var tbody = document.getElementById('tabelaBacklogInstalacaoBody');
        if (tbody) {
          tbody.innerHTML = '';
          var dadosCidades = indicadoresCidadesData || [];
          if (nomeColab) {
            dadosCidades = dadosCidades.filter(function(linha) {
              var resp = getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']);
              return comparaNomeFlexivel(resp, nomeColab);
            });
          }

          if (!dadosCidades || dadosCidades.length === 0) {
            tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Backlog Instalação encontrado.</td></tr>';
          } else {
            dadosCidades.sort(function(a, b) {
              var demA = getNumeroLimpo(getValueSafe(a, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO'])), medA = getNumeroLimpo(getValueSafe(a, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
              var backlogA = medA > 0 ? (demA / medA) : 0;
              var demB = getNumeroLimpo(getValueSafe(b, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO'])), medB = getNumeroLimpo(getValueSafe(b, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
              var backlogB = medB > 0 ? (demB / medB) : 0;
              return backlogB - backlogA;
            });

            dadosCidades.forEach(function(linha) {
              var tr = document.createElement('tr');
              var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
              var rawDemanda = getValueSafe(linha, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']), numDemanda = getNumeroLimpo(rawDemanda);
              var rawMedia7 = getValueSafe(linha, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']), numMedia7 = getNumeroLimpo(rawMedia7);
              var calcBacklog = numMedia7 > 0 ? (numDemanda / numMedia7) : 0;

              [colCidade, formatTableInteger(rawDemanda), formatTableNumber(rawMedia7), calcBacklog.toFixed(2).replace('.', ',')].forEach(function(val, index) {
                var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
                if (index === 3 && val !== '-') td.className = calcBacklog <= metaBacklogInstalacaoIdeal ? 'td-success' : 'td-danger';
                tr.appendChild(td);
              });
              tbody.appendChild(tr);
            });
          }
        }

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosBacklogInstalacao);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoBacklogInstalacao', 'meuGraficoBacklogInstalacao', 'Backlog Instalação', fitted.labels, fitted.dados, 5);
      }

      function renderizarConteudoModalBacklogIat() {
        var dataSemMes = getFilteredData(true), nomeSelecionado = selects['filterNome'] ? selects['filterNome'].getValue() : [];
        var nomeColab = Array.isArray(nomeSelecionado) ? nomeSelecionado[0] : nomeSelecionado;
        if (nomeColab) dataSemMes = dataSemMes.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var mesesUnicos = [];
        dataSemMes.forEach(function(d) { var m = formatarMes(d['MÊS']); if (m && mesesUnicos.indexOf(m) === -1) mesesUnicos.push(m); });
        var mesesOrdem = ['janeiro', 'fevereiro', 'março', 'abril', 'maio', 'junho', 'julho', 'agosto', 'setembro', 'outubro', 'novembro', 'dezembro'];
        mesesUnicos.sort(function(a, b) { var pA = a.split('/'), pB = b.split('/'); return (parseInt(pA[1], 10) !== parseInt(pB[1], 10)) ? parseInt(pA[1], 10) - parseInt(pB[1], 10) : mesesOrdem.indexOf(pA[0].toLowerCase()) - mesesOrdem.indexOf(pB[0].toLowerCase()); });

        var valoresHistoricosBacklogIat = [];
        mesesUnicos.forEach(function(mesStr) {
          var subset = dataSemMes.filter(function(d) { return formatarMes(d['MÊS']) === mesStr; }), sumDem = 0, sumMed = 0;
          subset.forEach(function(d) {
            var dInst = getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
            var dRep = getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
            var mInst = getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
            var mRep = getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));
            sumDem += (dInst + dRep);
            sumMed += (mInst + mRep);
          });
          valoresHistoricosBacklogIat.push(sumMed > 0 ? Number((sumDem / sumMed).toFixed(2)) : 0);
        });

        var subsetVigente = getFilteredData(false); if (nomeColab) subsetVigente = subsetVigente.filter(function(d) { return comparaNomeFlexivel(d['NOME'], nomeColab); });
        var totalDemandaVig = 0, totalMediaVig = 0, valVigenteBacklogIat = 0;
        if (subsetVigente.length > 0) {
          subsetVigente.forEach(function(d) {
            var dInst = getNumeroLimpo(getValueSafe(d, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']));
            var dRep = getNumeroLimpo(getValueSafe(d, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
            var mInst = getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']));
            var mRep = getNumeroLimpo(getValueSafe(d, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));
            totalDemandaVig += (dInst + dRep);
            totalMediaVig += (mInst + mRep);
          });
          valVigenteBacklogIat = totalMediaVig > 0 ? (totalDemandaVig / totalMediaVig) : 0;
        }

        var box = document.getElementById('modalHighlightBoxBacklogIat'), valEl = document.getElementById('modalValAtualBacklogIat'), diffEl = document.getElementById('modalValDiffBacklogIat'), msgEl = document.getElementById('modalValMsgBacklogIat');
        if (valEl) valEl.textContent = valVigenteBacklogIat.toFixed(2).replace('.', ',');
        if (box && diffEl && msgEl) {
          if (valVigenteBacklogIat <= metaBacklogIatIdeal) {
            box.className = 'modal-highlight-box box-success'; diffEl.className = 'modal-box-diff diff-positive'; diffEl.textContent = 'DENTRO DA META'; msgEl.textContent = 'Backlog IAT dentro da meta estipulada (≤ 2).';
          } else {
            box.className = 'modal-highlight-box box-danger'; diffEl.className = 'modal-box-diff diff-negative'; diffEl.textContent = 'ABAIXO DA META'; msgEl.textContent = 'Atenção! O Backlog IAT ultrapassou a meta ideal de 2.';
          }
        }

        var elDem = document.getElementById('calcDemandaIat'); if (elDem) elDem.textContent = formatTableInteger(totalDemandaVig);
        var elMed = document.getElementById('calcMedia7DiasIat'); if (elMed) elMed.textContent = formatTableNumber(totalMediaVig);

        var tbody = document.getElementById('tabelaBacklogIatBody');
        if (tbody) {
          tbody.innerHTML = '';
          var dadosCidades = indicadoresCidadesData || [];
          if (nomeColab) {
            dadosCidades = dadosCidades.filter(function(linha) {
              var resp = getValueSafe(linha, ['NOME', 'SUPERVISOR', 'GESTOR', 'COLABORADOR']);
              return comparaNomeFlexivel(resp, nomeColab);
            });
          }

          if (!dadosCidades || dadosCidades.length === 0) {
            tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--text-muted)">Nenhum registro de Backlog IAT encontrado.</td></tr>';
          } else {
            dadosCidades.sort(function(a, b) {
              var dInstA = getNumeroLimpo(getValueSafe(a, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO'])), dRepA = getNumeroLimpo(getValueSafe(a, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
              var mInstA = getNumeroLimpo(getValueSafe(a, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO'])), mRepA = getNumeroLimpo(getValueSafe(a, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));
              var backlogA = (mInstA + mRepA) > 0 ? ((dInstA + dRepA) / (mInstA + mRepA)) : 0;

              var dInstB = getNumeroLimpo(getValueSafe(b, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO'])), dRepB = getNumeroLimpo(getValueSafe(b, ['DEMANDA REPARO', 'DEMANDA_REPARO']));
              var mInstB = getNumeroLimpo(getValueSafe(b, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO'])), mRepB = getNumeroLimpo(getValueSafe(b, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']));
              var backlogB = (mInstB + mRepB) > 0 ? ((dInstB + dRepB) / (mInstB + mRepB)) : 0;

              return backlogB - backlogA;
            });

            dadosCidades.forEach(function(linha) {
              var tr = document.createElement('tr');
              var colCidade = getValueSafe(linha, ['CIDADE', 'CIDADE FILIAL']);
              var rawDInst = getValueSafe(linha, ['DEMANDA INSTALAÇÃO', 'DEMANDA INSTALACAO', 'DEMANDA_INSTALACAO']), numDInst = getNumeroLimpo(rawDInst);
              var rawMInst = getValueSafe(linha, ['MÉDIA 7 DIAS INSTALAÇÃO', 'MEDIA 7 DIAS INSTALACAO', 'MEDIA_7_DIAS_INSTALACAO']), numMInst = getNumeroLimpo(rawMInst);
              var rawDRep = getValueSafe(linha, ['DEMANDA REPARO', 'DEMANDA_REPARO']), numDRep = getNumeroLimpo(rawDRep);
              var rawMRep = getValueSafe(linha, ['MÉDIA 7 DIAS REPARO', 'MEDIA 7 DIAS REPARO', 'MEDIA_7_DIAS_REPARO']), numMRep = getNumeroLimpo(rawMRep);

              var calcBacklog = (numMInst + numMRep) > 0 ? ((numDInst + numDRep) / (numMInst + numMRep)) : 0;

              [colCidade, formatTableInteger(rawDInst), formatTableNumber(rawMInst), formatTableInteger(rawDRep), formatTableNumber(rawMRep), calcBacklog.toFixed(2).replace('.', ',')].forEach(function(val, index) {
                var td = document.createElement('td'); td.textContent = (val !== undefined && val !== null && val !== '') ? val : '-';
                if (index === 5 && val !== '-') td.className = calcBacklog <= metaBacklogIatIdeal ? 'td-success' : 'td-danger';
                tr.appendChild(td);
              });
              tbody.appendChild(tr);
            });
          }
        }

        var fitted = fitHistoricData(mesesUnicos, valoresHistoricosBacklogIat);
        renderizarGraficoHistoricoGenerico('graficoEvolucaoBacklogIat', 'meuGraficoBacklogIat', 'Backlog IAT', fitted.labels, fitted.dados, 5);
      }

      function sortTable(tbodyId, n, type) {
        var tbody = document.getElementById(tbodyId); if (!tbody) return;
        var rows = Array.from(tbody.rows); if (rows.length <= 1) return;
        var table = tbody.closest('table'), ths = table.getElementsByTagName("th");
        var dir = ths[n].classList.contains("dir-asc") ? "desc" : "asc";

        for (var j = 0; j < ths.length; j++) {
          ths[j].classList.remove("dir-asc", "dir-desc", "active-sort");
          var icon = ths[j].querySelector("i"); if (icon) icon.className = "fa-solid fa-sort";
        }
        ths[n].classList.add("dir-" + dir, "active-sort");
        var curIcon = ths[n].querySelector("i"); if (curIcon) curIcon.className = dir === "asc" ? "fa-solid fa-sort-up" : "fa-solid fa-sort-down";

        rows.sort(function(rowA, rowB) {
          var x = rowA.cells[n] ? rowA.cells[n].textContent.trim() : '';
          var y = rowB.cells[n] ? rowB.cells[n].textContent.trim() : '';
          if (type === 'num') {
            var valX = parseFloat(x.replace(/[^0-9,\.-]/g, '').replace(',', '.')) || -999999;
            var valY = parseFloat(y.replace(/[^0-9,\.-]/g, '').replace(',', '.')) || -999999;

            // Na coluna CERTIFICAÇÃO, usa ÍNDICE DE VISITAS como 2º critério (menor é melhor).
            if (tbodyId === 'tabelaRaioXBody' && n === 20 && Math.abs(valX - valY) < 0.000001) {
              var visitasX = parseFloat(rowA.dataset.indiceVisitasOrdenacao || '999999999');
              var visitasY = parseFloat(rowB.dataset.indiceVisitasOrdenacao || '999999999');
              if (isFinite(visitasX) && isFinite(visitasY) && Math.abs(visitasX - visitasY) > 0.000001) return visitasX - visitasY;
            }

            return dir === "asc" ? valX - valY : valY - valX;
          }
          return dir === "asc" ? x.localeCompare(y) : y.localeCompare(x);
        });
        rows.forEach(function(r) { tbody.appendChild(r); });
      }

      function resetSortIcons() {
        document.querySelectorAll("#modalPontoCabeca .analitica-table th").forEach(function(th, idx) {
          th.classList.remove("dir-asc", "dir-desc", "active-sort");
          var icon = th.querySelector("i"); if (icon) icon.className = "fa-solid fa-sort";
          if (idx === 4) { th.classList.add("dir-desc", "active-sort"); if (icon) icon.className = "fa-solid fa-sort-down"; }
        });
      }

      function renderizarGraficoHistoricoGenerico(canvasId, globalVarName, labelDataset, labels, dados, suggestedMax, formatType, mostrarZero) {
        var ctx = document.getElementById(canvasId); if (!ctx) return;
        var isDark = document.body.classList.contains('dark-mode'), textColor = isDark ? '#9ca3af' : '#6c757d', gridColor = isDark ? 'rgba(255,255,255,0.08)' : 'rgba(0,0,0,0.06)';
        if (window[globalVarName]) window[globalVarName].destroy();
        var isDuration = formatType === 'duracao';
        var formatChartValue = function(v) {
          if (isDuration) return segundosParaHHMMSS(Number(v) || 0);
          return labelDataset.indexOf('%') !== -1 ? Number(v).toFixed(2).replace('.', ',') + '%' : v;
        };

        window[globalVarName] = new Chart(ctx, {
          type: 'line',
          data: { labels: labels, datasets: [{ label: labelDataset, data: dados, borderColor: '#f4511e', backgroundColor: 'rgba(244, 81, 30, 0.1)', borderWidth: 3, pointBackgroundColor: '#f4511e', pointBorderColor: '#ffffff', pointBorderWidth: 2, pointRadius: 5, fill: true, tension: 0.3 }] },
          plugins: [ChartDataLabels],
          options: {
            responsive: true, maintainAspectRatio: false,
            layout: { padding: { top: 25, right: 25 } },
            plugins: {
              legend: { display: false },
              datalabels: {
                align: function(c) { return c.dataIndex === c.dataset.data.length - 1 ? 'left' : 'top'; },
                anchor: 'end', offset: 4,
                color: isDark ? '#e2e8f0' : '#2b3648',
                font: { family: 'Inter', size: 10, weight: 'bold' },
                formatter: function(v) { return (v > 0 || (mostrarZero && Number(v) === 0)) ? formatChartValue(v) : ''; }
              },
              tooltip: {
                callbacks: {
                  label: function(context) { return labelDataset + ': ' + formatChartValue(context.parsed.y); }
                }
              }
            },
            scales: {
              y: {
                beginAtZero: true, suggestedMax: suggestedMax, grid: { color: gridColor },
                ticks: {
                  color: textColor, font: { family: 'Inter', size: 11 },
                  callback: function(value) { return isDuration ? segundosParaHHMMSS(Number(value) || 0) : value; }
                }
              },
              x: { grid: { display: false }, ticks: { color: textColor, font: { family: 'Inter', size: 11 } } }
            }
          }
        });
      }

      function atualizarCoresGraficoGenerico(grafico, semAnimacao) {
        if (!grafico) return;
        var isDark = document.body.classList.contains('dark-mode');
        if (grafico.options.scales && grafico.options.scales.y) {
          grafico.options.scales.y.grid.color = isDark ? 'rgba(255,255,255,0.08)' : 'rgba(0,0,0,0.06)';
          grafico.options.scales.y.ticks.color = isDark ? '#9ca3af' : '#6c757d';
          grafico.options.scales.x.ticks.color = isDark ? '#9ca3af' : '#6c757d';
          if (grafico.options.plugins && grafico.options.plugins.datalabels) {
            grafico.options.plugins.datalabels.color = isDark ? '#e2e8f0' : '#2b3648';
          }
          grafico.update(semAnimacao ? 'none' : undefined);
        }
      }
    </script>
  
<style id="ranking-tooltip-final-fix">
/* ============================================================
   TOOLTIP FINAL — POSIÇÃO CORRETA
   Abre acima do ícone e não cobre o ranking.
   ============================================================ */

.ranking-executive-card {
  overflow: visible !important;
}

.ranking-executive-grid,
.ranking-executive-card-header,
.ranking-executive-card-title,
.ranking-name-line,
.ranking-tooltip {
  overflow: visible !important;
}

.ranking-tooltip {
  position: relative !important;
  z-index: 999999 !important;
}

.ranking-tooltip-content {
  position: absolute !important;

  /* FORÇA abertura para CIMA */
  top: auto !important;
  bottom: calc(100% + 12px) !important;

  /* Centralizado em relação ao ícone */
  left: 50% !important;
  right: auto !important;
  margin: 0 !important;

  transform: translate(-50%, 8px) !important;

  width: 285px !important;
  max-width: 285px !important;

  background: #ffffff !important;
  color: #26313a !important;
  border: 1px solid #d9dde3 !important;
  border-radius: 9px !important;
  box-shadow: 0 14px 30px rgba(23,35,48,.18) !important;

  z-index: 999999 !important;
}

.ranking-tooltip:hover .ranking-tooltip-content,
.ranking-tooltip:focus-within .ranking-tooltip-content {
  opacity: 1 !important;
  visibility: visible !important;
  transform: translate(-50%, 0) !important;
}

/* Seta na parte inferior do tooltip */
.ranking-tooltip-content::before {
  top: auto !important;
  bottom: -6px !important;
  left: 50% !important;
  right: auto !important;
  margin-left: -5px !important;

  width: 10px !important;
  height: 10px !important;

  background: #ffffff !important;
  border: 0 !important;
  border-right: 1px solid #d9dde3 !important;
  border-bottom: 1px solid #d9dde3 !important;

  transform: rotate(45deg) !important;
}

/* Não deixa o tooltip escuro no modo claro */
body:not(.dark-mode) .ranking-tooltip-content {
  background: #ffffff !important;
  color: #26313a !important;
}

body:not(.dark-mode) .ranking-tooltip-content::before {
  background: #ffffff !important;
}

/* No modo escuro mantém contraste */
body.dark-mode .ranking-tooltip-content,
body.tv-mode.dark-mode .ranking-tooltip-content,
body.dark-mode.tv-mode .ranking-tooltip-content {
  background: #171b20 !important;
  color: #f4f6f8 !important;
}

body.dark-mode .ranking-tooltip-content::before,
body.tv-mode.dark-mode .ranking-tooltip-content::before,
body.dark-mode.tv-mode .ranking-tooltip-content::before {
  background: #171b20 !important;
  border-right-color: #4a525a !important;
  border-bottom-color: #4a525a !important;
}
</style>



<style id="ranking-tooltip-definitive-fix">
/* ============================================================
   CORREÇÃO DEFINITIVA DO TOOLTIP DOS RANKINGS
   A regra específica .ranking-name-line estava vencendo o ajuste
   anterior. Aqui usamos a MESMA especificidade e sobrescrevemos
   explicitamente a posição.
   ============================================================ */

/* O cabeçalho/card não pode cortar o tooltip. */
.ranking-executive-card,
.ranking-executive-card-head,
.ranking-executive-card-title,
.ranking-name-line {
  overflow: visible !important;
}

/* Recolhimento e Multiskill — tooltip acima do ícone */
.ranking-name-line .ranking-tooltip {
  position: relative !important;
  overflow: visible !important;
  z-index: 999999 !important;
}

.ranking-name-line .ranking-tooltip-content {
  position: absolute !important;

  /* elimina completamente a posição abaixo */
  top: auto !important;
  bottom: calc(100% + 10px) !important;

  /* centraliza no ícone */
  left: 50% !important;
  right: auto !important;
  margin-left: 0 !important;
  margin-right: 0 !important;

  transform: translate(-50%, 6px) !important;

  width: 285px !important;
  max-width: 285px !important;

  z-index: 999999 !important;
  opacity: 0 !important;
  visibility: hidden !important;
  pointer-events: none !important;

  background: #ffffff !important;
  color: #26313a !important;
  border: 1px solid #d9dde3 !important;
  border-radius: 9px !important;
  box-shadow: 0 14px 30px rgba(23,35,48,.18) !important;
}

/* Hover/foco: aparece acima */
.ranking-name-line .ranking-tooltip:hover .ranking-tooltip-content,
.ranking-name-line .ranking-tooltip:focus-within .ranking-tooltip-content,
.ranking-name-line .ranking-tooltip:hover > .ranking-tooltip-content {
  opacity: 1 !important;
  visibility: visible !important;
  pointer-events: auto !important;
  transform: translate(-50%, 0) !important;
}

/* Seta fica embaixo do balão e aponta para o ícone */
.ranking-name-line .ranking-tooltip-content::before {
  top: auto !important;
  bottom: -6px !important;
  left: 50% !important;
  right: auto !important;

  width: 10px !important;
  height: 10px !important;
  margin-left: -5px !important;

  background: #ffffff !important;
  border: 0 !important;
  border-right: 1px solid #d9dde3 !important;
  border-bottom: 1px solid #d9dde3 !important;

  transform: rotate(45deg) !important;
}

/* Evita que o tooltip fique atrás das linhas do ranking */
.ranking-name-line .ranking-tooltip,
.ranking-name-line .ranking-tooltip-content {
  isolation: isolate !important;
}

/* Modo escuro */
body.dark-mode .ranking-name-line .ranking-tooltip-content,
body.tv-mode.dark-mode .ranking-name-line .ranking-tooltip-content,
body.dark-mode.tv-mode .ranking-name-line .ranking-tooltip-content {
  background: #171b20 !important;
  color: #eef1f3 !important;
}

body.dark-mode .ranking-name-line .ranking-tooltip-content::before,
body.tv-mode.dark-mode .ranking-name-line .ranking-tooltip-content::before,
body.dark-mode.tv-mode .ranking-name-line .ranking-tooltip-content::before {
  background: #171b20 !important;
  border-right-color: #4a525a !important;
  border-bottom-color: #4a525a !important;
}
</style>



<style id="ranking-tooltip-final-text-fix">
/* ============================================================
   TOOLTIP FINAL
   Corrige o texto "vazando" porque .ranking-name-line usa
   white-space: nowrap.
   ============================================================ */

.ranking-name-line .ranking-tooltip-content {
  box-sizing: border-box !important;
  white-space: normal !important;
  overflow: hidden !important;
  word-break: normal !important;
  overflow-wrap: break-word !important;
  hyphens: auto !important;

  width: 300px !important;
  max-width: 300px !important;
  min-width: 300px !important;

  padding: 12px 14px !important;
  line-height: 1.4 !important;
  text-align: left !important;

  top: auto !important;
  bottom: calc(100% + 10px) !important;
  left: 50% !important;
  right: auto !important;
  margin: 0 !important;
  transform: translate(-50%, 6px) !important;
}

.ranking-name-line .ranking-tooltip-content * {
  white-space: normal !important;
  overflow-wrap: break-word !important;
  word-break: normal !important;
  box-sizing: border-box !important;
}

.ranking-name-line .ranking-tooltip-title {
  display: flex !important;
  white-space: nowrap !important;
  width: 100% !important;
}

.ranking-name-line .ranking-tooltip-desc {
  display: block !important;
  width: 100% !important;
  white-space: normal !important;
  overflow-wrap: break-word !important;
  word-break: normal !important;
  line-height: 1.45 !important;
}

.ranking-name-line .ranking-tooltip-label {
  display: block !important;
  width: 100% !important;
  white-space: normal !important;
}

.ranking-name-line .ranking-tooltip-calc {
  display: block !important;
  width: 100% !important;
  white-space: normal !important;
  text-align: center !important;
  box-sizing: border-box !important;
}

.ranking-name-line .ranking-tooltip:hover .ranking-tooltip-content,
.ranking-name-line .ranking-tooltip:focus-within .ranking-tooltip-content {
  opacity: 1 !important;
  visibility: visible !important;
  pointer-events: auto !important;
  transform: translate(-50%, 0) !important;
}

.ranking-name-line .ranking-tooltip-content::before {
  top: auto !important;
  bottom: -6px !important;
  left: 50% !important;
  margin-left: -5px !important;
  width: 10px !important;
  height: 10px !important;
  background: #ffffff !important;
  border: 0 !important;
  border-right: 1px solid #d9dde3 !important;
  border-bottom: 1px solid #d9dde3 !important;
  transform: rotate(45deg) !important;
}
</style>



<script>
(function () {
  function forceRankingTitle() {
    document.querySelectorAll('.ranking-executive-title').forEach(function (title) {
      var spans = title.querySelectorAll('span');
      for (var i = 0; i < spans.length; i++) {
        if (!spans[i].classList.contains('ranking-crown')) {
          spans[i].textContent = 'RANKING';
        }
      }
    });
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', forceRankingTitle);
  } else {
    forceRankingTitle();
  }

  window.addEventListener('load', forceRankingTitle);
})();
</script>



<script id="ranking-labels-definitive-fix">
(function () {
  function corrigirCabecalhosRanking() {
    ['rankingMultiskillBoard', 'rankingRecolhimentoBoard'].forEach(function (id) {
      var board = document.getElementById(id);
      if (!board) return;
      var head = board.querySelector('.ranking-board-head');
      if (!head) return;
      var spans = head.querySelectorAll(':scope > span');
      var labels = ['POSIÇÃO', 'COLABORADOR', 'CERTIFICAÇÃO'];
      for (var i = 0; i < 3 && i < spans.length; i++) {
        if (spans[i].textContent.trim() !== labels[i]) {
          spans[i].textContent = labels[i];
        }
      }
    });
  }

  function iniciarCorrecaoRanking() {
    corrigirCabecalhosRanking();
    ['rankingMultiskillBoard', 'rankingRecolhimentoBoard'].forEach(function (id) {
      var board = document.getElementById(id);
      if (!board) return;
      var observer = new MutationObserver(function () {
        corrigirCabecalhosRanking();
      });
      observer.observe(board, { childList: true, subtree: true });
    });
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', iniciarCorrecaoRanking);
  } else {
    iniciarCorrecaoRanking();
  }
})();
</script>


<style id="ranking-icons-tv-fix">
/* MODO TV — os ícones/logotipos exclusivos do RANKING não aparecem na apresentação.
   O ranking continua funcionando normalmente; apenas os elementos visuais de marca
   ficam ocultos no Modo TV para não interferirem na barra/apresentação. */
body.tv-mode .ranking-executive-title .ranking-crown,
body.tv-mode .ranking-executive-card .ranking-executive-icon,
body.tv-mode .ranking-executive-card .ranking-tooltip,
body.tv-mode .ranking-executive-card .ranking-tooltip-trigger {
  display: none !important;
}


      /* =========================================================
         AJUSTE FINAL — KPIs COMPACTOS, MAS COM TAMANHO CONFORTÁVEL
         Mantém 8 cards nas telas grandes e adapta a quantidade
         de colunas nas telas menores, sem esmagar os conteúdos.
         ========================================================= */
      .kpi-grid {
        width: 100%;
        max-width: 100%;
        min-width: 0;
        display: grid;
        grid-template-columns: repeat(6, minmax(0, 1fr));
        gap: 10px;
      }

      .kpi-card {
        min-width: 0;
        min-height: 104px;
        height: 104px;
        padding: 11px 12px 9px 12px;
        border-radius: 10px;
        box-sizing: border-box;
      }

      .kpi-title {
        font-size: 9px;
        letter-spacing: 0.35px;
        gap: 5px;
        line-height: 1.15;
      }

      .kpi-title-icon {
        width: 19px;
        height: 19px;
        min-width: 19px;
        border-radius: 5px;
        font-size: 9px;
      }

      .kpi-info-btn {
        font-size: 11px;
        padding: 1px;
      }

      .kpi-body-row {
        margin-top: 4px;
        gap: 5px;
      }

      .kpi-value-container {
        gap: 3px;
        min-width: 0;
      }

      .kpi-value {
        font-size: 23px;
        letter-spacing: -0.4px;
      }

      .kpi-trend {
        font-size: 8.5px;
        gap: 3px;
        line-height: 1.05;
        white-space: nowrap;
      }

      .kpi-right-side span {
        font-size: 8.5px !important;
        line-height: 1.05;
        white-space: nowrap;
      }

      .kpi-progress-container {
        height: 4px;
        margin-top: 7px;
      }

      .section-title-kpi {
        font-size: 10px;
        margin-bottom: 8px;
        gap: 6px;
      }

      .kpi-category + .kpi-category {
        margin-top: 16px;
      }

      /* 1600px+: mantém o visual da tela grande original */
      @media (min-width: 1600px) {
        .kpi-grid {
          grid-template-columns: repeat(8, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* 1400–1599px */
      @media (max-width: 1599px) and (min-width: 1400px) {
        .kpi-grid {
          grid-template-columns: repeat(7, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* 1200–1399px — ex.: tela do seu último print */
      @media (max-width: 1399px) and (min-width: 1200px) {
        .kpi-grid {
          grid-template-columns: repeat(6, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* 1000–1199px */
      @media (max-width: 1199px) and (min-width: 1000px) {
        .kpi-grid {
          grid-template-columns: repeat(5, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* 800–999px */
      @media (max-width: 999px) and (min-width: 800px) {
        .kpi-grid {
          grid-template-columns: repeat(4, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* 600–799px */
      @media (max-width: 799px) and (min-width: 600px) {
        .kpi-grid {
          grid-template-columns: repeat(3, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* abaixo de 600px */
      @media (max-width: 599px) {
        .kpi-grid {
          grid-template-columns: repeat(2, minmax(0, 1fr));
          gap: 10px;
        }
      }

      /* No modo TV, também preserva o tamanho compacto sem deixar
         as regras antigas do TV esmagarem os cards. */
      body.tv-mode .kpi-card {
        min-height: 104px;
        height: 104px;
        padding: 11px 12px 9px 12px;
      }

      body.tv-mode .kpi-title {
        font-size: 9px;
      }

      body.tv-mode .kpi-value {
        font-size: 23px;
      }

      body.tv-mode .kpi-grid {
        grid-template-columns: repeat(5, minmax(0, 1fr));
        gap: 10px;
      }

      @media (min-width: 1600px) {
        body.tv-mode .kpi-grid {
          grid-template-columns: repeat(8, minmax(0, 1fr));
          gap: 10px;
        }
      }

      @media (max-width: 1599px) and (min-width: 1400px) {
        body.tv-mode .kpi-grid {
          grid-template-columns: repeat(7, minmax(0, 1fr));
        }
      }

      @media (max-width: 1399px) and (min-width: 1200px) {
        body.tv-mode .kpi-grid {
          grid-template-columns: repeat(6, minmax(0, 1fr));
        }
      }

</style>

<style id="ranking-fotos-layout-exato-v2">
/* ============================================================
   RANKING COM FOTOS — LAYOUT EXATO DO MOCKUP APROVADO
   - Todas as fotos com o MESMO tamanho
   - Foto entre posição e colaborador
   - Barra abaixo do nome
   - Certificação e estrelas em blocos separados
   ============================================================ */
.ranking-board-head,
.ranking-board-row {
  grid-template-columns: 82px minmax(0, 1fr) 132px !important;
}

.ranking-board-row {
  min-height: 48px !important;
  padding: 6px 10px !important;
}

.ranking-board-position {
  gap: 6px !important;
}

/* Todas as fotos exatamente do mesmo tamanho, inclusive 1º lugar */
.ranking-board-name-wrap,
.ranking-board-row.rank-1 .ranking-board-name-wrap,
body.tv-mode .ranking-board-name-wrap,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-name-wrap {
  grid-template-columns: 34px minmax(0, 1fr) !important;
  gap: 8px !important;
}

.ranking-board-avatar,
.ranking-board-row.rank-1 .ranking-board-avatar,
body.tv-mode .ranking-board-avatar,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar,
.ranking-board-avatar-fallback,
.ranking-board-row.rank-1 .ranking-board-avatar-fallback {
  width: 32px !important;
  height: 32px !important;
  flex: 0 0 32px !important;
  margin-left: 0 !important;
  border-width: 2px !important;
  font-size: 9px !important;
}

.ranking-board-name-content {
  gap: 5px !important;
}

.ranking-board-name,
.ranking-board-row.rank-1 .ranking-board-name {
  font-size: 10.5px !important;
  font-weight: 850 !important;
  line-height: 1.15 !important;
}

.ranking-mini-track {
  height: 5px !important;
  width: 100% !important;
  max-width: 100% !important;
}

/* Área de resultado: percentual separado das estrelas */
.ranking-board-award {
  height: 100%;
  min-width: 0;
  gap: 10px !important;
  padding-left: 8px;
  border-left: 0 !important;
}

.ranking-board-percent {
  min-width: 48px !important;
  font-size: 10px !important;
  text-align: right;
}

.ranking-board-stars {
  min-width: 52px;
  padding-left: 9px;
  border-left: 0 !important;
  color: #f5a623;
  font-size: 11px !important;
  letter-spacing: 0 !important;
  text-align: left;
}

.ranking-board-row.rank-1 .ranking-board-stars {
  font-size: 11px !important;
}

body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  border-left-color: transparent !important;
}

body.dark-mode .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-stars,
body.dark-mode.tv-mode .ranking-board-stars {
  border-left-color: transparent !important;
}

@media (max-width: 1100px) {
  .ranking-board-head,
  .ranking-board-row {
    grid-template-columns: 76px minmax(0, 1fr) 118px !important;
  }
  .ranking-board-row {
    min-height: 45px !important;
  }
}

body.tv-mode .ranking-board-head,
body.tv-mode .ranking-board-row {
  grid-template-columns: 76px minmax(0, 1fr) 118px !important;
}
body.tv-mode .ranking-board-row {
  min-height: 44px !important;
}
body.tv-mode .ranking-board-avatar,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar,
body.tv-mode .ranking-board-avatar-fallback,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar-fallback {
  width: 30px !important;
  height: 30px !important;
  flex-basis: 30px !important;
}
</style>
</body>


<style id="ranking-dark-mode-final-fix">
/* ============================================================
   RANKING — CORREÇÃO FINAL DO MODO ESCURO
   Mantém o ranking legível e elimina os fundos brancos.
   Não altera dados, ordem, percentuais ou barras.
   ============================================================ */

body.dark-mode .ranking-executive-card,
body.tv-mode.dark-mode .ranking-executive-card,
body.dark-mode.tv-mode .ranking-executive-card {
  background: #171b20 !important;
  border-color: #303841 !important;
  box-shadow: 0 8px 24px rgba(0,0,0,.38) !important;
}

body.dark-mode .ranking-executive-card-head,
body.tv-mode.dark-mode .ranking-executive-card-head,
body.dark-mode.tv-mode .ranking-executive-card-head {
  border-bottom-color: #303841 !important;
}

body.dark-mode .ranking-executive-card-title strong,
body.tv-mode.dark-mode .ranking-executive-card-title strong,
body.dark-mode.tv-mode .ranking-executive-card-title strong {
  color: #f4f7fa !important;
}

body.dark-mode .ranking-executive-card-title span,
body.tv-mode.dark-mode .ranking-executive-card-title span,
body.dark-mode.tv-mode .ranking-executive-card-title span,
body.dark-mode .ranking-board-head,
body.tv-mode.dark-mode .ranking-board-head,
body.dark-mode.tv-mode .ranking-board-head {
  color: #9da8b3 !important;
}

body.dark-mode .ranking-total-badge,
body.tv-mode.dark-mode .ranking-total-badge,
body.dark-mode.tv-mode .ranking-total-badge {
  background: #20262d !important;
  color: #d7dee5 !important;
  border-color: #39434d !important;
}

/* Linhas do ranking: fundo escuro uniforme, sem branco. */
body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: #222930 !important;
  border-color: #3a444e !important;
  color: #f4f7fa !important;
  box-shadow: none !important;
}

/* Destaques do pódio no Multiskill, em versão escura. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 {
  background: linear-gradient(90deg, #4b3a1d 0%, #30291f 58%, #222930 100%) !important;
  border-color: #b98b35 !important;
  box-shadow: 0 5px 16px rgba(0,0,0,.28) !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 {
  background: linear-gradient(90deg, #30373f 0%, #262d34 100%) !important;
  border-color: #56616c !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: linear-gradient(90deg, #3b3029 0%, #292e34 100%) !important;
  border-color: #665044 !important;
}

/* Texto principal: branco e com contraste forte. */
body.dark-mode .ranking-board-row .ranking-board-name,
body.dark-mode .ranking-board-row .ranking-board-place,
body.dark-mode .ranking-board-row .ranking-board-percent,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-name,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-place,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-percent,
body.dark-mode.tv-mode .ranking-board-row .ranking-board-name,
body.dark-mode.tv-mode .ranking-board-row .ranking-board-place,
body.dark-mode.tv-mode .ranking-board-row .ranking-board-percent {
  color: #f5f7fa !important;
  text-shadow: none !important;
}

body.dark-mode .ranking-board-row.rank-1 .ranking-board-place,
body.tv-mode.dark-mode .ranking-board-row.rank-1 .ranking-board-place,
body.dark-mode.tv-mode .ranking-board-row.rank-1 .ranking-board-place {
  color: #ffd66b !important;
}

/* Estrelas continuam destacadas. */
body.dark-mode .ranking-board-row .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-row .ranking-board-stars,
body.dark-mode.tv-mode .ranking-board-row .ranking-board-stars {
  color: #ffcf78 !important;
  text-shadow: 0 1px 4px rgba(0,0,0,.35) !important;
}

/* Quadradinhos de posição: deixam de ser brancos no modo escuro. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-number,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-number {
  background: #303841 !important;
  color: #dce3e9 !important;
  border-color: #4b5661 !important;
  box-shadow: none !important;
}

/* 1º lugar mantém a medalha dourada. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-number,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-number {
  background: linear-gradient(135deg, #ffd66b, #e9a52b) !important;
  color: #ffffff !important;
  border-color: #e7a72f !important;
}

/* 2º e 3º do Multiskill. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-number {
  background: #59636f !important;
  color: #ffffff !important;
  border-color: #707b87 !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-number {
  background: #765745 !important;
  color: #ffffff !important;
  border-color: #8b6a55 !important;
}

/* Ícone do cabeçalho também deixa de ser branco. */
body.dark-mode .ranking-executive-card.multiskill .ranking-executive-icon,
body.dark-mode .ranking-executive-card.recolhimento .ranking-executive-icon,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-executive-icon,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-executive-icon,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-executive-icon,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-executive-icon {
  background: #252c33 !important;
  border-color: #3d4751 !important;
  color: #ff9a62 !important;
  box-shadow: none !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-executive-icon i,
body.dark-mode .ranking-executive-card.recolhimento .ranking-executive-icon i,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-executive-icon i,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-executive-icon i {
  color: #ff9a62 !important;
}

/* Barra de progresso: laranja, mas com contraste controlado. */
body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  background: linear-gradient(90deg, rgba(244,81,30,.32), rgba(244,81,30,.08)) !important;
  border-right-color: rgba(255,154,98,.45) !important;
  box-shadow: none !important;
}

body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, rgba(244,166,111,.36), rgba(244,166,111,.10)) !important;
  border-right-color: rgba(255,195,150,.5) !important;
}

/* Linha ativa/hover sem voltar ao branco. */
body.dark-mode .ranking-board-row.ranking-active,
body.dark-mode .ranking-board-row:hover,
body.tv-mode.dark-mode .ranking-board-row.ranking-active,
body.tv-mode.dark-mode .ranking-board-row:hover,
body.dark-mode.tv-mode .ranking-board-row.ranking-active,
body.dark-mode.tv-mode .ranking-board-row:hover {
  background: #293139 !important;
  border-color: #f28c55 !important;
  box-shadow: 0 6px 18px rgba(0,0,0,.35), inset 3px 0 0 #f28c55 !important;
}
</style>


<style id="ranking-multiskill-palette-recolhimento-v4">
/* AJUSTE V4 — SOMENTE CORES DO PÓDIO MULTISKILL
   O 1º, 2º e 3º lugar usam exatamente a mesma identidade de cores
   visual do pódio do RECOLHIMENTO no modo escuro.
   Nenhuma estrutura, tamanho, posição, dado ou lógica foi alterada. */

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 {
  background: linear-gradient(90deg, #D06E44 0%, #E2875F 34%, #EDA681 59%, #F6C4A9 100%) !important;
  border-color: #F08E61 !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: linear-gradient(90deg, #6F3B29 0%, #804633 100%) !important;
  border-color: #B96A45 !important;
}

/* Apenas a cor do preenchimento acompanha a mesma paleta. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1::before,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1::before,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1::before,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3::before,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3::before,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3::before {
  background: linear-gradient(90deg, #D85E24 0%, #EA7A3D 48%, #F29A62 100%) !important;
  border-right-color: #FFAD78 !important;
}
</style>


<style id="ranking-fotos-colaboradores-v1">
/* ============================================================
   RANKING COM FOTO — acabamento executivo
   Foto entre posição e nome, sem alterar a estrutura dos cards.
   ============================================================ */
.ranking-board-name-wrap {
  min-width: 0;
  display: grid !important;
  grid-template-columns: 34px minmax(0, 1fr);
  align-items: center;
  gap: 9px;
}
.ranking-board-avatar {
  width: 32px;
  height: 32px;
  flex: 0 0 32px;
  border-radius: 50%;
  object-fit: cover;
  object-position: center;
  display: block;
  background: #f1f3f5;
  border: 2px solid rgba(255,255,255,.96);
  box-shadow: 0 2px 7px rgba(0,0,0,.14), 0 0 0 1px rgba(217,75,0,.16);
}
.ranking-board-row.rank-1 .ranking-board-avatar {
  width: 40px;
  height: 40px;
  margin-left: -4px;
  border-width: 2px;
  box-shadow: 0 3px 9px rgba(214,158,30,.24), 0 0 0 1px rgba(244,173,24,.35);
}
.ranking-board-row.rank-1 .ranking-board-name-wrap {
  grid-template-columns: 40px minmax(0, 1fr);
}
.ranking-board-name-content {
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.ranking-mini-track {
  height: 4px !important;
  width: 100% !important;
  max-width: 100% !important;
  border-radius: 99px;
  overflow: hidden;
  background: rgba(127,127,127,.13);
}
.ranking-mini-fill {
  height: 100%;
  border-radius: inherit;
  background: linear-gradient(90deg, #f4511e, #ffb300) !important;
  transition: width 1.15s cubic-bezier(.22,.8,.2,1);
}
.ranking-executive-card.recolhimento .ranking-mini-fill {
  background: linear-gradient(90deg, #f4511e, #ffb300) !important;
}
.ranking-board-avatar-fallback {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #fff0e5, #ffd2b5);
  color: #d94b00;
  border: 2px solid rgba(255,255,255,.96);
  box-shadow: 0 2px 7px rgba(0,0,0,.12);
  font-size: 9px;
  font-weight: 950;
  text-transform: uppercase;
}
.ranking-board-row.rank-1 .ranking-board-avatar-fallback {
  width: 40px;
  height: 40px;
  margin-left: -4px;
  font-size: 10px;
}
body.dark-mode .ranking-board-avatar,
body.tv-mode.dark-mode .ranking-board-avatar,
body.dark-mode.tv-mode .ranking-board-avatar {
  border-color: #252a30;
  background: #30353b;
  box-shadow: 0 2px 8px rgba(0,0,0,.32), 0 0 0 1px rgba(255,171,117,.18);
}
body.dark-mode .ranking-board-avatar-fallback,
body.tv-mode.dark-mode .ranking-board-avatar-fallback,
body.dark-mode.tv-mode .ranking-board-avatar-fallback {
  background: linear-gradient(135deg, #4a3024, #5b3827);
  color: #ffbd91;
  border-color: #2c3137;
}
@media (max-width: 1100px) {
  .ranking-board-name-wrap { grid-template-columns: 30px minmax(0,1fr); gap: 7px; }
  .ranking-board-avatar { width: 28px; height: 28px; }
  .ranking-board-row.rank-1 .ranking-board-name-wrap { grid-template-columns: 34px minmax(0,1fr); }
  .ranking-board-row.rank-1 .ranking-board-avatar { width: 34px; height: 34px; }
}
body.tv-mode .ranking-board-name-wrap { grid-template-columns: 30px minmax(0,1fr); gap: 7px; }
body.tv-mode .ranking-board-avatar { width: 28px; height: 28px; }
body.tv-mode .ranking-board-row.rank-1 .ranking-board-name-wrap { grid-template-columns: 34px minmax(0,1fr); }
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar { width: 34px; height: 34px; }
body.tv-mode .ranking-mini-track { height: 3px !important; }
</style>

</html>
<style id="ranking-dark-mode-final-v2">
/* ============================================================
   RANKING — AJUSTE FINAL V2
   Corrige os últimos pontos de contraste no modo escuro.
   Especialmente MULTISKILL: títulos, 1º/2º/3º lugares,
   nomes, posições e percentuais ficam brancos.
   ============================================================ */

body.dark-mode .ranking-executive-card,
body.tv-mode.dark-mode .ranking-executive-card,
body.dark-mode.tv-mode .ranking-executive-card {
  background: #171b20 !important;
}

/* Cabeçalho dos dois rankings: elimina qualquer branco residual. */
body.dark-mode .ranking-executive-card .ranking-executive-card-head,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-head,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-head {
  background: #171b20 !important;
  color: #ffffff !important;
  border-bottom-color: #303841 !important;
}

body.dark-mode .ranking-executive-card .ranking-executive-card-title strong,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-title strong,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-title strong {
  color: #ffffff !important;
}

body.dark-mode .ranking-executive-card .ranking-executive-card-title span,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-title span,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-title span {
  color: #aeb8c2 !important;
}

/* MULTISKILL — todos os textos da linha ficam brancos. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent {
  color: #ffffff !important;
  text-shadow: 0 1px 2px rgba(0,0,0,.28) !important;
}

/* 1º lugar: inclusive "01º LUGAR" fica branco. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent {
  color: #ffffff !important;
}

/* Texto dentro do quadradinho/medalha também branco. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-number,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-number,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-number {
  color: #ffffff !important;
}

/* 2º e 3º: remove qualquer cor herdada cinza/marrom. */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-percent,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-percent {
  color: #ffffff !important;
}

/* Recolhimento fica com o mesmo padrão de leitura. */
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-name,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-place,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row .ranking-board-percent {
  color: #ffffff !important;
}
</style>

<style id="ranking-dark-mode-final-v3">
/* ============================================================
   RANKING + GRÁFICO DE QUARTIS — AJUSTE FINAL V3
   1) Remove branco residual do topo dos dois rankings.
   2) MULTISKILL usa a mesma paleta escura do RECOLHIMENTO.
   3) Legendas do gráfico de quartis ficam brancas no modo escuro.
   ============================================================ */

/* TOPO DOS DOIS RANKINGS — ZERO FUNDO BRANCO */
body.dark-mode .ranking-executive-card .ranking-executive-card-head,
body.dark-mode .ranking-executive-card .ranking-executive-card-title,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-head,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-title,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-head,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-title {
  background: #171b20 !important;
  color: #ffffff !important;
}

body.dark-mode .ranking-executive-card .ranking-executive-card-title strong,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-title strong,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-title strong {
  color: #ffffff !important;
}

body.dark-mode .ranking-executive-card .ranking-executive-card-title span,
body.tv-mode.dark-mode .ranking-executive-card .ranking-executive-card-title span,
body.dark-mode.tv-mode .ranking-executive-card .ranking-executive-card-title span {
  color: #bfc7cf !important;
}

/* MULTISKILL — mesma base visual escura do RECOLHIMENTO */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row {
  background: linear-gradient(90deg, #4a382f 0%, #3c322d 58%, #292d31 100%) !important;
  border-color: #65564d !important;
}

/* 1º, 2º e 3º também seguem a mesma identidade do RECOLHIMENTO */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 {
  background: linear-gradient(90deg, #f39a6d 0%, #e9895c 55%, #d97745 100%) !important;
  border-color: #f0a06f !important;
}

body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: linear-gradient(90deg, #4a382f 0%, #3c322d 58%, #292d31 100%) !important;
  border-color: #65564d !important;
}

/* Texto das linhas — branco nos dois rankings */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-name,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-place,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row .ranking-board-percent {
  color: #ffffff !important;
}

/* 1º lugar: inclusive a posição fica branca */
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-place,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-name,
body.dark-mode.tv-mode .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent {
  color: #ffffff !important;
}

/* ============================================================
   GRÁFICO — LEGENDA BRANCA NO MODO ESCURO
   Chart.js desenha a legenda no canvas, então CSS sozinho não resolve.
   A função atualizarCoresGraficoGenerico abaixo também foi sobrescrita.
   ============================================================ */
body.dark-mode .quartil-chart-card,
body.dark-mode .chart-quartil-card {
  color: #ffffff !important;
}
</style>

<script id="ranking-quartil-dark-legend-v5">
(function () {
  /* Força EXCLUSIVAMENTE a legenda do gráfico de quartis para branco no modo escuro. */
  function forcarLegendaQuartilBranca() {
    var g = window.meuGraficoQuartilLideranca;
    if (!g || !g.options || !g.options.plugins || !g.options.plugins.legend) return;
    var isDark = document.body.classList.contains('dark-mode');
    var cor = isDark ? '#ffffff' : '#2b3648';
    g.options.plugins.legend.labels.color = cor;
    if (g.legend && g.legend.options && g.legend.options.labels) {
      g.legend.options.labels.color = cor;
      if (g.legend.legendItems) {
        g.legend.legendItems.forEach(function(item) {
          item.fontColor = cor;
          item.color = cor;
        });
      }
    }
  }

  function atualizarGraficoQuartilTema() {
    var g = window.meuGraficoQuartilLideranca;
    if (!g) return;

    var isDark = document.body.classList.contains('dark-mode');
    var corTexto = isDark ? '#ffffff' : '#2b3648';
    var corEixo = isDark ? '#e2e8f0' : '#6c757d';

    if (g.options.plugins && g.options.plugins.legend && g.options.plugins.legend.labels) {
      g.options.plugins.legend.labels.color = corTexto;
    }
    if (g.legend && g.legend.options && g.legend.options.labels) {
      g.legend.options.labels.color = corTexto;
    }

    if (g.options.scales) {
      if (g.options.scales.x && g.options.scales.x.ticks) g.options.scales.x.ticks.color = corEixo;
      if (g.options.scales.y && g.options.scales.y.ticks) g.options.scales.y.ticks.color = corEixo;
      if (g.options.scales.y && g.options.scales.y.grid) {
        g.options.scales.y.grid.color = isDark ? 'rgba(255,255,255,0.08)' : 'rgba(0,0,0,0.06)';
      }
    }

    g.update('none');
    forcarLegendaQuartilBranca();
  }

  var original = window.atualizarCoresGraficoGenerico;
  if (typeof original === 'function') {
    window.atualizarCoresGraficoGenerico = function (grafico, semAnimacao) {
      original(grafico, semAnimacao);
      if (grafico === window.meuGraficoQuartilLideranca) {
        atualizarGraficoQuartilTema();
      }
    };
  }

  document.addEventListener('DOMContentLoaded', function () {
    setTimeout(atualizarGraficoQuartilTema, 250);
    setTimeout(forcarLegendaQuartilBranca, 600);
  });
  window.addEventListener('load', function () {
    setTimeout(atualizarGraficoQuartilTema, 250);
    setTimeout(forcarLegendaQuartilBranca, 600);
  });
})();
</script>


<script id="quartil-legend-initial-fix-v6">
(function () {
  /*
   * CORREÇÃO DEFINITIVA:
   * O gráfico pode ser criado antes de a classe dark-mode chegar ao <body>.
   * Nesse caso o Chart.js nasce com a cor clara da legenda e só corrige
   * quando o usuário interage. Este observador reaplica a cor assim que
   * o tema inicial é aplicado, sem alterar dados ou estrutura do gráfico.
   */
  function corrigirLegendaQuartilInicial() {
    var g = window.meuGraficoQuartilLideranca;
    if (!g) return;

    var isDark = document.body && document.body.classList.contains('dark-mode');
    var corLegenda = isDark ? '#ffffff' : '#2b3648';
    var corEixo = isDark ? '#e2e8f0' : '#6c757d';

    try {
      if (g.options && g.options.plugins && g.options.plugins.legend && g.options.plugins.legend.labels) {
        g.options.plugins.legend.labels.color = corLegenda;
      }

      if (g.legend && g.legend.options && g.legend.options.labels) {
        g.legend.options.labels.color = corLegenda;
      }

      if (g.legend && Array.isArray(g.legend.legendItems)) {
        g.legend.legendItems.forEach(function (item) {
          item.fontColor = corLegenda;
          item.color = corLegenda;
        });
      }

      if (g.options && g.options.scales) {
        if (g.options.scales.x && g.options.scales.x.ticks) {
          g.options.scales.x.ticks.color = corEixo;
        }
        if (g.options.scales.y && g.options.scales.y.ticks) {
          g.options.scales.y.ticks.color = corEixo;
        }
      }

      g.update('none');
    } catch (e) {}
  }

  /* Executa várias vezes durante a inicialização porque os dados do painel
     chegam de forma assíncrona e o gráfico pode nascer depois do DOM. */
  function agendarCorrecaoQuartil() {
    corrigirLegendaQuartilInicial();
    requestAnimationFrame(corrigirLegendaQuartilInicial);
    setTimeout(corrigirLegendaQuartilInicial, 100);
    setTimeout(corrigirLegendaQuartilInicial, 300);
    setTimeout(corrigirLegendaQuartilInicial, 700);
    setTimeout(corrigirLegendaQuartilInicial, 1200);
  }

  document.addEventListener('DOMContentLoaded', agendarCorrecaoQuartil);
  window.addEventListener('load', agendarCorrecaoQuartil);

  /* Se o modo escuro for aplicado depois que o gráfico já nasceu,
     detecta a mudança da classe no <body> imediatamente. */
  function iniciarObservadorTema() {
    if (!document.body || !window.MutationObserver) return;

    var observer = new MutationObserver(function (mutations) {
      for (var i = 0; i < mutations.length; i++) {
        if (mutations[i].type === 'attributes' && mutations[i].attributeName === 'class') {
          agendarCorrecaoQuartil();
          break;
        }
      }
    });

    observer.observe(document.body, { attributes: true, attributeFilter: ['class'] });
  }

  if (document.body) {
    iniciarObservadorTema();
  } else {
    document.addEventListener('DOMContentLoaded', iniciarObservadorTema);
  }
})();
</script>

<style id="ranking-mockup-exato-correcao-final">
/* ============================================================
   RANKING — CORREÇÃO VISUAL PARA FICAR IGUAL AO MOCKUP APROVADO
   - A barra fica VISÍVEL abaixo do nome.
   - O preenchimento não colore a linha inteira.
   - Linha com fundo pêssego suave.
   - Área de certificação separada à direita.
   - Estrelas em bloco próprio, depois da certificação.
   - Todas as fotos permanecem exatamente do mesmo tamanho.
   ============================================================ */

/* A linha não deve mais usar o percentual como preenchimento de fundo. */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  display: none !important;
  width: 0 !important;
  background: none !important;
  border-right: 0 !important;
}

.ranking-board-row {
  grid-template-columns: 82px minmax(0, 1fr) 178px !important;
  min-height: 48px !important;
  padding: 6px 10px !important;
  background: linear-gradient(90deg, #ffd5b5 0%, #fbd8bd 58%, #f8e9df 100%) !important;
  border: 1px solid #f1b98f !important;
  box-sizing: border-box;
}

/* Mantém o destaque do pódio, mas sem esconder a barra real. */
.ranking-board-row.rank-1,
.ranking-executive-card.multiskill .ranking-board-row.rank-2,
.ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: linear-gradient(90deg, #ffd0a9 0%, #fbd4b7 58%, #fae9df 100%) !important;
}

.ranking-board-row.rank-1 {
  min-height: 48px !important;
  border-color: #f2a15f !important;
  box-shadow: 0 4px 12px rgba(244,129,49,.12) !important;
}

/* Foto e nome seguem exatamente a composição do mockup. */
.ranking-board-name-wrap,
.ranking-board-row.rank-1 .ranking-board-name-wrap,
body.tv-mode .ranking-board-name-wrap,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-name-wrap {
  grid-template-columns: 34px minmax(0, 1fr) !important;
  gap: 9px !important;
  align-items: center !important;
}

.ranking-board-avatar,
.ranking-board-row.rank-1 .ranking-board-avatar,
.ranking-board-avatar-fallback,
.ranking-board-row.rank-1 .ranking-board-avatar-fallback,
body.tv-mode .ranking-board-avatar,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar,
body.tv-mode .ranking-board-avatar-fallback,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar-fallback {
  width: 32px !important;
  height: 32px !important;
  flex: 0 0 32px !important;
  margin-left: 0 !important;
  border-radius: 50% !important;
}

.ranking-board-name-content {
  min-width: 0 !important;
  display: flex !important;
  flex-direction: column !important;
  justify-content: center !important;
  gap: 5px !important;
}

.ranking-board-name,
.ranking-board-row.rank-1 .ranking-board-name {
  font-size: 10.5px !important;
  font-weight: 850 !important;
  line-height: 1.15 !important;
  white-space: nowrap !important;
  overflow: hidden !important;
  text-overflow: ellipsis !important;
}

/* Barra real: trilho claro + preenchimento laranja. */
.ranking-mini-track {
  display: block !important;
  width: 100% !important;
  max-width: 100% !important;
  height: 6px !important;
  border-radius: 999px !important;
  overflow: hidden !important;
  background: rgba(255,255,255,.72) !important;
  box-shadow: inset 0 0 0 1px rgba(255,255,255,.28) !important;
}

.ranking-mini-fill,
.ranking-executive-card.recolhimento .ranking-mini-fill {
  display: block !important;
  height: 100% !important;
  border-radius: inherit !important;
  background: linear-gradient(90deg, #f36c16 0%, #ff8a1f 100%) !important;
  box-shadow: 0 1px 3px rgba(244,81,30,.22) !important;
}

/* Área da certificação: bloco claro separado da área do colaborador. */
.ranking-board-award {
  align-self: stretch !important;
  min-width: 0 !important;
  margin: -6px -10px -6px 0 !important;
  padding: 6px 10px 6px 12px !important;
  display: grid !important;
  grid-template-columns: minmax(48px, 1fr) 62px !important;
  align-items: center !important;
  gap: 10px !important;
  background: rgba(255,250,246,.78) !important;
  border-left: 1px solid rgba(205,112,55,.25) !important;
  border-radius: 0 9px 9px 0 !important;
}

.ranking-board-percent {
  min-width: 0 !important;
  color: var(--text-main) !important;
  font-size: 10px !important;
  font-weight: 950 !important;
  text-align: right !important;
  white-space: nowrap !important;
}

/* Estrelas em um bloco próprio, depois da certificação. */
.ranking-board-stars {
  min-width: 0 !important;
  padding-left: 10px !important;
  border-left: 1px solid rgba(170,120,90,.28) !important;
  color: #f28a18 !important;
  font-size: 10px !important;
  line-height: 1 !important;
  letter-spacing: 0 !important;
  text-align: left !important;
  white-space: nowrap !important;
}

.ranking-board-row.rank-1 .ranking-board-stars {
  font-size: 10.5px !important;
}

/* Remove qualquer coloração verde específica do Recolhimento. */
.ranking-executive-card.recolhimento .ranking-board-row {
  background: linear-gradient(90deg, #ffd5b5 0%, #fbd8bd 58%, #f8e9df 100%) !important;
  border-color: #f1b98f !important;
}

/* Cabeçalho alinhado com a nova composição. */
.ranking-board-head {
  grid-template-columns: 82px minmax(0, 1fr) 178px !important;
}

/* Modo escuro: preserva a estrutura, sem perder a barra. */
body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
  background: linear-gradient(90deg, #5d3425 0%, #68402f 58%, #392b26 100%) !important;
  border-color: #925336 !important;
}

body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  background: rgba(28,31,35,.82) !important;
  border-left-color: rgba(255,255,255,.14) !important;
}

body.dark-mode .ranking-mini-track,
body.tv-mode.dark-mode .ranking-mini-track,
body.dark-mode.tv-mode .ranking-mini-track {
  background: rgba(255,255,255,.16) !important;
}

body.dark-mode .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-stars,
body.dark-mode.tv-mode .ranking-board-stars {
  border-left-color: rgba(255,255,255,.14) !important;
}

@media (max-width: 1100px) {
  .ranking-board-head,
  .ranking-board-row {
    grid-template-columns: 76px minmax(0, 1fr) 156px !important;
  }
  .ranking-board-award {
    grid-template-columns: minmax(44px, 1fr) 56px !important;
    gap: 7px !important;
  }
}

body.tv-mode .ranking-board-head,
body.tv-mode .ranking-board-row {
  grid-template-columns: 76px minmax(0, 1fr) 156px !important;
}
</style>


<style id="tabela-sem-setinhas-final">
/* Remove APENAS os indicadores visuais de ordenação da tabela.
   A função de ordenação por clique continua preservada. */
table th .sort-arrow,
table th .sort-icon,
table th .sort-indicator,
table th i.fa-sort,
table th i.fa-sort-up,
table th i.fa-sort-down,
table th .material-icons,
table th [class*="sort"] {
  display: none !important;
}
table th::after,
table th::before {
  content: none !important;
  display: none !important;
}
</style>


<script id="historico-producao-toggle-final">
function toggleHistoricoQuartilProducao() {
  var trigger = document.getElementById('quartilProducaoCollapsed');
  var content = document.getElementById('quartilProducaoContent');
  if (!trigger || !content) return;
  var aberto = !content.hidden;
  content.hidden = aberto;
  trigger.setAttribute('aria-expanded', String(!aberto));
  var acao = trigger.querySelector('.quartil-producao-collapsed-action');
  if (acao) {
    acao.innerHTML = aberto
      ? '<i class="fa-solid fa-chevron-down"></i> Ver histórico'
      : '<i class="fa-solid fa-chevron-up"></i> Ocultar histórico';
  }
}
function recolherHistoricoQuartilProducao() {
  var trigger = document.getElementById('quartilProducaoCollapsed');
  var content = document.getElementById('quartilProducaoContent');
  if (!trigger || !content) return;
  content.hidden = true;
  trigger.setAttribute('aria-expanded', 'false');
  var acao = trigger.querySelector('.quartil-producao-collapsed-action');
  if (acao) acao.innerHTML = '<i class="fa-solid fa-chevron-down"></i> Ver histórico';
}
</script>

<style id="footer-institucional">
.dashboard-footer{width:100%;box-sizing:border-box;margin-top:28px;border-top:2px solid #f15a24;background:#fff;color:#5f6875;box-shadow:0 -2px 12px rgba(16,36,58,.05);overflow:hidden}
.dashboard-footer-main{width:100%;min-height:72px;padding:9px 22px 8px;box-sizing:border-box;display:flex;align-items:center;justify-content:flex-start;gap:24px}
.dashboard-footer-brand{display:flex;align-items:center;min-width:0}.dashboard-footer-logo{width:230px;height:46px;object-fit:cover;object-position:center center;display:block;flex:0 0 auto;border-radius:6px}.dashboard-footer-author-top{margin-left:auto;flex:0 0 auto;text-align:right;white-space:nowrap;font-size:10px;line-height:1.2;color:#53606e;font-weight:600;padding:5px 16px;border-right:1px solid #dfe4e9}.dashboard-footer-author-top strong{color:#e85a22;font-weight:800}.dashboard-footer-author-top .footer-author-group{color:#e85a22;font-weight:800}
.dashboard-footer-institutional{text-align:right;min-width:0}.dashboard-footer-institutional strong{display:block;font-size:10px;line-height:1.25;font-weight:800;color:#e85a22}.dashboard-footer-institutional span{display:block;margin-top:3px;font-size:9px;line-height:1.2;color:#6f7885;font-weight:600}
.dashboard-footer-bottom{min-height:39px;padding:5px 22px;box-sizing:border-box;border-top:1px solid #dde2e7;background:#eef1f5;display:flex;align-items:center;justify-content:space-between;gap:20px}.dashboard-footer-privacy{min-width:0;font-size:9.5px;line-height:1.35;color:#687280;font-weight:500}.dashboard-footer-author{flex:0 0 auto;white-space:nowrap;font-size:10px;line-height:1.2;color:#53606e;font-weight:600;background:#e5e9ee;border-left:3px solid #f15a24;border-radius:5px;padding:6px 11px;box-sizing:border-box;box-shadow:0 1px 3px rgba(16,36,58,.06)}.dashboard-footer-author strong{color:#e85a22;font-weight:800}.dashboard-footer-author .footer-author-group{color:#e85a22;font-weight:800}
body.dark-mode .dashboard-footer,body.dark .dashboard-footer,body.tv-mode.dark-mode .dashboard-footer,body.tv-mode.dark .dashboard-footer{background:#081a29;color:#cbd5df;border-top-color:#f15a24;box-shadow:0 -2px 14px rgba(0,0,0,.22)}body.dark-mode .dashboard-footer-institutional strong,body.dark .dashboard-footer-institutional strong{color:#ff9a62}body.dark-mode .dashboard-footer-institutional span,body.dark .dashboard-footer-institutional span{color:#aeb9c5}body.dark-mode .dashboard-footer-bottom,body.dark .dashboard-footer-bottom{background:#0d202f;border-top-color:#263847}body.dark-mode .dashboard-footer-privacy,body.dark .dashboard-footer-privacy{color:#aeb9c5}body.dark-mode .dashboard-footer-author,body.dark .dashboard-footer-author{color:#d2dbe4;background:#132a3b;border-left-color:#ff7b3f;box-shadow:0 1px 4px rgba(0,0,0,.2)}body.dark-mode .dashboard-footer-author strong,body.dark .dashboard-footer-author strong{color:#ff9a62}body.dark-mode .dashboard-footer-author .footer-author-group,body.dark .dashboard-footer-author .footer-author-group{color:#ff9a62}body.dark-mode .dashboard-footer-author-top,body.dark .dashboard-footer-author-top{color:#d2dbe4;border-right-color:#344553}body.dark-mode .dashboard-footer-author-top strong,body.dark .dashboard-footer-author-top strong,body.dark-mode .dashboard-footer-author-top .footer-author-group,body.dark .dashboard-footer-author-top .footer-author-group{color:#ff9a62}
body.tv-mode .dashboard-footer{margin-top:10px}body.tv-mode .dashboard-footer-main{min-height:58px;padding:6px 16px}body.tv-mode .dashboard-footer-logo{width:180px;height:38px}body.tv-mode .dashboard-footer-institutional strong,body.tv-mode .dashboard-footer-institutional span{font-size:8px}body.tv-mode .dashboard-footer-bottom{min-height:31px;padding:4px 16px}body.tv-mode .dashboard-footer-privacy,body.tv-mode .dashboard-footer-author{font-size:8px}body.tv-mode .dashboard-footer-author{padding:5px 9px}
@media(max-width:900px){.dashboard-footer-main{padding:10px 16px;gap:14px}.dashboard-footer-author-top{margin-left:0}.dashboard-footer-logo{width:210px;height:42px}.dashboard-footer-author-top{font-size:8px;padding:4px 10px}.dashboard-footer-institutional strong,.dashboard-footer-institutional span{font-size:8px}.dashboard-footer-bottom{padding:6px 16px;gap:12px}.dashboard-footer-privacy,.dashboard-footer-author{font-size:8px}}
@media(max-width:620px){.dashboard-footer-main{align-items:flex-start;flex-direction:column;padding:10px 14px}.dashboard-footer-logo{width:200px;height:40px}.dashboard-footer-author-top{width:100%;text-align:left;padding:5px 0;border-right:0;border-top:1px solid #e1e5ea}.dashboard-footer-institutional{width:100%;text-align:left}.dashboard-footer-bottom{align-items:flex-start;flex-direction:column;padding:7px 14px}.dashboard-footer-author{white-space:normal}}
</style>
<footer class="dashboard-footer" id="dashboardFooter" aria-label="Rodapé institucional"><div class="dashboard-footer-main"><div class="dashboard-footer-brand"><img class="dashboard-footer-logo" src="https://lh3.googleusercontent.com/d/13D3UFhOMHo5rUdzt8ILz6Us7g6V2HRFf" alt="Brisanet Inteligência de Dados"></div><div class="dashboard-footer-author-top">Desenvolvido por: <strong>Laíze Gomes</strong> • <span class="footer-author-group">Grupo de Melhorias</span></div><div class="dashboard-footer-institutional"><strong>Informações Pertinentes ao Grupo Brisanet</strong><span>Curadoria das Informações: IAT</span></div></div><div class="dashboard-footer-bottom"><div class="dashboard-footer-privacy">Política de Privacidade • Os valores podem mudar dependendo do horário da atualização. Achou alguma diferença nos números? Entre em contato com a equipe de Inteligência de Dados IAT.</div></div></footer>



<style id="ranking-layout-final-foto-apenas">
/* ============================================================
   RANKING — MODELO APROVADO PELO USUÁRIO
   Mantém a estrutura original da imagem e adiciona somente a foto.
   ============================================================ */

.ranking-board-head {
  grid-template-columns: 110px minmax(0, 1fr) 110px !important;
  padding: 0 12px 6px !important;
}

.ranking-board-row {
  grid-template-columns: 110px minmax(0, 1fr) !important;
  min-height: 40px !important;
  padding: 4px 10px !important;
  gap: 8px !important;
  background: linear-gradient(90deg, #f8b77f 0%, #f7c397 62%, #fff5ee 100%) !important;
  border: 1px solid #f2b181 !important;
  overflow: hidden !important;
}

/* A própria linha mostra a certificação como preenchimento, como no print. */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  display: block !important;
  content: '' !important;
  position: absolute !important;
  z-index: -1 !important;
  left: 0 !important;
  top: 0 !important;
  bottom: 0 !important;
  width: var(--rank-progress) !important;
  background: linear-gradient(90deg, rgba(244,81,30,.34), rgba(244,81,30,.18)) !important;
  border-right: 1px solid rgba(244,81,30,.62) !important;
  transition: width 1.1s cubic-bezier(.22,.8,.2,1) !important;
}

/* Não deixa o Recolhimento ficar verde. */
.ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, rgba(244,81,30,.34), rgba(244,81,30,.18)) !important;
  border-right-color: rgba(244,81,30,.62) !important;
}

.ranking-board-row::after {
  z-index: 0 !important;
}

.ranking-board-position,
.ranking-board-name-wrap,
.ranking-board-award {
  position: relative !important;
  z-index: 2 !important;
}

/* Posição */
.ranking-board-position {
  gap: 6px !important;
}

.ranking-board-number {
  width: 29px !important;
  height: 29px !important;
  flex: 0 0 29px !important;
  border-radius: 7px !important;
}

.ranking-board-row.rank-1 .ranking-board-number {
  width: 32px !important;
  height: 32px !important;
  flex-basis: 32px !important;
}

/* Foto + nome na mesma linha. Todas as fotos do mesmo tamanho. */
.ranking-board-name-wrap,
.ranking-board-row.rank-1 .ranking-board-name-wrap,
body.tv-mode .ranking-board-name-wrap,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-name-wrap {
  min-width: 0 !important;
  display: flex !important;
  flex-direction: row !important;
  align-items: center !important;
  gap: 8px !important;
}

.ranking-board-avatar,
.ranking-board-avatar-fallback,
.ranking-board-row.rank-1 .ranking-board-avatar,
.ranking-board-row.rank-1 .ranking-board-avatar-fallback,
body.tv-mode .ranking-board-avatar,
body.tv-mode .ranking-board-avatar-fallback,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar,
body.tv-mode .ranking-board-row.rank-1 .ranking-board-avatar-fallback {
  width: 30px !important;
  height: 30px !important;
  min-width: 30px !important;
  max-width: 30px !important;
  min-height: 30px !important;
  max-height: 30px !important;
  flex: 0 0 30px !important;
  border-radius: 50% !important;
  object-fit: cover !important;
  margin: 0 !important;
}

.ranking-board-name-content {
  min-width: 0 !important;
  display: block !important;
  flex: 1 1 auto !important;
}

/* O modelo original não usa uma segunda barra abaixo do nome. */
.ranking-mini-track {
  display: none !important;
}

.ranking-board-name,
.ranking-board-row.rank-1 .ranking-board-name {
  display: block !important;
  font-size: 10.5px !important;
  font-weight: 850 !important;
  line-height: 1.1 !important;
  white-space: nowrap !important;
  overflow: hidden !important;
  text-overflow: ellipsis !important;
}

/* Bloco de certificação/estrelas acompanha a posição do preenchimento. */
.ranking-board-award {
  position: absolute !important;
  z-index: 3 !important;
  top: 0 !important;
  right: 0 !important;
  bottom: 0 !important;
  width: calc(100% - var(--rank-progress)) !important;
  min-width: 58px !important;
  margin: 0 !important;
  padding: 0 9px !important;
  display: flex !important;
  align-items: center !important;
  justify-content: flex-end !important;
  gap: 7px !important;
  background: rgba(255,248,243,.82) !important;
  border-left: 1px solid rgba(205,112,55,.38) !important;
  border-radius: 0 9px 9px 0 !important;
  box-sizing: border-box !important;
}

.ranking-board-percent {
  min-width: 40px !important;
  color: var(--text-main) !important;
  font-size: 9.5px !important;
  font-weight: 950 !important;
  text-align: right !important;
  white-space: nowrap !important;
}

.ranking-board-stars {
  padding-left: 7px !important;
  border-left: 1px solid rgba(170,120,90,.32) !important;
  color: #e97816 !important;
  font-size: 10px !important;
  line-height: 1 !important;
  letter-spacing: 0 !important;
  white-space: nowrap !important;
}

.ranking-board-row.rank-1 .ranking-board-stars {
  font-size: 10px !important;
}

/* Cores do pódio continuam como no ranking original. */
.ranking-board-row.rank-1 {
  min-height: 40px !important;
  background: linear-gradient(90deg, #ffd08d 0%, #f8c79d 62%, #fff4ea 100%) !important;
  border-color: #f1a45f !important;
}

.ranking-executive-card.multiskill .ranking-board-row.rank-2,
.ranking-executive-card.multiskill .ranking-board-row.rank-3 {
  background: linear-gradient(90deg, #f8b77f 0%, #f7c397 62%, #fff5ee 100%) !important;
}

/* O fundo claro da área final não deve esconder a porcentagem. */
body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  background: rgba(31,31,31,.88) !important;
  border-left-color: rgba(255,255,255,.14) !important;
}

body.dark-mode .ranking-board-row,
body.tv-mode.dark-mode .ranking-board-row,
body.dark-mode.tv-mode .ranking-board-row {
  background: linear-gradient(90deg, #70432f 0%, #604033 62%, #302a27 100%) !important;
  border-color: #8e5337 !important;
}

@media (max-width: 1100px) {
  .ranking-board-head {
    grid-template-columns: 100px minmax(0, 1fr) 100px !important;
  }
  .ranking-board-row {
    grid-template-columns: 100px minmax(0, 1fr) !important;
  }
}

body.tv-mode .ranking-board-head {
  grid-template-columns: 100px minmax(0, 1fr) 100px !important;
}
body.tv-mode .ranking-board-row {
  grid-template-columns: 100px minmax(0, 1fr) !important;
}
</style>



<style id="ajuste-final-producao-ranking">
/* ============================================================
   HISTÓRICO DE PRODUÇÃO — OCULTO, COMPACTO E CLICÁVEL
   ============================================================ */
.quartil-producao-collapsed {
  width: 100% !important;
  min-height: 42px !important;
  margin: 8px 0 0 !important;
  padding: 7px 12px !important;
  box-sizing: border-box !important;
  display: flex !important;
  align-items: center !important;
  gap: 10px !important;
  cursor: pointer !important;
  border: 1px dashed #d8dce2 !important;
  border-radius: 8px !important;
  background: rgba(248,249,251,.72) !important;
  color: #17324d !important;
  transition: .2s ease !important;
}

.quartil-producao-collapsed:hover {
  background: #fff7f1 !important;
  border-color: #f36c16 !important;
  transform: translateY(-1px) !important;
}

.quartil-producao-collapsed-icon {
  width: 26px !important;
  height: 26px !important;
  min-width: 26px !important;
  border-radius: 6px !important;
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
  color: #f4511e !important;
  background: #fff0e8 !important;
}

.quartil-producao-collapsed-text {
  min-width: 0 !important;
  flex: 1 1 auto !important;
  display: flex !important;
  align-items: baseline !important;
  gap: 5px !important;
  line-height: 1.2 !important;
}

.quartil-producao-collapsed-text strong {
  font-size: 11px !important;
  font-weight: 850 !important;
  white-space: nowrap !important;
}

.quartil-producao-collapsed-text span {
  font-size: 10px !important;
  color: #657386 !important;
  white-space: nowrap !important;
  overflow: hidden !important;
  text-overflow: ellipsis !important;
}

.quartil-producao-collapsed-action {
  flex: 0 0 auto !important;
  display: inline-flex !important;
  align-items: center !important;
  gap: 5px !important;
  padding: 5px 9px !important;
  border-radius: 6px !important;
  background: #f4511e !important;
  color: #fff !important;
  font-size: 9.5px !important;
  font-weight: 800 !important;
  white-space: nowrap !important;
}

.quartil-producao-content[hidden] {
  display: none !important;
}

.quartil-producao-content:not([hidden]) {
  margin-top: 8px !important;
}

body.dark-mode .quartil-producao-collapsed {
  background: rgba(35,35,35,.75) !important;
  border-color: rgba(255,255,255,.14) !important;
  color: #fff !important;
}
body.dark-mode .quartil-producao-collapsed-text span {
  color: #b7bec8 !important;
}
body.dark-mode .quartil-producao-collapsed:hover {
  background: rgba(90,50,30,.45) !important;
}

/* ============================================================
   RANKING — RESERVA ESPAÇO REAL PARA CERTIFICAÇÃO + ESTRELAS
   Evita que as estrelas sejam cortadas quando a certificação
   estiver muito alta (ex.: 93,33% / 94,74%).
   ============================================================ */
.ranking-board-award {
  width: max(108px, calc(100% - var(--rank-progress))) !important;
  min-width: 108px !important;
  box-sizing: border-box !important;
  grid-template-columns: minmax(48px, 1fr) auto !important;
  gap: 7px !important;
  padding-left: 9px !important;
  padding-right: 8px !important;
  overflow: visible !important;
}

.ranking-board-stars {
  min-width: 38px !important;
  width: auto !important;
  overflow: visible !important;
  white-space: nowrap !important;
  display: inline-flex !important;
  align-items: center !important;
  justify-content: flex-start !important;
  letter-spacing: 0 !important;
  padding-left: 8px !important;
}

.ranking-board-percent {
  overflow: visible !important;
  white-space: nowrap !important;
}

@media (max-width: 1100px) {
  .ranking-board-award {
    width: max(104px, calc(100% - var(--rank-progress))) !important;
    min-width: 104px !important;
    grid-template-columns: minmax(44px, 1fr) auto !important;
    gap: 5px !important;
    padding-left: 7px !important;
    padding-right: 6px !important;
  }
  .ranking-board-stars {
    min-width: 34px !important;
    padding-left: 6px !important;
  }
}

@media (max-width: 700px) {
  .quartil-producao-collapsed-text {
    display: block !important;
  }
  .quartil-producao-collapsed-text strong,
  .quartil-producao-collapsed-text span {
    display: block !important;
  }
  .quartil-producao-collapsed-text span {
    margin-top: 2px !important;
  }
}
</style>



<style id="historicos-quartil-ordem-final">
/* ORDEM DEFINITIVA: RAIO X VISÍVEL -> PRODUÇÃO OCULTA */
.quartil-raiox-visible-history {
  width: 100%;
  margin-top: 4px;
  box-sizing: border-box;
}
.quartil-raiox-visible-history .quartil-raiox-history-title {
  margin-top: 0 !important;
}
.quartil-producao-collapsed {
  width: 100% !important;
  min-height: 42px !important;
  margin: 8px 0 0 !important;
  padding: 7px 12px !important;
  box-sizing: border-box !important;
  display: flex !important;
  align-items: center !important;
  gap: 10px !important;
  cursor: pointer !important;
}
.quartil-producao-content[hidden] {
  display: none !important;
}
.quartil-producao-content:not([hidden]) {
  margin-top: 8px !important;
}
</style>



<style id="ranking-remover-barra-extra-final">
/* ============================================================
   RANKING — REMOVE SOMENTE A BARRA VERTICAL EXTRA
   A única barra visual permanece sendo o preenchimento da
   certificação. A separação entre percentual e estrelas não
   terá mais uma linha vertical.
   ============================================================ */

/* Remove a linha que aparecia imediatamente antes das estrelas. */
.ranking-board-stars,
.ranking-board-row.rank-1 .ranking-board-stars,
body.dark-mode .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-stars,
body.dark-mode.tv-mode .ranking-board-stars {
  border-left: 0 !important;
  border-left-width: 0 !important;
  padding-left: 6px !important;
}

/* Remove também a linha de separação do bloco de resultado.
   Mantém o percentual e as estrelas limpos, sem uma segunda barra. */
.ranking-board-award,
body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  border-left: 0 !important;
}

/* Mantém somente a divisão visual da própria barra de certificação. */
.ranking-board-row::before,
.ranking-board-row.rank-1::before {
  border-right-width: 1px !important;
  border-right-style: solid !important;
}

/* Não criar linhas adicionais via pseudo-elementos. */
.ranking-board-stars::before,
.ranking-board-stars::after,
.ranking-board-percent::before,
.ranking-board-percent::after {
  display: none !important;
  content: none !important;
}
</style>



<style id="ranking-remover-ultima-linha-vertical">
/* ============================================================
   RANKING — REMOVE DEFINITIVAMENTE AS LINHAS VERTICAIS
   DO FINAL DA BARRA E DA DIVISÃO ENTRE % E ESTRELAS.
   A barra continua existindo pelo preenchimento, mas sem
   qualquer "risco" na extremidade.
   ============================================================ */

/* Extremidade do preenchimento: sem borda vertical */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before {
  border-right: 0 !important;
  border-right-width: 0 !important;
  border-right-color: transparent !important;
}

/* Bloco de percentual/estrelas: sem linha de separação */
.ranking-board-award,
body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  border-left: 0 !important;
  border-left-width: 0 !important;
  border-left-color: transparent !important;
}

/* Percentual e estrelas ficam visualmente contínuos */
.ranking-board-percent {
  border: 0 !important;
}

.ranking-board-stars,
.ranking-board-row.rank-1 .ranking-board-stars {
  border-left: 0 !important;
  border-left-width: 0 !important;
  border-left-color: transparent !important;
  padding-left: 5px !important;
}

/* Garante que nenhum pseudo-elemento volte a criar a linha */
.ranking-board-stars::before,
.ranking-board-stars::after,
.ranking-board-percent::before,
.ranking-board-percent::after {
  content: none !important;
  display: none !important;
}
</style>


<style id="ajuste-final-linha-ranking-e-kpi-warning-dark">
/* ============================================================
   AJUSTE FINAL
   1) Remove totalmente a mudança de fundo/linha na transição
      entre a barra de certificação, percentual e estrelas.
   2) Mantém a faixa de atenção AMARELA também no modo escuro.
   ============================================================ */

/* RANKING — percentual + estrelas usam o mesmo fundo da linha.
   Não existe mais divisor visual na extremidade da barra. */
.ranking-board-award,
body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  background: transparent !important;
  border: 0 !important;
  border-left: 0 !important;
  box-shadow: none !important;
}

/* Nenhuma linha entre percentual e estrelas. */
.ranking-board-percent,
.ranking-board-stars,
.ranking-board-row.rank-1 .ranking-board-stars,
body.dark-mode .ranking-board-percent,
body.dark-mode .ranking-board-stars,
body.tv-mode.dark-mode .ranking-board-percent,
body.tv-mode.dark-mode .ranking-board-stars {
  border: 0 !important;
  border-left: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
}

/* KPI — faixa de atenção amarela no modo escuro.
   Ex.: Safra FTTH 73,02% com meta 74% = warning, não verde. */
body.dark-mode .kpi-card.kpi-warning,
body.tv-mode.dark-mode .kpi-card.kpi-warning,
body.dark-mode.tv-mode .kpi-card.kpi-warning {
  background: linear-gradient(135deg, #3a2f12 0%, #4a3b15 100%) !important;
  border-color: #8a6d18 !important;
  color: #ffca28 !important;
}

body.dark-mode .kpi-card.kpi-warning .kpi-title,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-title,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-title {
  color: #ffca28 !important;
}

body.dark-mode .kpi-card.kpi-warning .kpi-value,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-value,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-value {
  color: #ffd54f !important;
}

body.dark-mode .kpi-card.kpi-warning .kpi-info-btn,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-info-btn,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-info-btn {
  color: #ffca28 !important;
}

body.dark-mode .kpi-card.kpi-warning .kpi-title-icon,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-title-icon,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-title-icon {
  background: rgba(249,168,37,.20) !important;
  color: #ffca28 !important;
}

body.dark-mode .kpi-card.kpi-warning .kpi-title-icon i,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-title-icon i,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-title-icon i {
  color: #ffca28 !important;
}

body.dark-mode .kpi-card.kpi-warning .bar-warning,
body.tv-mode.dark-mode .kpi-card.kpi-warning .bar-warning,
body.dark-mode.tv-mode .kpi-card.kpi-warning .bar-warning {
  background-color: #ffca28 !important;
}
</style>



<style id="ranking-bar-preenchimento-real-v9">
/* V9 — BARRA DE CERTIFICAÇÃO COMO PREENCHIMENTO REAL
   A área laranja representa exatamente --rank-progress.
   Não há linha vertical na extremidade. */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode .ranking-board-row::before,
body.tv-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  left: 0 !important;
  right: auto !important;
  top: 0 !important;
  bottom: 0 !important;
  width: var(--rank-progress) !important;
  border: 0 !important;
  border-right: 0 !important;
  outline: 0 !important;
  box-shadow: none !important;
  opacity: 1 !important;
  background: linear-gradient(90deg, #f29a63 0%, #f5ad78 100%) !important;
  transition: width 1.1s cubic-bezier(.22,.8,.2,1) !important;
  pointer-events: none !important;
}

/* O preenchimento do Recolhimento segue o mesmo padrão visual do ranking. */
.ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(90deg, #f29a63 0%, #f5ad78 100%) !important;
}

/* Nunca desenhar uma borda na ponta do preenchimento. */
.ranking-board-row::before {
  border-left: 0 !important;
  border-right: 0 !important;
}
</style>

<style id="ajuste-titulos-indicadores-destaque">
  /* Apenas os títulos INDICADORES DE CERTIFICAÇÃO e SINALIZADORES. */
  .section-title-kpi-destaque {
    font-size: 14px !important;
    font-weight: 700 !important;
    letter-spacing: 0.5px !important;
    line-height: 1.2 !important;
  }
  .section-title-kpi-destaque i {
    font-size: 13px !important;
  }
</style>
</body>
</html>

<style id="ranking-sem-linha-top3-definitivo">
/* ============================================================
   RANKING — REMOVE A MARCAÇÃO VERTICAL DOS 3 PRIMEIROS
   A extremidade do preenchimento não deve criar uma linha.
   Percentual + estrelas ficam contínuos como nos demais.
   ============================================================ */
.ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row::before,
.ranking-board-row.rank-1::before,
.ranking-board-row.rank-2::before,
.ranking-board-row.rank-3::before {
  border-right: 0 !important;
  box-shadow: none !important;
  /* deixa a extremidade 1px antes para não formar risco vertical */
  width: max(0px, calc(var(--rank-progress) - 1px)) !important;
}

/* A área do percentual/estrelas não cria divisor vertical. */
.ranking-board-award,
.ranking-board-row.rank-1 .ranking-board-award,
.ranking-board-row.rank-2 .ranking-board-award,
.ranking-board-row.rank-3 .ranking-board-award,
body.dark-mode .ranking-board-award,
body.tv-mode.dark-mode .ranking-board-award,
body.dark-mode.tv-mode .ranking-board-award {
  border-left: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
}

.ranking-board-percent,
.ranking-board-stars,
.ranking-board-row.rank-1 .ranking-board-stars,
.ranking-board-row.rank-2 .ranking-board-stars,
.ranking-board-row.rank-3 .ranking-board-stars {
  border: 0 !important;
  box-shadow: none !important;
}
</style>

<style id="ranking-remove-progress-edge-v5">
/* V5 — remove definitivamente a linha vertical no fim da barra de certificação */
.ranking-board-row::before,
.ranking-board-row.rank-1::before,
.ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
.ranking-executive-card.multiskill .ranking-board-row.rank-3::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-3::before {
  width: 100% !important;
  right: 0 !important;
  border: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
  background: linear-gradient(
    90deg,
    rgba(244,81,30,.30) 0%,
    rgba(255,122,0,.18) calc(var(--rank-progress) - 7%),
    rgba(255,193,7,.06) var(--rank-progress),
    transparent calc(var(--rank-progress) + 7%),
    transparent 100%
  ) !important;
}

.ranking-executive-card.recolhimento .ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-3::before {
  width: 100% !important;
  border: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
  background: linear-gradient(
    90deg,
    rgba(67,160,71,.30) 0%,
    rgba(67,160,71,.18) calc(var(--rank-progress) - 7%),
    rgba(192,202,51,.07) var(--rank-progress),
    transparent calc(var(--rank-progress) + 7%),
    transparent 100%
  ) !important;
}

body.dark-mode .ranking-board-row::before,
body.dark-mode .ranking-board-row.rank-1::before,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
body.dark-mode .ranking-executive-card.multiskill .ranking-board-row.rank-3::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row.rank-3::before {
  border: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
}

body.tv-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  border-right: 0 !important;
}
</style>

<style id="ranking-sem-risca-definitivo-v7">
/* V7 — elimina qualquer risca na extremidade da barra de certificação.
   A barra continua visível, mas sua transição para a área do percentual
   é totalmente suave. */
.ranking-board-row::before,
.ranking-board-row.rank-1::before,
.ranking-board-row.rank-2::before,
.ranking-board-row.rank-3::before,
.ranking-executive-card.recolhimento .ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-3::before,
body.dark-mode .ranking-board-row::before,
body.tv-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before {
  border: 0 !important;
  border-right: 0 !important;
  outline: 0 !important;
  box-shadow: none !important;
  width: 100% !important;
  background: linear-gradient(
    90deg,
    rgba(244,81,30,.34) 0%,
    rgba(244,81,30,.30) calc(var(--rank-progress) - 18%),
    rgba(244,81,30,.18) calc(var(--rank-progress) - 8%),
    rgba(244,81,30,.08) var(--rank-progress),
    rgba(244,81,30,0) calc(var(--rank-progress) + 8%),
    rgba(244,81,30,0) 100%
  ) !important;
}

/* A área do percentual e das estrelas não possui divisor. */
.ranking-board-award,
.ranking-board-percent,
.ranking-board-stars,
.ranking-board-row .ranking-board-award,
.ranking-board-row .ranking-board-percent,
.ranking-board-row .ranking-board-stars {
  border: 0 !important;
  border-left: 0 !important;
  border-right: 0 !important;
  outline: 0 !important;
  box-shadow: none !important;
}

/* Remove pseudo-elementos que possam recriar uma linha. */
.ranking-board-row::after,
.ranking-board-stars::before,
.ranking-board-stars::after,
.ranking-board-percent::before,
.ranking-board-percent::after {
  border: 0 !important;
  box-shadow: none !important;
}

/* Mantém a faixa amarela do KPI no modo escuro e o valor branco. */
body.dark-mode .kpi-card.kpi-warning .kpi-value,
body.tv-mode.dark-mode .kpi-card.kpi-warning .kpi-value,
body.dark-mode.tv-mode .kpi-card.kpi-warning .kpi-value {
  color: #ffffff !important;
}
</style>


<style id="ranking-bar-progress-real-v8">
/* V8 — BARRA DE PROGRESSO REAL + SEM RISCA
   A barra ocupa exatamente o percentual de certificação.
   Não há border-right nem divisor na extremidade.
   O último trecho faz um fade suave para evitar qualquer risco vertical. */
.ranking-board-row::before,
.ranking-board-row.rank-1::before,
.ranking-board-row.rank-2::before,
.ranking-board-row.rank-3::before,
.ranking-executive-card.recolhimento .ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-3::before,
body.dark-mode .ranking-board-row::before,
body.dark-mode .ranking-board-row.rank-1::before,
body.dark-mode .ranking-board-row.rank-2::before,
body.dark-mode .ranking-board-row.rank-3::before,
body.tv-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  left: 0 !important;
  right: auto !important;
  top: 0 !important;
  bottom: 0 !important;
  width: var(--rank-progress) !important;
  border: 0 !important;
  border-right: 0 !important;
  outline: 0 !important;
  box-shadow: none !important;
  background: linear-gradient(
    90deg,
    rgba(230,81,0,.42) 0%,
    rgba(244,81,30,.34) 72%,
    rgba(255,179,0,.20) 91%,
    rgba(255,179,0,0) 100%
  ) !important;
  transition: width 1.1s cubic-bezier(.22,.8,.2,1) !important;
}

/* Recolhimento mantém sua identidade, mas com a mesma lógica de barra. */
.ranking-executive-card.recolhimento .ranking-board-row::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
.ranking-executive-card.recolhimento .ranking-board-row.rank-3::before,
body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(
    90deg,
    rgba(230,81,0,.34) 0%,
    rgba(244,81,30,.28) 72%,
    rgba(255,179,0,.16) 91%,
    rgba(255,179,0,0) 100%
  ) !important;
}

/* No modo escuro a barra continua visível, sem linha na ponta. */
body.dark-mode .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-board-row::before {
  background: linear-gradient(
    90deg,
    rgba(244,81,30,.42) 0%,
    rgba(244,81,30,.30) 72%,
    rgba(255,179,0,.18) 91%,
    rgba(255,179,0,0) 100%
  ) !important;
}

body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
body.dark-mode.tv-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
  background: linear-gradient(
    90deg,
    rgba(244,81,30,.38) 0%,
    rgba(244,81,30,.27) 72%,
    rgba(255,179,0,.16) 91%,
    rgba(255,179,0,0) 100%
  ) !important;
}

/* Percentual e estrelas continuam sem qualquer divisor. */
.ranking-board-award,
.ranking-board-percent,
.ranking-board-stars,
.ranking-board-row .ranking-board-award,
.ranking-board-row .ranking-board-percent,
.ranking-board-row .ranking-board-stars {
  border: 0 !important;
  border-left: 0 !important;
  border-right: 0 !important;
  box-shadow: none !important;
}
</style>



<!-- ============================================================
     AJUSTE ÚNICO — BARRA DE PREENCHIMENTO PROPORCIONAL

     NÃO altera:
       • certificação
       • percentual
       • estrelas
       • posições
       • nomes
       • fotos
       • medalhas
       • animações
       • layout
       • demais funcionalidades

     ALTERA SOMENTE:
       • fundo da linha = área não preenchida
       • ::before = área preenchida conforme o percentual
     ============================================================ -->

<style id="ajuste-unico-barra-proporcional-final">

  /* Área não preenchida */
  .ranking-board-row {
    background: #FFF3EB !important;
  }

  /* Área preenchida: exatamente o percentual da linha */
  .ranking-board-row::before,
  .ranking-executive-card.multiskill .ranking-board-row::before,
  .ranking-executive-card.recolhimento .ranking-board-row::before,
  body.dark-mode .ranking-board-row::before,
  body.tv-mode .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-board-row::before,
  body.dark-mode.tv-mode .ranking-board-row::before {

    left: 0 !important;
    right: auto !important;
    top: 0 !important;
    bottom: 0 !important;

    width: var(--rank-progress) !important;

    border: 0 !important;
    border-right: 0 !important;

    outline: 0 !important;
    box-shadow: none !important;

    background: #F7C9B5 !important;

    opacity: 1 !important;

    pointer-events: none !important;
  }

</style>



<!-- ============================================================
     CORREÇÃO FINAL — BARRA EXATA PARA TODOS OS LUGARES

     CORRIGE SOMENTE O PROBLEMA VISUAL IDENTIFICADO:
     01º / 02º / 03º estavam aparentando ter preenchimento
     maior que o percentual por causa do bloco de
     certificação/estrelas com largura mínima.

     Agora:
       93,33% -> 93,33% preenchido
       86,67% -> 86,67% preenchido
       80,00% -> 80,00% preenchido
       76,19% -> 76,19% preenchido
       etc.

     O percentual e as estrelas ficam por cima da área final,
     sem criar uma segunda área colorida que altere visualmente
     o término da barra.

     NÃO altera dados, animação, fotos, nomes, medalhas,
     certificação ou estrutura do ranking.
     ============================================================ -->

<style id="correcao-barra-exata-top3-final">

  /* Fundo real da linha = parte NÃO preenchida */
  .ranking-board-row,
  .ranking-executive-card.recolhimento .ranking-board-row,
  .ranking-executive-card.multiskill .ranking-board-row {
    background: #FFF3EB !important;
  }


  /* ==========================================================
     PREENCHIMENTO REAL
     ========================================================== */

  .ranking-board-row::before,
  .ranking-board-row.rank-1::before,
  .ranking-board-row.rank-2::before,
  .ranking-board-row.rank-3::before,
  .ranking-board-row.rank-4::before,
  .ranking-board-row.rank-5::before,
  .ranking-board-row.rank-6::before,
  .ranking-board-row.rank-7::before,
  .ranking-board-row.rank-8::before,
  .ranking-board-row.rank-9::before,
  .ranking-board-row.rank-10::before,
  .ranking-executive-card.recolhimento .ranking-board-row::before,
  .ranking-executive-card.multiskill .ranking-board-row::before {

    left: 0 !important;
    right: auto !important;
    top: 0 !important;
    bottom: 0 !important;

    /* A ÚNICA referência da largura da barra */
    width: var(--rank-progress) !important;

    max-width: 100% !important;
    min-width: 0 !important;

    background: #F7C9B5 !important;
    background-image: none !important;

    border: 0 !important;
    border-right: 0 !important;

    box-shadow: none !important;
    outline: 0 !important;

    opacity: 1 !important;

    pointer-events: none !important;

    z-index: 0 !important;
  }


  /* ==========================================================
     CRÍTICO:
     O BLOCO % + ESTRELAS NÃO PODE MAIS "CRIAR" UMA BARRA.

     Antes:
       width: max(108px, calc(...))
       background: rgba(...)

     Isso fazia os primeiros lugares aparentarem ter
     preenchimento maior que o percentual.

     Agora ele é apenas conteúdo sobre a linha.
     ========================================================== */

  .ranking-board-award,
  .ranking-board-row.rank-1 .ranking-board-award,
  .ranking-board-row.rank-2 .ranking-board-award,
  .ranking-board-row.rank-3 .ranking-board-award {

    position: absolute !important;

    top: 0 !important;
    right: 0 !important;
    bottom: 0 !important;
    left: auto !important;

    width: auto !important;
    min-width: 0 !important;
    max-width: none !important;

    margin: 0 !important;

    background: transparent !important;

    border: 0 !important;
    border-left: 0 !important;
    border-right: 0 !important;

    box-shadow: none !important;

    z-index: 3 !important;

    display: flex !important;
    align-items: center !important;
    justify-content: flex-end !important;

    gap: 7px !important;

    padding-left: 5px !important;
    padding-right: 9px !important;

    box-sizing: border-box !important;

    overflow: visible !important;
  }


  /* Percentual */
  .ranking-board-percent,
  .ranking-board-row.rank-1 .ranking-board-percent,
  .ranking-board-row.rank-2 .ranking-board-percent,
  .ranking-board-row.rank-3 .ranking-board-percent {

    min-width: 40px !important;

    width: auto !important;

    background: transparent !important;

    border: 0 !important;
    box-shadow: none !important;

    white-space: nowrap !important;

    text-align: right !important;
  }


  /* Estrelas */
  .ranking-board-stars,
  .ranking-board-row.rank-1 .ranking-board-stars,
  .ranking-board-row.rank-2 .ranking-board-stars,
  .ranking-board-row.rank-3 .ranking-board-stars {

    min-width: 0 !important;
    width: auto !important;

    background: transparent !important;

    border: 0 !important;
    border-left: 0 !important;

    box-shadow: none !important;

    padding-left: 5px !important;

    white-space: nowrap !important;
  }


  /* ==========================================================
     GARANTIA:
     nenhum fundo específico dos primeiros lugares pode
     esconder a parte NÃO preenchida.
     ========================================================== */

  .ranking-board-row.rank-1,
  .ranking-board-row.rank-2,
  .ranking-board-row.rank-3,
  .ranking-board-row.rank-4 {

    background: #FFF3EB !important;
  }


  /* ==========================================================
     DARK MODE
     ========================================================== */

  body.dark-mode .ranking-board-row,
  body.tv-mode.dark-mode .ranking-board-row,
  body.dark-mode.tv-mode .ranking-board-row {

    background: #302A27 !important;
  }

  body.dark-mode .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-board-row::before,
  body.dark-mode.tv-mode .ranking-board-row::before {

    width: var(--rank-progress) !important;

    background: #C87552 !important;
    background-image: none !important;

    border: 0 !important;
  }

  body.dark-mode .ranking-board-award,
  body.tv-mode.dark-mode .ranking-board-award,
  body.dark-mode.tv-mode .ranking-board-award {

    background: transparent !important;

    border: 0 !important;
  }

</style>



<!-- ============================================================
     AJUSTE FINAL — MESMO PREENCHIMENTO VISUAL DO 7º LUGAR

     ÚNICA ALTERAÇÃO:
     todos os rankings passam a usar EXATAMENTE o mesmo
     preenchimento sólido do 7º lugar.

     O percentual continua definindo SOMENTE a largura.

     Não altera:
       • dados
       • posição
       • nomes
       • fotos
       • estrelas
       • certificação
       • medalhas
       • animação
       • layout
     ============================================================ -->

<style id="barra-final-mesmo-padrao-v17">

  /* Fundo único da parte não preenchida */
  .ranking-board-row {
    background: #FFF3EB !important;
    background-image: none !important;
  }

  /*
     UMA ÚNICA BARRA DE PREENCHIMENTO.
     A cor é a mesma para 1º, 2º, 3º ... 10º.
     Somente a largura muda conforme o percentual.
  */
  .ranking-board-row::before,
  .ranking-board-row.rank-1::before,
  .ranking-board-row.rank-2::before,
  .ranking-board-row.rank-3::before,
  .ranking-board-row.rank-4::before,
  .ranking-board-row.rank-5::before,
  .ranking-board-row.rank-6::before,
  .ranking-board-row.rank-7::before,
  .ranking-board-row.rank-8::before,
  .ranking-board-row.rank-9::before,
  .ranking-board-row.rank-10::before,
  .ranking-executive-card.multiskill .ranking-board-row::before,
  .ranking-executive-card.recolhimento .ranking-board-row::before {

    content: "" !important;

    position: absolute !important;

    left: 0 !important;
    right: auto !important;
    top: 0 !important;
    bottom: 0 !important;

    width: var(--rank-progress) !important;

    min-width: 0 !important;
    max-width: 100% !important;

    /*
       MESMA COR DO PREENCHIMENTO CORRETO
       observado nas posições inferiores.
    */
    background: #F7C9B5 !important;
    background-image: none !important;

    border: 0 !important;
    border-right: 0 !important;

    box-shadow: none !important;
    outline: none !important;

    opacity: 1 !important;

    pointer-events: none !important;

    z-index: 0 !important;
  }

  /*
     O bloco de percentual + estrelas é TRANSPARENTE.
     Assim ele não cria uma segunda tonalidade sobre a barra.
  */
  .ranking-board-award,
  .ranking-board-row.rank-1 .ranking-board-award,
  .ranking-board-row.rank-2 .ranking-board-award,
  .ranking-board-row.rank-3 .ranking-board-award,
  .ranking-board-row.rank-4 .ranking-board-award,
  .ranking-board-row.rank-5 .ranking-board-award,
  .ranking-board-row.rank-6 .ranking-board-award,
  .ranking-board-row.rank-7 .ranking-board-award,
  .ranking-board-row.rank-8 .ranking-board-award,
  .ranking-board-row.rank-9 .ranking-board-award,
  .ranking-board-row.rank-10 .ranking-board-award {

    background: transparent !important;
    background-image: none !important;

    border: 0 !important;
    box-shadow: none !important;

    z-index: 3 !important;
  }

  .ranking-board-percent,
  .ranking-board-stars {
    background: transparent !important;
    background-image: none !important;

    border: 0 !important;
    box-shadow: none !important;

    z-index: 3 !important;
  }

  /*
     Remove qualquer variação de cor criada especificamente
     para os três primeiros lugares.
  */
  .ranking-board-row.rank-1,
  .ranking-board-row.rank-2,
  .ranking-board-row.rank-3,
  .ranking-board-row.rank-4,
  .ranking-board-row.rank-5,
  .ranking-board-row.rank-6,
  .ranking-board-row.rank-7,
  .ranking-board-row.rank-8,
  .ranking-board-row.rank-9,
  .ranking-board-row.rank-10 {

    background: #FFF3EB !important;
    background-image: none !important;
  }

  /*
     A animação continua sendo somente um brilho.
     Ela não muda a cor-base nem a largura da barra.
  */
  .ranking-board-row.ranking-pulse::before {
    width: var(--rank-progress) !important;
    background: #F7C9B5 !important;
    background-image: none !important;
  }

  /* Dark mode: mesma lógica, sem interferir no layout */
  body.dark-mode .ranking-board-row,
  body.tv-mode.dark-mode .ranking-board-row,
  body.dark-mode.tv-mode .ranking-board-row {
    background: #302A27 !important;
    background-image: none !important;
  }

  body.dark-mode .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-board-row::before,
  body.dark-mode.tv-mode .ranking-board-row::before {

    width: var(--rank-progress) !important;

    background: #C87552 !important;
    background-image: none !important;

    border: 0 !important;
  }

</style>



<!-- ============================================================
     CORREÇÃO DEFINITIVA — BARRA IDÊNTICA AO 7º LUGAR

     ATENÇÃO:
     O problema estava em regras antigas com MAIOR ESPECIFICIDADE
     para rank-1/rank-2/rank-3. Elas continuavam pintando essas
     linhas com gradientes diferentes, mesmo depois dos ajustes.

     Esta regra final usa a mesma especificidade dessas regras
     antigas e força:
       1. fundo da linha igual ao 7º;
       2. preenchimento sólido igual ao 7º;
       3. largura exclusivamente pelo percentual.

     NÃO altera dados, posição, fotos, nomes, estrelas,
     certificação, medalhas ou animação.
     ============================================================ -->

<style id="correcao-definitiva-barra-v18">

  /* ==========================================================
     1 — FUNDO DA LINHA

     A parte NÃO preenchida precisa ser exatamente a mesma
     em todos os lugares.
     ========================================================== */

  .ranking-executive-card.multiskill .ranking-board-row.rank-1,
  .ranking-executive-card.multiskill .ranking-board-row.rank-2,
  .ranking-executive-card.multiskill .ranking-board-row.rank-3,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-1,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-2,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-3 {

    background: #FFF3EB !important;
    background-image: none !important;
  }


  /* ==========================================================
     2 — PREENCHIMENTO

     MESMA COR para 1º, 2º, 3º ... 10º.

     A única coisa que muda é:
       width: var(--rank-progress)
     ========================================================== */

  .ranking-executive-card.multiskill .ranking-board-row.rank-1::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-2::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-3::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-1::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-2::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-3::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-4::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-4::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-5::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-5::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-6::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-6::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-7::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-7::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-8::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-8::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-9::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-9::before,
  .ranking-executive-card.multiskill .ranking-board-row.rank-10::before,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-10::before {

    left: 0 !important;
    right: auto !important;
    top: 0 !important;
    bottom: 0 !important;

    width: var(--rank-progress) !important;

    min-width: 0 !important;
    max-width: 100% !important;

    background: #F7C9B5 !important;
    background-image: none !important;

    border: 0 !important;
    border-right: 0 !important;

    outline: none !important;
    box-shadow: none !important;

    opacity: 1 !important;

    z-index: 0 !important;

    pointer-events: none !important;
  }


  /* ==========================================================
     3 — % E ESTRELAS NÃO PINTAM NADA
     ========================================================== */

  .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-award,
  .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-award,
  .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-award,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-award,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-2 .ranking-board-award,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-3 .ranking-board-award {

    background: transparent !important;
    background-image: none !important;

    border: 0 !important;
    box-shadow: none !important;
  }


  .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-percent,
  .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-percent,
  .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-percent,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-percent,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-2 .ranking-board-percent,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-3 .ranking-board-percent,
  .ranking-executive-card.multiskill .ranking-board-row.rank-1 .ranking-board-stars,
  .ranking-executive-card.multiskill .ranking-board-row.rank-2 .ranking-board-stars,
  .ranking-executive-card.multiskill .ranking-board-row.rank-3 .ranking-board-stars,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-1 .ranking-board-stars,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-2 .ranking-board-stars,
  .ranking-executive-card.recolhimento .ranking-board-row.rank-3 .ranking-board-stars {

    background: transparent !important;
    background-image: none !important;

    border: 0 !important;
    box-shadow: none !important;
  }


  /* ==========================================================
     4 — ANIMAÇÃO NÃO ALTERA O PREENCHIMENTO
     ========================================================== */

  .ranking-executive-card.multiskill .ranking-board-row.ranking-pulse::before,
  .ranking-executive-card.recolhimento .ranking-board-row.ranking-pulse::before {

    width: var(--rank-progress) !important;
    background: #F7C9B5 !important;
    background-image: none !important;
    border: 0 !important;
  }

</style>



<style id="ajuste-dark-mode-laranja-todas-posicoes">
  /* SOMENTE MODO ESCURO
     Todas as posições passam a usar o mesmo tratamento laranja
     do 1º lugar do Multiskill. O preenchimento proporcional
     continua sendo controlado exclusivamente por --rank-progress. */

  body.dark-mode .ranking-board-row,
  body.tv-mode.dark-mode .ranking-board-row,
  body.dark-mode.tv-mode .ranking-board-row,
  body.dark-mode .ranking-executive-card.multiskill .ranking-board-row,
  body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row,
  body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row,
  body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row {
    background: linear-gradient(90deg, #4b3a1d 0%, #30291f 58%, #222930 100%) !important;
    background-image: linear-gradient(90deg, #4b3a1d 0%, #30291f 58%, #222930 100%) !important;
    border-color: #b98b35 !important;
    box-shadow: 0 5px 16px rgba(0,0,0,.28) !important;
  }

  /* Mantém o preenchimento proporcional e laranja. */
  body.dark-mode .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-board-row::before,
  body.dark-mode.tv-mode .ranking-board-row::before,
  body.dark-mode .ranking-executive-card.multiskill .ranking-board-row::before,
  body.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-executive-card.multiskill .ranking-board-row::before,
  body.tv-mode.dark-mode .ranking-executive-card.recolhimento .ranking-board-row::before {
    left: 0 !important;
    right: auto !important;
    width: var(--rank-progress) !important;
    background: #C87552 !important;
    background-image: none !important;
    border: 0 !important;
    box-shadow: none !important;
  }
</style>
