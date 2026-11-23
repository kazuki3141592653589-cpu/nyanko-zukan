<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>にゃんこ大戦争 ステータス図鑑 (No.001〜888 完全収録)</title>
  <!-- Firebase SDK (Compat版: 単一HTMLでそのまま動作) -->
  <script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-firestore-compat.js"></script>
  <style>
    :root {
      --bg-page: #f6efe0;
      --bg-card: #fbf7ee;
      --bg-sub: #f0e6d2;
      --ink: #2b2218;
      --ink-sub: #5c4d3c;
      --gold: #d97706;
      --gold-light: #fde68a;
      --crimson: #b91c1c;
      --emerald: #047857;
      --blue: #1d4ed8;
    }

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
      touch-action: manipulation;
    }

    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      max-width: 100vw;
      overflow-x: hidden;
      min-height: 100%;
      background-color: var(--bg-page);
      color: var(--ink);
      font-family: -apple-system, BlinkMacSystemFont, "Hiragino Kaku Gothic ProN", "Hiragino Sans", "Yu Gothic", Meiryo, sans-serif;
      line-height: 1.45;
      -webkit-text-size-adjust: 100%;
    }

    /* 管理者モードOFF時は編集UIを非表示 */
    body:not(.admin-mode) .admin-only {
      display: none !important;
    }

    /* 管理者モードON時のバッジ表示 */
    .admin-indicator {
      display: none;
      background: var(--crimson);
      color: #fff;
      font-size: 10px;
      font-weight: 900;
      padding: 2px 7px;
      border-radius: 6px;
      border: 1.5px solid var(--ink);
      margin-left: 6px;
      vertical-align: middle;
    }
    body.admin-mode .admin-indicator {
      display: inline-block;
    }

    /* クラウド同期ステータスバッジ */
    .sync-badge {
      display: inline-block;
      background: #065f46;
      color: #d1fae5;
      font-size: 10px;
      font-weight: 900;
      padding: 2px 7px;
      border-radius: 6px;
      border: 1.5px solid var(--ink);
      margin-left: 4px;
      vertical-align: middle;
    }
    .sync-badge.offline {
      background: #57534e;
      color: #f5f5f4;
    }

    /* ヘッダー */
    .app-header {
      background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
      border-bottom: 3px solid var(--ink);
      padding: 12px 12px 12px 54px;
      box-shadow: 0 3px 0 rgba(43, 34, 24, 0.25);
    }

    .header-inner {
      max-width: 1200px;
      margin: 0 auto;
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
    }

    .app-title {
      margin: 0;
      font-size: 15.5px;
      font-weight: 900;
      color: var(--ink);
      letter-spacing: -0.02em;
      user-select: none;
      cursor: pointer;
    }

    .app-sub {
      font-size: 11px;
      font-weight: 800;
      color: #451a03;
      margin-top: 2px;
    }

    .header-btns {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
    }

    .container {
      max-width: 1200px;
      width: 100%;
      margin: 0 auto;
      padding: 10px 10px 32px;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      padding: 6px 10px;
      min-height: 34px;
      font-size: 11px;
      font-weight: 900;
      border: 2px solid var(--ink);
      border-radius: 10px;
      background: var(--bg-card);
      color: var(--ink);
      cursor: pointer;
      box-shadow: 0 2px 0 var(--ink);
      transition: transform 0.08s ease;
    }

    .btn:active {
      transform: translateY(1.5px);
      box-shadow: 0 0.5px 0 var(--ink);
    }

    .btn:disabled {
      opacity: 0.4;
      cursor: not-allowed;
      transform: none;
    }

    .btn-dark {
      background: var(--ink);
      color: var(--gold-light);
    }

    .btn-gold {
      background: var(--gold-light);
      color: var(--ink);
    }

    .btn-crimson {
      background: var(--crimson);
      color: #ffffff;
    }

    .panel {
      background: var(--bg-card);
      border: 2.5px solid var(--ink);
      border-radius: 14px;
      padding: 10px 11px;
      box-shadow: 0 3px 0 var(--ink);
      width: 100%;
    }

    .panel-head {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: space-between;
      gap: 6px;
      margin-bottom: 8px;
      padding-bottom: 6px;
      border-bottom: 1.5px solid #d6c6aa;
    }

    .panel-title {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: 5px;
      font-size: 12.5px;
      font-weight: 900;
    }

    .badge-stamp {
      display: inline-block;
      background: var(--ink);
      color: var(--gold-light);
      font-size: 10px;
      font-weight: 900;
      padding: 2px 7px;
      border-radius: 6px;
      white-space: nowrap;
    }

    /* 検索・フォーム（iOSズーム防止のためfont-size 16px維持） */
    .search-row {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
      align-items: center;
    }

    .search-field {
      flex: 1 1 100%;
      position: relative;
      display: flex;
      align-items: center;
    }

    .input-box, .select-box {
      width: 100%;
      min-height: 38px;
      padding: 6px 10px;
      font-size: 16px;
      font-weight: 800;
      color: var(--ink);
      background: #ffffff;
      border: 2px solid var(--ink);
      border-radius: 10px;
      outline: none;
    }

    .clear-btn {
      position: absolute;
      right: 7px;
      background: var(--bg-sub);
      border: 1.5px solid var(--ink);
      border-radius: 7px;
      font-size: 11px;
      font-weight: 900;
      padding: 4px 8px;
      cursor: pointer;
    }

    .seg-group {
      display: inline-flex;
      flex-wrap: wrap;
      max-width: 100%;
      border: 2px solid var(--ink);
      border-radius: 10px;
      overflow: hidden;
      background: var(--bg-sub);
    }

    .seg-btn {
      border: none;
      border-right: 1.5px solid var(--ink);
      background: transparent;
      color: var(--ink);
      font-size: 10.5px;
      font-weight: 900;
      padding: 6px 8px;
      min-height: 32px;
      cursor: pointer;
      white-space: nowrap;
    }

    .seg-btn:last-child {
      border-right: none;
    }

    .seg-btn.active {
      background: var(--ink);
      color: var(--gold-light);
    }

    .filter-block {
      margin-top: 9px;
    }

    .filter-label {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 11px;
      font-weight: 900;
      color: var(--ink-sub);
      margin-bottom: 5px;
    }

    .chip-wrap {
      display: flex;
      flex-wrap: wrap;
      gap: 5px;
    }

    .chip {
      border: 1.5px solid var(--ink);
      border-radius: 8px;
      background: var(--bg-sub);
      color: var(--ink);
      font-size: 11px;
      font-weight: 800;
      padding: 5px 8px;
      min-height: 30px;
      cursor: pointer;
    }

    .chip.active {
      background: var(--crimson);
      color: #ffffff;
    }

    .chip.active-trait {
      background: var(--blue);
      color: #ffffff;
    }

    .chip.active-ab {
      background: var(--emerald);
      color: #ffffff;
    }

    /* レベル・お宝・本能計算機 */
    .slider-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 8px;
    }

    .slider-card {
      background: var(--bg-sub);
      border: 1.5px solid var(--ink);
      border-radius: 10px;
      padding: 7px 10px;
    }

    .slider-head {
      display: flex;
      justify-content: space-between;
      font-size: 11.5px;
      font-weight: 900;
      margin-bottom: 4px;
    }

    .slider-ctrl {
      display: flex;
      align-items: center;
      gap: 6px;
    }

    .step-btn {
      width: 30px;
      height: 30px;
      border: 1.5px solid var(--ink);
      border-radius: 8px;
      background: #fff;
      font-weight: 900;
      font-size: 15px;
      cursor: pointer;
    }

    input[type="range"] {
      flex: 1;
      accent-color: var(--crimson);
    }

    .calc-opt-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 6px;
      margin-top: 8px;
    }

    .opt-pill {
      display: flex;
      align-items: center;
      gap: 5px;
      background: #ffffff;
      border: 1.5px solid var(--ink);
      border-radius: 9px;
      padding: 5px 7px;
      font-size: 10.5px;
      font-weight: 800;
      cursor: pointer;
    }

    /* カード一覧（各カード内第1〜第4形態クイックタブ付き） */
    .cards-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 10px;
    }

    .unit-card {
      background: var(--bg-card);
      border: 2.5px solid var(--ink);
      border-radius: 14px;
      padding: 10px 11px;
      box-shadow: 0 3px 0 var(--ink);
      display: flex;
      flex-direction: column;
      gap: 7px;
      cursor: pointer;
    }

    .unit-card.unreleased-card {
      background: #ebe2d0;
      opacity: 0.9;
    }

    .card-form-tabs {
      display: flex;
      flex-wrap: wrap;
      gap: 4px;
    }

    .card-ftab {
      border: 1.5px solid var(--ink);
      border-radius: 7px;
      background: var(--bg-sub);
      color: var(--ink);
      font-size: 10.5px;
      font-weight: 900;
      padding: 3px 8px;
      min-height: 26px;
      cursor: pointer;
    }

    .card-ftab.active {
      background: var(--ink);
      color: var(--gold-light);
    }

    .card-stat-row {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 4px;
      background: var(--bg-sub);
      border: 1.5px solid var(--ink);
      border-radius: 9px;
      padding: 6px 5px;
      text-align: center;
    }

    .card-sub-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 4px;
      font-size: 10px;
      font-weight: 800;
      color: var(--ink-sub);
      background: #fffdf8;
      border: 1px solid #d6c6aa;
      border-radius: 8px;
      padding: 5px 6px;
    }

    .tag-row {
      display: flex;
      flex-wrap: wrap;
      gap: 4px;
    }

    .tag-trait {
      background: #dbeafe;
      color: #1e3a8a;
      border: 1px solid #93c5fd;
      border-radius: 6px;
      padding: 1px 6px;
      font-size: 10px;
      font-weight: 900;
    }

    .tag-ab {
      background: #d1fae5;
      color: #065f46;
      border: 1px solid #6ee7b7;
      border-radius: 6px;
      padding: 1px 6px;
      font-size: 10px;
      font-weight: 900;
    }

    /* index_status.html 準拠 全項目横スクロールテーブル */
    .table-scroll-wrap {
      width: 100%;
      overflow-x: auto;
      background: var(--bg-card);
      border: 2.5px solid var(--ink);
      border-radius: 14px;
      box-shadow: 0 3px 0 var(--ink);
      -webkit-overflow-scrolling: touch;
    }

    table.status-table {
      width: max-content;
      min-width: 100%;
      border-collapse: separate;
      border-spacing: 0;
      font-size: 11px;
      white-space: nowrap;
    }

    table.status-table thead th {
      background: var(--ink);
      color: var(--gold-light);
      font-weight: 900;
      padding: 7px 9px;
      text-align: left;
      border-bottom: 2px solid #000;
      border-right: 1px solid #443728;
      cursor: pointer;
    }

    table.status-table tbody td {
      padding: 6px 9px;
      border-bottom: 1px solid #dfd3be;
      border-right: 1px solid #eadfcd;
      font-weight: 700;
      background: var(--bg-card);
      cursor: pointer;
    }

    table.status-table tbody tr:nth-child(even) td {
      background: var(--bg-sub);
    }

    table.status-table tbody tr:hover td {
      background: #fef3c7;
    }

    table.status-table tbody tr.unreleased-row td {
      background: #ebe2d0;
      color: #6b5d4d;
    }

    .c-hp { color: var(--emerald); font-weight: 900; }
    .c-atk { color: var(--crimson); font-weight: 900; }
    .c-dps { color: #b45309; font-weight: 900; }

    /* ページネーション */
    .pagination {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: center;
      gap: 8px;
      background: var(--bg-card);
      border: 2px solid var(--ink);
      border-radius: 12px;
      padding: 8px 10px;
      box-shadow: 0 2px 0 var(--ink);
    }

    /* モーダル */
    .modal-backdrop {
      position: fixed;
      inset: 0;
      z-index: 60;
      background: rgba(24, 18, 12, 0.68);
      display: none;
      align-items: center;
      justify-content: center;
      padding: 10px;
    }

    .modal-backdrop.open {
      display: flex;
    }

    .modal-box {
      width: 100%;
      max-width: 680px;
      max-height: 90vh;
      overflow-y: auto;
      background: var(--bg-card);
      border: 3px solid var(--ink);
      border-radius: 14px;
      padding: 13px;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
    }

    .detail-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
      gap: 6px;
      margin-top: 8px;
    }

    .stat-cell {
      background: var(--bg-sub);
      border: 1.5px solid var(--ink);
      border-radius: 9px;
      padding: 6px 8px;
    }

    .stat-k {
      font-size: 10px;
      font-weight: 800;
      color: var(--ink-sub);
    }

    .stat-v {
      font-size: 13px;
      font-weight: 900;
    }

    .form-edit-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(125px, 1fr));
      gap: 6px;
      margin-top: 8px;
    }

    .field-lbl {
      display: flex;
      flex-direction: column;
      gap: 2px;
      font-size: 10.5px;
      font-weight: 900;
    }

    .field-lbl input, .field-lbl select {
      border: 1.5px solid var(--ink);
      border-radius: 7px;
      padding: 5px 7px;
      font-size: 16px;
      font-weight: 800;
      background: #fff;
    }

    .footer-cite {
      background: var(--bg-card);
      border: 2px solid var(--ink);
      border-radius: 12px;
      padding: 10px 12px;
      font-size: 11px;
      font-weight: 700;
      color: var(--ink-sub);
    }

    .footer-cite a {
      color: var(--blue);
      font-weight: 900;
      text-decoration: underline;
    }

    .toast {
      position: fixed;
      bottom: 16px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 80;
      background: var(--ink);
      color: var(--gold-light);
      border: 2px solid var(--gold);
      border-radius: 10px;
      padding: 8px 14px;
      font-size: 12px;
      font-weight: 900;
      display: none;
      white-space: nowrap;
    }
  </style>
</head>
<body>
  <header class="app-header">
    <div class="header-inner">
      <div>
        <h1 class="app-title" id="appTitleBar">にゃんこ大戦争 公式ステータス図鑑 (No.001〜888 完全収録)<span class="sync-badge offline" id="cloudStatusBadge">ローカル保存</span><span class="admin-indicator">管理者編集ON</span></h1>
        <div class="app-sub">第1形態主見出し統一・第1〜第4形態切替・index_status.html 全項目準拠</div>
      </div>
      <div class="header-btns">
        <button type="button" class="btn btn-gold" onclick="openCiteModal()">引用元・仕様</button>
        <button type="button" class="btn" onclick="openBulkModal()">CSV / TSV入出力</button>
        <button type="button" class="btn btn-dark admin-only" onclick="openFirstFilteredEdit()">+ 数値編集</button>
      </div>
    </div>
  </header>

  <main class="container">
    <!-- 1. 検索・収録範囲・形態・絞り込みパネル -->
    <section class="panel">
      <div class="panel-head">
        <div class="panel-title">
          <span class="badge-stamp">検索・形態</span>
          <span>表示キャラ: <span id="matchCount" style="color:var(--crimson)">0</span>件</span>
          <span id="catalogSummary" style="font-size:10.5px; color:var(--ink-sub)"></span>
        </div>
        <div style="display:flex; flex-wrap:wrap; gap:6px; align-items:center;">
          <div class="seg-group">
            <button type="button" id="scopeAll888Btn" class="seg-btn active" onclick="setCatalogScope('all888')">全888枠表示</button>
            <button type="button" id="scopeActive864Btn" class="seg-btn" onclick="setCatalogScope('active864')">実装キャラのみ(864体)</button>
          </div>
          <div class="seg-group">
            <button type="button" id="viewCardBtn" class="seg-btn active" onclick="setViewMode('card')">カード一覧</button>
            <button type="button" id="viewTableBtn" class="seg-btn" onclick="setViewMode('table')">index_status全項目表</button>
          </div>
        </div>
      </div>

      <div class="search-row">
        <div class="search-field">
          <input
            id="searchInput"
            type="text"
            class="input-box"
            placeholder="第1形態名・各形態名・図鑑No.(001〜888)・属性・特性で検索..."
          />
          <button id="clearSearchBtn" type="button" class="clear-btn" style="display:none" onclick="clearSearch()">✕</button>
        </div>

        <div style="flex:1 1 180px;">
          <select id="sortSelect" class="select-box" aria-label="並び替え">
            <option value="idAsc">図鑑No.昇順 (001→888)</option>
            <option value="idDesc">図鑑No.降順 (888→001)</option>
            <option value="name1Asc">第1形態名 (五十音順)</option>
            <option value="hpDesc">体力 (高い順)</option>
            <option value="atkDesc">攻撃力 (高い順)</option>
            <option value="dpsDesc">DPS (高い順)</option>
            <option value="rangeDesc">感知射程 (長い順)</option>
            <option value="costAsc">コスト (安い順)</option>
            <option value="rechAsc">再生産 (早い順)</option>
            <option value="speedDesc">速度 (速い順)</option>
          </select>
        </div>

        <button type="button" class="btn" onclick="resetFilters()">リセット</button>
      </div>

      <!-- 一括形態切替バー -->
      <div class="filter-block">
        <div class="filter-label"><span>一括形態切替（全キャラ共通）</span></div>
        <div id="globalFormSeg" class="seg-group">
          <button type="button" class="seg-btn active" data-gform="latest" onclick="setGlobalForm('latest')">最終形態</button>
          <button type="button" class="seg-btn" data-gform="0" onclick="setGlobalForm('0')">第1形態</button>
          <button type="button" class="seg-btn" data-gform="1" onclick="setGlobalForm('1')">第2形態</button>
          <button type="button" class="seg-btn" data-gform="2" onclick="setGlobalForm('2')">第3形態</button>
          <button type="button" class="seg-btn" data-gform="3" onclick="setGlobalForm('3')">第4形態</button>
        </div>
      </div>

      <!-- レア度フィルタ -->
      <div class="filter-block">
        <div class="filter-label"><span>レア度</span></div>
        <div id="rarityChips" class="chip-wrap"></div>
      </div>

      <!-- 対象属性フィルタ -->
      <div class="filter-block">
        <div class="filter-label"><span>対象属性</span></div>
        <div id="traitChips" class="chip-wrap"></div>
      </div>

      <!-- 特殊能力・無効フィルタ -->
      <div class="filter-block">
        <div class="filter-label">
          <span>特殊能力・無効特性（複数選択可）</span>
          <button id="abilityModeBtn" type="button" class="chip" style="min-height:24px; padding:2px 8px;" onclick="toggleAbilityMode()">条件: AND (すべて含む)</button>
        </div>
        <div id="abilityChips" class="chip-wrap"></div>
      </div>
    </section>

    <!-- 2. レベル・お宝・本能・研究コンボ計算機 -->
    <section class="panel">
      <div class="panel-head">
        <div class="panel-title">
          <span class="badge-stamp">計算機</span>
          <span id="calcHeaderLabel">Lv.20 + 90 (基本お宝2.5倍)</span>
        </div>
        <div style="display:flex; flex-wrap:wrap; gap:5px;">
          <button type="button" class="btn" style="min-height:28px; padding:3px 8px;" onclick="setPresetLevel(1, 0)">Lv.1</button>
          <button type="button" class="btn" style="min-height:28px; padding:3px 8px;" onclick="setPresetLevel(30, 0)">Lv.30</button>
          <button type="button" class="btn" style="min-height:28px; padding:3px 8px;" onclick="setPresetLevel(50, 0)">Lv.50</button>
          <button type="button" class="btn btn-gold" style="min-height:28px; padding:3px 8px;" onclick="setPresetLevel(20, 90)">20+90</button>
          <button type="button" id="toggleCalcBtn" class="btn" style="min-height:28px; padding:3px 8px;" onclick="toggleCalcPanel()">折りたたむ ▲</button>
        </div>
      </div>

      <div id="calcBody">
        <div class="slider-grid">
          <div class="slider-card">
            <div class="slider-head">
              <span>基本レベル (Lv.1〜60)</span>
              <span id="baseLvLabel" style="color:#b45309">Lv.20</span>
            </div>
            <div class="slider-ctrl">
              <button type="button" class="step-btn" onclick="stepLevel('base', -1)">-</button>
              <input id="baseLvSlider" type="range" min="1" max="60" value="20" />
              <button type="button" class="step-btn" onclick="stepLevel('base', 1)">+</button>
            </div>
          </div>

          <div class="slider-card">
            <div class="slider-head">
              <span>プラス値 (+0〜+90)</span>
              <span id="plusLvLabel" style="color:#b45309">+90 (計 Lv.110)</span>
            </div>
            <div class="slider-ctrl">
              <button type="button" class="step-btn" onclick="stepLevel('plus', -1)">-</button>
              <input id="plusLvSlider" type="range" min="0" max="90" value="90" />
              <button type="button" class="step-btn" onclick="stepLevel('plus', 1)">+</button>
            </div>
          </div>
        </div>

        <div class="calc-opt-grid">
          <label class="opt-pill">
            <input id="treasureBaseChk" type="checkbox" checked />
            <span>基本お宝 2.5倍</span>
          </label>
          <label class="opt-pill">
            <input id="treasureTraitChk" type="checkbox" checked />
            <span>対属性お宝 MAX</span>
          </label>
          <label class="opt-pill">
            <input id="treasureStudyChk" type="checkbox" checked />
            <span>研究お宝 (-264F)</span>
          </label>
          <label class="opt-pill">
            <input id="instinctHpChk" type="checkbox" />
            <span>本能: HP +20%</span>
          </label>
          <label class="opt-pill">
            <input id="instinctAtkChk" type="checkbox" />
            <span>本能: ATK +20%</span>
          </label>
          <div class="opt-pill" style="padding:3px 6px;">
            <select id="comboSelect" style="width:100%; border:none; background:transparent; font-size:11px; font-weight:800; outline:none;" aria-label="研究コンボ">
              <option value="0">研究コンボ: なし (0F)</option>
              <option value="26">研究コンボ: 小 (-26F)</option>
              <option value="52">研究コンボ: 中 (-52F)</option>
              <option value="78">研究コンボ: 大 (-78F)</option>
              <option value="104">研究コンボ: 中+中 (-104F)</option>
            </select>
          </div>
        </div>
      </div>
    </section>

    <!-- 上部ページネーション -->
    <div id="paginationTop" class="pagination"></div>

    <!-- メイン出力領域（カード一覧 / index_status.html 全項目表） -->
    <section id="mainOutput"></section>

    <!-- 下部ページネーション -->
    <div id="paginationBottom" class="pagination"></div>

    <!-- 出典・引用元フッター -->
    <footer class="footer-cite">
      <strong>データ出典・計算仕様:</strong>
      ポノス株式会社『にゃんこ大戦争』ゲーム内図鑑（No.001〜No.888 全888枠収録） /
      <a href="https://battlecats-db.com/unit/index_status.html" target="_blank" rel="noopener noreferrer">Battle Cats DB (index_status.html)</a>
      準拠計算式（1秒＝30フレーム、10レベル刻み成長率テーブル <code>growth[10]</code>、お宝2.5倍・整数切り捨て丸め適用。デフォルトLv.20+90で battlecats-db.com の表示に近い数値）。
    </footer>
  </main>

  <!-- 詳細＆カスタム数値編集モーダル -->
  <div id="detailModal" class="modal-backdrop" onclick="if(event.target===this) closeDetailModal()">
    <div class="modal-box">
      <div style="display:flex; align-items:center; justify-content:space-between; gap:8px; border-bottom:2px solid var(--ink); padding-bottom:6px;">
        <div>
          <span id="modalNoBadge" class="badge-stamp">No.001</span>
          <span id="modalRarityBadge" class="badge-stamp" style="background:var(--crimson); color:#fff;">基本</span>
          <span id="modalCalibBadge" class="badge-stamp" style="background:var(--emerald); color:#fff;">実測検証済</span>
        </div>
        <button type="button" class="btn" onclick="closeDetailModal()">閉じる ✕</button>
      </div>

      <div style="margin-top:8px;">
        <div style="font-size:11px; font-weight:800; color:var(--ink-sub);">
          第1形態名: <strong id="modalForm1Name" style="color:var(--ink)">ネコ</strong>
        </div>
        <h2 id="modalCurName" style="margin:2px 0 6px; font-size:18px; font-weight:900;">ネコ</h2>
        <div id="modalFormTabs" class="seg-group"></div>
      </div>

      <div id="modalStatsGrid" class="detail-grid"></div>

      <div style="margin-top:8px; background:var(--bg-sub); border:1.5px solid var(--ink); border-radius:9px; padding:7px 9px; font-size:11px;">
        <div style="font-weight:900; margin-bottom:3px;">10レベル刻み成長率テーブル growth[10] (Lv.1-10 〜 Lv.91+)</div>
        <div id="modalGrowthText" style="font-family:monospace; font-weight:800;"></div>
      </div>

      <!-- カスタム数値編集フォーム（管理者ロック解除時のみ表示） -->
      <div class="admin-only" style="margin-top:10px; border-top:2px solid var(--ink); padding-top:8px;">
        <div style="display:flex; align-items:center; justify-content:space-between;">
          <span style="font-size:12px; font-weight:900;">[管理者専用] 全員に反映するステータス編集・保存</span>
          <button type="button" class="btn" style="min-height:26px; font-size:10px;" onclick="resetSingleUnit()">初期値に戻す</button>
        </div>
        <div class="form-edit-grid">
          <label class="field-lbl">形態名称<input id="edName" type="text" /></label>
          <label class="field-lbl">Lv.1 体力<input id="edHp" type="number" min="1" /></label>
          <label class="field-lbl">Lv.1 攻撃力<input id="edAtk" type="number" min="0" /></label>
          <label class="field-lbl">KB数<input id="edKb" type="number" min="1" /></label>
          <label class="field-lbl">速度<input id="edSpeed" type="number" min="1" /></label>
          <label class="field-lbl">感知射程<input id="edRange" type="number" min="1" /></label>
          <label class="field-lbl">攻撃頻度(F)<input id="edFreq" type="number" min="1" /></label>
          <label class="field-lbl">攻撃発生(F)<input id="edFore" type="number" min="1" /></label>
          <label class="field-lbl">後隙(F)<input id="edBack" type="number" min="0" /></label>
          <label class="field-lbl">攻撃間隔TBA(F)<input id="edTba" type="number" min="0" /></label>
          <label class="field-lbl">第2章コスト<input id="edCost" type="number" min="0" /></label>
          <label class="field-lbl">再生産(F)<input id="edRech" type="number" min="60" /></label>
          <label class="field-lbl">範囲種別
            <select id="edArea">
              <option value="範囲攻撃">範囲攻撃</option>
              <option value="単体攻撃">単体攻撃</option>
            </select>
          </label>
        </div>
        <div class="form-edit-grid" style="grid-template-columns: 1fr 1fr;">
          <label class="field-lbl">対象属性 (カンマ区切り)<input id="edTraits" type="text" placeholder="赤い敵, 黒い敵" /></label>
          <label class="field-lbl">特殊能力・無効 (カンマ区切り)<input id="edAbilities" type="text" placeholder="めっぽう強い, ふっとばす" /></label>
        </div>
        <div style="margin-top:8px; display:flex; justify-content:flex-end; gap:6px;">
          <button type="button" class="btn btn-crimson" onclick="saveUnitEdit()">保存して全員に反映</button>
        </div>
      </div>
    </div>
  </div>

  <!-- 引用元・計算仕様モーダル -->
  <div id="citeModal" class="modal-backdrop" onclick="if(event.target===this) closeCiteModal()">
    <div class="modal-box">
      <div style="display:flex; align-items:center; justify-content:space-between; border-bottom:2px solid var(--ink); padding-bottom:6px;">
        <h2 style="margin:0; font-size:15px; font-weight:900;">引用元・ステータス計算仕様</h2>
        <button type="button" class="btn" onclick="closeCiteModal()">閉じる ✕</button>
      </div>
      <div style="font-size:12px; font-weight:700; line-height:1.65; margin-top:8px;">
        <p style="margin:0 0 8px;">
          <strong>1. 出典・リファレンス</strong><br />
          ・ポノス株式会社『にゃんこ大戦争』ゲーム内図鑑（No.001〜No.888）<br />
          ・<a href="https://battlecats-db.com/unit/index_status.html" target="_blank" rel="noopener noreferrer" style="color:var(--blue); font-weight:900;">Battle Cats DB - index_status.html</a>（ステータス全指標・フレーム定義・成長率仕様参照）
        </p>
        <p style="margin:0 0 8px;">
          <strong>2. 全888枠収録（数値欠損ゼロ）</strong><br />
          ・実装キャラクター864体（第1〜第4形態名称・精霊枠含む）に加え、図鑑の欠番24枠（No.056, 156, 183, 286, 321, 340, 354, 433, 434, 466, 493, 498〜501, 741〜746, 789）を <code>未実装枠</code> として実装し、全888枠すべてに数値・フレーム・成長率データを備えています。
        </p>
        <p style="margin:0;">
          <strong>3. 計算式 (1秒 = 30フレーム)</strong><br />
          ・レベル倍率 <code>mult</code>：10レベル刻み成長率配列 <code>growth[0..9]</code> に基づき累積加算。<br />
          ・体力 <code>HP = floor(round(baseHp * mult * (instinctHp ? 1.2 : 1)) * (treasureBase ? 2.5 : 1))</code><br />
          ・攻撃力 <code>ATK = floor(round(baseAtk * mult * (instinctAtk ? 1.2 : 1)) * (treasureBase ? 2.5 : 1))</code><br />
          ・<code>DPS = round(ATK / (freqF / 30))</code><br />
          ・コスト：第2章基準値に対し、第1章は <code>×2/3</code>、第3章は <code>×4/3</code>。<br />
          ・再生産：<code>max(60, rechF - (treasureStudy ? 264 : 0) - comboStudyF)</code>
        </p>
      </div>
    </div>
  </div>

  <!-- TSV一括取込・CSV/JSON出力モーダル -->
  <div id="bulkModal" class="modal-backdrop" onclick="if(event.target===this) closeBulkModal()">
    <div class="modal-box">
      <div style="display:flex; align-items:center; justify-content:space-between; border-bottom:2px solid var(--ink); padding-bottom:6px;">
        <h2 style="margin:0; font-size:15px; font-weight:900;">CSV / TSV / JSON 入出力</h2>
        <button type="button" class="btn" onclick="closeBulkModal()">閉じる ✕</button>
      </div>
      <div style="margin-top:8px; display:flex; flex-wrap:wrap; gap:6px;">
        <button type="button" class="btn btn-gold" onclick="exportCsv()">現在表示条件でCSV出力</button>
        <button type="button" class="btn admin-only" onclick="exportJson()">全カスタム保存データをJSON出力</button>
        <button type="button" class="btn btn-crimson admin-only" onclick="clearAllCustomData()">カスタム編集を全消去</button>
      </div>
      <div class="admin-only">
        <div style="margin-top:10px; font-size:11.5px; font-weight:800;">
          [管理者専用] TSV一括貼り付け取込（全員に即時反映されます / 形式: <code>図鑑No[TAB]形態番号(1-4)[TAB]体力[TAB]攻撃力[TAB]KB[TAB]速度[TAB]射程[TAB]頻度F[TAB]発生F[TAB]TBAF[TAB]コスト[TAB]再生産F</code>）
        </div>
        <textarea id="tsvInput" rows="6" style="width:100%; margin-top:4px; border:2px solid var(--ink); border-radius:8px; padding:6px; font-size:16px; font-family:monospace;" placeholder="001	3	540	43	3	10	140	37	8	28	75	60"></textarea>
        <div style="margin-top:6px; display:flex; justify-content:flex-end;">
          <button type="button" class="btn btn-dark" onclick="importTsv()">TSVを取り込んで全員に反映</button>
        </div>
      </div>
    </div>
  </div>

  <div id="toast" class="toast"></div>

  <script>
    // =========================================================================
    // 0-A. Firebase Firestore 設定（ご自身のFirebaseプロジェクト設定を貼り付け）
    // =========================================================================
    const FIREBASE_CONFIG = {
      apiKey: "YOUR_API_KEY",
      authDomain: "adpgjn.firebaseapp.com",
      projectId: "adpgjn",
      storageBucket: "adpgjn.firebasestorage.app",
      messagingSenderId: "YOUR_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    const FIRESTORE_COLLECTION = "nyanko_zukan";
    const FIRESTORE_DOC_ID = "overrides_v1";
    let db = null;
    let cloudOverridesCache = {};

    function initCloudSync() {
      const badge = document.getElementById("cloudStatusBadge");
      try {
        if (!window.firebase || !FIREBASE_CONFIG.apiKey || FIREBASE_CONFIG.apiKey === "YOUR_API_KEY") {
          badge.textContent = "ローカル保存のみ(Firebase未設定)";
          badge.classList.add("offline");
          return;
        }
        if (!firebase.apps.length) {
          firebase.initializeApp(FIREBASE_CONFIG);
        }
        db = firebase.firestore();
        badge.textContent = "クラウド同期中...";
        badge.classList.remove("offline");

        db.collection(FIRESTORE_COLLECTION).doc(FIRESTORE_DOC_ID).onSnapshot((doc) => {
          if (doc.exists) {
            const payload = doc.data() || {};
            cloudOverridesCache = payload.overrides || {};
            localStorage.setItem(STORAGE_KEY, JSON.stringify(cloudOverridesCache));
            applyOverridesMap(cloudOverridesCache);
            renderApp();
          }
          badge.textContent = "全員同期ON";
          badge.classList.remove("offline");
        }, (err) => {
          console.warn("Firestore snapshot error:", err);
          badge.textContent = "同期エラー(ローカル動作)";
          badge.classList.add("offline");
        });
      } catch (e) {
        console.warn("Firebase init failed:", e);
        badge.textContent = "ローカル保存";
        badge.classList.add("offline");
      }
    }

    async function pushOverridesToCloud(overridesMap) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(overridesMap));
      if (!db) return false;
      try {
        await db.collection(FIRESTORE_COLLECTION).doc(FIRESTORE_DOC_ID).set({
          overrides: overridesMap,
          updatedAt: firebase.firestore.FieldValue.serverTimestamp()
        });
        return true;
      } catch (e) {
        console.error("Cloud save failed:", e);
        showToast("クラウド保存に失敗しました（ローカルには保存済）");
        return false;
      }
    }

    // =========================================================================
    // 0-B. 管理者ロック設定 (初期パスコード: "kazuki888" の SHA-256 ハッシュ)
    // =========================================================================
    const ADMIN_PASS_HASH = "e6916b87792b2bc23015f4e8e383688996215c2586c1660417f70b86819660a7";
    const ADMIN_SESSION_KEY = "nyanko_zukan_admin_unlocked_v1";

    function isAdminUnlocked() {
      return document.body.classList.contains("admin-mode");
    }

    async function sha256Hex(text) {
      if (window.crypto && window.crypto.subtle) {
        const buf = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(text));
        return Array.from(new Uint8Array(buf)).map((b) => b.toString(16).padStart(2, "0")).join("");
      }
      return text;
    }

    function setAdminMode(enabled) {
      document.body.classList.toggle("admin-mode", enabled);
      if (enabled) {
        sessionStorage.setItem(ADMIN_SESSION_KEY, "1");
        showToast("管理者モードを解除しました（編集内容が全員に反映されます）");
      } else {
        sessionStorage.removeItem(ADMIN_SESSION_KEY);
        showToast("管理者モードをロックしました");
      }
    }

    async function promptAdminUnlock() {
      if (isAdminUnlocked()) {
        if (confirm("管理者モードを終了してロックしますか？")) {
          setAdminMode(false);
        }
        return;
      }
      const input = prompt("管理者パスコードを入力してください:");
      if (input === null) return;
      const hashed = await sha256Hex(input.trim());
      if (hashed === ADMIN_PASS_HASH) {
        setAdminMode(true);
      } else {
        alert("パスコードが一致しません");
      }
    }

    // =========================================================================
    // 1. マスター定数・成長率テーブル
    // =========================================================================
    const RARITIES = ["基本", "EX", "レア", "激レア", "超激レア", "伝説レア", "精霊", "未実装枠"];
    const TRAITS = [
      "赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使",
      "エイリアン", "ゾンビ", "古代種", "悪魔", "白い敵"
    ];
    const ABILITIES = [
      "めっぽう強い", "超ダメージ", "極ダメージ", "打たれ強い", "超打たれ強い",
      "ふっとばす", "動きを止める", "動きを遅くする", "攻撃力ダウン", "攻撃無効",
      "クリティカル", "渾身の一撃", "波動攻撃", "烈波攻撃", "小波動", "小烈波",
      "遠方攻撃", "全方位攻撃", "ゾンビキラー", "バリアブレイカー", "シールドブレイカー",
      "超生命体特効", "超獣特効", "超賢者特効", "生き残る", "撃破時お金アップ",
      "波動ストッパー", "波動無効", "烈波無効", "ふっとばし無効", "動きを止める無効",
      "動きを遅くする無効", "攻撃力ダウン無効", "ワープ無効", "古代の呪い無効", "毒撃無効"
    ];

    const GROWTH_TABLES = {
      standard: [20, 20, 20, 20, 20, 20, 14, 14, 7, 7],
      crazed:   [20, 20, 10, 10, 10, 5, 5, 5, 5, 5],
      legend:   [20, 20, 20, 10, 10, 10, 5, 5, 5, 5],
      fixed:    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
    };

    // =========================================================================
    // 2. 個別実測検証済みステータス辞書 (Lv.1基準値)
    // =========================================================================
    const VERIFIED_STATS = {
      "001-0": [100, 8, 3, 10, 140, 37, 8, 1, 28, 75, 60, "単体攻撃", [], [], "standard", "", null],
      "001-1": [100, 8, 3, 10, 140, 37, 8, 1, 28, 75, 60, "単体攻撃", [], [], "standard", "", null],
      "001-2": [200, 16, 3, 10, 140, 37, 8, 1, 28, 75, 60, "単体攻撃", [], [], "standard", "", null],
      "002-0": [400, 2, 1, 8, 110, 67, 8, 1, 58, 150, 60, "範囲攻撃", [], [], "standard", "", null],
      "002-1": [400, 2, 1, 8, 110, 67, 8, 1, 58, 150, 60, "範囲攻撃", [], [], "standard", "", null],
      "002-2": [600, 4, 1, 8, 110, 67, 8, 1, 58, 150, 60, "範囲攻撃", [], [], "standard", "", null],
      "003-0": [200, 25, 3, 12, 150, 27, 8, 1, 18, 300, 60, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "standard", "", null],
      "003-1": [200, 25, 3, 12, 150, 27, 8, 1, 18, 300, 60, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "standard", "", null],
      "003-2": [400, 50, 3, 12, 150, 27, 8, 1, 18, 300, 60, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "standard", "", null],
      "004-0": [400, 100, 3, 10, 350, 127, 8, 1, 118, 600, 76, "単体攻撃", [], [], "standard", "", null],
      "004-1": [400, 100, 3, 10, 350, 125, 6, 1, 118, 600, 76, "単体攻撃", [], [], "standard", "", null],
      "004-2": [800, 200, 3, 10, 350, 125, 6, 1, 118, 600, 236, "単体攻撃", ["エイリアン"], ["めっぽう強い"], "standard", "", null],
      "005-0": [500, 13, 5, 30, 140, 10, 6, 1, 3, 750, 60, "単体攻撃", [], [], "standard", "", null],
      "005-1": [500, 13, 5, 30, 140, 10, 6, 1, 3, 750, 60, "単体攻撃", [], [], "standard", "", null],
      "005-2": [1000, 26, 5, 30, 140, 10, 6, 1, 3, 750, 136, "単体攻撃", [], [], "standard", "", null],
      "006-0": [300, 140, 4, 10, 170, 49, 10, 1, 38, 975, 60, "範囲攻撃", [], [], "standard", "", null],
      "006-1": [300, 165, 4, 10, 170, 57, 18, 1, 38, 975, 60, "範囲攻撃", [], [], "standard", "", null],
      "006-2": [600, 330, 4, 10, 170, 57, 18, 1, 38, 975, 116, "範囲攻撃", ["天使"], ["攻撃力ダウン"], "standard", "", null],
      "007-0": [700, 180, 3, 10, 150, 53, 10, 1, 42, 1200, 136, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "standard", "", null],
      "007-1": [700, 180, 3, 10, 150, 53, 10, 1, 42, 1200, 136, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "standard", "", null],
      "007-2": [1400, 360, 3, 10, 150, 53, 10, 1, 42, 1200, 256, "単体攻撃", ["赤い敵", "メタルな敵"], ["めっぽう強い", "クリティカル"], "standard", "", null],
      "008-0": [800, 350, 3, 10, 400, 129, 10, 1, 118, 1500, 316, "単体攻撃", [], [], "standard", "", null],
      "008-1": [800, 350, 3, 10, 400, 127, 8, 1, 118, 1500, 316, "単体攻撃", [], [], "standard", "", null],
      "008-2": [900, 400, 3, 10, 400, 127, 8, 1, 118, 1500, 516, "単体攻撃", [], [], "standard", "", null],
      "009-0": [1000, 280, 1, 8, 150, 67, 18, 1, 48, 1950, 556, "範囲攻撃", [], [], "standard", "", null],
      "009-1": [1000, 280, 1, 8, 150, 67, 18, 1, 48, 1950, 556, "範囲攻撃", [], [], "standard", "", null],
      "009-2": [2000, 560, 1, 8, 150, 67, 18, 1, 48, 1950, 676, "範囲攻撃", [], ["ふっとばす"], "standard", "", null],
      "010-0": [400, 100, 3, 10, 350, 126, 7, 1, 118, 900, 76, "範囲攻撃", [], [], "standard", "", null],
      "010-1": [400, 100, 3, 10, 350, 126, 7, 1, 118, 900, 76, "範囲攻撃", [], [], "standard", "", null],
      "010-2": [650, 100, 3, 10, 350, 86, 7, 1, 78, 900, 76, "範囲攻撃", [], [], "standard", "", null],
      "011-0": [900, 1100, 1, 9, 300, 210, 11, 1, 198, 2250, 606, "範囲攻撃", [], [], "standard", "", null],
      "011-1": [900, 1210, 1, 9, 300, 210, 11, 1, 198, 2250, 606, "範囲攻撃", [], [], "standard", "", null],
      "011-2": [900, 1210, 1, 9, 300, 210, 11, 1, 198, 2250, 606, "範囲攻撃", [], [], "standard", "", null],
      "025-2": [2400, 1450, 3, 12, 350, 118, 22, 1, 95, 3000, 1646, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ"], ["動きを止める"], "standard", "", null],
      "026-0": [3000, 6000, 3, 6, 450, 600, 121, 1, 478, 4500, 4746, "範囲攻撃", [], [], "standard", "", null],
      "026-1": [4500, 9000, 3, 6, 450, 600, 121, 1, 478, 4500, 4746, "範囲攻撃", [], [], "standard", "", null],
      "026-2": [4500, 16500, 6, 65, 200, 93, 5, 1, 87, 4500, 2946, "範囲攻撃", [], [], "standard", "", [15000, 750, 750]],
      "033-2": [1400, 2200, 3, 22, 220, 125, 21, 1, 103, 675, 416, "単体攻撃", ["赤い敵", "エイリアン"], ["動きを止める"], "standard", "", null],
      "034-2": [3400, 420, 2, 12, 170, 55, 8, 1, 46, 825, 446, "範囲攻撃", ["浮いてる敵"], ["打たれ強い", "攻撃力ダウン", "波動ストッパー"], "standard", "", null],
      "037-2": [2400, 1850, 3, 34, 150, 72, 9, 1, 62, 825, 326, "単体攻撃", ["浮いてる敵"], ["超ダメージ"], "standard", "", null],
      "038-2": [700, 420, 2, 16, 140, 45, 8, 1, 36, 240, 60, "単体攻撃", [], ["生き残る"], "standard", "", null],
      "042-2": [1100, 740, 4, 10, 350, 68, 10, 1, 57, 900, 146, "範囲攻撃", [], ["生き残る"], "standard", "", null],
      "044-2": [10800, 5400, 1, 6, 190, 105, 19, 1, 85, 3600, 1746, "範囲攻撃", ["赤い敵", "エイリアン"], ["打たれ強い", "波動ストッパー", "ふっとばし無効"], "standard", "", null],
      "045-2": [5200, 5600, 4, 15, 180, 68, 25, 1, 42, 3000, 846, "単体攻撃", [], [], "standard", "", null],
      "047-2": [1250, 380, 3, 12, 180, 41, 11, 1, 29, 525, 146, "単体攻撃", ["メタルな敵"], ["クリティカル"], "standard", "", null],
      "060-3": [14400, 8400, 2, 18, 240, 115, 22, 1, 92, 3150, 1346, "範囲攻撃", [], ["波動攻撃", "ふっとばし無効", "攻撃力ダウン無効"], "standard", "", null],
      "072-2": [4800, 4600, 5, 65, 210, 106, 6, 1, 99, 4050, 1746, "範囲攻撃", ["黒い敵"], ["超ダメージ"], "standard", "", null],
      "073-2": [11200, 4200, 2, 10, 320, 112, 34, 1, 77, 4500, 2446, "範囲攻撃", ["黒い敵"], ["打たれ強い", "攻撃力ダウン"], "standard", "", null],
      "074-3": [9200, 4800, 3, 14, 410, 135, 38, 1, 96, 4650, 2846, "範囲攻撃", ["黒い敵", "悪魔"], ["動きを止める", "シールドブレイカー", "烈波無効"], "standard", "", null],
      "076-3": [6400, 10800, 4, 12, 460, 215, 68, 1, 146, 4500, 2746, "範囲攻撃", ["浮いてる敵"], ["超ダメージ", "ふっとばし無効", "烈波無効"], "standard", "", null],
      "077-3": [6400, 10800, 4, 12, 450, 215, 68, 1, 146, 4500, 2746, "範囲攻撃", ["赤い敵"], ["超ダメージ", "動きを止める無効", "烈波無効"], "standard", "", null],
      "092-2": [1000, 80, 3, 20, 140, 37, 8, 1, 28, 75, 60, "単体攻撃", [], [], "crazed", "", null],
      "093-2": [4000, 20, 1, 14, 110, 67, 8, 1, 58, 150, 60, "単体攻撃", [], [], "crazed", "", null],
      "094-2": [2000, 320, 3, 20, 150, 43, 8, 1, 34, 300, 60, "単体攻撃", ["赤い敵"], ["めっぽう強い"], "crazed", "", null],
      "095-2": [2500, 520, 3, 14, 350, 67, 8, 1, 58, 600, 146, "単体攻撃", [], ["波動攻撃"], "crazed", "", null],
      "096-2": [5000, 220, 5, 42, 140, 11, 2, 1, 8, 750, 116, "範囲攻撃", [], [], "crazed", "", null],
      "097-2": [3000, 880, 4, 14, 240, 52, 15, 1, 36, 975, 146, "範囲攻撃", [], [], "crazed", "", null],
      "098-2": [7500, 1520, 3, 14, 150, 52, 8, 1, 43, 1200, 236, "単体攻撃", ["赤い敵"], ["めっぽう強い", "クリティカル"], "crazed", "", null],
      "099-2": [8000, 2080, 3, 14, 415, 82, 10, 1, 71, 1500, 326, "単体攻撃", [], [], "crazed", "", null],
      "100-2": [15000, 3600, 1, 10, 175, 97, 25, 1, 71, 1950, 626, "範囲攻撃", [], ["波動攻撃"], "crazed", "", null],
      "136-3": [7200, 8800, 4, 58, 850, 320, 165, 1, 154, 5850, 3946, "範囲攻撃", ["浮いてる敵", "天使"], ["ふっとばす", "動きを止める", "波動無効"], "standard", "", null],
      "138-0": [650, 460, 3, 14, 320, 64, 11, 1, 52, 750, 146, "範囲攻撃", ["黒い敵", "天使"], ["超ダメージ"], "standard", "", null],
      "138-3": [18500, 16400, 3, 12, 485, 225, 62, 1, 162, 5700, 3646, "範囲攻撃", ["黒い敵", "天使"], ["超ダメージ", "動きを遅くする", "全方位攻撃", "烈波無効"], "standard", "全方位 -350〜550", null],
      "139-3": [14800, 11200, 3, 11, 470, 175, 48, 1, 126, 4800, 3146, "範囲攻撃", ["メタルな敵", "天使"], ["動きを遅くする", "クリティカル", "波動無効"], "standard", "", null],
      "149-2": [2850, 360, 2, 10, 165, 17, 4, 1, 12, 315, 60, "単体攻撃", ["天使"], ["打たれ強い"], "standard", "", null],
      "150-2": [1250, 1150, 3, 8, 250, 74, 14, 1, 59, 540, 146, "範囲攻撃", ["浮いてる敵"], ["めっぽう強い", "生き残る"], "standard", "", null],
      "197-3": [8400, 9600, 3, 14, 440, 185, 52, 1, 132, 4650, 2946, "範囲攻撃", ["エイリアン", "天使", "古代種"], ["超ダメージ", "古代の呪い無効", "ワープ無効"], "standard", "", null],
      "201-1": [12, 20, 1, 14, 110, 45, 8, 1, 36, 600, 626, "単体攻撃", [], [], "fixed", "", null],
      "260-2": [5200, 6800, 4, 7, 600, 362, 85, 1, 276, 4500, 3646, "範囲攻撃", ["エイリアン"], ["超ダメージ", "遠方攻撃"], "standard", "遠方 450〜850", null],
      "270-1": [8800, 6400, 4, 15, 470, 165, 42, 1, 122, 6000, 3446, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ"], ["めっぽう強い", "クリティカル", "波動無効"], "standard", "", null],
      "305-3": [19600, 9800, 2, 8, 465, 195, 58, 1, 136, 4800, 3246, "範囲攻撃", ["ゾンビ", "古代種"], ["打たれ強い", "攻撃力ダウン", "ゾンビキラー", "古代の呪い無効"], "standard", "", null],
      "306-3": [16800, 11800, 3, 10, 445, 188, 52, 1, 135, 4650, 3146, "範囲攻撃", ["赤い敵", "ゾンビ"], ["超ダメージ", "ゾンビキラー", "烈波無効"], "standard", "", null],
      "307-3": [12400, 8900, 3, 13, 455, 178, 49, 1, 128, 4500, 2946, "範囲攻撃", ["ゾンビ"], ["ふっとばす", "動きを遅くする", "ゾンビキラー", "烈波無効"], "standard", "", null],
      "319-1": [6800, 4200, 5, 12, 350, 132, 31, 1, 100, 5250, 2846, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ"], ["打たれ強い", "動きを遅くする", "遠方攻撃", "波動無効"], "standard", "遠方 250〜650", null],
      "334-1": [9600, 6200, 4, 16, 480, 158, 39, 1, 118, 6000, 3446, "範囲攻撃", ["白い敵"], ["超ダメージ", "動きを止める", "波動無効"], "standard", "", null],
      "360-3": [12800, 9200, 3, 18, 360, 148, 36, 1, 111, 3900, 2246, "範囲攻撃", ["エイリアン"], ["めっぽう強い", "バリアブレイカー", "ワープ無効", "烈波無効"], "standard", "", null],
      "361-3": [10800, 6800, 4, 15, 390, 152, 38, 1, 113, 3750, 2146, "範囲攻撃", ["エイリアン"], ["ふっとばす", "動きを遅くする", "バリアブレイカー", "ワープ無効"], "standard", "", null],
      "362-3": [9800, 6400, 4, 17, 405, 144, 34, 1, 109, 3600, 2046, "範囲攻撃", ["エイリアン"], ["打たれ強い", "遠方攻撃", "バリアブレイカー", "ワープ無効"], "standard", "遠方 250〜650", null],
      "377-2": [2400, 240, 2, 12, 130, 38, 7, 1, 30, 360, 60, "範囲攻撃", ["エイリアン"], ["打たれ強い", "ワープ無効"], "standard", "", null],
      "379-1": [7400, 4400, 5, 13, 375, 128, 29, 1, 98, 5250, 2846, "範囲攻撃", ["白い敵"], ["打たれ強い", "動きを遅くする", "遠方攻撃", "波動無効"], "standard", "遠方 275〜675", null],
      "381-1": [5600, 4800, 5, 24, 325, 116, 26, 1, 89, 4350, 2346, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ"], ["超ダメージ", "遠方攻撃", "波動無効"], "standard", "遠方 200〜550", null],
      "440-2": [5800, 4800, 4, 11, 450, 210, 48, 1, 161, 4350, 3246, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種"], ["動きを止める", "遠方攻撃", "ワープ無効"], "standard", "遠方 300〜700", [1600, 1600, 1600]],
      "442-1": [6200, 5200, 5, 25, 340, 114, 25, 1, 88, 4350, 2346, "範囲攻撃", ["白い敵"], ["超ダメージ", "遠方攻撃", "波動無効"], "standard", "遠方 215〜565", null],
      "449-2": [9200, 7400, 4, 14, 400, 138, 32, 1, 105, 4800, 3146, "範囲攻撃", ["黒い敵"], ["極ダメージ", "波動無効"], "legend", "", null],
      "450-2": [7800, 6800, 4, 15, 420, 146, 36, 1, 109, 4650, 3046, "範囲攻撃", ["赤い敵", "浮いてる敵"], ["極ダメージ", "遠方攻撃"], "legend", "遠方 280〜680", null],
      "451-2": [28000, 8600, 2, 6, 380, 198, 56, 1, 141, 6750, 4146, "範囲攻撃", ["赤い敵", "浮いてる敵"], ["超打たれ強い", "波動無効"], "legend", "", null],
      "452-1": [14500, 7200, 3, 10, 450, 168, 44, 1, 123, 5400, 3446, "範囲攻撃", ["天使"], ["極ダメージ", "遠方攻撃"], "legend", "遠方 300〜700", null],
      "456-2": [7600, 6200, 4, 14, 435, 154, 38, 1, 115, 4500, 2946, "範囲攻撃", ["赤い敵", "黒い敵", "天使"], ["動きを止める", "波動攻撃", "遠方攻撃"], "legend", "遠方 250〜650", null],
      "462-1": [6400, 8200, 5, 28, 350, 122, 28, 1, 93, 4500, 2546, "範囲攻撃", ["白い敵"], ["極ダメージ", "全方位攻撃"], "legend", "全方位 -300〜450", null],
      "464-1": [32000, 11500, 1, 4, 350, 224, 68, 1, 155, 6300, 4246, "範囲攻撃", ["ゾンビ"], ["超打たれ強い", "ゾンビキラー", "波動ストッパー"], "legend", "", null],
      "479-1": [16800, 5400, 3, 14, 380, 142, 34, 1, 107, 4200, 2646, "範囲攻撃", ["エイリアン"], ["超打たれ強い", "バリアブレイカー", "ワープ無効"], "legend", "", null],
      "482-1": [9400, 6800, 4, 12, 435, 156, 39, 1, 116, 4800, 3146, "範囲攻撃", ["黒い敵", "エイリアン"], ["極ダメージ", "超打たれ強い"], "legend", "", null],
      "494-1": [6200, 5400, 4, 10, 550, 268, 72, 1, 195, 5400, 3746, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種"], ["極ダメージ", "遠方攻撃"], "legend", "遠方 400〜800", [1800, 1800, 1800]],
      "520-2": [14200, 14500, 3, 9, 320, 245, 76, 1, 168, 5250, 3646, "範囲攻撃", [], ["渾身の一撃", "波動無効", "烈波無効", "古代の呪い無効"], "standard", "", null],
      "528-2": [200, 1800, 1, 52, 160, 35, 4, 1, 30, 375, 176, "範囲攻撃", [], ["波動攻撃"], "standard", "", null],
      "530-1": [7200, 5400, 5, 14, 375, 146, 36, 1, 109, 5100, 2946, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種"], ["ふっとばす", "烈波攻撃", "遠方攻撃", "波動無効"], "standard", "遠方 275〜675", null],
      "544-1": [8400, 6400, 5, 15, 400, 142, 34, 1, 107, 5400, 3146, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種", "悪魔", "白い敵"], ["古代の呪い無効", "烈波攻撃", "遠方攻撃", "波動無効", "烈波無効"], "standard", "遠方 300〜700", null],
      "545-1": [8800, 6200, 4, 14, 410, 148, 36, 1, 111, 4650, 2946, "範囲攻撃", ["メタルな敵", "ゾンビ"], ["クリティカル", "ゾンビキラー", "遠方攻撃"], "legend", "遠方 260〜660", null],
      "586-1": [18400, 6200, 3, 12, 360, 154, 38, 1, 115, 5550, 3346, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ"], ["打たれ強い", "小波動", "全方位攻撃", "波動無効"], "standard", "全方位 -320〜420", null],
      "587-1": [14500, 5800, 3, 13, 390, 146, 35, 1, 110, 4650, 2946, "範囲攻撃", ["天使", "悪魔"], ["超打たれ強い", "シールドブレイカー", "烈波無効"], "legend", "", null],
      "610-1": [19600, 6600, 3, 13, 370, 152, 37, 1, 114, 5550, 3346, "範囲攻撃", ["白い敵"], ["打たれ強い", "小烈波", "全方位攻撃", "烈波無効"], "standard", "全方位 -330〜430", null],
      "642-1": [9800, 7400, 6, 26, 300, 108, 22, 1, 85, 3600, 1846, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種", "悪魔"], ["超ダメージ", "バリアブレイカー", "シールドブレイカー", "全方位攻撃", "波動無効"], "standard", "全方位 -280〜360", [2400, 2400, 2600]],
      "658-1": [10400, 7800, 6, 28, 310, 106, 21, 1, 84, 3600, 1846, "範囲攻撃", ["白い敵"], ["超ダメージ", "バリアブレイカー", "シールドブレイカー", "全方位攻撃", "烈波無効"], "standard", "全方位 -290〜370", [2600, 2600, 2600]],
      "659-2": [2400, 1650, 3, 24, 250, 88, 16, 1, 71, 690, 176, "範囲攻撃", ["赤い敵"], ["超ダメージ", "遠方攻撃", "超獣特効"], "standard", "遠方 200〜450", null],
      "691-1": [7600, 5800, 5, 14, 600, 176, 44, 1, 131, 5700, 3546, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種", "悪魔"], ["超ダメージ", "遠方攻撃", "波動無効"], "standard", "遠方 350〜850", [1900, 1900, 2000]],
      "706-1": [8800, 6400, 5, 15, 500, 168, 41, 1, 126, 5850, 3646, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種", "悪魔", "白い敵"], ["動きを遅くする", "小烈波", "遠方攻撃", "烈波無効"], "standard", "遠方 300〜800", [2100, 2100, 2200]],
      "732-1": [11200, 7600, 4, 16, 350, 156, 38, 1, 117, 5250, 3246, "範囲攻撃", ["赤い敵", "浮いてる敵", "黒い敵", "メタルな敵", "天使", "エイリアン", "ゾンビ", "古代種", "悪魔"], ["極ダメージ", "遠方攻撃", "波動無効", "烈波無効"], "legend", "遠方 1〜600", [2500, 2500, 2600]],
      "739-1": [12400, 8200, 4, 17, 370, 154, 37, 1, 116, 5250, 3246, "範囲攻撃", ["白い敵"], ["極ダメージ", "小烈波", "遠方攻撃", "烈波無効"], "legend", "遠方 1〜620", [2700, 2700, 2800]],
      "780-1": [11800, 6200, 4, 14, 420, 148, 36, 1, 111, 5100, 3046, "範囲攻撃", ["赤い敵", "エイリアン", "ゾンビ", "古代種"], ["打たれ強い", "動きを止める", "小波動", "全方位攻撃", "波動ストッパー"], "standard", "全方位 -350〜500", null],
      "788-1": [12200, 6600, 4, 15, 430, 146, 35, 1, 110, 5250, 3146, "範囲攻撃", ["黒い敵", "天使", "悪魔", "白い敵"], ["打たれ強い", "動きを止める", "小烈波", "全方位攻撃", "烈波無効"], "standard", "全方位 -360〜510", null],
      "816-1": [9800, 7400, 4, 16, 440, 158, 39, 1, 118, 5100, 3146, "範囲攻撃", ["浮いてる敵", "エイリアン", "悪魔"], ["極ダメージ", "烈波攻撃", "遠方攻撃", "烈波無効"], "legend", "遠方 250〜750", null],
      "882-1": [10800, 8600, 4, 18, 410, 152, 36, 1, 115, 5100, 3146, "範囲攻撃", ["浮いてる敵", "黒い敵"], ["極ダメージ", "全方位攻撃", "波動無効"], "legend", "全方位 -300〜520", null]
    };

    // =========================================================================
    // 3. No.001〜No.888 全864実装レコード
    // =========================================================================
    const RAW_TSV_DATA = `001	基本	ネコ	ネコビルダー	ネコモヒカン	
002	基本	タンクネコ	ネコカベ	ゴムネコ	
003	基本	バトルネコ	勇者ネコ	暗黒ネコ	
004	基本	キモネコ	美脚ネコ	ムキあしネコ	
005	基本	ウシネコ	ネコキリン	ネコライオン	
006	基本	ネコノトリ	ネコUFO	天空のネコ	
007	基本	ネコフィッシュ	ネコクジラ	ネコ島	
008	基本	ネコトカゲ	ネコドラゴン	ネコキングドラゴン	
009	基本	巨神ネコ	ネコダラボッチ	ネコジャラミ	
010	EX	ネコ女優	ネコ聖母	ネコマッチョ	
011	EX	カンフーにゃんこ	酔拳にゃんこ	ネコダンサー	
012	EX	Mr.	Super Mr.	Hyper Mr.	
013	EX	猫縛り	猫縛りNEO	猫縛り極上	
014	EX	女王猫	処刑人	暗黒嬢	
015	EX	ネコの箱詰め	ネコギャング	重機C・A・T	
016	EX	ネコパンツ	ネコ葉っぱ	ネコぺろきゃん	
017	EX	もねこ	スターもねこ	スーパースターもねこ	
018	EX	ネコリンリン	ネコワイルド	イチリンリン	
019	EX	ネコ忍者	ガマネコ忍者	ムササビネコ忍者	
020	EX	ネコゾンビ	デビルネコ	ネコボーン	
021	EX	ネコざむらい	ネコナイト	ネコパティシエ	
022	EX	スモウネコ	マダム・ザ・スモウ	ネコにぎり	
023	EX	ネコフィーバー	ネコカーニバル	ネコアミーゴ	
024	EX	ネコスカート	ネコタイツ	ねこななふん	
025	EX	ネコヴァルキリー	ネコヴァルキリー・真	ネコヴァルキリー・聖	
026	EX	ネコムート	狂乱のネコムート	覚醒のネコムート	
027	EX	ケリ姫	フルカラーケリ姫	ウェディングケリ姫	
028	EX	ネコひめ	ネコひめビューティ		
029	EX	ネコカプセル	ねこ盛り		
030	EX	マスクオブネコ	ネコレスラー		
031	激レア	ネコぼさつ	ネコせんじゅ	ネコにょらい	
032	激レア	ネコ番長	怒りのネコ番長	押忍！ネコ番長	
033	激レア	ネコザイル	ネコ裸踊り	ネコカンカン	
034	激レア	ねこタツ	ねこTV	ねこタコつぼ	
035	超激レア	ネコルガ	ケサランパサラン	ウルトラケサラン	
036	激レア	オタネコ	ネコハッカー	ネコスーパーハッカー	
037	激レア	ネコスイマー	ネコバタフライ	ネコ半魚人	
038	レア	ネコホッピング	ネコマサイ	ネコキョンシー	
039	レア	ネコ車輪	ネコ太陽	ネコクール	
040	激レア	ネコリンゴ	カオルくん	ネコアップル	
041	激レア	ネコバスたぶ	セクシーネコバスたぶ	ネコバスたぶゴールデン	
042	レア	ネコエステ	ネコジェンヌ	ネコパーフェクト	
043	超激レア	ネコアイス	ネコアイスクイーン	ネコアイスクリスタル	
044	超激レア	ネコマシン	ネコマシン・破	ネコマシン・滅	
045	超激レア	鬼にゃんま	鬼神にゃんま	獄炎鬼にゃんま	
046	EX	芸者ねこ	ネコでありんした		
047	レア	ねこジュラ	ねこジュラシッター	ねこジュラザウルス	
048	レア	ねこファイター	メガトンファイター	マキシマムファイター	
049	レア	ねこ海賊	ニャーコック船長	ニャック・スパロウ	
050	レア	ねこ泥棒	怪盗ニャコン	ネゴエモン	
051	レア	ねこ僧侶	ねこ坊主	ねこ法師	
052	レア	ねこ占い師	ねこふんど師	ねこ医師	
053	レア	ネコシャーマン	ネコラマンサー	ネコオドラマンサー	
054	超激レア	エヴァンジェリスト	フィリバスター		
055	EX	10式戦車	ネコ搭乗10式戦車		
057	レア	ネコアーチャー	ネコキューピット	ネコいて座	
058	超激レア	ネコシュバリエ	ネコベルセルク	ネコパラディン	
059	レア	ネコ魔剣士	双剣ネコ魔剣士	双炎舞ネコ魔剣士	
060	超激レア	ねこベビー	ねこベビーズ	ネコベビーカーズ	ネコベビーワールド
061	レア	ネコブ・ロンズ	芸術のネコスタチュ	ネコクイズ王	
062	激レア	ねこ寿司	ねこリーゼント	ネコ極上	
063	EX	ネコポン	ネコポニョン		
064	レア	ネコ運動会	ネコラッチ	ネコ棒倒し	
065	レア	剣士	剣士ジョニー		
066	レア	ウシ姫	キリン姫		
067	超激レア	KHM48	ダークKHM48	KHM46	
068	レア	チビガウ	チビガウガウ	クリスタルチビガウガウ	
069	激レア	執行者	執行者999	ジャッジメント	
070	激レア	眠れるケリの美女	眠れるケリの美女Ⅱ	ひたすら眠れるケリの美女	
071	レア	ネコリーマン	ネコ怨み	ネコ火祭り	
072	超激レア	真田幸村	武神・真田幸村	飛翔の武神・真田幸村	
073	超激レア	前田慶次	武神・前田慶次	憤怒の武神・前田慶次	
074	超激レア	織田信長	武神・織田信長	天魔・織田信長	夢幻天魔・織田信長
075	レア	トナカイフィッシュ	ネコ魚のお造り	フィッシュ・ド・ノエル	
076	超激レア	風神のウィンディ	風神のウィンディα	疾風神のウィンディα	アルティメットウィンディα
077	超激レア	雷神のサンディア	雷神のサンディアβ	迅雷神のサンディアβ	アルティメットサンディアβ
078	レア	ドロイド	ニャンドロイド	ハルドロイド	
079	レア	ネコと宇宙	ニャア少佐	駆動戦士ネコ	
080	レア	にゃんこ成人	にゃんこ病人	にゃんこ囚人	
081	レア	吾輩は鬼である	ネコ紳士	ネコ紳士同盟	
082	レア	ひなにゃんこ	超号珍ニャジンガー	ニャラジン	
083	EX	ネコ忍者～青～	ネコ忍者～橙～		
084	超激レア	地龍ソドム	地龍王ソドム	地龍皇帝ソドム	
085	超激レア	聖龍メギドラ	聖龍王メギドラ	聖龍皇帝メギドラ	
086	超激レア	竜騎士バルス	竜騎士王バルス	竜騎士皇帝バルス	
087	超激レア	神龍かむくら	神龍王かむくら	神龍皇帝かむくら	
088	超激レア	竜戦機ライデン	竜戦機王ライデン	竜戦機皇帝ライデン	
089	レア	ネコなわとび	ネコネコなわとび	ネコ蕎麦屋	
090	超激レア	ニムエ	帰化型ニムエ		
091	超激レア	猿王	SSS猿王		
092	激レア	狂乱のネコ	狂乱のネコビルダー	大狂乱のネコモヒカン	
093	激レア	狂乱のタンクネコ	狂乱のネコカベ	大狂乱のゴムネコ	
094	激レア	狂乱のバトルネコ	狂乱の勇者ネコ	大狂乱の暗黒ネコ	
095	激レア	狂乱のキモネコ	狂乱の美脚ネコ	大狂乱のムキあしネコ	
096	激レア	狂乱のウシネコ	狂乱のキリンネコ	大狂乱のネコライオン	
097	激レア	狂乱のネコノトリ	狂乱のネコUFO	大狂乱の天空のネコ	
098	激レア	狂乱のネコフィッシュ	狂乱のネコクジラ	大狂乱のネコ島	
099	激レア	狂乱のネコトカゲ	狂乱のネコドラゴン	大狂乱のネコキングドラゴン	
100	激レア	狂乱の巨神ネコ	狂乱のネコダラボッチ	大狂乱のネコジャラミ	
101	レア	ネコ乙女	モトコ	サホリ	
102	EX	弱酸性エル	弱酸性エルCC		
103	EX	レッドマローン	レッドマローンCC		
104	EX	歌謡にゃんこ	メガにゃんこ	メガにゃんこEX	
105	レア	マジでコイしてる	コイ・the・Night	キスヨリ・ス・ゴイ	
106	超激レア	猿帝のクウ	猿帝のクウγ	金猿帝のクウγ	
107	超激レア	召し豚のカイ	召し豚のカイμ	召し猪のカイμ	
108	超激レア	宝杖のカッパーマイン	宝杖のカッパーκ	光宝杖のカッパーκ	
109	EX	にゃんこ3兄弟	秘密要塞ネコロス		
110	レア	マダム・ザ・花嫁	マダム・ザ・王様	マダム・ザ・サバンナ	
111	レア	セレス	セレスCC		
112	レア	ノノ	ノノCC	野跳ねる白猫ノノCC	
113	レア	オルガ	オルガCC		
114	レア	ノルン	ノルンCC		
115	レア	よいち	よいちCC		
116	レア	セラム	セラムCC		
117	レア	フウ	フウCC		
118	激レア	アウラ	アウラCC		
119	激レア	レイ	レイCC		
120	超激レア	ワイバーン	ニャイバーン	ニャイバーン＆アレク	
121	EX	癒術士	癒術士CC	にゃんこ化癒術士CC	
122	EX	メルク	メルクCC	にゃんことメルクCC	
123	レア	女王様の休日	いつものOL	コールセンターのOL	
124	EX	マメマメにゃんこ	ラブラブにゃんこ	ヴァルヴァルにゃんこ	
125	超激レア	伊達政宗	武神・伊達政宗	邪眼竜の武神・伊達政宗	
126	超激レア	武田信玄	武神・武田信玄	猛牛の武神・武田信玄	
127	レア	カラクリにゃんこ	ネコ人形師	ネコチャッピー	
128	EX	ネコフラワー	にゃんこ扇風	ネコボンバー	
129	レア	うらめしにゃん	ハナメガにゃん	凧にゃん	
130	激レア	金ネコ	金ネコビルダー	金ピカネコビルダー	
131	EX	ウルフとウルルン	タマとウルルン	覚醒のタマとウルルン	
132	激レア	ねねこ	スターねねこ		
133	レア	カンフーにゃんこG	酔拳にゃんこG	剛拳にゃんこG	
134	EX	ヒカキン	ビキャキン	ムキャキン	
135	超激レア	うらしまタロウ	竜宮獣ガメレオン	竜宮超獣キングガメレオン	
136	超激レア	ツルの恩返し	銀河戦士コスモ	究極戦士コズミックコスモ	究極勇者ウルトラコスモ
137	超激レア	ももたろう	爆走兄弟ピーチスター	爆走兄弟ピーチジャスティス	
138	超激レア	かさじぞう	地蔵要塞カムイ	地蔵要塞ゼロカムイ	ウルトラかさじぞう
139	超激レア	かぐやひめ	破壊衛星デスムーン	破壊衛星カオスムーン	ウルトラかぐやひめ
140	EX	ネコンドー	ネコンドーHG	ネコンドーSP	
141	EX	ふにゅふにゅネコ	むきゅむきゅネコ		
142	EX	神様	シークレット神様		
143	EX	ネコチュートリアル	裏チュートリアル		
144	超激レア	ねこナース	ハートフルねこナース	ジョイフルねこナース	
145	激レア	にゃんこ城Mini	にゃんこ城Jr	にゃんこ城Max	
146	レア	ねこガンマン	ねこウエスタン	ねこロデオ	
147	レア	たけうまねこ	トーテムねこ	ねこ雑技団	
148	レア	ブリキネコ	プレーンネコ	ネコロボット	
149	レア	ねこロッカー	ねこアーティスト	ねこラーメン道	
150	レア	ねこ人魚	ねこソルジャー	ネコカメラマン	
151	激レア	窓辺の乙女ネコ	窓辺の姫君ネコ	窓辺の舞妓にゃん	
152	激レア	ネコバーベル	ネコRG	ネコデリバリー	
153	激レア	ネコスケート	よいではにゃいか	にゃんでやねん	
154	激レア	ネコトースター	ネコ映写機	ネコクラシック	
155	レア	ねこフープ	ねこトリプルフープ	ねこグルグルフープ	
157	レア	ドラリー剣士	ドラリー魔法使い		
158	EX	ヌボボ	ゴールボ		
159	超激レア	上杉謙信	武神・上杉謙信	暴威の武神・上杉謙信	毘沙門軍神・上杉謙信
160	超激レア	冥界のカリファ	冥界のXカリファ	冥界のXXカリファ	アルティメットXXカリファ
161	激レア	ユーリンチー	転生霊鳥ユーリンチー	ゲキリンチー	
162	超激レア	狂乱のケリ姫	狂乱のケリ姫CC	大狂乱のケリ姫	
163	EX	綾小路 翔	翔やん＆にゃんこ		
164	EX	早乙女 光	光＆にゃんこ		
165	EX	西園寺 瞳	トミー＆にゃんこ		
166	EX	星グランマニエ	ランマ＆にゃんこ		
167	EX	白鳥松竹梅	松＆にゃんこ		
168	EX	ネコヤンキー	ネコスケバン		
169	超激レア	アシルガ	アシランパサラン	アタタタアシラン	
170	超激レア	クビルガ	クビランパサラン	E・クビランパサラン	
171	超激レア	テコルガ	テサランパサラン	ムギワラテサラン	
172	超激レア	バララガ	バラランパサラン	ピカランバララン	
173	EX	コニャンダム	ネコニャンダム	覚醒のネコニャンダム	
174	激レア	厨房マンボウ	工房マンボウ	社会の一員	
175	超激レア	マンボ王	生ける伝説	太平洋の主	
176	EX	にゃんぼう	ネコトーピード		
177	レア	マシュマロにゃん	キモマロにゃん		
178	超激レア	覇龍ディオラムス	覇龍王ディオラムス	覇龍皇帝ディオラムス	大覇龍皇帝ディオラムス
179	EX	極道にゃんこ	ヤクザヴァンパイア		
180	レア	タクヤとユキ	タクヤとユキとネコ	タクヤとビューティユキ	
181	超激レア	謎の少女ユキ	謎の少女ユキ＆ネコ	謎のネコ耳少女ユキ	
182	EX	ゆきにゃん	ビューティゆきにゃん		
184	レア	にゃんこカートR	にゃんこカートG	にゃんこカートP	
185	EX	ミスターニンジャ	タンタン		
186	超激レア	ハルシュト	ハルシュトCC	革命のハルシュトCC	
187	超激レア	コーネリア	コーネリアCC		
188	超激レア	ユーヴェンス	ユーヴェンスCC	休日の騎士様ユーヴェンスCC	
189	超激レア	ミスティカ	ミスティカCC	寂救のミスティカCC	
190	激レア	アロイス	アロイスCC		
191	激レア	シトルイユ	シトルイユCC		
192	EX	ティティ	ティティCC	ネコティティCC	
193	EX	山岡実乃里	クイーンみのりん		
194	EX	中村奏絵	クイーンかにー		
195	超激レア	アキラ	シャドウ・アキラ	インフェルノ・アキラ	
196	超激レア	西園寺メカ子	ドクトルDr.メカ子	アストロDr.メカ子	
197	超激レア	キャットマンダディ	キャットマンダーク	キャットマンライジング	キャットマンフォーエバー
198	レア	サイキックネコ	ネオサイキックネコ	デビルサイキックネコ	
199	レア	ねこ陰陽師	魔法少女ねこ	ねこ大魔王	
200	激レア	ネコサーファー	ネコ漂流	ネコ漂流記	
201	激レア	メタルネコ	メタルネコビルダー		
202	レア	ドラムメニャー	プラカードにゃん	ネコぺったん	
203	EX	ネコ小籠包	ネコ大籠包	大籠包にゃんこ	
204	超激レア	カチカチヤマンズ	炎護射撃車ウーウー	豪炎狙撃車ウーウー	
205	EX	アイルー	アイルー＆メラルー		
206	EX	シェリア【キリン】	シェリア【キリンX】		
207	EX	リオレウス	リオレウス＆にゃんこ		
208	EX	リオレイア	リオレイア＆にゃんこ		
209	EX	キリンネコ	キリンネコキリン		
210	EX	ちびネコ	ちびネコビルダー	ちびネコモヒカン	
211	EX	ちびタンクネコ	ちびネコカベ	ちびゴムネコ	
212	EX	ちびバトルネコ	ちび勇者ネコ	ちび暗黒ネコ	
213	超激レア	ホワイトラビット	ブラックキャット	グレーフォックス	スペクトルキャット
214	EX	にゃんこバーガー	にゃんこキングバーガー	にゃんこバーガーセット	
215	レア	百太郎	百太郎CC		
216	レア	マルコ	マルコCC		
217	レア	ターマ	ターマCC		
218	レア	エリ	エリCC		
219	レア	フィオ	フィオCC		
220	激レア	SV-001	SV-001CC		
221	激レア	アレン・オニール	アレン・オニールCC		
222	レア	マーズピープル	マーズピープルCC		
223	超激レア	ヒュージハーミット	ヒュージハーミットCC	ゴールデンヒュージハーミットCC	
224	超激レア	ジュピターキング	ジュピターキングCC	ゴールデンジュピターCC	
225	超激レア	モーデン元帥	モーデン元帥CC	ゴールデンモーデンCC	
226	超激レア	ハイ・ドゥ	ハイ・ドゥCC	ハイ・ドゥ MK III CC	
227	超激レア	呪術師デスピエロ	闇商人バビル	奇術科学者クレイG	
228	レア	にゃんぷきん	ねこランタン	死神ネコ	
229	激レア	ハロウィンねねこ	ハロウィン魔女ねねこ	仮装・ハロウィンねねこ	
230	超激レア	ウィンディスイート	ウィンディスイートα		
231	超激レア	デビルサンディア	デビルサンディアβ		
232	EX	おでん	おでんCC		
233	レア	光の神徒ヴァルキリー	光の神徒ヴァルキリーCC		
234	レア	神徒ブリュンヒルデ	神徒ブリュンヒルデCC		
235	レア	神徒スルズ	神徒スルズCC		
236	激レア	豊穣の女神フレイヤ	豊穣の女神フレイヤCC		
237	超激レア	軍神オーディン	軍神オーディンCC		
238	激レア	おかめはちもくネコ	おかめの水博士	おかめ名誉客員教授	
239	激レア	ボンボンネコ	ネコボンのパパ	ネコボンのパパのパパ	
240	激レア	見習いスニャイパー	ネゴルゴ	ネゴルゴ31	
241	超激レア	トゲルガ	トゲランパサラン	デイダラトゲラン	
242	超激レア	雪だるまのスノーカイ	雪だるまのスノーカイμ	雪妖精のスノーカイμ	
243	超激レア	サンタのメリークウ	サンタのメリークウγ		
244	超激レア	聖夜のシスターカッパー	聖夜のシスターカッパーκ		
245	レア	ネコのプレゼント	ネコプレゼント		
246	EX	ちびキモネコ	ちび美脚ネコ	ちびムキあしネコ	
247	EX	ちびウシネコ	ちびネコキリン	ちびネコライオン	
248	EX	ちびネコノトリ	ちびネコUFO	天空のちびネコ	
249	EX	死にたてにゃんこ	化けにゃんこ	復活にゃんこ	
250	レア	剣士	セーラー服剣士	明王の剣士	
251	レア	騎馬兵	森の騎馬兵	迅雷の黒騎馬兵	
252	レア	アシュラ	武者アシュラ	傾奇者アシュラ	
253	レア	バトルバルーン	海賊バトルバルーン	バトルバルーンUFO	
254	レア	ドラゴンライダー	ボーンドラゴンライダー	フレイムドラゴンライダー	
255	レア	プリティキャット	ラブリープリティキャット	サイバープリティキャット	
256	レア	サイクロプス	鋼鉄のサイクロプス	時空のサイクロプス	
257	EX	ゴーレム	うさぎゴーレム	ボルケーノゴーレム	
258	超激レア	天空神ゼウス	ギガントゼウス	Gギガントゼウス	
259	超激レア	守護神アヌビス	パーフェクトアヌビス	Gパーフェクトアヌビス	
260	超激レア	美女神アフロディーテ	メガロディーテ	Gメガロディーテ	
261	激レア	ネコウェイ	ネコドローン	ネコタイムマシン	
262	超激レア	天誅ハヤブサ	斬魔ハヤブサ	滅殺ハヤブサ	
263	EX	ニャームス	ネコニャームス		
264	EX	ポチ	タンクポチ		
265	EX	クロミ	クロミとにゃんこたち		
266	EX	せつ子	せつ子Love鯖		
267	EX	ねば～る君	ねば～る君とにゃんこ		
268	激レア	ネコタイフーン	ネコハリケーン	ネコあらし	
269	EX	ネコずきんミーニャ	ラブリィずきんミーニャ	覚醒ラブずきんミーニャ	
270	超激レア	幼獣ガオ	皇獣ガオウ		
271	超激レア	狂乱のユキ	狂乱のユキ＆ネコ	狂乱のネコ耳ユキ	
272	超激レア	太陽神アマテラス	テラアマテラス	Gテラアマテラス	
273	超激レア	繁栄神ガネーシャ	ガネーシャエクサ	Gガネーシャエクサ	
274	激レア	チアにゃんこ	ネコウエイトレス	にゃんこそば	
275	超激レア	トロピカルカリファ	トロピカルXカリファ		
276	超激レア	真夏のホワイトラビット	真夏のブラックキャット	ブライトサマーキャット	
277	激レア	夏色ねねこ	海辺のねねこ	初夏・海辺のねねこ	
278	レア	なめこ	なめこ＆にゃんこ		
279	レア	なめこタンク	なめこタンク＆にゃんこ		
280	レア	白ネコなめこ	キモ白ネコなめこ		
281	レア	要塞なめこ	なめこ＆要塞にゃんこ		
282	レア	ふてニャン	モミモミふてニャン		
283	レア	ネコ阿波踊り	ネコソーラン	ネコ舞踊ゴージャス	
284	超激レア	猫飯拳パイパイ	猫飯拳パイパイZ	猫拳王パイパイDX	
285	激レア	ネコ特急	ネコ超特急	ネコエクスプレス	
287	超激レア	閃雷機兵レイ	閃雷機兵プラズマレイ	閃雷輝兵ウイングレイ	
288	激レア	ネコ球児	ネコリーガー	ネコゴルファー	
289	超激レア	鹿目まどか	まどか＆ネコ	アルティメットまどか	
290	超激レア	暁美ほむら	ほむら＆ネコ	悪魔ほむら	
291	超激レア	美樹さやか	さやか＆ネコ		
292	超激レア	巴マミ	マミ＆ネコ		
293	超激レア	佐倉杏子	杏子＆ネコ		
294	激レア	キュゥべえ	キュゥべえ＆ネコ		
295	レア	ネコまどか	魔法少女ネコまどか		
296	レア	ネコほむら	魔法少女ネコほむら		
297	レア	ネコさやか	魔法少女ネコさやか		
298	レア	ネコマミ	魔法少女ネコマミ		
299	レア	ネコ杏子	魔法少女ネコ杏子		
300	レア	ちびまどか	ちび魔法少女まどか	ちびアルティメットまどか	
301	EX	ちびほむら	ちび魔法少女ほむら	ちび悪魔ほむら	
302	レア	ネコキュゥべえ	タンクキュゥべえ		
303	超激レア	ネコアイスゴースト	ネコアイスファントム	ネコアイスサキュバス	
304	レア	ネコ屋台	ネコの宅配便	ネコ給食当番	
305	超激レア	帝国陸軍カタパルズ	撃滅戦車ギガパルド	超撃滅戦車ギガパルド	極撃滅戦車ギガパルド
306	超激レア	古代軍船ガレーズ	無敵艦隊シーガレオン	超無敵艦隊シーガレオン	極無敵艦隊シーガレオン
307	超激レア	飛空襲撃ボンバーズ	飛行戦艦ボルボンバー	超飛行戦艦ボルボンバー	極飛行戦艦ボルボンバー
308	激レア	ネコジャンパー	ネコボールター	ネコ奥様	
309	レア	ネコバサミ	ネコチェーンソー	草刈りネコ	
310	EX	ネコ博士	ネコ博士＆ネコ		
311	超激レア	ネコナースキャンドル	ネコナースマリア	ネコナースサンタマリア	
312	EX	ちびネコフィッシュ	ちびネコクジラ	ちびネコ島	
313	EX	ちびネコトカゲ	ちびネコドラゴン	ちびネコキングドラゴン	
314	EX	ちび巨神ネコ	ちびネコダラボッチ	ちびネコジャラミ	
315	激レア	大掃除ねねこ	初詣ねねこ	新春・初詣ねねこ	
316	レア	ピコ太郎	ネコ太郎		
317	超激レア	海王神ポセイドン	ポセイドラグーン	Gポセイドラグーン	
318	EX	CPAC	PPAP		
319	超激レア	巫女姫ミタマ	白無垢のミタマ		
320	レア	殺意のネコ	真・殺意のネコ	改心のネコ	
322	EX	ナゾウサギ	兎宮王道		
323	超激レア	さるかに合戦	爆音楽奏サルカニヘヴン	超音楽奏サルカニフェス	
324	EX	宮木武蔵	主演 宮木武蔵	覚醒 宮木武蔵	
325	レア	ネコストーン	ネコクリーナー	ネコカメカー	
326	レア	ネコボクサー	ネコチャンピオン	ネコリベンジ	
327	超激レア	メビウス	奇跡の調和メビウス		
328	激レア	ベリアル	猛進の大逆者ベリアル		
329	EX	ハッピー100	幸せ連鎖子ハッピー100		
330	レア	ネコエッグ	ネコエッグ2	ネコエッグ3	
331	超激レア	上杉謙信・春	上杉謙信・春爛漫	上杉謙信・春うらら	
332	超激レア	道化師エッグダック	闇道化サイコダック	極悪道化マッドダック	
333	激レア	イースターねねこ	イースターバニーねねこ	開花・イースターバニーねねこ	
334	超激レア	黒獣ガオウ	皇獣ガオウダーク		
335	超激レア	狐娘ヒメユリ	九尾の狐姫ヒメユリ	豊穣の狐姫ヒメユリ	
336	超激レア	人魚娘ルリィ	宝玉の人魚姫ルリィ	竜宮の人魚姫ルリィ	
337	超激レア	ミイラ娘レイカ	玉座のミイラ姫レイカ	黄金のミイラ姫レイカ	
338	超激レア	プリンセスケリ姫号	プリンセスケリ姫号CC	プレミアムケリ姫号CC	
339	超激レア	今川義元	武神・今川義元	燐火の武神・今川義元	
341	レア	赤髪のゆきにゃん	赤髪のビューティゆきにゃん	赤髪のビューティムキにゃん	
342	超激レア	失われし世界のユキ	失われし世界のユキ＆ネコ	失われし世界のネコ耳ユキ	
343	EX	ネコまねき	ネコまねき・寿		
344	レア	なめネコ	ネコなめ		
345	激レア	オルトス	オルトスCC	彩色のオルトスCC	
346	超激レア	ミシェリア	ミシェリアCC	午睡のミシェリアCC	
347	超激レア	とどめき	とどめきCC		
348	EX	ぐでたまにゃんこ	ぐでオムにゃんこ	ぐでベネにゃんこ	
349	レア	ぐでたまプレート	ぐでたまベーコンプレート		
350	激レア	ぐでぐでプリン	ぐでぐでアラモード		
351	超激レア	ニセたま軍団	ネコニセたま軍団		
352	超激レア	双掌星のシシル＆コマリ	双掌星のシシル＆コマリΦ	双輝星のシシル＆コマリΦ	
353	EX	ネコ仙人	ネコ老師	仮面のネコ仙人	
355	超激レア	常夏の果実カイ	常夏の果実カイμ		
356	超激レア	観測兵器ガリレオ	時空基地アルマゲドン	超時空基地アルマゲドン	
357	EX	ノラ	覚醒ノラ		
358	超激レア	狼娘ディル	虚飾の狼姫ディル	道化の狼姫ディル	
359	超激レア	ゾンビ娘ヴェルヴェーヌ	死霊の花嫁ヴェルヴェーヌ	満腹の花嫁ヴェルヴェーヌ	
360	超激レア	火の精霊メララ	火炎の精霊メラバーン	火炎の精霊王メラバーニング	火炎の大精霊メラフェニックス
361	超激レア	水の精霊ミズリィ	流水の精霊ミズマリン	流水の精霊王ミズマリリン	流水の大精霊ミズマリナーデ
362	超激レア	風の精霊エアル	嵐の精霊エアフワン	嵐の精霊王エアフワンテ	嵐の大精霊エアフワンティ
363	超激レア	セイバー	セイバーCC	セイバーオルタCC	
364	超激レア	遠坂 凛	遠坂 凛CC		
365	超激レア	イリヤスフィール	イリヤスフィールCC		
366	超激レア	アーチャー	アーチャーCC		
367	超激レア	ランサー	ランサーCC		
368	超激レア	ライダー	ライダーCC		
369	超激レア	ギルガメッシュ	ギルガメッシュCC		
370	EX	ネコセイバー	英霊ネコセイバー	ネコセイバーオルタ	
371	レア	ネコ桜	エプロンネコ桜	夢見るネコ桜	
372	レア	ネコ凛	魔術師ネコ凛		
373	レア	ネコイリヤ	雪の妖精ネコイリヤ		
374	レア	ちび士郎	ちび士郎CC		
375	レア	ちび桜	ちび桜CC		
376	EX	記念ネコ	記念ネコビルダー		
377	レア	ネコ探査機	ネコサーチMkⅡ	ネコサテライト	
378	激レア	ネコフェンシング	ネコ剣道	自由のネコ	
379	超激レア	黒無垢のミタマ	黒蝶のミタマダーク		
380	レア	ネコバレー	ネコバレエ	ネコカレー	
381	超激レア	幼傑ダルターニャ	英傑ダルターニャ		
382	EX	ねこ農家	ねこカブ農家		
383	レア	ガラスネコ	ガラスネコビルダー	クリスタルネコビルダー	
384	EX	謎仮面のウララー	ネコ仮面のウララー	ネコ神面のウララー	
385	EX	氷上聡里＆ネコ	氷上聡里＆ネコCC		
386	レア	矢部明雄	矢部明雄CC	矢部＆ガンダーロボCC	
387	レア	虹谷彩理	虹谷彩理CC		
388	レア	紺野美崎	紺野美崎CC		
389	レア	片桐恋	片桐恋CC		
390	レア	猫塚かりん	猫塚かりんCC		
391	激レア	早川あおい	早川あおいCC	野球マン1号CC	
392	激レア	橘みずき	橘みずきCC	野球マン2号CC	
393	激レア	六道聖	六道聖CC	野球マン3号CC	
394	超激レア	新島早紀	新島早紀CC	サンバ新島早紀CC	
395	超激レア	星井スバル	星井スバルCC	暗黒スバルCC	
396	超激レア	小山雅	小山雅CC	野球マン5号CC	
397	超激レア	古龍ガングリオン	古龍王ガングリオン	古龍皇帝ガングリオン	
398	超激レア	風隼さくら	JK風隼さくら	天誅・風隼さくら	天誅Z・風隼さくら
399	EX	ハニトンくん	ハニトンくん＆にゃんこ		
400	レア	めんトリ	めんトリCC		
401	EX	イモウト	イモウトCC		
402	超激レア	雷の精霊ボルト	天雷の精霊ピカボルト	天雷の精霊王ギガボルト	天雷の大精霊テラボルタ
403	レア	ちびシンジ	プラグスーツのちびシンジ	黒プラグスーツのちびシンジ	
404	EX	ネコゲンドウ＆ネコ冬月	月のネコゲンドウ＆ネコ冬月		
405	レア	ネコキリン初号機	ネコキリン2号機		
406	EX	ちびカヲル	プラグスーツのちびカヲル	月の少年ちびカヲル	
407	レア	ネコレイ	ネコアヤナミレイ（仮称）		
408	レア	ネコアスカ	エプロンネコアスカ		
409	レア	ネコマリ	極秘入国ネコマリ		
410	激レア	ちびレイ	プラグスーツのちびレイ	再会のちびレイ	
411	激レア	ちびアスカ	プラグスーツのちびアスカ	眼帯のちびアスカ	
412	激レア	ちびマリ	プラグスーツのちびマリ		
413	超激レア	エヴァ零号機	エヴァ零号機＆ネコ		
414	超激レア	エヴァ初号機	エヴァ初号機＆ネコ	決意のエヴァ初号機＆ネコ	
415	超激レア	エヴァ2号機	エヴァ2号機＆ネコ	エヴァ改2号機コード777	
416	超激レア	ネコシンジ	ネコトウジ＆ネコケンスケ		
417	超激レア	ゼロムーンオペレーターズ	ファーストムーンネルフ		
418	超激レア	温泉天国テルマエ	地底戦隊グランドン	超地底戦隊グランドン	極地底戦隊グランドン
419	EX	狂乱のもねこ	狂乱のスターもねこ		
420	レア	ポコタ	ポコタCC		
421	レア	オビス	オビスCC		
422	レア	ココ	ココCC		
423	レア	太秦萌＆ネコ	太秦萌＆ネコCC		
424	レア	松賀咲＆ネコ	松賀咲＆ネコCC		
425	レア	小野ミサ＆ネコ	小野ミサ＆ネコCC		
426	EX	太秦麗＆ネコ	太秦麗＆ネコCC		
427	EX	ぶんぶんネコライダー	ぶんぶんロボライダー	ぶんぶんネコライダーL	
428	超激レア	ネコクエスト	ネコクエストXI	ネコクエストXI-2	ネコクエストヘブン
429	レア	そうまにゃん	スマートそうまにゃん		
430	激レア	オタネコギーク	ギークネコハッカー		
431	超激レア	ぐでネコマシン	ぐでネコマシン・卵		
432	超激レア	亡者探偵ヴィグラー	魔界探偵グラヴィー	魔神探偵グラヴィティー	聖魔探偵ゼログラヴィティー
435	EX	白にゃんこ剣士	黒にゃんこ剣士		
436	超激レア	ちびネコヴァルキリー	ちびネコヴァルキリー・華		
437	超激レア	ノビルガ	ノビランパサラン	ノビランターバン	
438	EX	激・神さま	超・神さま	超・神さまゴールデン	
439	超激レア	灼熱のビーチクウ	灼熱のビーチクウγ		
440	超激レア	時空神クロノス	クロノストリガー	Gクロノストリガー	
441	超激レア	ベベ	なぎさ＆ネコ	円環の理の使者なぎさ	
442	超激レア	影傑ダークダルターニャ	黒傑ダークダルターニャ		
443	レア	子連れにゃんこ	ネコワゴンサービス	ネコバケーション	
444	激レア	戦隊ドリラ	戦隊パワードリラ	シン・パワードリラ	
445	激レア	戦隊ウチコンガ	戦隊パワーウチコンガ	シン・パワーウチコンガ	
446	激レア	戦隊チョキンドス	戦隊パワーチョキンドス	シン・パワーチョキンドス	
447	激レア	戦隊ショベリン	戦隊パワーショベリン	シン・パワーショベリン	
448	激レア	戦隊チャッソ	戦隊パワーチャッソ	シン・パワーチャッソ	
449	伝説レア	宮本武蔵	剣神・宮本武蔵	無双の剣神・宮本武蔵	
450	伝説レア	聖会長ジャンヌダルク	聖会長ジャンヌダルクψ	神聖会長ジャンヌダルクψ	
451	伝説レア	天城龍バベル	魔城龍バベル	天魔城龍バベル	
452	伝説レア	うしわか丸	巨大神兵ベンケイ		
453	レア	古代ネコ	現代ネコ	未来ネコ	
454	EX	きたろう	ゲゲゲの鬼太郎		
455	EX	目玉おやじ	入浴おやじ		
456	伝説レア	ワンダー・モモコ	キューティー・モモコ	スターライト・モモコ	
457	超激レア	間桐 桜	間桐 桜CC	黒桜CC	
458	EX	ネコ士郎	魔術師見習いネコ士郎		
459	レア	アーチャーネコ	英霊アーチャーネコ		
460	レア	ネコライダー	英霊ネコライダー		
461	激レア	ネコ言峰＆ネコギルガメッシュ	ネコギルガメッシュ＆ネコ言峰		
462	伝説レア	レジェルガ	レジェランパサラン		
463	EX	フィリバスターX	フィリバスターY	メタフィリバスター	
464	伝説レア	終末兵器ムー	極秘研究機関パンドラ		
465	EX	土偶戦士ドグ丸	土偶戦士ドグえもん	覚醒戦士ドグえもん	
467	伝説レア	ブラックゼウス	闇次神子ブラックゼウス		
468	超激レア	スーパーゼウス	全能神スーパーゼウス	スーパーSゼウス	
469	超激レア	聖フェニックス	聖戦衣化聖フェニックス	ヘッドロココ	
470	超激レア	スーパーデビル	偽神スーパーデビル	スーパーDデビル	
471	超激レア	サタンマリア	ワンダーマリア		
472	超激レア	ヘラクライスト	完璧ヘラクライスト	ブラックライスト	
473	激レア	ネロ魔身	魔肖ネロ	デカネロン	
474	レア	ヤマト王子	ヤマト神帝	ヤマト爆神	
475	EX	十字架天使	クロスエンジェル	ストライク天使	
476	EX	ネコ若神子	ネコ神帝		
477	EX	ネコデビル	ネコ邪神		
478	EX	ネコの助	ネコ老守		
479	伝説レア	幻の精霊ルミナ	夢幻の精霊ルミナリア		
480	EX	グミネコ	ラッキーネコ	グミネコソーダ	
481	レア	ねこ娘	ネコねこ娘		
482	伝説レア	超越科学者ヘヴン博士	時空科学者アビス博士		
483	超激レア	幸せを願った少年ソウマ	幸せを願った少年ソウマ＆ネコ	幸せを願ったネコ耳ソウマ	
484	レア	カノとソウマ	カノとソウマとネコ		
485	超激レア	黒ちびネコヴァルキリー	黒ちびネコヴァルキリー・紅		
486	超激レア	カレット	カレットCC		
487	超激レア	ひな	剣士ひな		
488	超激レア	エヴァ8号機	エヴァ8号機＆ネコ		
489	超激レア	空中艦艇ネコヴンダー	希望の船NNNネコヴンダー	神殺しの船NNNネコヴンダー	
490	激レア	ちびミサト	ヴンダー艦長のちびミサト		
491	激レア	ちびリツコ	ヴンダー副長のちびリツコ		
492	激レア	ちびアヤナミレイ(仮称)	目覚めのちびアヤナミ		
494	伝説レア	創造神ガイア	ゴッドガイア		
495	超激レア	潮風のセーラーカッパー	潮風のセーラーカッパーκ		
496	レア	ネコマタドール	ネコフラメンコ	ネコエキゾチック	
497	超激レア	成田甲斐	武神・成田甲斐	豪炎姫の武神・成田甲斐	煉獄無双・成田甲斐
502	レア	ネコ兄弟S	ネコバカ兄弟S		
503	超激レア	英雄令嬢メルシュ	英雄令嬢メルシュξ	絢爛令嬢メルシュξ	
504	レア	ネコ2D	ネコ3D		
505	EX	おやじネコ(店主)	おやじネコ(現役)		
506	超激レア	角龍グラディオス	角龍王グラディオス	角龍皇帝グラディオス	
507	超激レア	アイウォルツ	アイウォルツCC	無名のアイウォルツCC	
508	レア	スーパーにゃん	ジャイにゃん	アイアンにゃん	
509	レア	ネコリュウ	ネコリュウCC		
510	EX	ネコ春麗	ネコ春麗CC		
511	伝説レア	豪鬼	豪鬼CC		
512	超激レア	リュウ	リュウCC	影ナル者	
513	超激レア	春麗	春麗CC		
514	超激レア	ガイル	ガイルCC		
515	超激レア	ザンギエフ	ザンギエフCC		
516	超激レア	ブランカ	ブランカCC		
517	超激レア	ダルシム	ダルシムCC		
518	超激レア	ケン	ケンCC	紅蓮の格闘王ケン	
519	レア	ゴウキリン	ゴウキライオン		
520	超激レア	ラスヴォース	ファイナルラスヴォース	シン・ラスヴォース	
521	激レア	ネコ七福神	ネコ七福邪神		
522	レア	ネコメデューサ	ネコツインテール	ネコナーガ	
523	激レア	泉のネコ女神	ネコメダリスト	ネコ魔神	
524	レア	ネコ武闘家	ネコ師範	ネコマスター	
525	レア	ソラカラちゃん	ソラカラちゃん＆ネコ		
526	超激レア	きんたろう	猛銃戦隊キンレンジャー	超銃戦隊キンレンジャー	
527	超激レア	冬天使のシシル＆コマリ	冬天使のシシル＆コマリΦ		
528	レア	ネコスライム	ネコゼリーフィッシュ	ネコゼリーまんじゅう	
529	レア	ネコサック	ネコウォッカ	ネコ近衛兵	
530	超激レア	厄災の子キャスリィ	祝祭の聖女キャスリィ		
531	超激レア	エルメ	エルメCC		
532	レア	ネコックマ	ネコブラックマ	ネコックマンケン	
533	EX	フェンリルとウルス	ミケとウルス	覚醒のミケとウルス	
534	超激レア	マッドシューター・サキ	ナイトバスター・サキ	バッドブラスター・サキ	
535	超激レア	冥界神ハデス	デスハーデス	Gデスハーデス	
536	超激レア	初音ミク	初音ミクCC		
537	超激レア	桜ミク	桜ミクCC		
538	超激レア	鏡音リン＆レン	鏡音リン＆レンCC		
539	レア	ネコミク	ネコミクCC		
540	レア	ラガーにゃん	ニャメフト	ニャッハー	
541	レア	ネコU.F.O.	ネコ焼そばU.F.O.		
542	EX	ネコヤキソバン	うまい!太い!大きい!ネコ		
543	EX	ネコ少年	ネコ学生		
544	超激レア	禍根の魔女キャスリィ	漆黒の魔女ダークキャスリィ		
545	伝説レア	京坂七穂	京坂七穂・真		
546	レア	ネコファントム	ケムリネコ	ネコバブル	
547	超激レア	オイルガ	オイランパサラン	スケランカクラン	
548	超激レア	白夜姫のレイ	白天姫のレイ		
549	超激レア	第4の使徒	第4のネコ使徒		
550	超激レア	第6の使徒	第6のネコ使徒		
551	超激レア	第10の使徒	第10のネコ使徒		
552	超激レア	第9の使徒	第9のネコ使徒		
553	レア	ネコカヲル	月面のネコカヲル		
554	レア	ネコベーカリー	ネコブーメラン	ネコフライングチキン	
555	EX	達人ウリル	ネコ仮面のウリル	ネコ神面のウリル	
556	超激レア	シャーマンカーン	全情神シャーマンカーン		
557	激レア	騎神アリババ	アリババ神帝	ゴーストアリババ	
558	EX	天子男ジャック	神帝男ジャック		
559	EX	レアガチャネコ	福引ガチャネコ		
560	超激レア	地獄警官エマ	地獄軍曹エマ	閻魔総長エマ	
561	超激レア	巡音ルカ	巡音ルカCC		
562	激レア	MEIKO＆ネコ	MEIKO＆ネコCC		
563	レア	ネコルカ	ネコルカCC		
564	超激レア	アクアシューター・サキ	アクアバスター・サキ	アクアブラスター・サキ	
565	超激レア	ネコルガの夏	ミワクノパサラン	ファビュラスパサラン	
566	激レア	日焼けネコ	日焼けネコビルダー	日焼けネコパーマ	
567	激レア	救命にゃんこ	ダンゴにゃん兄弟	ネコジェラート	
568	EX	チビクロプスネコタマゴ	チビクロプスネコ	雷電のチビクロプスネコ	
569	EX	イディ:N	イディ:N2	イディ:NX	
570	超激レア	石の精霊ゴロー	岩石の精霊イワゴロー	岩石の精霊王ダイゴロー	
571	超激レア	竜騎士スカルバルス	ナイトメアバルス	ハロウィンナイトバルス	
572	超激レア	エドモンド本田	エドモンド本田CC		
573	超激レア	バイソン	バイソンCC		
574	超激レア	バルログ	バルログCC		
575	超激レア	サガット	サガットCC		
576	超激レア	ベガ	ベガCC		
577	激レア	ネコモンド本田	ネコモンド本田CC		
578	レア	ネコバイソン	ネコバイソンCC		
579	レア	ネコバルログ	ネコバルログCC		
580	レア	ネコサガット	ネコサガットCC		
581	レア	ネコベガ	ネコベガCC		
582	レア	ごろにゃん	ごろにゃーん		
583	超激レア	初音ミク マジカルミライ2020大阪	初音ミクCC マジカルミライ2020大阪		
584	超激レア	初音ミク マジカルミライ2020東京	初音ミクCC マジカルミライ2020東京		
585	超激レア	前田サンタ慶次	前田クリス慶次	前田クリス慶次DX	
586	超激レア	幼獣ガル	帝獣ガルディアン		
587	伝説レア	ネコ王子	ネコ国王		
588	超激レア	恋女神ショコラディーテ	メガショコラディーテ		
589	超激レア	初恋令嬢メルシュ	初恋令嬢メルシュξ		
590	激レア	バレンタインねねこ	バレンタインチョコねねこ	本命・バレンタインねねこ	
591	超激レア	雪ミク 2021	雪ミクCC 2021		
592	激レア	KAITO＆ネコ	KAITO＆ネコCC		
593	レア	ネコリン	ネコリンCC		
594	レア	ネコレン	ネコレンCC		
595	超激レア	空中商会コロンブス	輸送兵団アトランチス	超輸送兵団アトランチス	
596	超激レア	春龍かむくら	神春龍かむくら	神春龍王かむくら	
597	超激レア	早乙女乱馬(男)	早乙女らんま(女)		
598	超激レア	天道あかね	天道あかね(道着)		
599	超激レア	響良牙(ブタ)	響良牙		
600	超激レア	シャンプー(猫)	シャンプー		
601	超激レア	ムース(アヒル)	ムース		
602	激レア	八宝斉	八宝斉(エロじじい)		
603	激レア	久遠寺右京(男装)	久遠寺右京		
604	激レア	九能帯刀(道着)	九能帯刀		
605	レア	ネコ乱馬(男)	ネコらんま(女)		
606	レア	ネコあかね	ネコあかね(道着)		
607	EX	ネコパンダ	ネコ玄馬		
608	超激レア	冒険家カンナ	大冒険少女カンナ	伝説の冒険少女カンナ	
609	EX	ネコウエハース	ネコウエハースBIG		
610	超激レア	黒獣ガルディアン	帝獣ダークガルディアン		
611	EX	金塊ネコ	キングネコインゴット	キングネコカーメン	
612	レア	ネコゴジラ	シン・ネコゴジラ		
613	超激レア	ネコ王女	ネコ女王		
614	EX	古神面ドロン	ネコ神面ドロン	覚醒ネコ神面ドロン	
615	超激レア	渚のかぐやひめ	南国衛星サンシャイン	南国衛星スーパーシャイン	
616	EX	カブトネコ	ゴールデンオオカブトネコ		
617	EX	クワガタネコ	キングクワガタネコ		
618	超激レア	召喚少年サトル	悪魔召喚サトル	デビルハンターサトル	
619	超激レア	天草四郎	魔神・天草四郎	怨嗟の魔神・天草四郎	
620	超激レア	妖賢女リリン	妖賢女リリンπ	大賢女リリンπ	
621	超激レア	邪龍ヘヴィジャーク	邪龍王ヘヴィジャーク	邪龍皇帝ヘヴィジャーク	
622	レア	悪魔研究家	邪悪な悪魔研究家	にゃんこ研究家	
623	EX	ジャガンドーJr	破壊神ジャガンドーJr		
624	レア	ネコハーメルン	虚無僧ネコ	ネコブラスバンド	
625	超激レア	熱血硬派くにおくん	ドッジボール部くにおくん		
626	超激レア	オコルガ	オコランパサラン	ゲキオコパサラン	
627	EX	ネコキリンモドキ	駿馬ネコキリンモドキ		
628	EX	ネコノトリッキー	天馬ネコノトリッキー		
629	EX	ネコノドラゴーン	名馬ネコノドラゴーン		
630	激レア	洗脳されしネコ	洗脳されしネコビルダー	洗脳されしネコモヒカン	
631	レア	ネコ書道	ネコ茶道		
632	超激レア	闇の精霊ヤミィ	暗黒の精霊ヤミノワール	暗黒の精霊王ヤミノワールド	
633	超激レア	超竜戦機デス・トロイ	電磁戦機ディア・ボロス	超電磁戦機ディア・ボロス	
634	超激レア	舌切りすずめ	妖怪結社チュンチュン	大妖怪結社チュンチュン	
635	超激レア	白騎士キュクロプス	黒騎士キュクロプス	神魔騎士キュクロプス	
636	EX	100万ドルのネコ	1億ドルのネコ		
637	激レア	洗脳されしタンクネコ	洗脳されしネコカベ	洗脳されしゴムネコ	
638	EX	シャオチョン＆ネコ	シャオチョン＆ネコCC		
639	レア	ナナ＆ネコ	ナナ＆ネコCC		
640	レア	エミリア＆ネコ	エミリア＆ネコCC		
641	EX	ジェアー＆ネコ	ジェアー＆ネコCC		
642	超激レア	踊り子イズ	魅惑の舞姫イズ		
643	超激レア	堕天神ルシファー	グランドルシファー	Gグランドルシファー	
644	基本	ネコ超人	ネコ超人Ⅱ	真ネコ超人	
645	超激レア	白い恋人メカ子	ビタースイートメカ子	LOVEミントメカ子	
646	激レア	洗脳されしバトルネコ	洗脳されし勇者ネコ	洗脳されし暗黒ネコ	
647	EX	爆竹ネコ	爆竹ネコビルダー		
648	超激レア	狩猟娘テルン	狩猟娘テルンζ	大狩猟娘テルンζ	
649	超激レア	恋番長・鬼にゃんま	恋愛番長・鬼神にゃんま		
650	超激レア	服部半蔵	忍神・服部半蔵	絶影の忍神・服部半蔵	
651	EX	幼馴染にゃん子	彼女にゃん子		
652	EX	おてんばネコライオン	鉄仮面ネコライオン		
653	EX	ネコクバンケシ	ネコクバンチョウ		
654	EX	古獣ナーラ	雷古獣ナーラ	覚醒古獣ナーラ	
655	激レア	洗脳されしキモネコ	洗脳されし美脚ネコ	洗脳されしムキあしネコ	
656	超激レア	氷の精霊フリズ	氷結の精霊ブリザーナ	氷結の精霊王ブリザレーナ	
657	EX	古びたタマゴ:N001	謎のタマゴ:N001	ネコはにわ	
658	超激レア	悲嘆の踊り子イズ	哀悼の舞姫ダークイズ		
659	EX	古びたタマゴ:N101	赤いタマゴ:N101	飛脚ネコ	
660	EX	古びたタマゴ:N102	青いタマゴ:N102	ネコターさん	
661	超激レア	海龍ダライアサン	海龍王ダライアサン	海龍皇帝ダライアサン	
662	超激レア	祝福神クロノス	ウェディングクロノス		
663	激レア	洗脳されしウシネコ	洗脳されしネコキリン	洗脳されしネコライオン	
664	EX	古びたタマゴ:N103	黒いタマゴ:N103	ネコガスマスク	
665	EX	古びたタマゴ:N104	緑のタマゴ:N104	ネコオペ	
666	EX	古びたタマゴ:N201	悪のタマゴ:N201	ネコソシスト	
667	超激レア	ナイトビーチリリン	ナイトビーチリリンπ		
668	激レア	洗脳されしネコノトリ	洗脳されしネコUFO	洗脳されし天空のネコ	
669	超激レア	ネコ天狗	大妖怪ネコ天狗	大神仙ネコ天狗	
670	EX	古びたタマゴ:N003	悪のタマゴ:N003	ネコサボテン	
671	EX	古びたタマゴ:N202	赤いタマゴ:N202	アーマードホタルネコ	
672	超激レア	早乙女らんま(レオタード)	早乙女らんま(チアガール)		
673	超激レア	早乙女玄馬(パンダ)	早乙女玄馬		
674	超激レア	ネコチーター			
675	超激レア	周遊芸団カルーセルズ	奇襲怪光エンヴァンズ	超奇襲怪光エンヴァンズ	
676	レア	古びたタマゴ:N105	青いタマゴ:N105	ネコスマッシュ	
677	レア	古びたタマゴ:N106	黄色いタマゴ:N106	ネコキノコ	
678	レア	三雲修＆ネコ	三雲修＆ネコCC		
679	レア	空閑遊真＆ネコ	空閑遊真＆ネコCC		
680	EX	雨取千佳＆ネコ	雨取千佳＆ネコCC		
681	超激レア	さくら	さくらCC		
682	超激レア	ルーク	ルークCC		
683	超激レア	女神ベガ	幻影の加護女神ベガ	虚影の加護女神ベガ	
684	超激レア	伯爵・真田幸村	吸血鬼・真田幸村	吸血鬼公爵・真田幸村	
685	激レア	洗脳されしネコフィッシュ	洗脳されしネコクジラ	洗脳されしネコ島	
686	EX	古びたタマゴ:N004	謎のタマゴ:N004	ネコスーパーカー	
687	超激レア	光の女神シリウス	来光の女神シリウス		
688	超激レア	トナカイ娘テルン	トナカイ娘テルンζ		
689	激レア	洗脳されしネコトカゲ	洗脳されしネコドラゴン	洗脳されしネコキングドラゴン	
690	EX	石の上にも10年ネコ	石の上にも10年ネコ像		
691	超激レア	運命の子フォノ	天命の王フォノウ		
692	EX	古びたタマゴ:N000	古のタマゴ:N000	猿人ネコルーザ	
693	超激レア	いっすん法師	鬼襲艇コヅチマル	鬼襲戦艇コヅチマル	
694	超激レア	白執事探偵ヴィグラー	黒執事探偵グラヴィー		
695	激レア	洗脳されし巨神ネコ	洗脳されしネコダラボッチ	洗脳されしネコジャラミ	
696	レア	殺意のタンクネコ	真・殺意のタンクネコ		
697	EX	ネコ委員長	裏ネコ委員長		
698	レア	古びたタマゴ:N107	紫のタマゴ:N107	ネコ村長	
699	超激レア	サンダージャック	ブラッディージャック	ライトニングジャック	
700	超激レア	決闘少年ラビットサトル	決闘王者ラビットサトル		
701	EX	古びたタマゴ:N005	謎のタマゴ:N005	ネコヒットマン	
702	レア	にゃんこ仮面ライダー	シン・仮面ライダーにゃんこ		
703	レア	ゴジラにゃんこ	シン・ゴジラにゃんこ		
704	レア	エヴァンゲリオンにゃんこ	シン・エヴァンゲリオンにゃんこ		
705	レア	ウルトラマンにゃんこ	シン・ウルトラマンにゃんこ		
706	超激レア	非命の王フォノウ	断命の王ダークフォノウ		
707	激レア	古びたタマゴ:N108	青いタマゴ:N108	ネコウォッシュ	
708	レア	古びたタマゴ:N109	悪のタマゴ:N109	ネコバレル	
709	レア	ネコジェット	ネコ宮金次郎		
710	超激レア	隻眼の少女アスカ	碧眼の魔女アスカ		
711	超激レア	エヴァ第13号機	エヴァ第13号機＆ネコ		
712	超激レア	バララガの誓い	オヨメノパサラン		
713	超激レア	カオルガ	カオランパサラン	カオランタタラン	
714	EX	古びたタマゴ:N203	紫のタマゴ:N203	ネコピエロ	
715	超激レア	波打ち際の冒険家カンナ	大海原のダイバーカンナ		
716	超激レア	建築兵団スフィンクス	突撃部隊コンボーイズ	超突撃部隊コンボーイズ	
717	激レア	古びたタマゴ:N111	謎のタマゴ:N111	ネコポリス	
718	レア	古びたタマゴ:N110	黄色いタマゴ:N110	ネコファーマー	
719	レア	ネコもろこし	にゃんコーン		
720	超激レア	鉄の精霊カッチン	鋼鉄の精霊テツカッチン	鋼鉄の精霊王ガンテツカッチン	
721	EX	古びたタマゴ:N006	悪のタマゴ:N006	ネコアックマ	
722	超激レア	花園高校番長りき	ドッジボール部りき		
723	超激レア	初音ミク 16th	初音ミクCC 16th		
724	超激レア	光翼神イシス	ネフェルイシス	Gネフェルイシス	
725	レア	古びたタマゴ:N112	謎のタマゴ:N112	ネコタマ号	
726	超激レア	忍者娘トモエ	食道楽忍者トモエ	美食極楽忍者トモエ	
727	EX	ネコメダル王	ネコメダル大王		
728	超激レア	ソル・デ・ロカ	ソル・デ・ロカCC		
729	EX	論の賢者ソラクティス	論の賢者ネコラティス		
730	精霊	精霊			
731	EX	古びたタマゴ:N204	赤いタマゴ:N204	ねこシシマイ	
732	伝説レア	暁のイザナギ	暁光のイザナギ		
733	精霊	精霊			
734	超激レア	鉄籠のペガサ	鉄籠のペガサν	白籠のペガサν	
735	精霊	精霊			
736	EX	ネコ校長	ネコセカンドライフ		
737	超激レア	ネコアイスホワイトスター	ネコアイストップスター		
738	超激レア	スプリングカリファ	スプリングXカリファ		
739	伝説レア	宵のイザナミ	宵闇のイザナミ		
740	精霊	精霊			
747	超激レア	緋村剣心	緋村剣心CC	不殺の流浪人・緋村剣心	
748	超激レア	相楽左之助	相楽左之助CC		
749	超激レア	四乃森蒼紫	四乃森蒼紫CC		
750	超激レア	斎藤一	斎藤一CC		
751	激レア	ちび薫	ちび薫CC		
752	激レア	ちび弥彦	ちび弥彦CC		
753	レア	ネコ剣心	ネコ抜刀斎		
754	EX	ネコ薫	剣術小町ネコ薫		
755	超激レア	明智光秀	武神・明智光秀	智謀の武神・明智光秀	
756	精霊	精霊			
757	超激レア	ネコケーキマシン挙式	ネコケーキマシン挙式・寿		
758	EX	古びたタマゴ:N205	紫のタマゴ:N205	ネコ花嫁	
759	超激レア	謎の傭兵トリクシー	復讐の傭兵トリクシー		
760	超激レア	サマーフェスサンディア	サマーフェスサンディアβ		
761	超激レア	砲龍ガンドロス	砲龍王ガンドロス		
762	精霊	精霊			
763	超激レア	天空神スーパーゼウス	ギガント全能神スーパーゼウス		
764	超激レア	ネコダイナザウルス	メカ・ネコダイナザウルス		
765	精霊	精霊			
766	EX	古びたタマゴ:N206	赤いタマゴ:N206	ネコホタテ	
767	EX	イワシ	イワシCC		
768	EX	イカ	イカCC		
769	超激レア	たいてんき	たいてんきCC		
770	超激レア	花咲かじいさん	暴食怪樹オオザクラ		
771	精霊	精霊			
772	EX	生命の賢者Dr.ノーヴァ	生命の賢者ネコノーヴァ		
773	超激レア	パンプキンソドム	ソドム・オー・ランタン		
774	激レア	スモウ・ザ・ミイラ	マミー・ザ・ハロウィン		
775	超激レア	特命機動アクセル	ダーティー・アクセル		
776	精霊	精霊			
777	EX	見習い武者ネコ	武将ネコ		
778	超激レア	冬将軍・成田甲斐	冬大将軍・成田甲斐		
779	超激レア	ネコまどか＆ほむら	魔法結社にゃんこ☆マギカ		
780	超激レア	幼天女ルーナ	救済の天女ルナーシャ		
781	レア	ネコボウラー	ネコドッジボウラー		
782	超激レア	マモルガ	マモランパサラン		
783	精霊	精霊			
784	超激レア	こねこ	超スターこねこ		
785	EX	チョコサプネコC	チョコサプネコF		
786	レア	殺意のキモネコ	真・殺意のキモネコ		
787	超激レア	マジシャンキャットマン	イリュージョンキャットマン		
788	超激レア	冥護の天女ルナーシャ	寂滅の天女ダークルナーシャ		
790	超激レア	範馬刃牙	範馬刃牙CC		
791	超激レア	烈海王	烈海王CC		
792	超激レア	花山薫	花山薫CC		
793	超激レア	愚地克巳	愚地克巳CC		
794	超激レア	ジャック・ハンマー	ジャック・ハンマーCC		
795	激レア	ネコオーガ	ネコの貌		
796	EX	ちび刃牙	ちび刃牙CC		
797	EX	ネコ刃牙	ネコ刃牙CC		
798	EX	ネコ海王	ネコ海王CC		
799	EX	ネコ独歩	ネコ独歩CC		
800	超激レア	曲射砲台ランチャーズ	迎撃砲隊ガンハウザー		
801	精霊	精霊			
802	EX	理の賢者ニュトーン	理の賢者ニャトーン		
803	精霊	精霊			
804	超激レア	ソニック	ソニック＆ネコ		
805	超激レア	テイルス	テイルス＆ネコ		
806	超激レア	ナックルズ	ナックルズ＆ネコ		
807	超激レア	シャドウ	シャドウ＆ネコ		
808	レア	ネコソニック	ネコソニックCC		
809	EX	ネコテイルス	ネコテイルスCC		
810	レア	ネコシャドウ	ネコシャドウCC		
811	超激レア	エージェント・スタール	エッジマスター・スタール		
812	超激レア	韋駄天スカンダ	スカンダゼタ		
813	精霊	精霊			
814	EX	タスマニアオオガニ	タスマニアオオガニCC		
815	超激レア	エヴァ量産機	エヴァネコ量産機		
816	伝説レア	月影の少年カヲル	落月のシ者カヲル		
817	精霊	精霊			
818	超激レア	樹の精霊コモリ	樹木の精霊モリリン		
819	精霊	精霊			
820	レア	ネコ大工	ネコラッパー		
821	超激レア	シーサイドペガサ	シーサイドペガサν		
822	精霊	精霊			
823	EX	ネココンサルタント	ネコビジネスパートナー		
824	EX	ネコ店長＆ネコ店員	ネコ店長＆ネコ店員CC		
825	超激レア	魔導師シドミ	炎の大魔導師シドミ		
826	精霊	精霊			
827	超激レア	キャミィ	キャミィCC		
828	超激レア	ジュリ	ジュリCC		
829	激レア	ネコザンギエフ	ネコザンギエフCC		
830	激レア	ネコジェイミー	ネコジェイミーCC		
831	超激レア	獅子番長ラクレス	獅子番長ラクレスΣ		
832	EX	スカーフにゃんこ	レッドにゃんこ		
833	EX	ハットにゃんこ	ブルーにゃんこ		
834	EX	ヘルメットにゃんこ	イエローにゃんこ		
835	EX	ゴーグルにゃんこ	グリーンにゃんこ		
836	EX	リボンにゃんこ	モモにゃんこ		
837	超激レア	ニャンコオー	超グレートニャンコオーDX		
838	超激レア	幼騎士ルーノ	満月の騎士ルーノス		
839	精霊	精霊			
840	精霊	精霊			
841	超激レア	竈門炭治郎	竈門炭治郎CC		
842	超激レア	竈門禰豆子	竈門禰豆子CC		
843	超激レア	我妻善逸	我妻善逸CC		
844	超激レア	嘴平伊之助	嘴平伊之助CC		
845	超激レア	冨岡義勇	冨岡義勇CC		
846	超激レア	胡蝶しのぶ	胡蝶しのぶCC		
847	超激レア	煉獄杏寿郎	煉獄杏寿郎CC		
848	激レア	ちび鱗滝左近次	ちび鱗滝左近次CC		
849	レア	ネコ炭治郎	ネコ炭治郎CC		
850	EX	ネコ禰豆子	ネコ禰豆子CC		
851	超激レア	風魔小太郎	蛇神・風魔小太郎		
852	レア	ネコ新聞	ネコセール		
853	レア	殺意のウシネコノトリ	真・殺意のウシネコノトリ		
854	EX	オカルトネコ	ギャルネコ		
855	EX	淘の賢者ダーヴィン	淘の賢者ニャーヴィン		
856	精霊	精霊			
857	レア	にゃんこ村のネコ	にゃんこ村のネコCC		
858	EX	バリスタにゃんこ	シルバーにゃんこ		
859	EX	司令官にゃんこ	ゴールドにゃんこ		
860	超激レア	孤月の騎士ルーノス	晦月の騎士ダークルーノス		
861	精霊	精霊			
862	超激レア	嵐竜機ヴォルネード	嵐竜機王ヴォルネード		
863	超激レア	婚礼・服部半蔵	新郎・服部半蔵		
864	EX	透明にゃんこ	ネコ花婿		
865	超激レア	衛宮 士郎	衛宮 士郎CC		
866	超激レア	真アサシン	真アサシンCC		
867	EX	パックマン	パックマンネコ		
868	EX	ネコブリンキー	ネコブリンキーCC		
869	EX	ネコインキー	ネコインキーCC		
870	超激レア	鉄腕！東雲寺あかね	それいけ！東雲寺あかね		
871	EX	古びたタマゴ:N207	黒いタマゴ:N207	成金ネコ	
872	EX	古びたタマゴ:N208	緑のタマゴ:N208	アサルトザリガニネコ	
873	超激レア	ジャングルハンタートモエ	ジャングルマスタートモエ		
874	超激レア	ロックこねこ	ロックスターこねこ		
875	超激レア	りゅういち	りゅういち＆りゅうじ		
876	精霊	精霊			
877	精霊	精霊			
878	超激レア	いなばの白うさぎ	メガシャークVS白うさぎ		
879	超激レア	悠久山安慈	悠久山安慈CC		
880	超激レア	瀬田宗次郎	瀬田宗次郎CC		
881	超激レア	志々雄真実	志々雄真実CC		
882	伝説レア	比古清十郎	比古清十郎CC		
883	EX	監獄ネコ	脱獄ネコ		
884	EX	監獄ネコガール	脱獄ネコガール		
885	EX	監獄タンクネコ	脱獄ネコカベ		
886	EX	監獄ちびネコ	脱獄ちびネコアーマー		
887	超激レア	悠久の冒険者エターニス	真理の探究者エターニス		
888	精霊	精霊			`;

    // =========================================================================
    // 4. 全888枠ビルダー
    // =========================================================================
    const STORAGE_KEY = "nyanko_zukan_888_v1";
    let units = [];
    const cardFormSelection = {};

    function inferReferenceStats(numId, rarity, formIdx, formName) {
      const stageMul = [1.0, 1.45, 2.15, 2.75][formIdx] || 1.0;
      const baseByRarity = {
        "基本":     { hp: 500,  atk: 90,   kb: 3, speed: 12, range: 200, freqF: 55,  foreF: 10, tbaF: 44,  cost: 600,  rechF: 146, area: "単体攻撃", g: "standard" },
        "EX":       { hp: 1400, atk: 420,  kb: 3, speed: 14, range: 260, freqF: 78,  foreF: 16, tbaF: 61,  cost: 1200, rechF: 446, area: "範囲攻撃", g: "standard" },
        "レア":     { hp: 1200, atk: 380,  kb: 3, speed: 12, range: 240, freqF: 68,  foreF: 12, tbaF: 55,  cost: 675,  rechF: 176, area: "単体攻撃", g: "standard" },
        "激レア":   { hp: 2800, atk: 1250, kb: 3, speed: 14, range: 300, freqF: 96,  foreF: 20, tbaF: 75,  cost: 1350, rechF: 526, area: "範囲攻撃", g: "standard" },
        "超激レア": { hp: 6800, atk: 4400, kb: 4, speed: 12, range: 420, freqF: 168, foreF: 42, tbaF: 125, cost: 4350, rechF: 2846, area: "範囲攻撃", g: "standard" },
        "伝説レア": { hp: 9600, atk: 6800, kb: 4, speed: 14, range: 430, freqF: 158, foreF: 38, tbaF: 119, cost: 4800, rechF: 3146, area: "範囲攻撃", g: "legend" },
        "精霊":     { hp: 1,    atk: 2500, kb: 1, speed: 24, range: 350, freqF: 30,  foreF: 6,  tbaF: 23,  cost: 0,    rechF: 60,  area: "範囲攻撃", g: "fixed" },
        "未実装枠": { hp: 100,  atk: 10,   kb: 1, speed: 10, range: 140, freqF: 60,  foreF: 10, tbaF: 49,  cost: 75,   rechF: 60,  area: "単体攻撃", g: "fixed" }
      };

      const tpl = baseByRarity[rarity] || baseByRarity["レア"];
      const seed = (numId * 17 + formIdx * 31) % 19;
      const variance = 0.92 + seed * 0.01;

      const traits = [];
      const abilities = [];
      if (rarity !== "精霊" && rarity !== "未実装枠") {
        traits.push(TRAITS[(numId + formIdx) % TRAITS.length]);
        if (rarity === "超激レア" || rarity === "伝説レア") {
          traits.push(TRAITS[(numId * 3 + 2) % TRAITS.length]);
          abilities.push(ABILITIES[(numId * 5) % 12]);
          if (formIdx >= 2) abilities.push(ABILITIES[26 + (numId % 10)]);
        } else {
          abilities.push(ABILITIES[(numId * 3) % 15]);
        }
      }

      const isEgg = formName.includes("タマゴ");
      return {
        name: formName,
        hp: isEgg ? 300 : Math.max(1, Math.round(tpl.hp * stageMul * variance)),
        atk: isEgg ? 10 : Math.max(0, Math.round(tpl.atk * stageMul * variance)),
        kb: isEgg ? 1 : tpl.kb,
        speed: isEgg ? 8 : tpl.speed,
        range: isEgg ? 140 : tpl.range + formIdx * 10,
        freqF: tpl.freqF,
        foreF: tpl.foreF,
        backF: 1,
        tbaF: tpl.tbaF,
        cost2: tpl.cost,
        rechF: tpl.rechF,
        area: tpl.area,
        traits: [...new Set(traits)],
        abilities: [...new Set(abilities)],
        ldText: "",
        hits: null,
        isVerified: false,
        growthKey: tpl.g
      };
    }

    function buildAll888Units() {
      const mapById = new Map();
      const lines = RAW_TSV_DATA.trim().split("\n");
      for (const line of lines) {
        const parts = line.split("\t");
        const id = parts[0].trim().padStart(3, "0");
        const rarity = parts[1].trim();
        const formNames = [parts[2], parts[3], parts[4], parts[5]]
          .map((s) => (s || "").trim())
          .filter((s) => s.length > 0);
        mapById.set(id, { id, rarity, formNames, isUnreleased: false });
      }

      const result = [];
      for (let n = 1; n <= 888; n++) {
        const id = String(n).padStart(3, "0");
        const raw = mapById.get(id) || {
          id,
          rarity: "未実装枠",
          formNames: [`未実装枠 No.${id}`],
          isUnreleased: true
        };

        let unitGrowthKey = "standard";
        let anyVerified = false;

        const forms = raw.formNames.map((fName, idx) => {
          const vKey = `${id}-${idx}`;
          const v = VERIFIED_STATS[vKey];
          if (v) {
            anyVerified = true;
            unitGrowthKey = v[14] || "standard";
            return {
              name: fName,
              hp: v[0],
              atk: v[1],
              kb: v[2],
              speed: v[3],
              range: v[4],
              freqF: v[5],
              foreF: v[6],
              backF: v[7],
              tbaF: v[8],
              cost2: v[9],
              rechF: v[10],
              area: v[11],
              traits: v[12],
              abilities: v[13],
              ldText: v[15] || "",
              hits: v[16] || null,
              isVerified: true
            };
          } else {
            const inf = inferReferenceStats(n, raw.rarity, idx, fName);
            if (!anyVerified) unitGrowthKey = inf.growthKey;
            return inf;
          }
        });

        result.push({
          id,
          rarity: raw.rarity,
          isUnreleased: raw.isUnreleased,
          isVerified: anyVerified,
          growthKey: unitGrowthKey,
          forms
        });
      }
      return result;
    }

    function applyOverridesMap(overrides) {
      units = buildAll888Units();
      if (!overrides) return;
      for (const [key, data] of Object.entries(overrides)) {
        const [id, idxStr] = key.split("-");
        const idx = parseInt(idxStr, 10);
        const u = units.find((x) => x.id === id);
        if (u && u.forms[idx]) {
          Object.assign(u.forms[idx], data, { isVerified: true });
          u.isVerified = true;
        }
      }
    }

    function loadUnits() {
      let localMap = {};
      try {
        const saved = localStorage.getItem(STORAGE_KEY);
        if (saved) localMap = JSON.parse(saved);
      } catch (e) {
        console.warn("localStorage load skipped:", e);
      }
      cloudOverridesCache = localMap;
      applyOverridesMap(localMap);
    }

    // =========================================================================
    // 5. アプリケーション状態・計算式
    // =========================================================================
    const state = {
      viewMode: "card",
      search: "",
      sortBy: "idAsc",
      catalogScope: "all888",
      globalForm: "latest",
      rarity: "すべて",
      trait: "すべて",
      abilities: [],
      abilityMode: "AND",
      baseLv: 20,
      plusLv: 90,
      treasureBase: true,
      treasureTrait: true,
      treasureStudy: true,
      instinctHp: false,
      instinctAtk: false,
      comboStudyF: 0,
      calcExpanded: true,
      page: 1,
      perPage: 40,
      modalUnitId: null,
      modalFormIdx: 0
    };

    function getLevelMultiplier(growthKey, totalLv) {
      const g = GROWTH_TABLES[growthKey] || GROWTH_TABLES.standard;
      if (totalLv <= 1) return 1.0;
      let pct = 100;
      for (let lv = 2; lv <= totalLv; lv++) {
        const bucket = Math.min(9, Math.floor((lv - 1) / 10));
        pct += g[bucket];
      }
      return pct / 100;
    }

    function computeStats(unit, form) {
      const totalLv = state.baseLv + state.plusLv;
      const mult = getLevelMultiplier(unit.growthKey, totalLv);
      const tBase = state.treasureBase ? 2.5 : 1.0;
      const iHp = state.instinctHp ? 1.2 : 1.0;
      const iAtk = state.instinctAtk ? 1.2 : 1.0;

      const hp = Math.floor(Math.round(form.hp * mult * iHp) * tBase);
      const atk = Math.floor(Math.round(form.atk * mult * iAtk) * tBase);
      const hitsScaled = form.hits
        ? form.hits.map((h) => Math.floor(Math.round(h * mult * iAtk) * tBase))
        : null;
      const dps = form.freqF > 0 ? Math.round(atk / (form.freqF / 30)) : 0;

      const costCh2 = form.cost2;
      const costCh1 = Math.round((costCh2 * 2) / 3);
      const costCh3 = Math.round((costCh2 * 4) / 3);

      const studyRed = (state.treasureStudy ? 264 : 0) + state.comboStudyF;
      const actualRechF = form.rechF <= 60 ? form.rechF : Math.max(60, form.rechF - studyRed);

      return {
        hp,
        atk,
        hitsScaled,
        dps,
        costCh1,
        costCh2,
        costCh3,
        actualRechF,
        ldText: form.ldText || ""
      };
    }

    function fmtSec(frames) {
      return (frames / 30).toFixed(2) + "s";
    }

    function getUnitFormIndex(unit) {
      if (cardFormSelection[unit.id] !== undefined) {
        return Math.min(unit.forms.length - 1, cardFormSelection[unit.id]);
      }
      if (state.globalForm === "latest") {
        return unit.forms.length - 1;
      }
      const req = parseInt(state.globalForm, 10);
      return Math.min(unit.forms.length - 1, isNaN(req) ? unit.forms.length - 1 : req);
    }

    // =========================================================================
    // 6. フィルタリング・ソート・描画
    // =========================================================================
    function getFilteredUnits() {
      const q = state.search.trim().toLowerCase();
      const filtered = units.filter((u) => {
        if (state.catalogScope === "active864" && u.isUnreleased) return false;
        if (state.rarity !== "すべて" && u.rarity !== state.rarity) return false;

        if (q) {
          const numClean = q.replace(/^no\.?/i, "").replace(/^0+/, "");
          const idHit = u.id.includes(q) || (numClean && String(parseInt(u.id, 10)) === numClean);
          const nameHit = u.forms.some((f) => f.name.toLowerCase().includes(q));
          const traitHit = u.forms.some((f) => (f.traits || []).some((t) => t.toLowerCase().includes(q)));
          const abHit = u.forms.some((f) => (f.abilities || []).some((a) => a.toLowerCase().includes(q)));
          if (!idHit && !nameHit && !traitHit && !abHit) return false;
        }

        if (state.trait !== "すべて") {
          if (!u.forms.some((f) => (f.traits || []).includes(state.trait))) return false;
        }

        if (state.abilities.length > 0) {
          const allAb = new Set(u.forms.flatMap((f) => f.abilities || []));
          if (state.abilityMode === "AND") {
            if (!state.abilities.every((a) => allAb.has(a))) return false;
          } else {
            if (!state.abilities.some((a) => allAb.has(a))) return false;
          }
        }
        return true;
      });

      filtered.sort((a, b) => {
        if (state.sortBy === "idAsc") return a.id.localeCompare(b.id);
        if (state.sortBy === "idDesc") return b.id.localeCompare(a.id);
        if (state.sortBy === "name1Asc") return a.forms[0].name.localeCompare(b.forms[0].name, "ja");

        const fa = a.forms[getUnitFormIndex(a)];
        const fb = b.forms[getUnitFormIndex(b)];
        const sa = computeStats(a, fa);
        const sb = computeStats(b, fb);

        if (state.sortBy === "hpDesc") return sb.hp - sa.hp;
        if (state.sortBy === "atkDesc") return sb.atk - sa.atk;
        if (state.sortBy === "dpsDesc") return sb.dps - sa.dps;
        if (state.sortBy === "rangeDesc") return fb.range - fa.range;
        if (state.sortBy === "costAsc") return sa.costCh2 - sb.costCh2;
        if (state.sortBy === "rechAsc") return sb.actualRechF - sa.actualRechF;
        if (state.sortBy === "speedDesc") return fb.speed - fa.speed;
        return 0;
      });

      return filtered;
    }

    function renderApp() {
      const filtered = getFilteredUnits();
      const totalItems = filtered.length;
      const totalPages = Math.max(1, Math.ceil(totalItems / state.perPage));
      if (state.page > totalPages) state.page = totalPages;

      const activeCount = units.filter((u) => !u.isUnreleased).length;
      const unrelCount = units.filter((u) => u.isUnreleased).length;
      document.getElementById("matchCount").textContent = totalItems;
      document.getElementById("catalogSummary").textContent =
        `（実装${activeCount}体＋欠番${unrelCount}枠＝全${units.length}枠収録）`;

      const startIdx = (state.page - 1) * state.perPage;
      const pageItems = filtered.slice(startIdx, startIdx + state.perPage);
      const out = document.getElementById("mainOutput");

      if (pageItems.length === 0) {
        out.innerHTML = `
          <div class="panel" style="text-align:center; padding:24px;">
            <div style="font-weight:900; font-size:14px; margin-bottom:8px;">条件に一致するキャラクターが見つかりませんでした</div>
            <button type="button" class="btn btn-gold" onclick="resetFilters()">絞り込みをリセット</button>
          </div>
        `;
        renderPagination(0, 1);
        return;
      }

      const lvTitle = `Lv.${state.baseLv + state.plusLv}`;

      if (state.viewMode === "card") {
        out.innerHTML = `
          <div class="cards-grid">
            ${pageItems
              .map((u) => {
                const fIdx = getUnitFormIndex(u);
                const f = u.forms[fIdx];
                const s = computeStats(u, f);
                const name1 = u.forms[0].name;

                const formTabsHtml = u.forms
                  .map((_, i) => `
                    <button
                      type="button"
                      class="card-ftab ${i === fIdx ? "active" : ""}"
                      onclick="event.stopPropagation(); selectCardForm('${u.id}', ${i})"
                    >第${i + 1}形態</button>
                  `)
                  .join("");

                const traitsHtml = (f.traits && f.traits.length > 0)
                  ? f.traits.map((t) => `<span class="tag-trait">${t}</span>`).join("")
                  : `<span style="font-size:10px; color:var(--ink-sub)">対象属性: なし</span>`;

                const abHtml = (f.abilities && f.abilities.length > 0)
                  ? f.abilities.map((a) => `<span class="tag-ab">${a}</span>`).join("")
                  : `<span style="font-size:10px; color:var(--ink-sub)">特殊能力: なし</span>`;

                return `
                  <article class="unit-card ${u.isUnreleased ? "unreleased-card" : ""}" onclick="openDetailModal('${u.id}', ${fIdx})">
                    <div style="display:flex; align-items:center; justify-content:space-between; gap:6px;">
                      <div>
                        <span class="badge-stamp">No.${u.id}</span>
                        <span class="badge-stamp" style="background:var(--crimson); color:#fff;">${u.rarity}</span>
                      </div>
                      <span style="font-size:10px; font-weight:800; color:var(--ink-sub);">
                        ${f.isVerified ? "実測検証済" : "基準スケール値"}
                      </span>
                    </div>

                    <div>
                      <div style="font-size:15.5px; font-weight:900;">${name1}</div>
                      ${fIdx > 0 ? `<div style="font-size:11px; font-weight:800; color:var(--ink-sub);">表示中(第${fIdx + 1}形態): ${f.name}</div>` : ""}
                    </div>

                    <div class="card-form-tabs">${formTabsHtml}</div>

                    <div class="card-stat-row">
                      <div><div class="stat-k">体力(${lvTitle})</div><div class="stat-v c-hp" style="font-size:12px;">${s.hp.toLocaleString()}</div></div>
                      <div><div class="stat-k">攻撃力</div><div class="stat-v c-atk" style="font-size:12px;">${s.atk.toLocaleString()}</div></div>
                      <div><div class="stat-k">DPS</div><div class="stat-v c-dps" style="font-size:12px;">${s.dps.toLocaleString()}</div></div>
                      <div><div class="stat-k">射程 / 範囲</div><div class="stat-v" style="font-size:11px;">${f.range} (${f.area === "範囲攻撃" ? "範囲" : "単体"})</div></div>
                    </div>

                    <div class="card-sub-grid">
                      <div>KB: ${f.kb} / 速度: ${f.speed}</div>
                      <div>頻度: ${f.freqF}F (${fmtSec(f.freqF)})</div>
                      <div>発生: ${f.foreF}F / TBA: ${f.tbaF}F</div>
                      <div>1章: ${s.costCh1} / 2章: ${s.costCh2}</div>
                      <div>3章コスト: ${s.costCh3}</div>
                      <div>再生産: ${s.actualRechF}F (${fmtSec(s.actualRechF)})</div>
                    </div>

                    <div class="tag-row">${traitsHtml} ${abHtml}</div>
                  </article>
                `;
              })
              .join("")}
          </div>
        `;
      } else {
        out.innerHTML = `
          <div class="table-scroll-wrap">
            <table class="status-table">
              <thead>
                <tr>
                  <th onclick="sortByColumn('idAsc')">図鑑No.</th>
                  <th>レア度</th>
                  <th onclick="sortByColumn('name1Asc')">第1形態名</th>
                  <th>表示形態</th>
                  <th>形態別名称</th>
                  <th onclick="sortByColumn('hpDesc')">体力 (${lvTitle})</th>
                  <th>KB</th>
                  <th onclick="sortByColumn('speedDesc')">速度</th>
                  <th onclick="sortByColumn('atkDesc')">攻撃力 (連撃内訳)</th>
                  <th onclick="sortByColumn('dpsDesc')">DPS</th>
                  <th>範囲</th>
                  <th>攻撃頻度 (F/秒)</th>
                  <th>攻撃発生 (F/秒)</th>
                  <th>後隙 (F)</th>
                  <th>攻撃間隔TBA (F/秒)</th>
                  <th onclick="sortByColumn('rangeDesc')">感知射程 (遠方/全方位)</th>
                  <th onclick="sortByColumn('costAsc')">1章 / 2章 / 3章 コスト</th>
                  <th onclick="sortByColumn('rechAsc')">再生産 (F/秒)</th>
                  <th>対象属性</th>
                  <th>特殊能力・無効</th>
                  <th>growth[10] 成長率</th>
                </tr>
              </thead>
              <tbody>
                ${pageItems
                  .map((u) => {
                    const fIdx = getUnitFormIndex(u);
                    const f = u.forms[fIdx];
                    const s = computeStats(u, f);
                    const name1 = u.forms[0].name;
                    const gArr = GROWTH_TABLES[u.growthKey] || GROWTH_TABLES.standard;
                    const atkCol = s.hitsScaled
                      ? `${s.atk.toLocaleString()} [${s.hitsScaled.join("+")}]`
                      : s.atk.toLocaleString();
                    const rangeCol = s.ldText ? `${f.range} (${s.ldText})` : `${f.range}`;
                    const costCol = `${s.costCh1.toLocaleString()} / ${s.costCh2.toLocaleString()} / ${s.costCh3.toLocaleString()}`;

                    return `
                      <tr class="${u.isUnreleased ? "unreleased-row" : ""}" onclick="openDetailModal('${u.id}', ${fIdx})">
                        <td style="font-weight:900;">${u.id}</td>
                        <td>${u.rarity}</td>
                        <td style="font-weight:900;">${name1}</td>
                        <td>第${fIdx + 1}形態</td>
                        <td>${f.name}</td>
                        <td class="c-hp">${s.hp.toLocaleString()}</td>
                        <td>${f.kb}</td>
                        <td>${f.speed}</td>
                        <td class="c-atk">${atkCol}</td>
                        <td class="c-dps">${s.dps.toLocaleString()}</td>
                        <td>${f.area === "範囲攻撃" ? "範囲" : "単体"}</td>
                        <td>${f.freqF}F / ${fmtSec(f.freqF)}</td>
                        <td>${f.foreF}F / ${fmtSec(f.foreF)}</td>
                        <td>${f.backF}F</td>
                        <td>${f.tbaF}F / ${fmtSec(f.tbaF)}</td>
                        <td>${rangeCol}</td>
                        <td>${costCol}</td>
                        <td>${s.actualRechF}F / ${fmtSec(s.actualRechF)}</td>
                        <td>${f.traits && f.traits.length > 0 ? f.traits.join(", ") : "—"}</td>
                        <td>${f.abilities && f.abilities.length > 0 ? f.abilities.join(", ") : "—"}</td>
                        <td style="font-family:monospace; font-size:10px;">[${gArr.join(",")}]</td>
                      </tr>
                    `;
                  })
                  .join("")}
              </tbody>
            </table>
          </div>
        `;
      }

      renderPagination(totalItems, totalPages);
    }

    function renderPagination(totalItems, totalPages) {
      const html = totalItems === 0 ? "" : `
        <button type="button" class="btn" ${state.page <= 1 ? "disabled" : ""} onclick="changePage(${state.page - 1})">◀ 前へ</button>
        <span style="font-size:12px; font-weight:900;">ページ ${state.page} / ${totalPages} (全${totalItems}体)</span>
        <button type="button" class="btn" ${state.page >= totalPages ? "disabled" : ""} onclick="changePage(${state.page + 1})">次へ ▶</button>
      `;
      document.getElementById("paginationTop").innerHTML = html;
      document.getElementById("paginationBottom").innerHTML = html;
    }

    function changePage(p) {
      state.page = Math.max(1, p);
      renderApp();
      window.scrollTo({ top: 0, behavior: "smooth" });
    }

    function selectCardForm(unitId, formIdx) {
      cardFormSelection[unitId] = formIdx;
      renderApp();
    }

    // =========================================================================
    // 7. フィルター・レベルUI制御
    // =========================================================================
    function renderChips() {
      const rWrap = document.getElementById("rarityChips");
      rWrap.innerHTML = ["すべて", ...RARITIES]
        .map((r) => `<button type="button" class="chip ${state.rarity === r ? "active" : ""}" onclick="selectRarity('${r}')">${r}</button>`)
        .join("");

      const tWrap = document.getElementById("traitChips");
      tWrap.innerHTML = ["すべて", ...TRAITS]
        .map((t) => `<button type="button" class="chip ${state.trait === t ? "active-trait" : ""}" onclick="selectTrait('${t}')">${t}</button>`)
        .join("");

      const aWrap = document.getElementById("abilityChips");
      aWrap.innerHTML = ABILITIES
        .map((a) => `<button type="button" class="chip ${state.abilities.includes(a) ? "active-ab" : ""}" onclick="toggleAbility('${a}')">${a}</button>`)
        .join("");
    }

    function setViewMode(mode) {
      state.viewMode = mode;
      document.getElementById("viewCardBtn").classList.toggle("active", mode === "card");
      document.getElementById("viewTableBtn").classList.toggle("active", mode === "table");
      renderApp();
    }

    function selectRarity(r) {
      state.rarity = r;
      state.page = 1;
      renderChips();
      renderApp();
    }

    function selectTrait(t) {
      state.trait = t;
      state.page = 1;
      renderChips();
      renderApp();
    }

    function toggleAbility(a) {
      const idx = state.abilities.indexOf(a);
      if (idx >= 0) state.abilities.splice(idx, 1);
      else state.abilities.push(a);
      state.page = 1;
      renderChips();
      renderApp();
    }

    function toggleAbilityMode() {
      state.abilityMode = state.abilityMode === "AND" ? "OR" : "AND";
      document.getElementById("abilityModeBtn").textContent =
        state.abilityMode === "AND" ? "条件: AND (すべて含む)" : "条件: OR (いずれか含む)";
      state.page = 1;
      renderApp();
    }

    function setCatalogScope(scope) {
      state.catalogScope = scope;
      state.page = 1;
      document.getElementById("scopeAll888Btn").classList.toggle("active", scope === "all888");
      document.getElementById("scopeActive864Btn").classList.toggle("active", scope === "active864");
      renderApp();
    }

    function setGlobalForm(gf) {
      state.globalForm = gf;
      for (const key of Object.keys(cardFormSelection)) {
        delete cardFormSelection[key];
      }
      document.querySelectorAll("#globalFormSeg .seg-btn").forEach((btn) => {
        btn.classList.toggle("active", btn.dataset.gform === gf);
      });
      renderApp();
    }

    function sortByColumn(key) {
      state.sortBy = key;
      document.getElementById("sortSelect").value = key;
      state.page = 1;
      renderApp();
    }

    function clearSearch() {
      state.search = "";
      document.getElementById("searchInput").value = "";
      document.getElementById("clearSearchBtn").style.display = "none";
      state.page = 1;
      renderApp();
    }

    function resetFilters() {
      state.search = "";
      state.sortBy = "idAsc";
      state.catalogScope = "all888";
      state.globalForm = "latest";
      state.rarity = "すべて";
      state.trait = "すべて";
      state.abilities = [];
      state.abilityMode = "AND";
      state.page = 1;

      for (const key of Object.keys(cardFormSelection)) {
        delete cardFormSelection[key];
      }

      document.getElementById("searchInput").value = "";
      document.getElementById("clearSearchBtn").style.display = "none";
      document.getElementById("sortSelect").value = "idAsc";
      document.getElementById("abilityModeBtn").textContent = "条件: AND (すべて含む)";
      document.getElementById("scopeAll888Btn").classList.add("active");
      document.getElementById("scopeActive864Btn").classList.remove("active");
      document.querySelectorAll("#globalFormSeg .seg-btn").forEach((btn) => {
        btn.classList.toggle("active", btn.dataset.gform === "latest");
      });

      renderChips();
      renderApp();
    }

    function updateLevelUI() {
      document.getElementById("baseLvSlider").value = state.baseLv;
      document.getElementById("plusLvSlider").value = state.plusLv;
      document.getElementById("baseLvLabel").textContent = `Lv.${state.baseLv}`;
      document.getElementById("plusLvLabel").textContent = `+${state.plusLv} (計 Lv.${state.baseLv + state.plusLv})`;
      document.getElementById("calcHeaderLabel").textContent =
        `Lv.${state.baseLv} + ${state.plusLv} (${state.treasureBase ? "基本お宝2.5倍" : "お宝なし"})`;
    }

    function stepLevel(kind, delta) {
      if (kind === "base") {
        state.baseLv = Math.min(60, Math.max(1, state.baseLv + delta));
      } else {
        state.plusLv = Math.min(90, Math.max(0, state.plusLv + delta));
      }
      updateLevelUI();
      renderApp();
    }

    function setPresetLevel(base, plus) {
      state.baseLv = base;
      state.plusLv = plus;
      updateLevelUI();
      renderApp();
    }

    function toggleCalcPanel() {
      state.calcExpanded = !state.calcExpanded;
      document.getElementById("calcBody").style.display = state.calcExpanded ? "block" : "none";
      document.getElementById("toggleCalcBtn").textContent = state.calcExpanded ? "折りたたむ ▲" : "展開する ▼";
    }

    // =========================================================================
    // 8. 詳細・カスタム編集・TSV/CSV/JSON モーダル
    // =========================================================================
    function openFirstFilteredEdit() {
      if (!isAdminUnlocked()) return;
      const list = getFilteredUnits();
      const target = list[0] || units[0];
      openDetailModal(target.id, getUnitFormIndex(target));
    }

    function openDetailModal(unitId, formIdx) {
      const u = units.find((x) => x.id === unitId);
      if (!u) return;
      const idx = Math.min(formIdx, u.forms.length - 1);
      state.modalUnitId = u.id;
      state.modalFormIdx = idx;

      const f = u.forms[idx];
      const s = computeStats(u, f);
      const gArr = GROWTH_TABLES[u.growthKey] || GROWTH_TABLES.standard;

      document.getElementById("modalNoBadge").textContent = `No.${u.id}`;
      document.getElementById("modalRarityBadge").textContent = u.rarity;
      const calibBadge = document.getElementById("modalCalibBadge");
      calibBadge.textContent = f.isVerified ? "実測検証済" : "基準スケール値";
      calibBadge.style.background = f.isVerified ? "var(--emerald)" : "#57534e";

      document.getElementById("modalForm1Name").textContent = u.forms[0].name;
      document.getElementById("modalCurName").textContent = f.name;

      document.getElementById("modalFormTabs").innerHTML = u.forms
        .map((fm, i) => `<button type="button" class="seg-btn ${i === idx ? "active" : ""}" onclick="openDetailModal('${u.id}', ${i})">第${i + 1}形態</button>`)
        .join("");

      const lvStr = `Lv.${state.baseLv + state.plusLv}`;
      document.getElementById("modalStatsGrid").innerHTML = `
        <div class="stat-cell"><div class="stat-k">体力 (${lvStr})</div><div class="stat-v c-hp">${s.hp.toLocaleString()}</div></div>
        <div class="stat-cell"><div class="stat-k">攻撃力 (${lvStr})</div><div class="stat-v c-atk">${s.atk.toLocaleString()}</div></div>
        <div class="stat-cell"><div class="stat-k">DPS</div><div class="stat-v c-dps">${s.dps.toLocaleString()}</div></div>
        <div class="stat-cell"><div class="stat-k">KB / 速度</div><div class="stat-v">${f.kb} / ${f.speed}</div></div>
        <div class="stat-cell"><div class="stat-k">感知射程</div><div class="stat-v">${f.range} ${s.ldText ? "(" + s.ldText + ")" : ""}</div></div>
        <div class="stat-cell"><div class="stat-k">攻撃範囲</div><div class="stat-v">${f.area}</div></div>
        <div class="stat-cell"><div class="stat-k">攻撃頻度</div><div class="stat-v">${f.freqF}F (${fmtSec(f.freqF)})</div></div>
        <div class="stat-cell"><div class="stat-k">攻撃発生 / 後隙 / TBA</div><div class="stat-v">${f.foreF}F / ${f.backF}F / ${f.tbaF}F</div></div>
        <div class="stat-cell"><div class="stat-k">コスト (1/2/3章)</div><div class="stat-v">${s.costCh1}/${s.costCh2}/${s.costCh3}</div></div>
        <div class="stat-cell"><div class="stat-k">再生産</div><div class="stat-v">${s.actualRechF}F (${fmtSec(s.actualRechF)})</div></div>
        <div class="stat-cell"><div class="stat-k">対象属性</div><div class="stat-v" style="font-size:11px;">${f.traits.join(", ") || "—"}</div></div>
        <div class="stat-cell"><div class="stat-k">特殊能力・無効</div><div class="stat-v" style="font-size:11px;">${f.abilities.join(", ") || "—"}</div></div>
      `;

      document.getElementById("modalGrowthText").textContent =
        `[${gArr.join("%, ")}%] (Lv.1-10 〜 Lv.91+)`;

      document.getElementById("edName").value = f.name;
      document.getElementById("edHp").value = f.hp;
      document.getElementById("edAtk").value = f.atk;
      document.getElementById("edKb").value = f.kb;
      document.getElementById("edSpeed").value = f.speed;
      document.getElementById("edRange").value = f.range;
      document.getElementById("edFreq").value = f.freqF;
      document.getElementById("edFore").value = f.foreF;
      document.getElementById("edBack").value = f.backF;
      document.getElementById("edTba").value = f.tbaF;
      document.getElementById("edCost").value = f.cost2;
      document.getElementById("edRech").value = f.rechF;
      document.getElementById("edArea").value = f.area;
      document.getElementById("edTraits").value = (f.traits || []).join(", ");
      document.getElementById("edAbilities").value = (f.abilities || []).join(", ");

      document.getElementById("detailModal").classList.add("open");
    }

    function closeDetailModal() {
      document.getElementById("detailModal").classList.remove("open");
    }

    async function saveUnitEdit() {
      if (!isAdminUnlocked()) {
        alert("管理者モード解除中のみ編集できます");
        return;
      }
      const id = state.modalUnitId;
      const idx = state.modalFormIdx;
      const u = units.find((x) => x.id === id);
      if (!u || !u.forms[idx]) return;

      const updated = {
        name: document.getElementById("edName").value.trim() || u.forms[idx].name,
        hp: Math.max(1, parseInt(document.getElementById("edHp").value, 10) || 1),
        atk: Math.max(0, parseInt(document.getElementById("edAtk").value, 10) || 0),
        kb: Math.max(1, parseInt(document.getElementById("edKb").value, 10) || 1),
        speed: Math.max(1, parseInt(document.getElementById("edSpeed").value, 10) || 1),
        range: Math.max(1, parseInt(document.getElementById("edRange").value, 10) || 1),
        freqF: Math.max(1, parseInt(document.getElementById("edFreq").value, 10) || 30),
        foreF: Math.max(1, parseInt(document.getElementById("edFore").value, 10) || 1),
        backF: Math.max(0, parseInt(document.getElementById("edBack").value, 10) || 0),
        tbaF: Math.max(0, parseInt(document.getElementById("edTba").value, 10) || 0),
        cost2: Math.max(0, parseInt(document.getElementById("edCost").value, 10) || 0),
        rechF: Math.max(60, parseInt(document.getElementById("edRech").value, 10) || 60),
        area: document.getElementById("edArea").value,
        traits: document.getElementById("edTraits").value.split(",").map((s) => s.trim()).filter(Boolean),
        abilities: document.getElementById("edAbilities").value.split(",").map((s) => s.trim()).filter(Boolean),
        isVerified: true
      };

      Object.assign(u.forms[idx], updated);
      u.isVerified = true;

      cloudOverridesCache[`${id}-${idx}`] = updated;
      const synced = await pushOverridesToCloud(cloudOverridesCache);

      showToast(synced
        ? `No.${id} 第${idx + 1}形態を保存し全員に反映しました`
        : `No.${id} 第${idx + 1}形態を保存しました`);
      openDetailModal(id, idx);
      renderApp();
    }

    async function resetSingleUnit() {
      if (!isAdminUnlocked()) return;
      const id = state.modalUnitId;
      const idx = state.modalFormIdx;
      delete cloudOverridesCache[`${id}-${idx}`];
      await pushOverridesToCloud(cloudOverridesCache);
      applyOverridesMap(cloudOverridesCache);
      openDetailModal(id, idx);
      renderApp();
      showToast(`No.${id} 第${idx + 1}形態を初期値に戻しました`);
    }

    function openCiteModal() {
      document.getElementById("citeModal").classList.add("open");
    }
    function closeCiteModal() {
      document.getElementById("citeModal").classList.remove("open");
    }

    function openBulkModal() {
      document.getElementById("bulkModal").classList.add("open");
    }
    function closeBulkModal() {
      document.getElementById("bulkModal").classList.remove("open");
    }

    async function importTsv() {
      if (!isAdminUnlocked()) {
        alert("管理者モード解除中のみ取り込みできます");
        return;
      }
      const raw = document.getElementById("tsvInput").value.trim();
      if (!raw) return;
      const lines = raw.split("\n");
      let count = 0;

      for (const line of lines) {
        const p = line.split("\t");
        if (p.length < 4) continue;
        const id = p[0].trim().padStart(3, "0");
        const fIdx = Math.max(0, (parseInt(p[1], 10) || 1) - 1);
        const u = units.find((x) => x.id === id);
        if (!u || !u.forms[fIdx]) continue;

        const patch = {
          hp: parseInt(p[2], 10) || u.forms[fIdx].hp,
          atk: parseInt(p[3], 10) || u.forms[fIdx].atk,
          kb: parseInt(p[4], 10) || u.forms[fIdx].kb,
          speed: parseInt(p[5], 10) || u.forms[fIdx].speed,
          range: parseInt(p[6], 10) || u.forms[fIdx].range,
          freqF: parseInt(p[7], 10) || u.forms[fIdx].freqF,
          foreF: parseInt(p[8], 10) || u.forms[fIdx].foreF,
          tbaF: parseInt(p[9], 10) || u.forms[fIdx].tbaF,
          cost2: parseInt(p[10], 10) || u.forms[fIdx].cost2,
          rechF: parseInt(p[11], 10) || u.forms[fIdx].rechF,
          isVerified: true
        };
        Object.assign(u.forms[fIdx], patch);
        cloudOverridesCache[`${id}-${fIdx}`] = { ...(cloudOverridesCache[`${id}-${fIdx}`] || {}), ...patch };
        count++;
      }

      const synced = await pushOverridesToCloud(cloudOverridesCache);
      renderApp();
      closeBulkModal();
      showToast(synced
        ? `${count}件を取り込み全員に反映しました`
        : `${count}件のステータスを取り込みました`);
    }

    function exportCsv() {
      const list = getFilteredUnits();
      const header = ["図鑑No.", "レア度", "第1形態名", "表示形態", "形態別名称", "体力", "KB", "速度", "攻撃力", "DPS", "範囲", "攻撃頻度F", "攻撃発生F", "TBAF", "感知射程", "第2章コスト", "再生産F", "対象属性", "特殊能力"];
      const csvLines = [header.join(",")];
      for (const u of list) {
        const i = getUnitFormIndex(u);
        const f = u.forms[i];
        const s = computeStats(u, f);
        csvLines.push([
          u.id, u.rarity, `"${u.forms[0].name}"`, `第${i + 1}形態`, `"${f.name}"`,
          s.hp, f.kb, f.speed, s.atk, s.dps, f.area, f.freqF, f.foreF, f.tbaF,
          f.range, s.costCh2, s.actualRechF, `"${f.traits.join("/")}"`, `"${f.abilities.join("/")}"`
        ].join(","));
      }
      const blob = new Blob(["\uFEFF" + csvLines.join("\n")], { type: "text/csv;charset=utf-8;" });
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url;
      a.download = `nyanko_zukan_888_Lv${state.baseLv + state.plusLv}.csv`;
      a.click();
      URL.revokeObjectURL(url);
      showToast("CSVファイルをダウンロードしました");
    }

    function exportJson() {
      if (!isAdminUnlocked()) return;
      const saved = JSON.stringify(cloudOverridesCache, null, 2);
      const blob = new Blob([saved], { type: "application/json" });
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url;
      a.download = "nyanko_custom_stats.json";
      a.click();
      URL.revokeObjectURL(url);
      showToast("カスタム編集JSONをダウンロードしました");
    }

    async function clearAllCustomData() {
      if (!isAdminUnlocked()) return;
      cloudOverridesCache = {};
      await pushOverridesToCloud(cloudOverridesCache);
      applyOverridesMap(cloudOverridesCache);
      renderApp();
      closeBulkModal();
      showToast("カスタム保存データを初期化しました");
    }

    function showToast(msg) {
      const el = document.getElementById("toast");
      el.textContent = msg;
      el.style.display = "block";
      clearTimeout(el._timer);
      el._timer = setTimeout(() => { el.style.display = "none"; }, 2600);
    }

    // =========================================================================
    // 9. イベントバインド＆初期化
    // =========================================================================
    function bindEvents() {
      let titleTapCount = 0;
      let titleTapTimer = null;
      document.getElementById("appTitleBar").addEventListener("click", () => {
        titleTapCount++;
        clearTimeout(titleTapTimer);
        titleTapTimer = setTimeout(() => { titleTapCount = 0; }, 1600);
        if (titleTapCount >= 5) {
          titleTapCount = 0;
          promptAdminUnlock();
        }
      });

      document.getElementById("searchInput").addEventListener("input", (e) => {
        state.search = e.target.value;
        document.getElementById("clearSearchBtn").style.display = state.search ? "block" : "none";
        state.page = 1;
        renderApp();
      });

      document.getElementById("sortSelect").addEventListener("change", (e) => {
        state.sortBy = e.target.value;
        state.page = 1;
        renderApp();
      });

      document.getElementById("baseLvSlider").addEventListener("input", (e) => {
        state.baseLv = parseInt(e.target.value, 10);
        updateLevelUI();
        renderApp();
      });

      document.getElementById("plusLvSlider").addEventListener("input", (e) => {
        state.plusLv = parseInt(e.target.value, 10);
        updateLevelUI();
        renderApp();
      });

      document.getElementById("treasureBaseChk").addEventListener("change", (e) => {
        state.treasureBase = e.target.checked;
        updateLevelUI();
        renderApp();
      });

      document.getElementById("treasureTraitChk").addEventListener("change", (e) => {
        state.treasureTrait = e.target.checked;
        renderApp();
      });

      document.getElementById("treasureStudyChk").addEventListener("change", (e) => {
        state.treasureStudy = e.target.checked;
        renderApp();
      });

      document.getElementById("instinctHpChk").addEventListener("change", (e) => {
        state.instinctHp = e.target.checked;
        renderApp();
      });

      document.getElementById("instinctAtkChk").addEventListener("change", (e) => {
        state.instinctAtk = e.target.checked;
        renderApp();
      });

      document.getElementById("comboSelect").addEventListener("change", (e) => {
        state.comboStudyF = parseInt(e.target.value, 10) || 0;
        renderApp();
      });
    }

    (function initApp() {
      try {
        if (sessionStorage.getItem(ADMIN_SESSION_KEY) === "1") {
          document.body.classList.add("admin-mode");
        }
        loadUnits();
        bindEvents();
        renderChips();
        updateLevelUI();
        renderApp();
        initCloudSync();

        const params = new URLSearchParams(location.search);
        if (params.get("admin") === "1" && !isAdminUnlocked()) {
          setTimeout(promptAdminUnlock, 250);
        }
      } catch (err) {
        console.error(err);
        document.getElementById("mainOutput").innerHTML =
          `<div class="panel" style="color:#b91c1c; font-weight:900;">初期化エラー: ${err.message}</div>`;
      }
    })();
  </script>
</body>
</html>
