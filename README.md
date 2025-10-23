<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
    <title>Registro de Ponto</title>
    <!-- Forçar HTTPS para funcionamento do GPS -->
    <script>
    if (location.protocol !== 'https:' && location.hostname !== 'localhost' && location.hostname !== '127.0.0.1') {
        location.replace(`https:${location.href.substring(location.protocol.length)}`);
    }
    </script>
    <style>
    :root{--bg1:#0d47a1;--bg2:#1976d2;--white:#fff;--accent:#00c853;}
    *{box-sizing:border-box}
    body{font-family:Segoe UI,Tahoma,Verdana,sans-serif;margin:0;padding:0;min-height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:flex-start;background:linear-gradient(180deg,var(--bg1),var(--bg2));color:var(--white);}
    .header{width:100%;padding:12px 16px;display:flex;align-items:center;gap:12px;position:fixed;top:0;left:0;z-index:1000;}
    .header h1{margin:0;font-size:1.2rem;font-weight:800;}
    #menuBtn{font-size:1.6rem;cursor:pointer;position:fixed;top:14px;left:12px;z-index:1010;}
    #menuOptions{position:fixed;top:56px;left:12px;width:260px;background:#fff;color:#111;flex-direction:column;padding:12px;border-radius:8px;box-shadow:0 6px 18px rgba(0,0,0,0.25);z-index:1005;display:none;transition:all 0.2s ease;}
    #menuOptions button{margin:6px 0;padding:8px 10px;border:none;border-radius:6px;background:#1976d2;color:#fff;font-weight:700;cursor:pointer;}
    main{margin-top:72px;width:100%;max-width:480px;display:flex;flex-direction:column;align-items:center;padding:12px;}
    .formCard{width:100%;background:rgba(255,255,255,0.06);padding:12px;border-radius:12px;backdrop-filter: blur(4px);box-shadow:0 8px 24px rgba(0,0,0,0.2);}
    .row{display:flex;gap:8px;width:100%;}
    .field{display:flex;flex-direction:column;margin-bottom:8px;}
    label{font-size:13px;font-weight:700;margin-bottom:6px;}
    input[type="text"], select, textarea {padding:10px;border-radius:8px;border:0;outline:none;font-size:14px;}
    textarea{resize:vertical;min-height:64px;}
    #relogio{font-size:1.5rem;font-weight:700;margin:8px 0 14px 0;text-align:center;}
    .botao-circular{width:160px;height:160px;border-radius:50%;display:flex;align-items:center;justify-content:center;background:linear-gradient(145deg,#00c853,#009624);box-shadow:0 8px 24px rgba(0,0,0,.3);font-weight:800;cursor:pointer;user-select:none;position:relative;margin:12px auto;}
    .mensagem-confirmacao{margin-top:10px;font-weight:600;color:#ffeb3b;text-align:center;min-height:20px;}
    .statusBar{margin-top:10px;font-size:13px;opacity:0.95;background:rgba(255,255,255,0.06);padding:8px;border-radius:8px;display:flex;align-items:center;gap:8px;justify-content:space-between;}
    .pendentes{font-weight:700;color:#ffd54f;}
    .thumbnail{width:48px;height:48px;border-radius:6px;object-fit:cover;margin-left:8px;border:1px solid rgba(255,255,255,0.12);}
    .warn{background:#ffcc80;color:#3e2723;padding:6px;border-radius:6px;margin-top:8px;font-weight:700;text-align:center;}
    .rodape{position:fixed;bottom:12px;font-size:12px;opacity:.9;display:flex;flex-direction:column;align-items:center;gap:6px;}
    .rodape button{padding:6px 10px;border-radius:6px;border:0;background:#fff;color:var(--bg1);font-weight:700;cursor:pointer;}

    /* Toast */
    #toastContainer{position:fixed;top:16px;right:16px;z-index:99999;display:flex;flex-direction:column;gap:8px;pointer-events:none;}
    .toast{min-width:220px;padding:10px 14px;border-radius:8px;color:#fff;font-weight:700;box-shadow:0 6px 20px rgba(0,0,0,0.25);pointer-events:auto;opacity:0;transform:translateY(-8px);transition:all .28s ease}
    .toast.show{opacity:1;transform:translateY(0);}
    .toast.success{background:linear-gradient(90deg,#2e7d32,#00c853);}
    .toast.info{background:linear-gradient(90deg,#1976d2,#42a5f5);}
    .toast.error{background:linear-gradient(90deg,#c62828,#ff7043);}

    /* Modal Câmera */
    #modalCamera{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:#000;z-index:3000;flex-direction:column;align-items:center;justify-content:center;}
    #videoCamera{width:100%;max-width:400px;height:auto;border-radius:10px;}
    #capturarFotoBtn{position:fixed;bottom:50px;width:80px;height:80px;border-radius:50%;background:#fff;border:4px solid #00c853;cursor:pointer;}
    #fecharCameraBtn{position:fixed;top:20px;right:20px;background:rgba(255,255,255,0.2);color:white;border:none;border-radius:50%;width:50px;height:50px;font-size:20px;cursor:pointer;}

    /* Novos estilos para GPS */
    .botao-circular:disabled {
        opacity: 0.6;
        cursor: not-allowed;
    }

    .botao-circular.processing {
        background: linear-gradient(145deg, #ff9800, #f57c00);
        animation: pulse 1.5s infinite;
    }

    @keyframes pulse {
        0% { opacity: 1; }
        50% { opacity: 0.7; }
        100% { opacity: 1; }
    }

    /* Tela de cadastro */
    #telaCadastro{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:linear-gradient(180deg,var(--bg1),var(--bg2));z-index:2000;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:20px;}
    .cadastroCard{width:100%;max-width:400px;background:rgba(255,255,255,0.1);padding:20px;border-radius:12px;backdrop-filter: blur(10px);}

    /* small screens adjustments */
    @media (max-width:420px){
      .row{flex-direction:column;}
      .botao-circular{width:140px;height:140px;}
    }
    </style>
</head>
<body>

<!-- Tela de Cadastro (aparece apenas uma vez) -->
<div id="telaCadastro">
  <div class="cadastroCard">
    <h2 style="text-align:center;margin-bottom:20px;">Cadastro do Colaborador</h2>
    <div class="field">
      <label for="cadastroNome">Nome completo</label>
      <input type="text" id="cadastroNome" placeholder="Seu nome completo" />
    </div>
    <div class="field">
      <label for="cadastroCargo">Cargo / Função</label>
      <input type="text" id="cadastroCargo" placeholder="Ex.: Zelador" />
    </div>
    <div class="field">
      <label for="cadastroTelefone">Telefone WhatsApp</label>
      <input type="text" id="cadastroTelefone" placeholder="Ex: 31 986952093" maxlength="11" />
      <small style="color:#ccc;font-size:12px;">DDD + número (ex: 31 986952093)</small>
    </div>
    <button onclick="salvarCadastro()" style="width:100%;padding:12px;background:#00c853;color:white;border:none;border-radius:8px;font-weight:700;margin-top:15px;cursor:pointer;">
      SALVAR CADASTRO
    </button>
  </div>
</div>

<!-- Modal Câmera -->
<div id="modalCamera">
  <button id="fecharCameraBtn">✕</button>
  <video id="videoCamera" autoplay playsinline></video>
  <button id="capturarFotoBtn"></button>
</div>

<div id="menuBtn" title="Menu">&#9776;</div>
<div id="menuOptions" aria-hidden="true">
  <button onclick="mostrarPontoDia()">Ponto do Dia</button>
  <button onclick="mostrarPontoMes()">Ponto do Mês</button>
  <button onclick="abrirJustificativa()">Falta/Atraso</button>
  <button onclick="editarCadastro()">Editar Cadastro</button>
</div>

<div class="header">
  <h1>Registro de Ponto</h1>
</div>

<main>
  <div class="formCard">
    <div id="relogio">--:--:--</div>

    <div class="row" style="margin-bottom:8px;">
      <div class="field" style="flex:1">
        <label>Nome do colaborador</label>
        <input type="text" id="nomeUsuario" readonly style="background:rgba(255,255,255,0.1);" />
      </div>
      <div class="field" style="width:150px;">
        <label>Cargo / Função</label>
        <input type="text" id="cargoUsuario" readonly style="background:rgba(255,255,255,0.1);" />
      </div>
    </div>

    <div class="row">
      <div class="field" style="flex:1">
        <label for="observacoes">Observações (opcional)</label>
        <textarea id="observacoes" placeholder="Ex.: Portão fechado, cliente ausente..."></textarea>
      </div>
    </div>

    <div style="text-align:center;margin-top:6px;">
      <div class="botao-circular" id="baterPontoBtn">BATER PONTO</div>
    </div>

    <div class="mensagem-confirmacao" id="mensagemConfirmacao"></div>

    <div class="statusBar" id="statusBar">
      <div>Pontos Hoje: <span id="pontosHojeCount">0/8</span></div>
      <div>Próximo ponto em: <span id="tempoRestante">--:--</span></div>
      <img id="lastThumb" class="thumbnail" style="display:none" alt="thumb">
    </div>

    <div id="warnGPS" class="warn" style="display:none"></div>
    <div id="warnTempo" class="warn" style="display:none"></div>
  </div>

  <div id="visualizacaoPontos" style="width:100%;max-width:480px;margin-top:12px;"></div>
</main>

<div class="rodape">
  © LCSoftware Ltda
</div>

<!-- Modal Justificativa -->
<div id="modalJustificativa" style="display:none;position:fixed;inset:0;background:rgba(0,0,0,0.7);align-items:center;justify-content:center;z-index:9999;">
  <div style="width:100%;max-width:420px;background:#fff;color:#000;border-radius:10px;overflow:hidden;display:flex;flex-direction:column;align-items:center;padding:12px;">
    <h3>Falta/Atraso</h3>
    <textarea id="justText" rows="4" style="width:100%;"></textarea>
    <div style="margin-top:10px;">
      <button onclick="enviarJustificativa()" style="background:#1976d2;color:#fff;padding:6px 12px;border:0;border-radius:6px;font-weight:700">Enviar</button>
      <button onclick="fecharJustificativa()" style="background:#e53935;color:#fff;padding:6px 12px;border:0;border-radius:6px;font-weight:700;margin-left:6px">Cancelar</button>
    </div>
  </div>
</div>

<!-- Toast container -->
<div id="toastContainer" aria-live="polite" aria-atomic="true"></div>

<script>
/* ============================
   CONFIGURAÇÃO DO WEBAPP
   ============================ */
const WEBAPP_URL = 'https://script.google.com/macros/s/AKfycbw08-sk6Sl7dieQ-CjyalbQwqZplZoYwYJdb3oCHAbjbz0st4Ic30TuLLZUbQmoAFEb/exec';
const WEBAPP_SECRET = 'Pontobh#4'; // mesmo do Apps Script

/* ============================
   Variáveis e elementos
   ============================ */
const KEY_PONTOS_DIA = 'pontosDia_v11';
const KEY_CADASTRO = 'cadastroUsuario_v2';
const DB_NAME = 'PontoCBH_DB';
const DB_STORE = 'pendingPoints';
const TEMPO_MINIMO_ENTRE_PONTOS = 0; // 0 segundos entre pontos

const nomeUsuarioEl = document.getElementById('nomeUsuario');
const cargoUsuarioEl = document.getElementById('cargoUsuario');
const observacoesEl = document.getElementById('observacoes');
const mensagemConfirmacao = document.getElementById('mensagemConfirmacao');
const botao = document.getElementById('baterPontoBtn');
const relogioEl = document.getElementById('relogio');
const visualizacaoPontos = document.getElementById('visualizacaoPontos');
const pontosHojeCountEl = document.getElementById('pontosHojeCount');
const tempoRestanteEl = document.getElementById('tempoRestante');
const warnGPS = document.getElementById('warnGPS');
const warnTempo = document.getElementById('warnTempo');
const lastThumb = document.getElementById('lastThumb');
const toastContainer = document.getElementById('toastContainer');
const telaCadastro = document.getElementById('telaCadastro');
const modalCamera = document.getElementById('modalCamera');
const videoCamera = document.getElementById('videoCamera');
const capturarFotoBtn = document.getElementById('capturarFotoBtn');
const fecharCameraBtn = document.getElementById('fecharCameraBtn');

let pontosDia = [];
let db = null;
let dbReadyPromise = null;
let usuarioCadastrado = null;
let ultimoPontoTimestamp = null;
let tempoRestanteInterval = null;
let deviceId = null;
let cameraStream = null;

/* ============================
   Sistema de Identificação do Dispositivo
   ============================ */
function gerarDeviceId() {
    const KEY_DEVICE_ID = 'ponto_device_id';
    let deviceId = localStorage.getItem(KEY_DEVICE_ID);
    
    if (!deviceId) {
        deviceId = 'device_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9);
        localStorage.setItem(KEY_DEVICE_ID, deviceId);
    }
    
    return deviceId;
}

/* ============================
   Sistema de Cadastro
   ============================ */
function verificarCadastro() {
    deviceId = gerarDeviceId();
    const cadastroSalvo = localStorage.getItem(KEY_CADASTRO);
    
    if (cadastroSalvo) {
        try {
            usuarioCadastrado = JSON.parse(cadastroSalvo);
            
            if (usuarioCadastrado.deviceId === deviceId) {
                nomeUsuarioEl.value = usuarioCadastrado.nome;
                cargoUsuarioEl.value = usuarioCadastrado.cargo;
                telaCadastro.style.display = 'none';
                return;
            } else {
                localStorage.removeItem(KEY_CADASTRO);
                usuarioCadastrado = null;
            }
        } catch (e) {
            localStorage.removeItem(KEY_CADASTRO);
        }
    }
    
    telaCadastro.style.display = 'flex';
}

function salvarCadastro() {
    const nome = document.getElementById('cadastroNome').value.trim();
    const cargo = document.getElementById('cadastroCargo').value.trim();
    let telefone = document.getElementById('cadastroTelefone').value.trim();

    if (!nome || !cargo || !telefone) {
        showToast('Preencha todos os campos', 'error', 3000);
        return;
    }

    telefone = telefone.replace(/\D/g, '');
    
    if (!/^\d{11}$/.test(telefone)) {
        showToast('Telefone inválido. Use DDD + número (ex: 31986952093)', 'error', 4000);
        return;
    }

    const telefoneFormatado = telefone.replace(/(\d{2})(\d{5})(\d{4})/, '$1 $2-$3');

    usuarioCadastrado = {
        nome: nome,
        cargo: cargo,
        telefone: telefone,
        telefoneFormatado: telefoneFormatado,
        deviceId: deviceId,
        dataCadastro: new Date().toISOString()
    };

    localStorage.setItem(KEY_CADASTRO, JSON.stringify(usuarioCadastrado));
    nomeUsuarioEl.value = nome;
    cargoUsuarioEl.value = cargo;
    telaCadastro.style.display = 'none';
    
    showToast('Cadastro salvo com sucesso!', 'success', 3000);
}

function editarCadastro() {
    localStorage.removeItem(KEY_CADASTRO);
    usuarioCadastrado = null;
    verificarCadastro();
}

/* ============================
   Sistema de Câmera Manual
   ============================ */
async function abrirCameraManual() {
    return new Promise((resolve, reject) => {
        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
            navigator.mediaDevices.getUserMedia({ 
                video: { 
                    facingMode: 'user',
                    width: { ideal: 1280 },
                    height: { ideal: 720 }
                } 
            })
            .then(stream => {
                cameraStream = stream;
                videoCamera.srcObject = stream;
                modalCamera.style.display = 'flex';
                
                capturarFotoBtn.onclick = () => {
                    capturarFoto().then(resolve).catch(reject);
                };
                
                fecharCameraBtn.onclick = () => {
                    fecharCamera();
                    reject(new Error('Câmera cancelada pelo usuário'));
                };
            })
            .catch(err => {
                reject(new Error('Não foi possível acessar a câmera frontal: ' + err.message));
            });
        } else {
            reject(new Error('Seu navegador não suporta acesso à câmera'));
        }
    });
}

function capturarFoto() {
    return new Promise((resolve, reject) => {
        const canvas = document.createElement('canvas');
        const context = canvas.getContext('2d');
        
        canvas.width = videoCamera.videoWidth;
        canvas.height = videoCamera.videoHeight;
        
        context.drawImage(videoCamera, 0, 0, canvas.width, canvas.height);
        
        canvas.toBlob(blob => {
            fecharCamera();
            
            if (blob) {
                const file = new File([blob], 'selfie.jpg', { type: 'image/jpeg' });
                resolve(file);
            } else {
                reject(new Error('Não foi possível capturar a foto'));
            }
        }, 'image/jpeg', 0.8);
    });
}

function fecharCamera() {
    if (cameraStream) {
        cameraStream.getTracks().forEach(track => track.stop());
        cameraStream = null;
    }
    modalCamera.style.display = 'none';
    videoCamera.srcObject = null;
}

/* ============================
   Toast helper
   ============================ */
function showToast(message, type='success', duration=3000){
    const el = document.createElement('div');
    el.className = `toast ${type} show`;
    el.textContent = message;
    toastContainer.appendChild(el);
    setTimeout(()=> {
        el.classList.remove('show');
        el.style.opacity = '0';
        setTimeout(()=>{ if(el.parentNode) el.parentNode.removeChild(el); }, 350);
    }, duration);
}

/* ============================
   IndexedDB helpers
   ============================ */
function openDB(){
    if(dbReadyPromise) return dbReadyPromise;
    dbReadyPromise = new Promise((resolve,reject)=>{
        const req = indexedDB.open(DB_NAME, 1);
        req.onupgradeneeded = (e) => {
            const idb = e.target.result;
            if(!idb.objectStoreNames.contains(DB_STORE)){
                const store = idb.createObjectStore(DB_STORE, { keyPath: '_id' });
                store.createIndex('timestamp', 'timestamp', { unique: false });
            }
        };
        req.onsuccess = (e) => { db = e.target.result; resolve(db); };
        req.onerror = (e) => reject(e.target.error);
    });
    return dbReadyPromise;
}

async function getDB(){ if(db) return db; return await openDB(); }

async function addPendingToDB(pendingObj){
    const idb = await getDB();
    return new Promise((resolve,reject)=>{
        const tx = idb.transaction(DB_STORE,'readwrite');
        const store = tx.objectStore(DB_STORE);
        const req = store.add(pendingObj);
        req.onsuccess = ()=> resolve(true);
        req.onerror = (e) => {
            if(e && e.target && e.target.error && e.target.error.name === 'ConstraintError'){
                const putReq = store.put(pendingObj);
                putReq.onsuccess = ()=> resolve(true);
                putReq.onerror = (err)=> reject(err.target ? err.target.error : err);
            } else {
                reject(e.target ? e.target.error : e);
            }
        };
    });
}

async function getAllPendingFromDB(){
    const idb = await getDB();
    return new Promise((resolve,reject)=>{
        const tx = idb.transaction(DB_STORE,'readonly');
        const store = tx.objectStore(DB_STORE);
        const req = store.getAll();
        req.onsuccess = ()=> resolve(req.result || []);
        req.onerror = (e)=> reject(e.target.error);
    });
}

async function deletePendingFromDB(id){
    const idb = await getDB();
    return new Promise((resolve,reject)=>{
        const tx = idb.transaction(DB_STORE,'readwrite');
        const store = tx.objectStore(DB_STORE);
        const req = store.delete(id);
        req.onsuccess = ()=> resolve(true);
        req.onerror = (e)=> reject(e.target.error);
    });
}

/* ============================
   Sincronização
   ============================ */
async function sincronizarPontosPendentes() {
    if (!navigator.onLine) return;
    
    try {
        const pendentes = await getAllPendingFromDB();
        if (!pendentes || pendentes.length === 0) return;
        
        for (let i = 0; i < pendentes.length; i++) {
            const p = pendentes[i];
            try {
                await enviarPorEmail(p);
                await deletePendingFromDB(p._id);
                
                const idx = pontosDia.findIndex(x => x._id === p._id);
                if (idx !== -1) {
                    pontosDia[idx].enviado = true;
                    localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
                }
                
                showToast('Ponto pendente enviado!', 'success', 2000);
                
            } catch (err) {
                console.warn(`Falha no envio do ponto pendente ${p._id}:`, err);
                continue;
            }
        }
        
        atualizarVisualizacaoResumo();
        
    } catch (err) {
        console.warn('Erro geral na sincronização:', err);
    }
}

setInterval(() => {
    if (navigator.onLine) {
        sincronizarPontosPendentes();
    }
}, 30000);

window.addEventListener('online', () => {
    showToast('Conexão restaurada - sincronizando pontos...', 'info', 2000);
    setTimeout(sincronizarPontosPendentes, 1000);
});

/* ============================
   ENVIO PARA WEB APP (Google Apps Script)
   ============================ */
async function enviarPorEmail(pontoObj) {
  try {
    const base64 = await blobToDataURL(pontoObj.fotoBlob);

    const payload = {
      secret: WEBAPP_SECRET,
      nome: pontoObj.nome,
      cargo: pontoObj.cargo,
      telefone: pontoObj.telefone,
      tipo: pontoObj.tipo,
      numero: pontoObj.numero,
      data: pontoObj.data,
      hora: pontoObj.hora,
      observacoes: pontoObj.observacoes,
      localizacao: pontoObj.localizacao,
      deviceId: pontoObj._id,
      selfieBase64: base64
    };

    // USA A NOVA FUNÇÃO COM CORS
    const res = await fetchWithCORS(WEBAPP_URL, {
      method: 'POST',
      body: JSON.stringify(payload),
      headers: { 'Content-Type': 'application/json' }
    });

    const json = await res.json();
    if (!json.success) throw new Error(json.msg);

    showToast('Ponto enviado e registrado com sucesso!', 'success', 3000);
    return true;
  } catch (err) {
    console.error('Erro ao enviar ponto:', err);
    showToast('Falha no envio: ' + err.message, 'error', 4000);
    throw err;
  }
}

/* ============================
   LocalStorage inicial
   ============================ */
function carregarLocalStorage(){
    try{
        const raw = localStorage.getItem(KEY_PONTOS_DIA);
        pontosDia = raw ? JSON.parse(raw) : [];
        atualizarContadorPontosHoje();
        verificarUltimoPonto();
    }catch(e){ pontosDia = []; }
    atualizarVisualizacaoResumo();
}

/* ============================
   Relógio
   ============================ */
function atualizarRelogio(){
    const agora = new Date();
    relogioEl.textContent = `${String(agora.getHours()).padStart(2,'0')}:${String(agora.getMinutes()).padStart(2,'0')}:${String(agora.getSeconds()).padStart(2,'0')}`;
}
setInterval(atualizarRelogio,1000);
atualizarRelogio();

/* ============================
   Sistema de tempo entre pontos (0 segundos)
   ============================ */
function verificarUltimoPonto() {
    const hoje = new Date().toLocaleDateString('pt-BR');
    const pontosHoje = pontosDia.filter(p => p.data === hoje);
    
    if (pontosHoje.length > 0) {
        const ultimoPonto = pontosHoje[pontosHoje.length - 1];
        ultimoPontoTimestamp = new Date(ultimoPonto.timestamp).getTime();
        iniciarContadorTempo();
    } else {
        ultimoPontoTimestamp = null;
        tempoRestanteEl.textContent = 'Pronto';
        warnTempo.style.display = 'none';
    }
}

function iniciarContadorTempo() {
    if (tempoRestanteInterval) {
        clearInterval(tempoRestanteInterval);
    }

    tempoRestanteInterval = setInterval(() => {
        if (!ultimoPontoTimestamp) {
            tempoRestanteEl.textContent = 'Pronto';
            warnTempo.style.display = 'none';
            botao.disabled = false;
            return;
        }

        const agora = Date.now();
        const tempoPassado = agora - ultimoPontoTimestamp;
        const tempoRestante = TEMPO_MINIMO_ENTRE_PONTOS - tempoPassado;

        if (tempoRestante <= 0) {
            tempoRestanteEl.textContent = 'Pronto';
            warnTempo.style.display = 'none';
            botao.disabled = false;
            clearInterval(tempoRestanteInterval);
        } else {
            const minutos = Math.floor(tempoRestante / 60000);
            const segundos = Math.floor((tempoRestante % 60000) / 1000);
            tempoRestanteEl.textContent = `${minutos.toString().padStart(2, '0')}:${segundos.toString().padStart(2, '0')}`;
            
            if (tempoRestante > 0) {
                botao.disabled = true;
                warnTempo.style.display = 'block';
                warnTempo.innerHTML = `Aguarde ${minutos} minutos para o próximo ponto`;
            }
        }
    }, 1000);
}

function podeBaterPonto() {
    if (!ultimoPontoTimestamp) return true;
    
    const tempoPassado = Date.now() - ultimoPontoTimestamp;
    return tempoPassado >= TEMPO_MINIMO_ENTRE_PONTOS;
}

/* ============================
   Próximo Ponto - Sistema de 8 pontos (4 entradas + 4 saídas)
   ============================ */
function proximoPonto(){
    if (!podeBaterPonto()) {
        const tempoPassado = Date.now() - ultimoPontoTimestamp;
        const tempoRestante = TEMPO_MINIMO_ENTRE_PONTOS - tempoPassado;
        const minutos = Math.ceil(tempoRestante / 60000);
        throw new Error(`Aguarde ${minutos} minutos para o próximo ponto`);
    }

    const hoje = new Date().toLocaleDateString('pt-BR');
    const pontosHoje = pontosDia.filter(p => p.data === hoje);
    
    const sequencia = ['Entrada 1', 'Saída 1', 'Entrada 2', 'Saída 2', 'Entrada 3', 'Saída 3', 'Entrada 4', 'Saída 4'];
    
    for (let i = 0; i < sequencia.length; i++) {
        const pontoExistente = pontosHoje.find(p => p.tipo === sequencia[i]);
        if (!pontoExistente) {
            return { tipo: sequencia[i], numero: i + 1 };
        }
    }
    
    return null;
}

/* ============================
   Atualizar contador de pontos do dia
   ============================ */
function atualizarContadorPontosHoje(){
    const hoje = new Date().toLocaleDateString('pt-BR');
    const pontosHoje = pontosDia.filter(p => p.data === hoje).length;
    pontosHojeCountEl.textContent = `${pontosHoje}/8`;
}

/* ============================
   Botão 'Bater Ponto' fluxo principal
   ============================ */
botao.addEventListener('click', async ()=>{
    if (!usuarioCadastrado) {
        showToast('Complete seu cadastro primeiro', 'error', 3000);
        verificarCadastro();
        return;
    }

    let prox;
    try {
        prox = proximoPonto();
        if(!prox){ 
            mensagemConfirmacao.textContent='Todos os 8 pontos já registrados hoje!'; 
            showToast('Todos os pontos do dia já foram registrados', 'info', 3000);
            return; 
        }
    } catch (err) {
        showToast(err.message, 'error', 4000);
        return;
    }

    warnGPS.style.display = 'none';
    mensagemConfirmacao.textContent = 'Obtendo localização GPS...';
    
    try {
        botao.style.opacity = '0.7';
        botao.style.pointerEvents = 'none';
        botao.classList.add('processing');
        
        const pos = await obterLocalizacaoComTimeout(20000);
        
        mensagemConfirmacao.textContent = 'Abrindo câmera...';
        showToast('GPS OK! Posicione e tire a selfie', 'success', 2000);
        
        const selfieFile = await abrirCameraManual();
        
        await processarSelfie(selfieFile, pos, prox);
        
    } catch (err) {
        console.error('Erro no processo:', err);
        
        botao.style.opacity = '1';
        botao.style.pointerEvents = 'auto';
        botao.classList.remove('processing');
        
        if (err.message.includes('câmera')) {
            warnGPS.style.display = 'block';
            warnGPS.innerHTML = `
                <strong>Erro na câmera:</strong> ${err.message}<br>
                <small>• Verifique as permissões da câmera<br>
                • Tente novamente</small>
            `;
        } else if (err.message.includes('GPS')) {
            warnGPS.style.display = 'block';
            warnGPS.innerHTML = `
                <strong>Falha na localização:</strong> ${err.message}<br>
                <small>• Verifique permissões do navegador<br>
                • Ative o GPS do dispositivo<br>
                • Tente em área aberta com boa recepção</small>
            `;
        } else if (err.message.includes('cancelada')) {
            mensagemConfirmacao.textContent = 'Selfie cancelada';
        } else {
            mensagemConfirmacao.textContent = 'Falha no processo. Tente novamente.';
            showToast('Erro: ' + err.message, 'error', 5000);
        }
    }
});

/* ============================
   Função para processar selfie
   ============================ */
async function processarSelfie(file, pos, prox) {
    try {
        const compressedBlob = await compressImageFileToJpegBlob(file, 800, 0.75);
        const thumbDataUrl = await blobToDataURL(await compressImageBlobForThumb(compressedBlob, 140, 0.65));
        const agora = new Date();
        const ponto = {
            _id: 'p-' + agora.getTime() + '-' + Math.random().toString(36).slice(2,8),
            nome: usuarioCadastrado.nome,
            cargo: usuarioCadastrado.cargo,
            telefone: usuarioCadastrado.telefoneFormatado,
            tipo: prox.tipo,
            numero: prox.numero,
            observacoes: observacoesEl.value.trim() || '',
            data: agora.toLocaleDateString('pt-BR'),
            hora: agora.toLocaleTimeString('pt-BR'),
            timestamp: agora.toISOString(),
            thumbnail: thumbDataUrl,
            localizacao: pos,
            enviado: false,
            fotoBlob: compressedBlob
        };

        pontosDia.push(ponto);
        localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
        lastThumb.src = thumbDataUrl; 
        lastThumb.style.display = 'inline-block';
        atualizarVisualizacaoResumo();
        atualizarContadorPontosHoje();

        ultimoPontoTimestamp = agora.getTime();
        iniciarContadorTempo();

        // ENVIO PARA WEB APP
        mensagemConfirmacao.textContent = 'Preparando envio do ponto...';
        
        if(navigator.onLine){
            try{
                await enviarPorEmail(ponto);
                ponto.enviado = true;
                localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
                mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (enviada)`;
                showToast('Ponto registrado com sucesso!', 'success', 3000);
                
                atualizarVisualizacaoResumo();
                
            } catch(err){
                try{
                    await addPendingToDB(ponto);
                    mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (salva - será enviada)`;
                    showToast('Ponto salvo. Será enviado automaticamente quando online.', 'info', 3500);
                }catch(dbErr){
                    console.error('Falha ao salvar pendente no DB:', dbErr);
                    mensagemConfirmacao.textContent = 'Erro ao salvar ponto localmente.';
                    showToast('Erro ao salvar localmente', 'error', 3500);
                }
            } finally {
                atualizarVisualizacaoResumo();
            }
        } else {
            try{
                await addPendingToDB(ponto);
                mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (offline - será enviada)`;
                showToast('Ponto salvo offline. Será sincronizado quando online.', 'info', 3200);
            }catch(err){
                console.error('Erro addPendingToDB:', err);
                mensagemConfirmacao.textContent = 'Erro ao salvar ponto offline.';
                showToast('Erro ao salvar offline', 'error', 3200);
            }
        }

    } catch(err){
        console.error('Erro ao processar selfie/ponto:', err);
        mensagemConfirmacao.textContent = 'Erro ao processar a selfie. Tente novamente.';
        showToast('Erro ao processar a selfie', 'error', 3000);
        throw err;
    } finally {
        botao.style.opacity = '1';
        botao.style.pointerEvents = 'auto';
        botao.classList.remove('processing');
    }
}

/* ============================
   GEOLOCALIZAÇÃO
   ============================ */
function obterLocalizacaoComTimeout(ms = 20000) {
    return new Promise((resolve, reject) => {
        if (!navigator.geolocation) {
            reject(new Error('Geolocalização não suportada pelo navegador'));
            return;
        }

        let timeoutFired = false;
        let watchId = null;

        const timeoutId = setTimeout(() => {
            timeoutFired = true;
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
            }
            reject(new Error('Tempo esgotado. Verifique se o GPS está ativo e tente em área aberta.'));
        }, ms);

        const success = (position) => {
            if (timeoutFired) return;
            
            clearTimeout(timeoutId);
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
            }

            const coords = {
                latitude: position.coords.latitude,
                longitude: position.coords.longitude,
                accuracy: position.coords.accuracy,
                timestamp: position.timestamp
            };
            
            console.log('Localização obtida:', coords);
            resolve(coords);
        };

        const error = (error) => {
            if (timeoutFired) return;
            
            clearTimeout(timeoutId);
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
            }

            let message = 'Erro desconhecido ao obter localização';
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    message = 'Permissão de localização negada. Habilite nas configurações do navegador.';
                    break;
                case error.POSITION_UNAVAILABLE:
                    message = 'Localização indisponível. Verifique se o GPS está ativado.';
                    break;
                case error.TIMEOUT:
                    message = 'Tempo esgotado. Tente novamente em área aberta.';
                    break;
            }
            reject(new Error(message));
        };

        const options = {
            enableHighAccuracy: true,
            timeout: ms,
            maximumAge: 0
        };

        watchId = navigator.geolocation.watchPosition(success, error, options);
    });
}

/* ============================
   Compressão helpers
   ============================ */
function compressImageFileToJpegBlob(file, maxWidth = 800, quality = 0.75){
    return new Promise((resolve,reject)=>{
        const reader = new FileReader();
        reader.onload = () => {
            const img = new Image();
            img.onload = () => {
                const ratio = (img.width && img.height) ? img.width / img.height : 1;
                const width = img.width > maxWidth ? maxWidth : img.width;
                const height = Math.round(width / ratio);
                const canvas = document.createElement('canvas');
                canvas.width = width;
                canvas.height = height;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, width, height);
                canvas.toBlob((blob) => {
                    if(blob) resolve(blob);
                    else reject(new Error('Falha ao converter canvas to Blob'));
                }, 'image/jpeg', quality);
            };
            img.onerror = (e)=> reject(e);
            img.src = reader.result;
        };
        reader.onerror = (e)=> reject(e);
        reader.readAsDataURL(file);
    });
}

function compressImageBlobForThumb(blob, maxWidth = 140, quality = 0.65){
    return new Promise((resolve,reject)=>{
        const reader = new FileReader();
        reader.onload = () => {
            const img = new Image();
            img.onload = () => {
                const ratio = (img.width && img.height) ? img.width / img.height : 1;
                const width = img.width > maxWidth ? maxWidth : img.width;
                const height = Math.round(width / ratio);
                const canvas = document.createElement('canvas');
                canvas.width = width;
                canvas.height = height;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, width, height);
                canvas.toBlob((b) => { if(b) resolve(b); else reject(new Error('thumb blob fail')); }, 'image/jpeg', quality);
            };
            img.onerror = (e)=> reject(e);
            img.src = reader.result;
        };
        reader.onerror = (e)=> reject(e);
        reader.readAsDataURL(blob);
    });
}

function blobToDataURL(blob){
    return new Promise((resolve,reject)=>{
        const reader = new FileReader();
        reader.onload = ()=> resolve(reader.result);
        reader.onerror = ()=> reject(new Error('Falha blob->dataURL'));
        reader.readAsDataURL(blob);
    });
}

/* ============================
   Visualização UI resumo
   ============================ */
function atualizarVisualizacaoResumo(){
    let html = '<h3 style="margin:8px 0 6px 0;">Pontos Registrados (local)</h3>';
    if(pontosDia.length===0) html += '<div style="padding:8px;background:rgba(255,255,255,0.03);border-radius:8px;">Nenhum ponto registrado ainda.</div>';
    else {
        html += '<ul style="padding-left:14px; margin:0;">';
        pontosDia.forEach(p=>{
            const coord = p.localizacao ? ` • ${p.localizacao.latitude.toFixed(5)},${p.localizacao.longitude.toFixed(5)}` : '';
            const enviadoBadge = p.enviado ? '✅' : '⏳';
            html += `<li style="font-size:14px;margin-bottom:8px;background:rgba(255,255,255,0.03);padding:8px;border-radius:8px;display:flex;align-items:center;justify-content:space-between">
                <div>
                <strong>${p.nome}</strong> — ${p.cargo} <br>
                <small>${p.tipo} — ${p.data} ${p.hora}${coord}</small><br>
                <small style="color:#ffd;">${p.observacoes || ''}</small>
                </div>
                <div style="display:flex;align-items:center;gap:8px">
                ${p.thumbnail ? `<img src="${p.thumbnail}" alt="thumb" style="width:56px;height:56px;border-radius:8px;object-fit:cover;border:1px solid rgba(255,255,255,0.12)">` : ''}
                <div style="font-weight:800">${enviadoBadge}</div>
                </div>
            </li>`;
        });
        html += '</ul>';
    }
    visualizacaoPontos.innerHTML = html;
}

/* ============================
   Ponto Dia / Mês simples
   ============================ */
function mostrarPontoDia(){ atualizarVisualizacaoResumo(); }

function mostrarPontoMes(){
    let html = '<h3>Ponto do Mês (visão local)</h3>';
    if(pontosDia.length===0) html += '<div>Nenhum ponto.</div>';
    else {
        html += '<ul>';
        pontosDia.forEach(p => html += `<li>${p.data} ${p.hora} — ${p.nome} — ${p.tipo}</li>`);
        html += '</ul>';
    }
    visualizacaoPontos.innerHTML = html;
}

/* ============================
   Justificativa
   ============================ */
function abrirJustificativa(){ document.getElementById('modalJustificativa').style.display='flex'; }
function fecharJustificativa(){ document.getElementById('modalJustificativa').style.display='none'; }
function enviarJustificativa(){
    const txt = document.getElementById('justText').value.trim();
    if(txt===''){ alert('Digite uma justificativa.'); return; }
    alert('Justificativa enviada: '+txt);
    document.getElementById('justText').value='';
    fecharJustificativa();
}

/* ============================
   Menu toggle
   ============================ */
const menuBtn = document.getElementById('menuBtn');
const menuOptions = document.getElementById('menuOptions');
menuBtn.addEventListener('click', (e)=>{
    const visible = menuOptions.style.display === 'flex' || menuOptions.style.display === 'block';
    menuOptions.style.display = visible ? 'none' : 'flex';
    menuOptions.setAttribute('aria-hidden', visible ? 'true' : 'false');
});
document.addEventListener('click', (e)=>{
    if(!menuOptions.contains(e.target) && e.target !== menuBtn){
        menuOptions.style.display = 'none';
        menuOptions.setAttribute('aria-hidden','true');
    }
});

/* ============================
   Inicialização
   ============================ */
async function inicializar(){
    verificarCadastro();
    carregarLocalStorage();
    
    if (!navigator.geolocation) {
        showToast('Seu navegador não suporta GPS', 'error', 5000);
    }
    
    if(navigator.onLine) {
        setTimeout(sincronizarPontosPendentes, 2000);
    }
}

inicializar();

</script>
</body>
</html>
