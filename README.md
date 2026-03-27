<!DOCTYPE html>
<html lang="PT-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>EURO DIESEL - GESTÃO</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons+Outlined" rel="stylesheet">
    <style>
        :root {
            --BG: #0D1117; --SIDE: #161B22; --CARD: #1C2128; --PRIMARY: #2F81F7;
            --SUCCESS: #238636; --DANGER: #F85149; --TEXT: #C9D1D9; --BORDER: #30363D;
            --PURPLE: #8957E5; --WARNING: #D29922;
        }
        
        /* REGRA: TUDO EM CAIXA ALTA E SEM ZOOM ACIDENTAL */
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; text-transform: uppercase; touch-action: manipulation; }
        
        body { 
            font-family: 'Segoe UI', system-ui, sans-serif; 
            margin: 0; background: var(--BG); color: var(--TEXT); 
            display: flex; height: 100vh; overflow: hidden; 
        }

        /* SIDEBAR INTELIGENTE (PC) */
        .SIDEBAR { 
            width: 280px; background: var(--SIDE); border-right: 1px solid var(--BORDER); 
            display: flex; flex-direction: column; padding: 25px; flex-shrink: 0; 
        }

        /* CONTEÚDO PRINCIPAL */
        .MAIN { flex: 1; overflow-y: auto; padding: 30px; -webkit-overflow-scrolling: touch; }

        /* GRADE DE METAS (ADAPTÁVEL) */
        .STATS-GRID { 
            display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); 
            gap: 20px; margin-bottom: 30px; 
        }

        /* REGRA PARA CELULAR (MOBILE FIRST) */
        @media (max-width: 800px) {
            body { flex-direction: column; }
            .SIDEBAR { 
                width: 100%; height: 75px; flex-direction: row; border-right: none; 
                border-top: 1px solid var(--BORDER); position: fixed; bottom: 0; left: 0; 
                padding: 0; z-index: 1000; justify-content: space-around; align-items: center;
                background: var(--SIDE);
            }
            .SIDEBAR div:first-child, .SIDEBAR .NAV-TEXT, .SIDEBAR .ADMIN-LABEL { display: none; }
            .SIDEBAR .NAV-ITEM { flex-direction: column; margin: 0; padding: 5px; flex: 1; height: 100%; justify-content: center; }
            .SIDEBAR .NAV-ITEM span { font-size: 26px; margin: 0; }
            
            .MAIN { padding: 15px 15px 100px 15px; }
            .STATS-GRID { grid-template-columns: 1fr; gap: 12px; }
            h1 { font-size: 1.2rem !important; }
        }

        .STAT-CARD { background: var(--CARD); padding: 20px; border-radius: 15px; border: 1px solid var(--BORDER); box-shadow: 0 4px 10px rgba(0,0,0,0.2); }
        
        .NAV-ITEM { display: flex; align-items: center; padding: 14px; border-radius: 12px; cursor: pointer; color: #8B949E; transition: 0.2s; }
        .NAV-ITEM.ACTIVE, .NAV-ITEM:hover { background: #21262D; color: WHITE; }
        .NAV-ITEM span { margin-right: 15px; }

        .PROGRESS-BG { background: #30363D; height: 14px; border-radius: 7px; margin: 12px 0; overflow: hidden; }
        .PROGRESS-FILL { height: 100%; width: 0%; transition: 1.5s cubic-bezier(0.1, 0.7, 1.0, 0.1); }

        .BTN-MASTER { 
            background: var(--PRIMARY); color: WHITE; border: none; border-radius: 10px; 
            padding: 18px; font-weight: 900; cursor: pointer; display: flex; 
            align-items: center; justify-content: center; gap: 10px; width: 100%;
            font-size: 14px;
        }

        .TABLE-AREA { background: var(--CARD); border-radius: 15px; border: 1px solid var(--BORDER); overflow-x: auto; margin-top: 10px; }
        table { width: 100%; border-collapse: collapse; min-width: 450px; }
        th { background: rgba(255,255,255,0.03); color: var(--PRIMARY); font-size: 0.7rem; }
        th, td { padding: 15px; text-align: left; border-bottom: 1px solid var(--BORDER); }

        /* MODAIS E TELAS */
        .MODAL { position: fixed; inset: 0; background: var(--BG); display: flex; justify-content: center; align-items: center; z-index: 2000; padding: 20px; }
        .MODAL-CONTENT { background: var(--CARD); padding: 35px; border-radius: 25px; width: 100%; max-width: 420px; border: 1px solid var(--BORDER); box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        
        input, select { width: 100%; padding: 18px; background: #0D1117; border: 1px solid var(--BORDER); border-radius: 12px; color: WHITE; margin-bottom: 15px; font-size: 16px; font-weight: bold; outline: none; }
        input:focus { border-color: var(--PRIMARY); }
        .HIDDEN { display: none !important; }

        .STATUS-TAG { padding: 4px 8px; border-radius: 4px; font-size: 0.6rem; font-weight: 900; }
    </style>
</head>
<body>

    <nav id="APP-SIDE" class="SIDEBAR HIDDEN">
        <div style="font-size: 1.6rem; font-weight: 900; color: var(--PRIMARY); margin-bottom: 30px; text-align: center; letter-spacing: -1px;">EURO DIESEL</div>
        
        <div class="NAV-ITEM ACTIVE" onclick="carregarVisaoPropria()"><span class="material-icons-outlined">dashboard</span> <div class="NAV-TEXT">MEU PAINEL</div></div>
        
        <div id="MASTER-TOOLS" class="HIDDEN">
            <div class="ADMIN-LABEL" style="font-size: 0.7rem; color: #555; margin: 20px 0 10px 10px; font-weight: 900;">ADMIN MASTER</div>
            <div class="NAV-ITEM" style="color: var(--PURPLE)" onclick="carregarVisaoGlobal()"><span class="material-icons-outlined">analytics</span> <div class="NAV-TEXT">MÉTRICAS TOTAIS</div></div>
            <div class="NAV-ITEM" onclick="abrirGeradorToken()"><span class="material-icons-outlined">vpn_key</span> <div class="NAV-TEXT">GERAR TOKENS</div></div>
            <div id="USERS-LIST-NAV"></div>
        </div>

        <div style="margin-top: auto; width: 100%;">
            <div class="NAV-ITEM" onclick="openModal('MODAL-PERFIL')"><span class="material-icons-outlined">manage_accounts</span> <div class="NAV-TEXT">EDITAR PERFIL</div></div>
            <div class="NAV-ITEM" style="color: var(--DANGER)" onclick="location.reload()"><span class="material-icons-outlined">power_settings_new</span> <div class="NAV-TEXT">SAIR</div></div>
        </div>
    </nav>

    <main id="APP-MAIN" class="MAIN HIDDEN">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px;">
            <div>
                <h1 id="V-TITLE" style="margin:0; font-size: 1.6rem; letter-spacing: -1px;">DASHBOARD</h1>
                <button id="BTN-EDIT-META" class="HIDDEN" style="background:var(--WARNING); border:none; padding:5px 10px; font-size:0.6rem; cursor:pointer; margin-top:8px; font-weight:900; border-radius:6px; color:black;" onclick="abrirAjusteMeta()">AJUSTAR METAS</button>
            </div>
            <button class="BTN-MASTER" style="width: auto; padding: 12px 25px; background: var(--PRIMARY);" onclick="openModal('MODAL-ADD')"> + LANÇAR</button>
        </div>

        <div class="STATS-GRID">
            <div class="STAT-CARD">
                <div style="display:flex; justify-content:space-between;"><b>META GERAL</b> <span id="TXT-G" style="color:var(--PURPLE)">0%</span></div>
                <div class="PROGRESS-BG"><div id="BAR-G" class="PROGRESS-FILL" style="background: linear-gradient(90deg, var(--PRIMARY), var(--PURPLE))"></div></div>
                <small id="F-G" style="color:#555"></small>
            </div>
            <div class="STAT-CARD">
                <div style="display:flex; justify-content:space-between;"><b>SERVIÇOS</b> <span id="TXT-S" style="color:var(--PRIMARY)">0%</span></div>
                <div class="PROGRESS-BG"><div id="BAR-S" class="PROGRESS-FILL" style="background: var(--PRIMARY)"></div></div>
                <small id="F-S" style="color:#555"></small>
            </div>
            <div class="STAT-CARD">
                <div style="display:flex; justify-content:space-between;"><b>PEÇAS</b> <span id="TXT-P" style="color:var(--SUCCESS)">0%</span></div>
                <div class="PROGRESS-BG"><div id="BAR-P" class="PROGRESS-FILL" style="background: var(--SUCCESS)"></div></div>
                <small id="F-P" style="color:#555"></small>
            </div>
        </div>

        <div class="TABLE-AREA">
            <table>
                <thead><tr><th>TIPO</th><th>DESCRIÇÃO</th><th>VALOR</th><th>AÇÃO</th></tr></thead>
                <tbody id="MAIN-TABLE"></tbody>
            </table>
        </div>
    </main>

    <div id="AUTH-SCREEN" class="MODAL">
        <div id="LOGIN-BOX" class="MODAL-CONTENT">
            <h1 style="color: var(--PRIMARY); text-align:center; margin-bottom: 35px; font-size: 2rem;">EURO DIESEL</h1>
            <div onkeydown="if(event.keyCode===13) tentarLogin()">
                <input type="text" id="U-LOGIN" placeholder="USUÁRIO" autocomplete="off">
                <input type="password" id="P-LOGIN" placeholder="SENHA">
                <button id="BTN-ENTRAR" class="BTN-MASTER" onclick="tentarLogin()">ENTRAR NO SISTEMA</button>
            </div>
            <p style="text-align:center; margin-top:25px; font-size: 0.8rem; color:#555;">OU</p>
            <button class="BTN-MASTER" style="background: transparent; border: 1px solid var(--SUCCESS); color: var(--SUCCESS);" onclick="switchAuth('REG-BOX')">CADASTRE-SE</button>
        </div>

        <div id="REG-BOX" class="MODAL-CONTENT HIDDEN">
            <h2 style="text-align:center; color: var(--SUCCESS);">NOVA CONTA</h2>
            <div onkeydown="if(event.keyCode===13) tentarRegistro()">
                <input type="text" id="U-REG" placeholder="NOME DO COLABORADOR">
                <input type="password" id="P-REG" placeholder="CRIE UMA SENHA">
                <input type="text" id="T-REG" placeholder="TOKEN DE ACESSO">
                <button class="BTN-MASTER" style="background: var(--SUCCESS)" onclick="tentarRegistro()">CRIAR MEU ACESSO</button>
            </div>
            <button class="BTN-MASTER" style="background: #25D366; margin-top: 15px;" onclick="window.open('https://wa.me/SEUNUMERO?text=SOLICITO%20TOKEN%20EURO%20DIESEL')">PEDIR TOKEN (WHATSAPP)</button>
            <button style="width:100%; background:none; border:none; color:var(--DANGER); margin-top:20px; cursor:pointer; font-weight:900;" onclick="switchAuth('LOGIN-BOX')">VOLTAR AO INÍCIO</button>
        </div>
    </div>

    <div id="MODAL-TOKEN" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT">
            <h3 id="META-TITLE">METAS & TOKENS</h3>
            <label>META SERVIÇOS</label> <select id="M-S"></select>
            <label>META PEÇAS</label> <select id="M-P"></select>
            <label>META GERAL</label> <select id="M-G"></select>
            
            <div id="AREA-TOKEN-NOVA"><button class="BTN-MASTER" onclick="gerarToken()">GERAR NOVO TOKEN</button></div>
            <div id="AREA-AJUSTE-META" class="HIDDEN"><button class="BTN-MASTER" style="background: var(--SUCCESS)" onclick="salvarMetasExistentes()">ATUALIZAR TUDO</button></div>
            
            <div id="TK-BOX" class="HIDDEN" style="text-align:center; margin-top:20px; border: 3px dashed var(--PRIMARY); padding: 15px; border-radius: 12px;">
                <DIV ID="TK-VAL" style="font-size: 1.8rem; font-weight: 900; color: var(--PRIMARY);"></DIV>
            </div>
            <button class="BTN-MASTER" style="background:none; margin-top:15px; color:#555;" onclick="closeModal('MODAL-TOKEN')">FECHAR</button>
        </div>
    </div>

    <div id="MODAL-PERFIL" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT">
            <h3>EDITAR PERFIL</h3>
            <input type="text" id="SET-USER" placeholder="NOME">
            <input type="password" id="SET-PASS" placeholder="SENHA">
            <button class="BTN-MASTER" onclick="salvarPerfil()">SALVAR ALTERAÇÕES</button>
            <button style="width:100%; background:none; border:none; color:var(--DANGER); margin-top:15px; cursor:pointer; font-weight:900;" onclick="closeModal('MODAL-PERFIL')">CANCELAR</button>
        </div>
    </div>

    <div id="MODAL-ADD" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT" onkeydown="if(event.keyCode===13) salvarItem()">
            <h3>NOVO LANÇAMENTO $$$</h3>
            <select id="IN-TIPO"><option value="SERVIÇO">SERVIÇO</option><option value="PEÇA">PEÇA</option></select>
            <input type="text" id="IN-DESC" placeholder="DESCRIÇÃO">
            <input type="number" id="IN-VAL" placeholder="VALOR R$" inputmode="decimal">
            <button class="BTN-MASTER" style="background: var(--SUCCESS);" onclick="salvarItem()">CONFIRMAR VENDA</button>
            <button style="width:100%; background:none; border:none; color:#AAA; margin-top:15px; cursor:pointer; font-weight:900;" onclick="closeModal('MODAL-ADD')">SAIR</button>
        </div>
    </div>

<script>
    let userLogado = ""; let alvoView = "";
    const cashSound = new Audio('https://www.myinstants.com/media/sounds/cash-register-purchase.mp3');

    function tocarSom() { cashSound.currentTime = 0; cashSound.play().catch(() => {}); }

    function popularMetas() {
        const ids = ['M-S', 'M-P', 'M-G'];
        ids.forEach(id => {
            const el = document.getElementById(id); el.innerHTML = '';
            for(let i=5000; i<=100000; i+=5000) el.innerHTML += `<option value="${i}">R$ ${i}</option>`;
        });
    }
    popularMetas();

    function tentarLogin() {
        const btn = document.getElementById('BTN-ENTRAR');
        btn.innerText = "CARREGANDO...";
        tocarSom();

        setTimeout(() => {
            const u = document.getElementById('U-LOGIN').value.toUpperCase().trim();
            const p = document.getElementById('P-LOGIN').value;
            const users = JSON.parse(localStorage.getItem('euro_users') || "[]");

            if(u === "LEANDRO" && p === "12344321") logar("LEANDRO");
            else {
                const f = users.find(x => x.u === u && x.p === p);
                if(f) logar(u); 
                else { alert("ACESSO BLOQUEADO: DADOS INCORRETOS!"); btn.innerText = "ENTRAR NO SISTEMA"; }
            }
        }, 600);
    }

    function logar(nome) {
        userLogado = nome; alvoView = nome;
        document.getElementById('AUTH-SCREEN').classList.add('HIDDEN');
        document.getElementById('APP-SIDE').classList.remove('HIDDEN');
        document.getElementById('APP-MAIN').classList.remove('HIDDEN');
        document.getElementById('SET-USER').value = userLogado;
        if(nome === "LEANDRO") {
            document.getElementById('MASTER-TOOLS').classList.remove('HIDDEN');
            document.getElementById('BTN-EDIT-META').classList.remove('HIDDEN');
            atualizarListaMaster();
        }
        renderDashboard();
    }

    function renderDashboard() {
        let dados = []; let meta = {s:10000, p:10000, g:20000};
        const allUsers = JSON.parse(localStorage.getItem('euro_users') || "[]");
        
        if(alvoView === "GLOBAL") {
            meta = {s:0, p:0, g:0};
            allUsers.forEach(u => {
                const d = JSON.parse(localStorage.getItem('ed_data_' + u.u) || "[]");
                dados = [...dados, ...d.map(i => ({...i, dono: u.u}))];
                meta.s += Number(u.metas.s); meta.p += Number(u.metas.p); meta.g += Number(u.metas.g);
            });
        } else {
            dados = JSON.parse(localStorage.getItem('ed_data_' + alvoView) || "[]");
            const u = allUsers.find(x => x.u === alvoView) || {metas:meta};
            meta = u.metas;
        }

        let s=0, p=0, g=0; const tb = document.getElementById('MAIN-TABLE'); tb.innerHTML = "";
        dados.sort((a,b) => b.val - a.val).forEach((item, idx) => {
            const v = Number(item.val);
            tb.innerHTML += `<tr>
                <td><span class="STATUS-TAG" style="background:${item.tipo==='SERVIÇO'?'#2f81f7':'#238636'}">${item.tipo}</span></td>
                <td>${item.desc} ${item.dono ? '<br><small style="color:var(--PRIMARY)">'+item.dono+'</small>' : ''}</td>
                <td>R$ ${v.toLocaleString()}</td>
                <td><button onclick="del(${idx})" style="background:none; border:none; color:var(--DANGER); cursor:pointer;"><span class="material-icons-outlined">delete</span></button></td>
            </tr>`;
            g += v; if(item.tipo === 'SERVIÇO') s += v; else p += v;
        });

        const updateBar = (id, v, m) => {
            const perc = Math.min((v / m) * 100, 100);
            document.getElementById('BAR-'+id).style.width = perc + "%";
            document.getElementById('TXT-'+id).innerText = Math.round(perc) + "%";
            document.getElementById('F-'+id).innerText = v >= m ? "META OK!" : "FALTA R$ " + (m-v).toLocaleString();
        };
        updateBar('S', s, meta.s); updateBar('P', p, meta.p); updateBar('G', g, meta.g);
    }

    function gerarToken() {
        const tk = "EURO-" + Math.random().toString(36).substring(2,8).toUpperCase();
        let tks = JSON.parse(localStorage.getItem('euro_tks') || "{}");
        tks[tk] = { s: document.getElementById('M-S').value, p: document.getElementById('M-P').value, g: document.getElementById('M-G').value };
        localStorage.setItem('euro_tks', JSON.stringify(tks));
        document.getElementById('TK-VAL').innerText = tk; document.getElementById('TK-BOX').classList.remove('HIDDEN');
    }

    function tentarRegistro() {
        const u = document.getElementById('U-REG').value.toUpperCase().trim();
        const p = document.getElementById('P-REG').value;
        const t = document.getElementById('T-REG').value.toUpperCase().trim();
        let tks = JSON.parse(localStorage.getItem('euro_tks') || "{}");
        if(!tks[t]) return alert("ERRO: TOKEN INVÁLIDO OU EXPIRADO!");
        
        let users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        users.push({u, p, metas: tks[t]});
        localStorage.setItem('euro_users', JSON.stringify(users));
        delete tks[t]; localStorage.setItem('euro_tks', JSON.stringify(tks));
        alert("CONTA EURO DIESEL CRIADA COM SUCESSO!"); switchAuth('LOGIN-BOX');
    }

    function atualizarListaMaster() {
        const nav = document.getElementById('USERS-LIST-NAV');
        const users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        nav.innerHTML = "";
        users.forEach(u => nav.innerHTML += `<div class="NAV-ITEM" onclick="carregarVisaoUser('${u.u}')"><span class="material-icons-outlined">person_search</span> <div class="NAV-TEXT">${u.u}</div></div>`);
    }

    function salvarPerfil() {
        const novoU = document.getElementById('SET-USER').value.toUpperCase().trim();
        const novaP = document.getElementById('SET-PASS').value;
        let users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        users = users.map(u => u.u === userLogado ? { ...u, u: novoU, p: novaP } : u);
        localStorage.setItem('euro_users', JSON.stringify(users));
        alert("PERFIL ATUALIZADO COM SUCESSO!"); location.reload();
    }

    function salvarItem() {
        const d = document.getElementById('IN-DESC').value.toUpperCase();
        const v = document.getElementById('IN-VAL').value;
        if(!d || !v) return alert("PREENCHA OS DADOS!");
        tocarSom();
        let storage = JSON.parse(localStorage.getItem('ed_data_'+alvoView) || "[]");
        storage.push({tipo: document.getElementById('IN-TIPO').value, desc: d, val: v});
        localStorage.setItem('ed_data_'+alvoView, JSON.stringify(storage));
        closeModal('MODAL-ADD'); renderDashboard();
    }

    function del(idx) {
        if(!confirm("CONFIRMA A EXCLUSÃO?")) return;
        let key = 'ed_data_' + alvoView;
        let d = JSON.parse(localStorage.getItem(key) || "[]");
        d.splice(idx, 1);
        localStorage.setItem(key, JSON.stringify(d));
        renderDashboard();
    }

    function carregarVisaoGlobal() { alvoView = "GLOBAL"; document.getElementById('V-TITLE').innerText = "VISTA TOTAL EMPRESA"; renderDashboard(); }
    function carregarVisaoPropria() { alvoView = userLogado; document.getElementById('V-TITLE').innerText = "MEU PAINEL"; renderDashboard(); }
    function carregarVisaoUser(n) { alvoView = n; document.getElementById('V-TITLE').innerText = "EQUIPE: " + n; renderDashboard(); }
    function openModal(id) { document.getElementById(id).classList.remove('HIDDEN'); }
    function closeModal(id) { document.getElementById(id).classList.add('HIDDEN'); }
    function switchAuth(b) { document.getElementById('LOGIN-BOX').classList.add('HIDDEN'); document.getElementById('REG-BOX').classList.add('HIDDEN'); document.getElementById(b).classList.remove('HIDDEN'); }
    function abrirGeradorToken() { openModal('MODAL-TOKEN'); document.getElementById('AREA-TOKEN-NOVA').classList.remove('HIDDEN'); document.getElementById('AREA-AJUSTE-META').classList.add('HIDDEN'); }
    function abrirAjusteMeta() { openModal('MODAL-TOKEN'); document.getElementById('AREA-TOKEN-NOVA').classList.add('HIDDEN'); document.getElementById('AREA-AJUSTE-META').classList.remove('HIDDEN'); }
    function salvarMetasExistentes() {
        let users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        users = users.map(u => { if(u.u === alvoView) u.metas = { s: document.getElementById('M-S').value, p: document.getElementById('M-P').value, g: document.getElementById('M-G').value }; return u; });
        localStorage.setItem('euro_users', JSON.stringify(users)); closeModal('MODAL-TOKEN'); renderDashboard();
    }
</script>
</body>
</html>
