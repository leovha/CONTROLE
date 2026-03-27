<!DOCTYPE html>
<html lang="PT-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>EURO DIESEL - V002.4 MASTER</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons+Outlined" rel="stylesheet">
    <style>
        :root {
            --BG: #0D1117; --SIDE: #161B22; --CARD: #1C2128; --PRIMARY: #2F81F7;
            --SUCCESS: #238636; --DANGER: #F85149; --TEXT: #C9D1D9; --BORDER: #30363D;
            --PURPLE: #8957E5; --WARNING: #D29922;
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; text-transform: uppercase; }
        body { font-family: 'Segoe UI', Impact, sans-serif; margin: 0; background: var(--BG); color: var(--TEXT); display: flex; height: 100vh; overflow: hidden; }
        
        /* ESTRUTURA RESPONSIVA AUTO */
        .SIDEBAR { width: 260px; background: var(--SIDE); border-right: 1px solid var(--BORDER); display: flex; flex-direction: column; padding: 20px; flex-shrink: 0; transition: 0.3s; }
        .MAIN { flex: 1; overflow-y: auto; padding: 30px; position: relative; }
        .STATS-GRID { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-bottom: 25px; }
        
        @media (max-width: 800px) {
            body { flex-direction: column; }
            .SIDEBAR { 
                width: 100%; height: 70px; flex-direction: row; border-right: none; border-top: 1px solid var(--BORDER);
                position: fixed; bottom: 0; left: 0; padding: 0; z-index: 1000; justify-content: space-around; align-items: center;
            }
            .SIDEBAR div:first-child, .SIDEBAR .NAV-TEXT, .SIDEBAR .ADMIN-LABEL { display: none; }
            .SIDEBAR .NAV-ITEM { flex-direction: column; margin: 0; padding: 10px; flex: 1; justify-content: center; }
            .MAIN { padding: 15px 15px 90px 15px; }
            .STATS-GRID { grid-template-columns: 1fr; gap: 10px; }
        }

        .STAT-CARD { background: var(--CARD); padding: 15px; border-radius: 12px; border: 1px solid var(--BORDER); }
        .NAV-ITEM { display: flex; align-items: center; padding: 12px; border-radius: 10px; cursor: pointer; color: #8B949E; font-weight: bold; font-size: 0.8rem; }
        .NAV-ITEM.ACTIVE, .NAV-ITEM:hover { background: #21262D; color: WHITE; }
        
        .PROGRESS-BG { background: #30363D; height: 12px; border-radius: 6px; margin: 10px 0; overflow: hidden; }
        .PROGRESS-FILL { height: 100%; width: 0%; transition: 1s ease; }
        
        .BTN-MASTER { 
            background: var(--PRIMARY); color: WHITE; border: none; border-radius: 8px; 
            padding: 15px; font-weight: 900; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 10px; width: 100%;
        }

        .TABLE-AREA { background: var(--CARD); border-radius: 12px; border: 1px solid var(--BORDER); overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; min-width: 500px; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid var(--BORDER); font-size: 0.8rem; }
        
        .MODAL { position: fixed; inset: 0; background: rgba(0,0,0,0.9); display: flex; justify-content: center; align-items: center; z-index: 2000; padding: 20px; }
        .MODAL-CONTENT { background: var(--CARD); padding: 30px; border-radius: 20px; width: 100%; max-width: 400px; border: 1px solid var(--BORDER); }
        
        input, select { width: 100%; padding: 15px; background: #0D1117; border: 1px solid var(--BORDER); border-radius: 8px; color: WHITE; margin-bottom: 15px; font-size: 16px; font-weight: bold; }
        .HIDDEN { display: none !important; }
    </style>
</head>
<body>

    <nav id="APP-SIDE" class="SIDEBAR HIDDEN">
        <div style="font-size: 1.4rem; font-weight: 900; color: var(--PRIMARY); margin-bottom: 25px; text-align: center;">EURO DIESEL</div>
        
        <div class="NAV-ITEM ACTIVE" onclick="carregarVisaoPropria()"><span class="material-icons-outlined">dashboard</span> <div class="NAV-TEXT">MEU PAINEL</div></div>
        
        <div id="MASTER-TOOLS" class="HIDDEN">
            <div class="ADMIN-LABEL" style="font-size: 0.65rem; color: #555; margin: 15px 0 5px 10px;">ADMINISTRAÇÃO MASTER</div>
            <div class="NAV-ITEM" style="color: var(--PURPLE)" onclick="carregarVisaoGlobal()"><span class="material-icons-outlined">analytics</span> <div class="NAV-TEXT">MÉTRICAS GERAIS</div></div>
            <div class="NAV-ITEM" onclick="abrirGeradorToken()"><span class="material-icons-outlined">vpn_key</span> <div class="NAV-TEXT">GERAR TOKENS</div></div>
            <div id="USERS-LIST-NAV"></div> </div>

        <div style="margin-top: auto; width: 100%;">
            <div class="NAV-ITEM" onclick="openModal('MODAL-PERFIL')"><span class="material-icons-outlined">person_outline</span> <div class="NAV-TEXT">EDITAR PERFIL</div></div>
            <div class="NAV-ITEM" style="color: var(--DANGER)" onclick="location.reload()"><span class="material-icons-outlined">logout</span> <div class="NAV-TEXT">SAIR</div></div>
        </div>
    </nav>

    <main id="APP-MAIN" class="MAIN HIDDEN">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <div>
                <h1 id="V-TITLE" style="margin:0; font-size: 1.3rem;">DASHBOARD</h1>
                <button id="BTN-EDIT-META" class="HIDDEN" style="background:var(--WARNING); border:none; padding:4px 8px; font-size:0.6rem; cursor:pointer; margin-top:5px; font-weight:bold; border-radius:4px;" onclick="abrirAjusteMeta()">AJUSTAR METAS</button>
            </div>
            <button class="BTN-MASTER" style="width: auto; padding: 10px 20px;" onclick="openModal('MODAL-ADD')"> + LANÇAR</button>
        </div>

        <div class="STATS-GRID">
            <div class="STAT-CARD"><b>GERAL</b> <span id="TXT-G">0%</span><div class="PROGRESS-BG"><div id="BAR-G" class="PROGRESS-FILL" style="background: linear-gradient(90deg, var(--PRIMARY), var(--PURPLE))"></div></div></div>
            <div class="STAT-CARD"><b>SERVIÇOS</b> <span id="TXT-S">0%</span><div class="PROGRESS-BG"><div id="BAR-S" class="PROGRESS-FILL" style="background: var(--PRIMARY)"></div></div></div>
            <div class="STAT-CARD"><b>PEÇAS</b> <span id="TXT-P">0%</span><div class="PROGRESS-BG"><div id="BAR-P" class="PROGRESS-FILL" style="background: var(--SUCCESS)"></div></div></div>
        </div>

        <div class="TABLE-AREA">
            <table>
                <thead><tr><th>TIPO</th><th>DESCRIÇÃO</th><th>VALOR</th><th>AÇÃO</th></tr></thead>
                <tbody id="MAIN-TABLE"></tbody>
            </table>
        </div>
    </main>

    <div id="AUTH-SCREEN" class="MODAL" style="background: var(--BG)">
        <div id="LOGIN-BOX" class="MODAL-CONTENT">
            <h1 style="color: var(--PRIMARY); text-align:center; margin-bottom: 30px;">EURO DIESEL</h1>
            <div onkeydown="if(event.keyCode===13) tentarLogin()">
                <input type="text" id="U-LOGIN" placeholder="USUÁRIO">
                <input type="password" id="P-LOGIN" placeholder="SENHA">
                <button id="BTN-ENTRAR" class="BTN-MASTER" onclick="tentarLogin()">ENTRAR</button>
            </div>
            <hr style="border: 0; border-top: 1px solid var(--BORDER); margin: 25px 0;">
            <button class="BTN-MASTER" style="background: var(--SUCCESS)" onclick="switchAuth('REG-BOX')">CADASTRE-SE</button>
        </div>

        <div id="REG-BOX" class="MODAL-CONTENT HIDDEN">
            <h2 style="text-align:center; color: var(--SUCCESS);">CRIAR CONTA</h2>
            <div onkeydown="if(event.keyCode===13) tentarRegistro()">
                <input type="text" id="U-REG" placeholder="NOME DO COLABORADOR">
                <input type="password" id="P-REG" placeholder="CRIE SUA SENHA">
                <input type="text" id="T-REG" placeholder="DIGITE O TOKEN">
                <button class="BTN-MASTER" style="background: var(--SUCCESS)" onclick="tentarRegistro()">FINALIZAR CADASTRO</button>
            </div>
            <button class="BTN-MASTER" style="background: #25D366; margin-top: 15px;" onclick="window.open('https://wa.me/SEUNUMERO?text=SOLICITO%20TOKEN%20EURO%20DIESEL')">PEDIR TOKEN (WHATSAPP)</button>
            <button style="width:100%; background:none; border:none; color:var(--DANGER); margin-top:15px; cursor:pointer; font-weight:bold;" onclick="switchAuth('LOGIN-BOX')">VOLTAR AO INÍCIO</button>
        </div>
    </div>

    <div id="MODAL-TOKEN" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT">
            <h3 id="META-TITLE">GERENCIAR METAS/TOKENS</h3>
            <label>META SERVIÇOS (R$)</label> <select id="M-S"></select>
            <label>META PEÇAS (R$)</label> <select id="M-P"></select>
            <label>META GERAL (R$)</label> <select id="M-G"></select>
            
            <div id="AREA-TOKEN-NOVA"><button class="BTN-MASTER" onclick="gerarToken()">GERAR NOVO TOKEN</button></div>
            <div id="AREA-AJUSTE-META" class="HIDDEN"><button class="BTN-MASTER" style="background: var(--SUCCESS)" onclick="salvarMetasExistentes()">ATUALIZAR METAS</button></div>
            
            <div id="TK-BOX" class="HIDDEN" style="text-align:center; margin-top:15px; border: 2px dashed var(--PRIMARY); padding: 10px;">
                <DIV ID="TK-VAL" style="font-size: 1.5rem; font-weight: 900; color: var(--PRIMARY);"></DIV>
            </div>
            <button class="BTN-MASTER" style="background:none; margin-top:10px;" onclick="closeModal('MODAL-TOKEN')">FECHAR</button>
        </div>
    </div>

    <div id="MODAL-PERFIL" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT">
            <h3>EDITAR MEU PERFIL</h3>
            <input type="text" id="SET-USER" placeholder="NOME">
            <input type="password" id="SET-PASS" placeholder="SENHA">
            <button class="BTN-MASTER" onclick="salvarPerfil()">ATUALIZAR DADOS</button>
            <button style="width:100%; background:none; border:none; color:WHITE; margin-top:10px; cursor:pointer;" onclick="closeModal('MODAL-PERFIL')">CANCELAR</button>
        </div>
    </div>

    <div id="MODAL-ADD" class="MODAL HIDDEN">
        <div class="MODAL-CONTENT" onkeydown="if(event.keyCode===13) salvarItem()">
            <h3>REGISTRAR VENDA $$$</h3>
            <select id="IN-TIPO"><option value="SERVIÇO">SERVIÇO</option><option value="PEÇA">PEÇA</option></select>
            <input type="text" id="IN-DESC" placeholder="DESCRIÇÃO DO ITEM">
            <input type="number" id="IN-VAL" placeholder="VALOR R$" inputmode="decimal">
            <button class="BTN-MASTER" style="background: var(--SUCCESS);" onclick="salvarItem()">SALVAR E LANÇAR</button>
            <button style="width:100%; background:none; border:none; color:WHITE; margin-top:10px; cursor:pointer;" onclick="closeModal('MODAL-ADD')">CANCELAR</button>
        </div>
    </div>

<script>
    let userLogado = ""; let alvoView = "";
    const cashSound = new Audio('https://www.myinstants.com/media/sounds/cash-register-purchase.mp3');

    function tocarSom() { cashSound.currentTime = 0; cashSound.play().catch(() => {}); }

    // POPULAR SELEÇÃO DE METAS
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
        tocarSom(); // REGRA DO TOQUE

        setTimeout(() => {
            const u = document.getElementById('U-LOGIN').value.toUpperCase().trim();
            const p = document.getElementById('P-LOGIN').value;
            const users = JSON.parse(localStorage.getItem('euro_users') || "[]");

            if(u === "LEANDRO" && p === "12344321") logar("LEANDRO");
            else {
                const f = users.find(x => x.u === u && x.p === p);
                if(f) logar(u); 
                else { alert("ACESSO NEGADO!"); btn.innerText = "ENTRAR"; }
            }
        }, 800);
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
        dados.forEach((item, idx) => {
            const v = Number(item.val);
            tb.innerHTML += `<tr><td><b>${item.tipo}</b></td><td>${item.desc} ${item.dono ? '<br><small style="color:var(--PRIMARY)">'+item.dono+'</small>' : ''}</td><td>R$ ${v.toFixed(2)}</td>
            <td><button onclick="del(${idx})" style="background:none; border:none; color:var(--DANGER); cursor:pointer;"><span class="material-icons-outlined">delete</span></button></td></tr>`;
            g += v; if(item.tipo === 'SERVIÇO') s += v; else p += v;
        });

        const updateBar = (id, v, m) => {
            const perc = Math.min((v / m) * 100, 100);
            document.getElementById('BAR-'+id).style.width = perc + "%";
            document.getElementById('TXT-'+id).innerText = Math.round(perc) + "%";
        };
        updateBar('S', s, meta.s); updateBar('P', p, meta.p); updateBar('G', g, meta.g);
    }

    function gerarToken() {
        const tk = "EURO-" + Math.random().toString(36).substring(2,7).toUpperCase();
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
        if(!tks[t]) return alert("TOKEN INVÁLIDO OU JÁ USADO!");
        
        let users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        users.push({u, p, metas: tks[t]});
        localStorage.setItem('euro_users', JSON.stringify(users));
        delete tks[t]; localStorage.setItem('euro_tks', JSON.stringify(tks));
        alert("CONTA CRIADA! AGORA FAÇA O LOGUIN."); switchAuth('LOGIN-BOX');
    }

    function atualizarListaMaster() {
        const nav = document.getElementById('USERS-LIST-NAV');
        const users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        nav.innerHTML = "";
        users.forEach(u => nav.innerHTML += `<div class="NAV-ITEM" onclick="carregarVisaoUser('${u.u}')"><span class="material-icons-outlined">person</span> <div class="NAV-TEXT">${u.u}</div></div>`);
    }

    function salvarPerfil() {
        const novoU = document.getElementById('SET-USER').value.toUpperCase().trim();
        const novaP = document.getElementById('SET-PASS').value;
        let users = JSON.parse(localStorage.getItem('euro_users') || "[]");
        users = users.map(u => u.u === userLogado ? { ...u, u: novoU, p: novaP } : u);
        localStorage.setItem('euro_users', JSON.stringify(users));
        alert("PERFIL ATUALIZADO!"); location.reload();
    }

    function salvarItem() {
        const d = document.getElementById('IN-DESC').value.toUpperCase();
        const v = document.getElementById('IN-VAL').value;
        if(!d || !v) return;
        tocarSom();
        let storage = JSON.parse(localStorage.getItem('ed_data_'+alvoView) || "[]");
        storage.push({tipo: document.getElementById('IN-TIPO').value, desc: d, val: v});
        localStorage.setItem('ed_data_'+alvoView, JSON.stringify(storage));
        closeModal('MODAL-ADD'); renderDashboard();
    }

    function del(idx) {
        let key = 'ed_data_' + alvoView;
        let d = JSON.parse(localStorage.getItem(key) || "[]");
        d.splice(idx, 1);
        localStorage.setItem(key, JSON.stringify(d));
        renderDashboard();
    }

    // NAVEGAÇÃO
    function carregarVisaoGlobal() { alvoView = "GLOBAL"; document.getElementById('V-TITLE').innerText = "VISTA TOTAL EMPRESA"; renderDashboard(); }
    function carregarVisaoPropria() { alvoView = userLogado; document.getElementById('V-TITLE').innerText = "MEU PAINEL"; renderDashboard(); }
    function carregarVisaoUser(n) { alvoView = n; document.getElementById('V-TITLE').innerText = "VISTA COLABORADOR: " + n; renderDashboard(); }
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
