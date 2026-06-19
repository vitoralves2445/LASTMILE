<!DOCTYPE html>
<html lang="pt-BR">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Monitoring Last Mile</title>

    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-datalabels@2.2.0"></script>

    <style>
         :root {
            --ml-yellow: #FFE600;
            --bg: #F5F7FA;
            --bg-2: #EEF2F6;
            --panel: #FFFFFF;
            --panel-soft: #FAFBFC;
            --line: #E9EDF3;
            --text: #111827;
            --text-2: #1F2937;
            --text-3: #6B7280;
            --text-4: #9CA3AF;
            --blue: #5B8DEF;
            --teal: #38C5C1;
            --green: #33C56B;
            --amber: #F4B740;
            --red: #F26666;
            --purple: #8B6FF0;
            --slate: #B6C0CE;
            --shadow-xs: 0 1px 3px rgba(15, 23, 42, .03);
            --shadow-sm: 0 4px 10px rgba(15, 23, 42, .04);
            --shadow-md: 0 12px 28px rgba(15, 23, 42, .06);
            --shadow-lg: 0 24px 40px rgba(15, 23, 42, .08);
            --radius-md: 14px;
            --radius-lg: 20px;
            --radius-xl: 28px;
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Inter', sans-serif;
        }
        
        html,
        body {
            min-height: 100%;
        }
        
        body {
            background: radial-gradient(circle at top left, rgba(91, 141, 239, .04), transparent 18%), linear-gradient(180deg, var(--bg) 0%, var(--bg-2) 100%);
            color: var(--text);
            -webkit-font-smoothing: antialiased;
            overflow-x: hidden;
        }
        
         ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        
         ::-webkit-scrollbar-thumb {
            background: #D4DCE7;
            border-radius: 999px;
        }
        
        #status-pill {
            position: fixed;
            top: 18px;
            left: 50%;
            transform: translate(-50%, -20px);
            background: rgba(255, 255, 255, .96);
            color: var(--text);
            padding: 10px 16px;
            border-radius: 999px;
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 800;
            font-size: 12px;
            z-index: 9999;
            opacity: 0;
            visibility: hidden;
            transition: .3s;
            box-shadow: var(--shadow-md);
            border: 1px solid rgba(17, 24, 39, .05);
        }
        
        #status-pill.show {
            opacity: 1;
            visibility: visible;
            transform: translate(-50%, 0);
        }
        
        .spinner {
            width: 14px;
            height: 14px;
            border: 2px solid rgba(17, 24, 39, .12);
            border-top-color: var(--ml-yellow);
            border-radius: 50%;
            animation: spin .8s linear infinite;
        }
        
        @keyframes spin {
            to {
                transform: rotate(360deg);
            }
        }
        
        .topbar {
            position: sticky;
            top: 0;
            z-index: 100;
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 20px;
            padding: 16px 24px;
            background: rgba(255, 255, 255, .84);
            backdrop-filter: blur(14px);
            border-bottom: 1px solid rgba(17, 24, 39, .04);
            box-shadow: 0 4px 12px rgba(15, 23, 42, .03);
        }
        
        .topbar::before {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 3px;
            background: linear-gradient(90deg, var(--ml-yellow), #FFFFFF);
        }
        
        .top-left {
            display: flex;
            align-items: center;
            gap: 12px;
            min-width: 0;
        }
        
        .ml-logo {
            height: 30px;
            width: auto;
        }
        
        .top-title {
            font-size: 20px;
            font-weight: 900;
            color: var(--text);
            line-height: 1.05;
            letter-spacing: -.8px;
        }
        
        .top-sub {
            margin-top: 3px;
            font-size: 10px;
            color: var(--text-3);
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .top-right {
            display: flex;
            gap: 8px;
            align-items: center;
            flex-wrap: wrap;
            justify-content: flex-end;
        }
        
        .pill,
        .btn {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 9px 12px;
            border-radius: 999px;
            border: 1px solid var(--line);
            background: #FFFFFF;
            box-shadow: var(--shadow-xs);
            font-size: 11px;
            font-weight: 800;
            color: var(--text);
        }
        
        .btn {
            cursor: pointer;
            border-radius: 12px;
            transition: .2s ease;
        }
        
        .btn:hover {
            transform: translateY(-1px);
            background: #FCFDFE;
            box-shadow: var(--shadow-sm);
        }
        
        .btn:disabled {
            opacity: .45;
            cursor: not-allowed;
            transform: none;
        }
        
        .content {
            max-width: 1480px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .panel,
        .card,
        .table-container {
            background: var(--panel);
            border: 1px solid rgba(17, 24, 39, .04);
            border-radius: var(--radius-xl);
            box-shadow: var(--shadow-md);
        }
        
        .filter-panel {
            padding: 10px 12px;
            margin-bottom: 10px;
            position: relative;
            overflow: visible;
            z-index: 20;
            border-radius: 22px;
        }
        
        .filter-head {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
            padding-bottom: 8px;
            border-bottom: 1px solid var(--line);
            margin-bottom: 10px;
            position: relative;
            z-index: 10;
        }
        
        .filter-title {
            font-size: 12px;
            font-weight: 900;
            color: var(--text);
        }
        
        .btn-clear {
            background: transparent;
            border: 1px solid transparent;
            color: var(--text-2);
            padding: 6px 10px;
            border-radius: 10px;
            font-size: 11px;
            font-weight: 800;
            cursor: pointer;
            transition: .2s;
        }
        
        .btn-clear:hover {
            background: #F8FAFC;
            border-color: var(--line);
        }
        
        .filter-grid {
            display: grid;
            grid-template-columns: repeat(3, minmax(0, 1fr));
            gap: 8px;
            align-items: end;
        }
        
        .filter-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
            min-width: 0;
        }
        
        .filter-group label {
            font-size: 9px;
            color: var(--text-3);
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .filter-control {
            width: 100%;
            height: 34px;
            padding: 0 10px;
            border: 1px solid var(--line);
            border-radius: 10px;
            font-size: 11px;
            font-weight: 700;
            background: #FFFFFF;
            color: var(--text);
            outline: none;
        }
        
        .filter-control:focus {
            border-color: #C5D2E1;
            box-shadow: 0 0 0 4px rgba(91, 141, 239, .08);
        }
        
        input[type="date"] {
            color-scheme: light;
        }
        
        .status-multiselect {
            position: relative;
            z-index: 999;
        }
        
        .status-dropdown {
            display: none;
            position: absolute;
            top: calc(100% + 8px);
            left: 0;
            width: 100%;
            background: #FFFFFF;
            border: 1px solid #E6EBF2;
            border-radius: 18px;
            box-shadow: 0 18px 40px rgba(15, 23, 42, .10);
            z-index: 9999;
            padding: 12px;
        }
        
        .status-dropdown.open {
            display: block;
        }
        
        .status-option {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px 14px;
            border-radius: 12px;
            cursor: pointer;
            font-weight: 800;
            color: var(--text-2);
            transition: .18s ease;
        }
        
        .status-option input {
            accent-color: var(--ml-yellow);
            width: 15px;
            height: 15px;
        }
        
        .status-option:hover {
            transform: translateX(2px);
        }
        
        .status-option.checked {
            box-shadow: inset 0 0 0 1px rgba(0, 0, 0, .04);
        }
        
        #opt-ALL {
            background: linear-gradient(90deg, #FFF8CC 0%, #FFFDF0 100%);
            color: #6B5B00;
        }
        
        #opt-ALL.checked {
            background: linear-gradient(90deg, #FFE600 0%, #FFF3A6 100%);
            color: #4A3F00;
        }
        
        #opt-ANDAMENTO {
            background: linear-gradient(90deg, #EAF2FF 0%, #F6F9FF 100%);
            color: #315B9F;
        }
        
        #opt-ANDAMENTO.checked {
            background: linear-gradient(90deg, #D9E8FF 0%, #EEF5FF 100%);
            color: #214A8A;
            border: 1px solid rgba(79, 140, 255, .22);
        }
        
        #opt-COLETADO {
            background: linear-gradient(90deg, #EAFBF1 0%, #F5FFF8 100%);
            color: #217A48;
        }
        
        #opt-COLETADO.checked {
            background: linear-gradient(90deg, #DDF8E8 0%, #F0FFF6 100%);
            color: #17663A;
            border: 1px solid rgba(34, 197, 94, .22);
        }
        
        #opt-FALHA {
            background: linear-gradient(90deg, #FFF0F0 0%, #FFF8F8 100%);
            color: #B14040;
        }
        
        #opt-FALHA.checked {
            background: linear-gradient(90deg, #FFE1E1 0%, #FFF1F1 100%);
            color: #982E2E;
            border: 1px solid rgba(239, 68, 68, .22);
        }
        
        #opt-PENDENTE {
            background: linear-gradient(90deg, #FFF7E8 0%, #FFFDF7 100%);
            color: #A36A10;
        }
        
        #opt-PENDENTE.checked {
            background: linear-gradient(90deg, #FFECC8 0%, #FFF8E6 100%);
            color: #8C5808;
            border: 1px solid rgba(245, 158, 11, .22);
        }
        
        .status-divider {
            border-top: 1px solid var(--line);
            margin: 10px 0;
        }
        
        #activeFiltersBar {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-bottom: 12px;
            justify-content: flex-start;
        }
        
        .filter-pill {
            background: #FFFFFF;
            border: 1px solid var(--line);
            color: var(--text-2);
            padding: 7px 12px;
            border-radius: 999px;
            font-size: 11px;
            font-weight: 800;
            box-shadow: var(--shadow-xs);
        }
        
        .kpi-grid {
            display: grid;
            grid-template-columns: repeat(8, minmax(0, 1fr));
            gap: 10px;
            margin-bottom: 14px;
            align-items: stretch;
        }
        
        .kpi-box {
            min-height: 88px;
            padding: 12px;
            border-radius: 18px;
            background: linear-gradient(180deg, #FFFFFF 0%, #FBFCFE 100%);
            border: 1px solid rgba(17, 24, 39, .04);
            box-shadow: var(--shadow-sm);
            position: relative;
            overflow: hidden;
            cursor: pointer;
            transition: .2s ease;
        }
        
        .kpi-box:hover {
            transform: translateY(-2px);
            box-shadow: var(--shadow-md);
        }
        
        .kpi-box.active {
            border-color: rgba(91, 141, 239, .22);
            box-shadow: 0 0 0 1px rgba(91, 141, 239, .10), var(--shadow-md);
        }
        
        .kpi-box::after {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 3px;
        }
        
        .kpi-box[data-kpi="ANDAMENTO"]::after {
            background: var(--blue);
        }
        
        .kpi-box[data-kpi="COLETADO"]::after {
            background: var(--green);
        }
        
        .kpi-box[data-kpi="FALHA"]::after {
            background: var(--red);
        }
        
        .kpi-box[data-kpi="NAO_ROTEIRIZADO"]::after {
            background: var(--amber);
        }
        
        .kpi-box[data-kpi="FALTA_ETIQUETA"]::after {
            background: var(--slate);
        }
        
        .kpi-box[data-kpi="NONE"]::after {
            background: #D6DEE8;
        }
        
        .kpi-top {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 8px;
            margin-bottom: 8px;
        }
        
        .kpi-icon {
            width: 28px;
            height: 28px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
        }
        
        .kpi-icon.info {
            background: rgba(91, 141, 239, .10);
            color: var(--blue);
        }
        
        .kpi-icon.success {
            background: rgba(51, 197, 107, .10);
            color: var(--green);
        }
        
        .kpi-icon.danger {
            background: rgba(242, 102, 102, .10);
            color: var(--red);
        }
        
        .kpi-icon.warning {
            background: rgba(244, 183, 64, .10);
            color: var(--amber);
        }
        
        .kpi-icon.gray {
            background: rgba(168, 176, 189, .10);
            color: #7B8796;
        }
        
        .kpi-trend {
            font-size: 8px;
            font-weight: 800;
            color: var(--text-3);
            background: #F8FAFC;
            border: 1px solid var(--line);
            padding: 3px 6px;
            border-radius: 999px;
        }
        
        .kpi-title {
            font-size: 8px;
            font-weight: 800;
            color: var(--text-4);
            text-transform: uppercase;
            letter-spacing: .9px;
            margin-bottom: 6px;
        }
        
        .kpi-val {
            font-size: 18px;
            font-weight: 900;
            line-height: 1;
            letter-spacing: -.8px;
            color: var(--text);
        }
        
        .kpi-foot {
            margin-top: 4px;
            font-size: 9px;
            color: var(--text-4);
            font-weight: 500;
            line-height: 1.2;
        }
        
        .charts-grid {
            display: grid;
            grid-template-columns: 0.9fr 1.1fr;
            gap: 12px;
            margin-bottom: 12px;
            align-items: stretch;
        }
        
        .card {
            padding: 12px;
            min-height: 170px;
            display: flex;
            flex-direction: column;
            border-radius: 22px;
            background: #FFFFFF;
        }
        
        .card-header {
            margin-bottom: 6px;
        }
        
        .card-title {
            font-size: 12px;
            font-weight: 800;
            color: var(--text);
            letter-spacing: -.2px;
        }
        
        .card-sub {
            margin-top: 3px;
            font-size: 9px;
            color: var(--text-4);
            font-weight: 500;
            line-height: 1.3;
        }
        
        .card-fill {
            flex: 1;
            min-height: 120px;
            position: relative;
        }
        
        #c-culp,
        #c-ocorr {
            width: 100% !important;
            height: 100% !important;
        }
        
        .table-container {
            margin-bottom: 18px;
            overflow: hidden;
            border-radius: 24px;
            background: #FFFFFF;
            box-shadow: var(--shadow-md);
            border: 1px solid rgba(17, 24, 39, .04);
        }
        
        .table-top {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
            padding: 16px 18px;
            border-bottom: 1px solid rgba(17, 24, 39, .05);
            background: #FFFFFF;
        }
        
        .table-top h2 {
            font-size: 14px;
            font-weight: 900;
            color: var(--text);
            line-height: 1;
        }
        
        .table-wrapper {
            overflow: auto;
            max-height: 520px;
            background: #FFFFFF;
        }
        
        table {
            width: 100%;
            min-width: 1100px;
            border-collapse: collapse;
            background: #FFFFFF;
        }
        
        th {
            background: #F8FAFC;
            padding: 13px 16px;
            font-size: 10px;
            font-weight: 800;
            color: var(--text-3);
            text-transform: uppercase;
            letter-spacing: .8px;
            border-bottom: 1px solid rgba(17, 24, 39, .05);
            position: sticky;
            top: 0;
            z-index: 10;
            white-space: nowrap;
            cursor: pointer;
            text-align: left;
        }
        
        td {
            padding: 12px 16px;
            border-bottom: 1px solid rgba(17, 24, 39, .04);
            font-size: 13px;
            font-weight: 500;
            color: var(--text);
            white-space: nowrap;
            background: #FFFFFF;
            vertical-align: top;
        }
        
        tr:hover td {
            background: #FBFCFE;
        }
        
        .col-id {
            font-weight: 800;
            color: var(--text);
        }
        
        .col-resumo {
            max-width: 340px;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        
        .status-badge {
            padding: 7px 12px;
            border-radius: 999px;
            font-size: 11px;
            font-weight: 800;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }
        
        .b-green {
            background: #DCFCE7;
            color: #166534;
        }
        
        .b-red {
            background: #FEE2E2;
            color: #B91C1C;
        }
        
        .b-blue {
            background: #DBEAFE;
            color: #1D4ED8;
        }
        
        .b-orange {
            background: #FEF3C7;
            color: #B45309;
        }
        
        .table-footer {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
            padding: 14px 16px;
            border-top: 1px solid rgba(17, 24, 39, .05);
            background: #FFFFFF;
            color: var(--text-3);
        }
        
        .search-wrap {
            position: relative;
            min-width: 240px;
        }
        
        .search-wrap i {
            position: absolute;
            left: 12px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--text-4);
            font-size: 13px;
        }
        
        .search-wrap input {
            padding: 10px 12px 10px 36px;
            border: 1px solid rgba(17, 24, 39, .08);
            border-radius: 999px;
            font-size: 13px;
            outline: none;
            width: 260px;
            background: #F9FAFB;
            color: var(--text);
            font-weight: 600;
        }
        
        .search-wrap input::placeholder {
            color: var(--text-4);
        }
        
        .search-wrap input:focus {
            border-color: #C5D2E1;
            box-shadow: 0 0 0 4px rgba(91, 141, 239, .08);
        }
        
        .empty-state {
            text-align: center;
            padding: 32px;
            color: var(--text);
            font-weight: 800;
        }
        
        @media (max-width: 1400px) {
            .kpi-grid {
                grid-template-columns: repeat(4, minmax(0, 1fr));
            }
        }
        
        @media (max-width:1300px) {
            .charts-grid {
                grid-template-columns: 1fr;
            }
            .card {
                min-height: 230px;
            }
            .card-fill {
                min-height: 180px;
            }
        }
        
        @media (max-width:1100px) {
            .filter-grid {
                grid-template-columns: 1fr;
            }
        }
        
        @media (max-width:900px) {
            .kpi-grid {
                grid-template-columns: repeat(2, minmax(0, 1fr));
            }
            .topbar {
                flex-direction: column;
                align-items: flex-start;
            }
            .top-right {
                width: 100%;
                justify-content: flex-start;
            }
            .table-top,
            .table-footer {
                flex-direction: column;
                align-items: flex-start;
            }
            .search-wrap input {
                width: 100%;
                min-width: 240px;
            }
        }
        
        @media (max-width:600px) {
            .kpi-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>

<body>
    <div id="status-pill">
        <div class="spinner"></div>
        <span id="status-text">Carregando dados...</span>
    </div>

    <header class="topbar">
        <div class="top-left">
            <img class="ml-logo" src="https://http2.mlstatic.com/frontend-assets/ml-web-navigation/ui-navigation/5.21.22/mercadolibre/logo__large_plus.png" alt="Mercado Livre" />
            <div>
                <div class="top-title">CCO Brasil Control Tower</div>
                <div class="top-sub">Mercado Libre | Monitoring Last Mile</div>
            </div>
        </div>

        <div class="top-right">
            <div class="pill">
                <i class="fa-regular fa-clock"></i>
                <span id="liveClock">--/--/---- --:--</span>
            </div>

            <button class="btn" onclick="loadDataFromServer(false)">
        <i class="fa-solid fa-arrows-rotate"></i>
        Atualizar Base
      </button>
        </div>
    </header>

    <main class="content">
        <section class="filter-panel panel">
            <div class="filter-head">
                <div class="filter-title">Filtros</div>
                <button class="btn-clear" onclick="resetFilters()">
          <i class="fa-solid fa-filter-circle-xmark"></i> Limpar Filtros
        </button>
            </div>

            <div class="filter-grid">
                <div class="filter-group">
                    <label>Selecione a Data</label>
                    <input type="date" id="filter-data" class="filter-control" onchange="loadDataFromServer(false)">
                </div>

                <div class="filter-group status-multiselect">
                    <label>Status do Pacote</label>
                    <div id="status-btn" class="filter-control" onclick="toggleStatusDropdown(event)" style="cursor:pointer;display:flex;justify-content:space-between;align-items:center;">
                        <span id="status-label" style="flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;">Visão Geral (Todos)</span>
                        <i class="fa-solid fa-chevron-down" id="status-chevron" style="font-size:12px;color:var(--text-3);transition:transform .2s;"></i>
                    </div>

                    <div id="status-dropdown" class="status-dropdown">
                        <label class="status-option checked" id="opt-ALL">
              <input type="checkbox" id="chk-ALL" checked onchange="toggleStatusAll()">
              <span>Visão Geral (Todos)</span>
            </label>
                        <div class="status-divider"></div>

                        <label class="status-option" id="opt-ANDAMENTO">
              <input type="checkbox" id="chk-ANDAMENTO" value="ANDAMENTO" onchange="toggleStatus(this)">
              <span>Em Andamento / Em Rota</span>
            </label>

                        <label class="status-option" id="opt-COLETADO">
              <input type="checkbox" id="chk-COLETADO" value="COLETADO" onchange="toggleStatus(this)">
              <span>Sucesso / Coletado</span>
            </label>

                        <label class="status-option" id="opt-FALHA">
              <input type="checkbox" id="chk-FALHA" value="FALHA" onchange="toggleStatus(this)">
              <span>Falhas Operacionais</span>
            </label>

                        <label class="status-option" id="opt-PENDENTE">
              <input type="checkbox" id="chk-PENDENTE" value="PENDENTE" onchange="toggleStatus(this)">
              <span>Não Roteirizado</span>
            </label>
                    </div>
                </div>

                <div class="filter-group">
                    <label>Busca Específica</label>
                    <input type="text" id="filter-id" class="filter-control" placeholder="Ex: Shipment, Rota, Entregador..." oninput="applyFilters()">
                </div>
            </div>
        </section>

        <div id="activeFiltersBar"></div>

        <section class="kpi-grid">
            <div class="kpi-box" data-kpi="NONE">
                <div class="kpi-top">
                    <div class="kpi-icon gray"><i class="fa-solid fa-boxes-stacked"></i></div>
                    <div class="kpi-trend">Volume total</div>
                </div>
                <div class="kpi-title">Shipments</div>
                <div class="kpi-val" id="lk-shipments">0</div>
                <div class="kpi-foot">Base total na visão aplicada</div>
            </div>

            <div class="kpi-box" data-kpi="NONE">
                <div class="kpi-top">
                    <div class="kpi-icon gray"><i class="fa-solid fa-route"></i></div>
                    <div class="kpi-trend">Malha ativa</div>
                </div>
                <div class="kpi-title">Rotas Ativas</div>
                <div class="kpi-val" id="lk-rotas">0</div>
                <div class="kpi-foot">Rotas válidas identificadas</div>
            </div>

            <div class="kpi-box" data-kpi="ANDAMENTO">
                <div class="kpi-top">
                    <div class="kpi-icon info"><i class="fa-solid fa-truck-fast"></i></div>
                    <div class="kpi-trend">Operação</div>
                </div>
                <div class="kpi-title">Em Rota</div>
                <div class="kpi-val" id="lk-em-rota">0</div>
                <div class="kpi-foot">Pacotes em andamento</div>
            </div>

            <div class="kpi-box" data-kpi="COLETADO">
                <div class="kpi-top">
                    <div class="kpi-icon success"><i class="fa-solid fa-circle-check"></i></div>
                    <div class="kpi-trend">Sucesso</div>
                </div>
                <div class="kpi-title">Coletados</div>
                <div class="kpi-val" id="lk-coletados">0</div>
                <div class="kpi-foot">Finalizados com sucesso</div>
            </div>

            <div class="kpi-box" data-kpi="FALTA_ETIQUETA">
                <div class="kpi-top">
                    <div class="kpi-icon gray"><i class="fa-solid fa-tag"></i></div>
                    <div class="kpi-trend">Qualidade</div>
                </div>
                <div class="kpi-title">Falta Etiqueta</div>
                <div class="kpi-val" id="lk-falta-etiqueta">0</div>
                <div class="kpi-foot">Ocorrências sem etiqueta</div>
            </div>

            <div class="kpi-box" data-kpi="NAO_ROTEIRIZADO">
                <div class="kpi-top">
                    <div class="kpi-icon warning"><i class="fa-solid fa-map-location-dot"></i></div>
                    <div class="kpi-trend">Pendência</div>
                </div>
                <div class="kpi-title">Não Roteirizado</div>
                <div class="kpi-val" id="lk-nao-roteirizado">0</div>
                <div class="kpi-foot">Sem roteirização válida</div>
            </div>

            <div class="kpi-box" data-kpi="FALHA">
                <div class="kpi-top">
                    <div class="kpi-icon danger"><i class="fa-solid fa-triangle-exclamation"></i></div>
                    <div class="kpi-trend">Crítico</div>
                </div>
                <div class="kpi-title">Falhas</div>
                <div class="kpi-val" id="lk-falhas">0</div>
                <div class="kpi-foot">Status do pacote em falha</div>
            </div>

            <div class="kpi-box" data-kpi="NONE">
                <div class="kpi-top">
                    <div class="kpi-icon info"><i class="fa-solid fa-percent"></i></div>
                    <div class="kpi-trend">Performance</div>
                </div>
                <div class="kpi-title">% PS</div>
                <div class="kpi-val" id="lk-ps">0%</div>
                <div class="kpi-foot">Eficiência da operação</div>
            </div>
        </section>

        <section class="charts-grid">
            <div class="card">
                <div class="card-header">
                    <div class="card-title">Culpabilidade</div>
                    <div class="card-sub">Distribuição das áreas com maior concentração de falhas</div>
                </div>
                <div class="card-fill">
                    <canvas id="c-culp"></canvas>
                </div>
            </div>

            <div class="card">
                <div class="card-header">
                    <div class="card-title">Ocorrências</div>
                    <div class="card-sub">Principais ofensores da operação</div>
                </div>
                <div class="card-fill">
                    <canvas id="c-ocorr"></canvas>
                </div>
            </div>
        </section>

        <div class="table-container">
            <div class="table-top">
                <h2>Detalhamento de Ocorrências</h2>
                <div class="search-wrap">
                    <i class="fa-solid fa-magnifying-glass"></i>
                    <input type="text" id="search-ocorr" placeholder="Buscar na tabela..." oninput="renderTables()">
                </div>
            </div>

            <div class="table-wrapper">
                <table>
                    <thead>
                        <tr>
                            <th onclick="sortT1('SVC')">SVC</th>
                            <th onclick="sortT1('SHIPMENT_ID')">Shipment</th>
                            <th onclick="sortT1('ROUTE_ID')">Route ID</th>
                            <th onclick="sortT1('ID_ROTA')">ID Rota</th>
                            <th onclick="sortT1('PREVISAO')">Previsão</th>
                            <th onclick="sortT1('STATUS')">Status</th>
                            <th onclick="sortT1('OCORRENCIA_SISTEMICA')">Ocorrência Sist.</th>
                            <th onclick="sortT1('OCORRENCIA_MONITORING')">Ocorrência Monit.</th>
                            <th onclick="sortT1('RESUMO_COMENTARIO')">Resumo Comentário</th>
                        </tr>
                    </thead>
                    <tbody id="tbody-ocorrencias"></tbody>
                </table>
            </div>

            <div class="table-footer">
                <span id="info-ocorr" style="font-weight:700;color:var(--text);">Carregando...</span>
                <div style="display:flex;gap:8px;">
                    <button class="btn" id="btnPrevOc" onclick="changePageOc(-1)">Anterior</button>
                    <button class="btn" id="btnNextOc" onclick="changePageOc(1)">Próxima</button>
                </div>
            </div>
        </div>

        <div class="table-container">
            <div class="table-top">
                <h2>Monitoramento Geral</h2>
                <div class="search-wrap">
                    <i class="fa-solid fa-magnifying-glass"></i>
                    <input type="text" id="search-monit" placeholder="Buscar na tabela..." oninput="renderTables()">
                </div>
            </div>

            <div class="table-wrapper">
                <table>
                    <thead>
                        <tr>
                            <th onclick="sortT2('SVC')">SVC</th>
                            <th onclick="sortT2('ROUTE_ID')">Route ID</th>
                            <th onclick="sortT2('ID_ROTA')">ID Rota</th>
                            <th onclick="sortT2('MLP')">Transportadora</th>
                            <th onclick="sortT2('PREVISAO_INICIO')">Previsão Início</th>
                            <th onclick="sortT2('STATUS_PACOTE')">Status Pacote</th>
                            <th onclick="sortT2('OCORRENCIA_MONITORING')">Ocorrência</th>
                            <th onclick="sortT2('SHIPMENT_ID')">Shipment</th>
                        </tr>
                    </thead>
                    <tbody id="tbody-monitoramento"></tbody>
                </table>
            </div>

            <div class="table-footer">
                <span id="info-monit" style="font-weight:700;color:var(--text);">Carregando...</span>
                <div style="display:flex;gap:8px;">
                    <button class="btn" id="btnPrevMo" onclick="changePageMo(-1)">Anterior</button>
                    <button class="btn" id="btnNextMo" onclick="changePageMo(1)">Próxima</button>
                </div>
            </div>
        </div>
    </main>

    <script>
        Chart.register(ChartDataLabels);
        Chart.defaults.font.family = "'Inter', sans-serif";
        Chart.defaults.color = "#111827";

        let serverData = [];
        let serverKpis = null;
        let subFilteredData = [];
        let pageOc = 1;
        let pageMo = 1;
        let sortOc = {
            col: null,
            dir: 'asc'
        };
        let sortMo = {
            col: null,
            dir: 'asc'
        };
        let __topFilter = 'NONE';
        let __selectedOcorr = null;
        let selectedStatuses = new Set();
        window.__culpFilter = null;
        const rowsPerPage = 15;
        window.__extraCharts = {};

        function norm(s) {
            return String(s || '')
                .toUpperCase()
                .trim()
                .normalize("NFD")
                .replace(/[\u0300-\u036f]/g, "");
        }

        function hardNorm(s) {
            return String(s || '')
                .toUpperCase()
                .trim()
                .normalize("NFD")
                .replace(/[\u0300-\u036f]/g, "")
                .replace(/[^A-Z0-9]/g, "");
        }

        function showLoading(b, text = 'Carregando dados...') {
            const pill = document.getElementById('status-pill');
            const tx = document.getElementById('status-text');
            tx.innerText = text;
            pill.classList.toggle('show', b);
        }

        function updateClock() {
            const now = new Date();
            document.getElementById('liveClock').innerText =
                now.toLocaleDateString('pt-BR') + ' ' +
                now.toLocaleTimeString('pt-BR', {
                    hour: '2-digit',
                    minute: '2-digit'
                });
        }
        setInterval(updateClock, 1000);
        updateClock();

        function animateInt(el, to) {
            const currentText = (el.innerText || "0").replace(/[^\d.-]/g, '');
            const from = parseInt(currentText) || 0;
            const start = performance.now();

            requestAnimationFrame(function tick(t) {
                const p = Math.min(1, (t - start) / 400);
                el.innerText = Math.round(from + (to - from) * p).toLocaleString('pt-BR');
                if (p < 1) requestAnimationFrame(tick);
            });
        }

        function animateTextValue(el, finalText) {
            el.innerText = finalText;
        }

        function getFlags(row) {
            const stRaw = String(row.STATUS_PACOTE || '');
            const ocMonRaw = String(row.OCORRENCIA_MONITORING || '');
            const routeIdRaw = String(row.ROUTE_ID || '');
            const flagTypeRaw = String(row.FLAG_TYPE || '');

            const stLimpo = hardNorm(stRaw);
            const obLimpo = hardNorm(ocMonRaw);
            const routeIdLimpo = hardNorm(routeIdRaw);
            const flagType = norm(flagTypeRaw);

            const isCancelado = stLimpo.includes("CANCELADO");

            const isRevertidoAndamento =
                stLimpo.includes("REVERTIDOEMANDAMENTO") ||
                stLimpo.includes("REVERTIDOANDAMENTO");

            const isNaoColetado =
                stLimpo.includes("NAOCOLETADO");

            const isColetado =
                stLimpo.includes("COLETADO") && !isNaoColetado;

            const isEmRota =
                stLimpo.includes("EMROTA");

            const isNaoRoteirizado =
                routeIdLimpo.includes("NAOROTEIRIZAD") ||
                stLimpo.includes("NAOROTEIRIZAD") ||
                obLimpo.includes("NAOROTEIRIZAD");

            const isFalha = !isColetado &&
                (isNaoColetado || isRevertidoAndamento || isNaoRoteirizado);

            const isAndamento = !isColetado &&
                !isFalha &&
                isEmRota;

            const isFaltaEtiqueta =
                flagType === 'GM' ||
                flagType === 'SEM ETIQUETA' ||
                flagType === 'FALTA ETIQUETA';

            return {
                routeId: routeIdRaw,
                isCancelado,
                isNaoRoteirizado,
                isColetado,
                isAndamento,
                isFalha,
                isFaltaEtiqueta
            };
        }

        function toggleStatusDropdown(event) {
            event.stopPropagation();
            const dropdown = document.getElementById('status-dropdown');
            const chevron = document.getElementById('status-chevron');
            dropdown.classList.toggle('open');
            chevron.style.transform = dropdown.classList.contains('open') ? 'rotate(180deg)' : 'rotate(0deg)';
        }

        function updateStatusLabel() {
            const label = document.getElementById('status-label');
            if (selectedStatuses.size === 0) {
                label.innerText = 'Visão Geral (Todos)';
                return;
            }

            const map = {
                ANDAMENTO: 'Em Rota',
                COLETADO: 'Coletado',
                FALHA: 'Falha',
                PENDENTE: 'Não Roteirizado'
            };

            label.innerText = [...selectedStatuses].map(x => map[x] || x).join(', ');
        }

        function toggleStatusAll() {
            selectedStatuses.clear();
            ['ANDAMENTO', 'COLETADO', 'FALHA', 'PENDENTE'].forEach(v => {
                document.getElementById('chk-' + v).checked = false;
                document.getElementById('opt-' + v).classList.remove('checked');
            });
            document.getElementById('chk-ALL').checked = true;
            document.getElementById('opt-ALL').classList.add('checked');
            updateStatusLabel();
            applyFilters();
        }

        function toggleStatus(chk) {
            const val = chk.value;
            if (chk.checked) {
                selectedStatuses.add(val);
                document.getElementById('chk-ALL').checked = false;
                document.getElementById('opt-ALL').classList.remove('checked');
            } else {
                selectedStatuses.delete(val);
            }

            document.getElementById('opt-' + val).classList.toggle('checked', chk.checked);

            if (selectedStatuses.size === 0) {
                document.getElementById('chk-ALL').checked = true;
                document.getElementById('opt-ALL').classList.add('checked');
            }

            updateStatusLabel();
            applyFilters();
        }

        function mapServerRows(rawData) {
            return rawData.map(row => {
                const up = {};
                Object.keys(row || {}).forEach(k => {
                    up[norm(k)] = String(row[k] ? ? '').trim();
                });

                const getE = (...names) => {
                    for (let n of names) {
                        const v = up[norm(n)];
                        if (v !== undefined && v !== '') return v;
                    }
                    return "";
                };

                const getP = (...names) => {
                    let v = getE(...names);
                    if (v) return v;
                    for (let n of names) {
                        const key = Object.keys(up).find(k => k.includes(norm(n)));
                        if (key && up[key] !== '') return up[key];
                    }
                    return "-";
                };

                let dataLinha = getE("DATA") || getP("DATA");
                if (dataLinha.includes("/")) {
                    const [dd, mm, yyyy] = dataLinha.split("/");
                    if (yyyy && mm && dd) dataLinha = `${yyyy}-${mm}-${dd}`;
                }

                return {
                    "SVC": getP("SVC"),
                    "SHIPMENT_ID": getP("SHIPMENT_ID", "SHIPMENT", "SHIPMENT_ID_V2"),
                    "ROUTE_ID": getE("ROUTE_ID", "ROUTE ID"),
                    "ROUTE_ID_SHORT": getP("ROUTE_ID_SHORT", "ROUTE ID SHORT", "ROTA CURTA"),
                    "ID_ROTA": getP("ROUTE_NAME", "ID_ROTA", "ID ROTA", "ROUTE NAME"),
                    "MLP": getP("MLP", "TRANSPORTADORA"),
                    "PREVISAO": getP("PREVISAO START ROTA", "PREVISAO", "PREVISAO DE INICIO"),
                    "PREVISAO_INICIO": getP("PREVISAO DE INICIO", "PREVISAO"),
                    "STATUS": getP("STATUS DO PACOTE", "STATUS_PACOTE", "STATUS -", "STATUS"),
                    "STATUS_PACOTE": getP("STATUS DO PACOTE", "STATUS_PACOTE", "STATUS PACOTE"),
                    "OCORRENCIA_SISTEMICA": getP("OCORRÊNCIA SISTÊMICA", "OCORRENCIA SISTEMICA", "OCORRÊNCIA SIST"),
                    "OCORRENCIA_MONITORING": getP("OCORRÊNCIA MONITORING", "OCORRENCIA MONITORING", "OCORRÊNCIA MONIT"),
                    "RESUMO_COMENTARIO": getP("RESUMO COMENTARIO", "COMENTÁRIO"),
                    "RESPONSAVEL": getP("CULPABILIDADE", "RESPONSAVEL"),
                    "FLAG_TYPE": getP("FLAG_TYPE", "FLAG TYPE", "ETIQUETA"),
                    "DATA": dataLinha && dataLinha !== "-" ? dataLinha : ""
                };
            });
        }

        function loadDataFromServer(isSilent = false) {
            if (!isSilent) showLoading(true, 'Carregando base...');

            const selectedDate = document.getElementById('filter-data').value || '';

            google.script.run
                .withSuccessHandler(res => {
                    try {
                        const rawData = Array.isArray(res ? .data) ? res.data : [];
                        serverKpis = res ? .kpis || null;
                        serverData = mapServerRows(rawData);
                        applyFilters();
                    } catch (e) {
                        console.error('Erro ao processar dados', e);
                    } finally {
                        if (!isSilent) showLoading(false);
                    }
                })
                .withFailureHandler(err => {
                    console.error('Erro no Apps Script', err);
                    if (!isSilent) showLoading(false);
                })
                .getSheetData(selectedDate);
        }

        function resetFilters() {
            document.getElementById('filter-id').value = "";
            document.getElementById('search-ocorr').value = "";
            document.getElementById('search-monit').value = "";
            selectedStatuses.clear();
            __topFilter = 'NONE';
            __selectedOcorr = null;
            window.__culpFilter = null;
            pageOc = 1;
            pageMo = 1;
            sortOc = {
                col: null,
                dir: 'asc'
            };
            sortMo = {
                col: null,
                dir: 'asc'
            };

            document.getElementById('chk-ALL').checked = true;
            document.getElementById('opt-ALL').classList.add('checked');

            ['ANDAMENTO', 'COLETADO', 'FALHA', 'PENDENTE'].forEach(v => {
                document.getElementById('chk-' + v).checked = false;
                document.getElementById('opt-' + v).classList.remove('checked');
            });

            updateStatusLabel();
            loadDataFromServer(false);
        }

        function applyFilters() {
            const inputId = norm(document.getElementById('filter-id').value);

            let filtrado = serverData.filter(row => {
                const flags = getFlags(row);
                if (flags.isCancelado) return false;

                let match = true;

                if (match && inputId) match = Object.values(row).join(' ').toUpperCase().includes(inputId);

                if (match && selectedStatuses.size > 0) {
                    match =
                        (selectedStatuses.has('ANDAMENTO') && flags.isAndamento) ||
                        (selectedStatuses.has('COLETADO') && flags.isColetado) ||
                        (selectedStatuses.has('FALHA') && flags.isFalha) ||
                        (selectedStatuses.has('PENDENTE') && flags.isNaoRoteirizado);
                }

                return match;
            });

            if (window.__culpFilter) {
                filtrado = filtrado.filter(r => norm(r.RESPONSAVEL || 'OUTROS') === window.__culpFilter);
            }

            if (__selectedOcorr) {
                filtrado = filtrado.filter(r => norm(r.OCORRENCIA_MONITORING || '') === norm(__selectedOcorr));
            }

            if (__topFilter === 'NAO_ROTEIRIZADO') filtrado = filtrado.filter(r => getFlags(r).isNaoRoteirizado);
            else if (__topFilter === 'FALHA') filtrado = filtrado.filter(r => getFlags(r).isFalha);
            else if (__topFilter === 'COLETADO') filtrado = filtrado.filter(r => getFlags(r).isColetado);
            else if (__topFilter === 'ANDAMENTO') filtrado = filtrado.filter(r => getFlags(r).isAndamento);
            else if (__topFilter === 'FALTA_ETIQUETA') filtrado = filtrado.filter(r => getFlags(r).isFaltaEtiqueta);

            subFilteredData = [...filtrado];

            pageOc = 1;
            pageMo = 1;

            renderKpis(subFilteredData);
            renderTables();
            renderCharts(subFilteredData);

            document.querySelectorAll('.kpi-box').forEach(b => b.classList.remove('active'));
            if (__topFilter !== 'NONE') {
                const activeBox = document.querySelector(`.kpi-box[data-kpi="${__topFilter}"]`);
                if (activeBox) activeBox.classList.add('active');
            }

            const pills = [
                __topFilter !== 'NONE' ? 'Filtro KPI: ' + __topFilter : null,
                __selectedOcorr ? 'Ocorrência: ' + __selectedOcorr : null,
                window.__culpFilter ? 'Culpabilidade: ' + window.__culpFilter : null,
                selectedStatuses.size ? 'Status: ' + [...selectedStatuses].join(', ') : null
            ].filter(Boolean);

            document.getElementById('activeFiltersBar').innerHTML =
                pills.map(p => `<span class="filter-pill">${p}</span>`).join('');
        }

        function renderKpis(data) {
            const onlyDateFilterActive =
                __topFilter === 'NONE' &&
                !__selectedOcorr &&
                !window.__culpFilter &&
                selectedStatuses.size === 0 &&
                norm(document.getElementById('filter-id').value) === '';

            if (serverKpis && onlyDateFilterActive) {
                animateInt(document.getElementById('lk-shipments'), Number(serverKpis.shipments || 0));
                animateInt(document.getElementById('lk-rotas'), Number(serverKpis.rotas || 0));
                animateInt(document.getElementById('lk-em-rota'), Number(serverKpis.emRota || 0));
                animateInt(document.getElementById('lk-coletados'), Number(serverKpis.coletados || 0));
                animateInt(document.getElementById('lk-nao-roteirizado'), Number(serverKpis.naoRoteirizado || 0));
                animateInt(document.getElementById('lk-falhas'), Number(serverKpis.falhas || 0));
                animateInt(document.getElementById('lk-falta-etiqueta'), Number(serverKpis.faltaEtiqueta || 0));
                animateTextValue(
                    document.getElementById('lk-ps'),
                    Number(serverKpis.ps || 0).toFixed(2).replace('.', ',') + '%'
                );
                return;
            }

            const validData = data.filter(r => !getFlags(r).isCancelado);

            const ship = validData.length;
            let emAnd = 0;
            let fin = 0;
            let falha = 0;
            let naoRot = 0;
            let faltaEt = 0;
            const rotas = new Set();

            validData.forEach(r => {
                const f = getFlags(r);

                if (f.isColetado) fin++;
                if (f.isAndamento) emAnd++;
                if (f.isFalha) falha++;
                if (f.isNaoRoteirizado) naoRot++;
                if (f.isFaltaEtiqueta) faltaEt++;

                const routeNorm = hardNorm(f.routeId);
                if (
                    routeNorm &&
                    routeNorm !== "-" &&
                    routeNorm !== "0" &&
                    routeNorm !== "N/A" &&
                    routeNorm !== "#N/A" &&
                    !routeNorm.includes("NAOROTEIRIZAD")
                ) {
                    rotas.add(String(f.routeId).trim());
                }
            });

            animateInt(document.getElementById('lk-shipments'), ship);
            animateInt(document.getElementById('lk-rotas'), rotas.size);
            animateInt(document.getElementById('lk-em-rota'), emAnd);
            animateInt(document.getElementById('lk-coletados'), fin);
            animateInt(document.getElementById('lk-nao-roteirizado'), naoRot);
            animateInt(document.getElementById('lk-falhas'), falha);
            animateInt(document.getElementById('lk-falta-etiqueta'), faltaEt);

            const ps = ship > 0 ? ((fin / ship) * 100) : 0;
            animateTextValue(
                document.getElementById('lk-ps'),
                ps.toFixed(2).replace('.', ',') + '%'
            );
        }

        function getModernBadge(st) {
            const raw = st || '-';
            const s = hardNorm(raw);

            const isNaoColetado = s.includes("NAOCOLETADO");
            const isColetado = s.includes("COLETADO") && !isNaoColetado;
            const isEmRota = s.includes("EMROTA");
            const isNaoRoteirizado = s.includes("NAOROTEIRIZAD");
            const isFalha = isNaoColetado || s.includes("REVERTIDOEMANDAMENTO") || s.includes("REVERTIDOANDAMENTO");

            if (isColetado) {
                return `<span class="status-badge b-green"><i class="fa-solid fa-check"></i> ${raw}</span>`;
            }

            if (isEmRota && !isFalha) {
                return `<span class="status-badge b-blue"><i class="fa-solid fa-truck-fast"></i> ${raw}</span>`;
            }

            if (isFalha || isNaoRoteirizado) {
                return `<span class="status-badge b-red"><i class="fa-solid fa-xmark"></i> ${raw}</span>`;
            }

            return `<span class="status-badge b-orange"><i class="fa-solid fa-clock"></i> ${raw}</span>`;
        }

        function sortData(data, cfg) {
            if (!cfg.col) return data;
            const dir = cfg.dir === 'asc' ? 1 : -1;
            return [...data].sort((a, b) =>
                String(a[cfg.col] || '').localeCompare(String(b[cfg.col] || ''), 'pt-BR', {
                    numeric: true
                }) * dir
            );
        }

        function sortT1(c) {
            sortOc.dir = (sortOc.col === c && sortOc.dir === 'asc') ? 'desc' : 'asc';
            sortOc.col = c;
            pageOc = 1;
            renderTables();
        }

        function changePageOc(d) {
            pageOc += d;
            renderTables();
        }

        function sortT2(c) {
            sortMo.dir = (sortMo.col === c && sortMo.dir === 'asc') ? 'desc' : 'asc';
            sortMo.col = c;
            pageMo = 1;
            renderTables();
        }

        function changePageMo(d) {
            pageMo += d;
            renderTables();
        }

        function renderTables() {
            let dataOc = subFilteredData.filter(r => {
                const f = getFlags(r);
                return !f.isCancelado && (f.isFalha || f.isNaoRoteirizado);
            });

            const qOc = norm(document.getElementById('search-ocorr').value);
            if (qOc) dataOc = dataOc.filter(r => Object.values(r).join(' ').toUpperCase().includes(qOc));
            dataOc = sortData(dataOc, sortOc);

            const tb1 = document.getElementById('tbody-ocorrencias');
            tb1.innerHTML = '';

            const totOc = Math.ceil(dataOc.length / rowsPerPage) || 1;
            pageOc = Math.max(1, Math.min(pageOc, totOc));
            const slOc = dataOc.slice((pageOc - 1) * rowsPerPage, pageOc * rowsPerPage);

            if (!slOc.length) {
                tb1.innerHTML = '<tr><td colspan="9" class="empty-state">Nenhuma ocorrência encontrada.</td></tr>';
            } else {
                slOc.forEach(r => {
                    tb1.innerHTML += `
            <tr>
              <td>${r.SVC || '-'}</td>
              <td class="col-id">${r.SHIPMENT_ID || '-'}</td>
              <td>${r.ROUTE_ID || '-'}</td>
              <td>${r.ID_ROTA || '-'}</td>
              <td>${r.PREVISAO || '-'}</td>
              <td>${getModernBadge(r.STATUS)}</td>
              <td>${r.OCORRENCIA_SISTEMICA || '-'}</td>
              <td>${r.OCORRENCIA_MONITORING || '-'}</td>
              <td class="col-resumo" title="${r.RESUMO_COMENTARIO || '-'}">${r.RESUMO_COMENTARIO || '-'}</td>
            </tr>
          `;
                });
            }

            document.getElementById('info-ocorr').innerText =
                `Página ${pageOc} de ${totOc} (Total: ${dataOc.length.toLocaleString('pt-BR')})`;
            document.getElementById('btnPrevOc').disabled = pageOc === 1;
            document.getElementById('btnNextOc').disabled = pageOc === totOc;

            let dataMo = subFilteredData.filter(r => !getFlags(r).isCancelado);
            const qMo = norm(document.getElementById('search-monit').value);
            if (qMo) dataMo = dataMo.filter(r => Object.values(r).join(' ').toUpperCase().includes(qMo));
            dataMo = sortData(dataMo, sortMo);

            const tb2 = document.getElementById('tbody-monitoramento');
            tb2.innerHTML = '';

            const totMo = Math.ceil(dataMo.length / rowsPerPage) || 1;
            pageMo = Math.max(1, Math.min(pageMo, totMo));
            const slMo = dataMo.slice((pageMo - 1) * rowsPerPage, pageMo * rowsPerPage);

            if (!slMo.length) {
                tb2.innerHTML = '<tr><td colspan="8" class="empty-state">Nenhum dado de monitoramento.</td></tr>';
            } else {
                slMo.forEach(r => {
                    tb2.innerHTML += `
            <tr>
              <td>${r.SVC || '-'}</td>
              <td>${r.ROUTE_ID || '-'}</td>
              <td>${r.ID_ROTA || '-'}</td>
              <td>${r.MLP || '-'}</td>
              <td>${r.PREVISAO_INICIO || '-'}</td>
              <td>${getModernBadge(r.STATUS_PACOTE)}</td>
              <td>${r.OCORRENCIA_MONITORING || '-'}</td>
              <td class="col-id">${r.SHIPMENT_ID || '-'}</td>
            </tr>
          `;
                });
            }

            document.getElementById('info-monit').innerText =
                `Página ${pageMo} de ${totMo} (Total: ${dataMo.length.toLocaleString('pt-BR')})`;
            document.getElementById('btnPrevMo').disabled = pageMo === 1;
            document.getElementById('btnNextMo').disabled = pageMo === totMo;
        }

        function destroyChart(id) {
            if (window.__extraCharts[id]) window.__extraCharts[id].destroy();
        }

        function renderCharts(baseData) {
            const baseValida = baseData.filter(r => !getFlags(r).isCancelado);
            const falhas = baseValida.filter(r => getFlags(r).isFalha);

            // CULPABILIDADE -> barras horizontais executivas
            const mapCulp = new Map();
            falhas.forEach(r => {
                const k = r.RESPONSAVEL && r.RESPONSAVEL !== '-' ? r.RESPONSAVEL : 'Outros';
                mapCulp.set(k, (mapCulp.get(k) || 0) + 1);
            });

            const culpEntries = [...mapCulp.entries()]
                .sort((a, b) => b[1] - a[1])
                .slice(0, 5);

            const labelsCulp = culpEntries.map(x => x[0]);
            const valuesCulp = culpEntries.map(x => x[1]);
            const totalCulp = valuesCulp.reduce((a, b) => a + b, 0) || 1;

            const ctxCulp = document.getElementById('c-culp').getContext('2d');

            const gradC1 = ctxCulp.createLinearGradient(0, 0, 500, 0);
            gradC1.addColorStop(0, '#7BA7FF');
            gradC1.addColorStop(1, '#4A7FF0');

            const gradC2 = ctxCulp.createLinearGradient(0, 0, 500, 0);
            gradC2.addColorStop(0, '#57D8C6');
            gradC2.addColorStop(1, '#28C7C1');

            const gradC3 = ctxCulp.createLinearGradient(0, 0, 500, 0);
            gradC3.addColorStop(0, '#FFD97B');
            gradC3.addColorStop(1, '#F5B83D');

            destroyChart('c-culp');
            window.__extraCharts['c-culp'] = new Chart(document.getElementById('c-culp'), {
                type: 'bar',
                data: {
                    labels: labelsCulp,
                    datasets: [{
                        data: valuesCulp,
                        backgroundColor: valuesCulp.map((_, i) => {
                            if (norm(labelsCulp[i]) === norm(window.__culpFilter)) return '#111827';
                            if (i === 0) return gradC1;
                            if (i === 1) return gradC2;
                            if (i === 2) return gradC3;
                            return '#B8C2CF';
                        }),
                        borderRadius: 999,
                        borderSkipped: false,
                        barThickness: 8
                    }]
                },
                options: {
                    maintainAspectRatio: false,
                    indexAxis: 'y',
                    layout: {
                        padding: {
                            top: 4,
                            bottom: 4,
                            left: 4,
                            right: 48
                        }
                    },
                    plugins: {
                        legend: {
                            display: false
                        },
                        tooltip: {
                            backgroundColor: '#FFFFFF',
                            titleColor: '#111827',
                            bodyColor: '#111827',
                            borderColor: '#E5E7EB',
                            borderWidth: 1,
                            padding: 10,
                            callbacks: {
                                label: function(context) {
                                    const idx = context.dataIndex;
                                    const val = valuesCulp[idx];
                                    const pct = ((val / totalCulp) * 100).toFixed(1);
                                    return `${labelsCulp[idx]}: ${val} (${pct}%)`;
                                }
                            }
                        },
                        datalabels: {
                            color: '#111827',
                            anchor: 'end',
                            align: 'right',
                            offset: 8,
                            clamp: true,
                            clip: false,
                            font: {
                                weight: '800',
                                size: 8
                            },
                            formatter: (value) => {
                                const pct = ((value / totalCulp) * 100).toFixed(1);
                                return `${value} • ${pct}%`;
                            }
                        }
                    },
                    scales: {
                        x: {
                            beginAtZero: true,
                            display: false,
                            grid: {
                                display: false
                            },
                            border: {
                                display: false
                            }
                        },
                        y: {
                            grid: {
                                display: false
                            },
                            border: {
                                display: false
                            },
                            ticks: {
                                color: '#334155',
                                font: {
                                    size: 8,
                                    weight: '600'
                                }
                            }
                        }
                    },
                    onClick: (e, els) => {
                        if (!els.length) {
                            window.__culpFilter = null;
                        } else {
                            const val = labelsCulp[els[0].index];
                            window.__culpFilter = (norm(window.__culpFilter) === norm(val)) ? null : norm(val);
                        }
                        pageOc = 1;
                        pageMo = 1;
                        applyFilters();
                    }
                }
            });

            // OCORRÊNCIAS
            let baseOc = falhas;

            if (window.__culpFilter) {
                baseOc = baseOc.filter(r => norm(r.RESPONSAVEL || 'Outros') === window.__culpFilter);
            }

            const mapOc = new Map();
            baseOc.forEach(r => {
                const k = r.OCORRENCIA_MONITORING || 'Sem ocorrência';
                mapOc.set(k, (mapOc.get(k) || 0) + 1);
            });

            const topOc = [...mapOc.entries()]
                .sort((a, b) => b[1] - a[1])
                .slice(0, 7);

            const labelsOc = topOc.map(x => x[0]);
            const valuesOc = topOc.map(x => x[1]);

            const ctxOc = document.getElementById('c-ocorr').getContext('2d');

            const grad1 = ctxOc.createLinearGradient(0, 0, 500, 0);
            grad1.addColorStop(0, '#74A6FF');
            grad1.addColorStop(1, '#4A7FF0');

            const grad2 = ctxOc.createLinearGradient(0, 0, 500, 0);
            grad2.addColorStop(0, '#57D8C6');
            grad2.addColorStop(1, '#28C7C1');

            const grad3 = ctxOc.createLinearGradient(0, 0, 500, 0);
            grad3.addColorStop(0, '#FFD97B');
            grad3.addColorStop(1, '#F5B83D');

            destroyChart('c-ocorr');
            window.__extraCharts['c-ocorr'] = new Chart(document.getElementById('c-ocorr'), {
                type: 'bar',
                data: {
                    labels: labelsOc.map(x => x.length > 28 ? x.substring(0, 28) + '...' : x),
                    datasets: [{
                        data: valuesOc,
                        backgroundColor: valuesOc.map((_, i) => {
                            if (norm(labelsOc[i]) === norm(__selectedOcorr)) return '#D1D9E6';
                            if (i === 0) return grad1;
                            if (i === 1) return grad2;
                            if (i === 2) return grad3;
                            return '#AEB8C7';
                        }),
                        borderRadius: 999,
                        borderSkipped: false,
                        barThickness: 8
                    }]
                },
                options: {
                    maintainAspectRatio: false,
                    indexAxis: 'y',
                    layout: {
                        padding: {
                            top: 4,
                            bottom: 4,
                            left: 4,
                            right: 32
                        }
                    },
                    plugins: {
                        legend: {
                            display: false
                        },
                        tooltip: {
                            backgroundColor: '#FFFFFF',
                            titleColor: '#111827',
                            bodyColor: '#111827',
                            borderColor: '#E5E7EB',
                            borderWidth: 1,
                            padding: 10,
                            callbacks: {
                                label: function(context) {
                                    const idx = context.dataIndex;
                                    return `${labelsOc[idx]}: ${valuesOc[idx]}`;
                                }
                            }
                        },
                        datalabels: {
                            color: '#111827',
                            anchor: 'end',
                            align: 'right',
                            offset: 8,
                            clamp: true,
                            clip: false,
                            font: {
                                weight: '800',
                                size: 8
                            },
                            formatter: (value) => value
                        }
                    },
                    scales: {
                        x: {
                            beginAtZero: true,
                            display: false,
                            grid: {
                                display: false
                            },
                            border: {
                                display: false
                            }
                        },
                        y: {
                            grid: {
                                display: false
                            },
                            border: {
                                display: false
                            },
                            ticks: {
                                color: '#334155',
                                font: {
                                    size: 8,
                                    weight: '600'
                                }
                            }
                        }
                    },
                    onClick: (e, els) => {
                        if (!els.length) {
                            __selectedOcorr = null;
                        } else {
                            const val = labelsOc[els[0].index];
                            __selectedOcorr = (norm(__selectedOcorr) === norm(val)) ? null : val;
                        }

                        pageOc = 1;
                        pageMo = 1;
                        applyFilters();
                    }
                }
            });
        }

        function toggleCulpFilter(label) {
            const normalized = norm(label);
            window.__culpFilter = (window.__culpFilter === normalized) ? null : normalized;
            pageOc = 1;
            pageMo = 1;
            applyFilters();
        }

        document.addEventListener("DOMContentLoaded", () => {
            document.querySelectorAll('.kpi-box[data-kpi]').forEach(b => {
                b.addEventListener('click', () => {
                    __topFilter = (__topFilter === b.dataset.kpi) ? 'NONE' : b.dataset.kpi;
                    pageOc = 1;
                    pageMo = 1;
                    applyFilters();
                });
            });

            document.addEventListener('click', (e) => {
                const btn = document.getElementById('status-btn');
                const dd = document.getElementById('status-dropdown');
                const chevron = document.getElementById('status-chevron');

                if (dd && btn && !dd.contains(e.target) && !btn.contains(e.target)) {
                    dd.classList.remove('open');
                    chevron.style.transform = 'rotate(0deg)';
                }
            });

            updateStatusLabel();

            google.script.run
                .withSuccessHandler(res => {
                    const rawData = Array.isArray(res ? .data) ? res.data : [];
                    const tempData = mapServerRows(rawData);

                    const dates = [...new Set(tempData.map(r => r.DATA).filter(Boolean))].sort();
                    if (dates.length > 0) {
                        document.getElementById('filter-data').value = dates[dates.length - 1];
                    }

                    loadDataFromServer(false);
                })
                .withFailureHandler(err => {
                    console.error('Erro ao buscar datas iniciais', err);
                    loadDataFromServer(false);
                })
                .getSheetData('');
        });
    </script>
</body>

</html>
