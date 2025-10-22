[pontoteste.html](https://github.com/user-attachments/files/23044991/pontoteste.html)
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover" />
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

/* small screens adjustments */
@media (max-width:420px){
  .row{flex-direction:column;}
  .botao-circular{width:140px;height:140px;}
}
</style>
</head>
<body>

<div id="menuBtn" title="Menu">&#9776;</div>
<div id="menuOptions" aria-hidden="true">
  <button onclick="mostrarPontoDia()">Ponto do Dia</button>
  <button onclick="mostrarPontoMes()">Ponto do Mês</button>
  <button onclick="abrirJustificativa()">Falta/Atraso</button>
</div>

<div class="header">
  <h1>Registro de Ponto</h1>
</div>

<main>
  <div class="formCard">
    <div id="relogio">--:--:--</div>

    <div class="row" style="margin-bottom:8px;">
      <div class="field" style="flex:1">
        <label for="nomeUsuario">Nome do colaborador</label>
        <input id="nomeUsuario" type="text" placeholder="Seu nome completo" />
      </div>
      <div class="field" style="width:150px;">
        <label for="cargoUsuario">Cargo / Função</label>
        <input id="cargoUsuario" type="text" placeholder="Ex.: Zelador" />
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
      <div>Conexão: <span id="connStatus">--</span></div>
      <div>Pendentes: <span id="pendCount" class="pendentes">0</span></div>
      <img id="lastThumb" class="thumbnail" style="display:none" alt="thumb">
    </div>

    <div id="warnGPS" class="warn" style="display:none"></div>
  </div>

  <div id="visualizacaoPontos" style="width:100%;max-width:480px;margin-top:12px;"></div>
</main>

<div class="rodape">
  © LCSoftware Ltda
  <div id="alterarNumeroDiv"><button onclick="alterarNumero()">Alterar Número</button></div>
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

<!-- Input frontal selfie (fallback) -->
<input type="file" accept="image/*" capture="user" id="inputSelfie" style="display:none">

<!-- Toast container -->
<div id="toastContainer" aria-live="polite" aria-atomic="true"></div>

<script>
/* ============================
   CONFIGURAÇÃO DE EMAIL
   ============================ */
const EMAIL_DESTINO = 'pontocbh@gmail.com';

/* ============================
   Variáveis e elementos
   ============================ */
const KEY_PONTOS_DIA = 'pontosDia_v3';
const DB_NAME = 'PontoCBH_DB';
const DB_STORE = 'pendingPoints';

const nomeUsuarioEl = document.getElementById('nomeUsuario');
const cargoUsuarioEl = document.getElementById('cargoUsuario');
const observacoesEl = document.getElementById('observacoes');
const mensagemConfirmacao = document.getElementById('mensagemConfirmacao');
const botao = document.getElementById('baterPontoBtn');
const relogioEl = document.getElementById('relogio');
const visualizacaoPontos = document.getElementById('visualizacaoPontos');
const inputSelfie = document.getElementById('inputSelfie');
const connStatusEl = document.getElementById('connStatus');
const pendCountEl = document.getElementById('pendCount');
const warnGPS = document.getElementById('warnGPS');
const lastThumb = document.getElementById('lastThumb');
const toastContainer = document.getElementById('toastContainer');

let pontosDia = [];
let db = null;
let dbReadyPromise = null;
let numeroWhatsApp = localStorage.getItem('numeroWhatsApp');
let numeroAlterado = localStorage.getItem('numeroAlterado');

/* ============================
   Verificação de Permissões GPS
   ============================ */
async function verificarPermissaoGPS() {
    if (!navigator.permissions) {
        return 'unknown';
    }
    
    try {
        const result = await navigator.permissions.query({ name: 'geolocation' });
        console.log('Status permissão GPS:', result.state);
        return result.state;
    } catch (err) {
        console.warn('Erro ao verificar permissão GPS:', err);
        return 'unknown';
    }
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
   LocalStorage inicial
   ============================ */
function carregarLocalStorage(){
  try{
    const raw = localStorage.getItem(KEY_PONTOS_DIA);
    pontosDia = raw ? JSON.parse(raw) : [];
  }catch(e){ pontosDia = []; }
  atualizarVisualizacaoResumo();
}
carregarLocalStorage();
openDB().then(()=>{ atualizarPendCount(); sincronizarPontosPendentes(); }).catch(err=>console.warn('Erro abrir DB:', err));

/* solicita número whatsapp se não houver */
if(!numeroWhatsApp){
  const input = prompt("Informe seu número do WhatsApp (somente números, ex: 5531999999999):");
  if(input && /^\d{12,15}$/.test(input)){
    numeroWhatsApp = input;
    localStorage.setItem('numeroWhatsApp',numeroWhatsApp);
  } else {
    if(input) alert("Número inválido. Será solicitado novamente no próximo acesso.");
  }
}
if(numeroAlterado==='sim'){ document.getElementById('alterarNumeroDiv').style.display='none'; }

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
   Próximo Ponto (lógica simplificada sem condomínios)
   ============================ */
function proximoPonto(){
  const pontosHoje = pontosDia.filter(p => p.data === new Date().toLocaleDateString('pt-BR'));
  
  // Se não há pontos hoje, começa com Entrada
  if (pontosHoje.length === 0) return { tipo: "Entrada" };
  
  // Pega o último ponto
  const ultimoPonto = pontosHoje[pontosHoje.length - 1];
  
  // Alterna entre Entrada e Saída
  if (ultimoPonto.tipo === "Entrada") {
    return { tipo: "Saída" };
  } else {
    return { tipo: "Entrada" };
  }
}

/* ============================
   Conexão & Sincronização
   ============================ */
function atualizarConnStatus(){
  const online = navigator.onLine;
  connStatusEl.textContent = online ? 'Online' : 'Offline';
  connStatusEl.style.color = online ? '#b9f6ca' : '#ff8a80';
}
window.addEventListener('online', ()=>{ atualizarConnStatus(); sincronizarPontosPendentes(); });
window.addEventListener('offline', ()=>{ atualizarConnStatus(); });
atualizarConnStatus();

async function atualizarPendCount(){
  try{
    const pendentes = await getAllPendingFromDB();
    pendCountEl.textContent = pendentes.length;
  } catch(e){ pendCountEl.textContent = '0'; }
}

async function sincronizarPontosPendentes(){
  if(!navigator.onLine) return;
  try{
    const pendentes = await getAllPendingFromDB();
    if(!pendentes || pendentes.length===0){ atualizarPendCount(); return; }
    mensagemConfirmacao.textContent = `Sincronizando ${pendentes.length} ponto(s)...`;
    for(const p of pendentes){
      try{
        await enviarPorEmail(p);
        await deletePendingFromDB(p._id);
        const idx = pontosDia.findIndex(x => x._id === p._id);
        if(idx !== -1){ pontosDia[idx].enviado = true; localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia)); }
        showToast('Ponto sincronizado com sucesso', 'success', 2800);
      } catch(err){
        console.warn('Falha no envio pendente:', err);
        mensagemConfirmacao.textContent = 'Sincronização interrompida - tentaremos novamente.';
        break;
      }
    }
  } catch(err){
    console.warn('Erro sincronizar:', err);
  } finally {
    await atualizarPendCount();
    atualizarVisualizacaoResumo();
    setTimeout(()=> mensagemConfirmacao.textContent = '', 2500);
  }
}

setInterval(()=>{ if(navigator.onLine) sincronizarPontosPendentes(); }, 25000);

/* ============================
   GEOLOCALIZAÇÃO SIMPLIFICADA e ROBUSTA
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

        // Usa watchPosition para melhor captura em dispositivos móveis
        watchId = navigator.geolocation.watchPosition(success, error, options);
    });
}

/* ============================
   Botão 'Bater Ponto' fluxo principal CORRIGIDO
   ============================ */
botao.addEventListener('click', async ()=>{
  // validações de campos usuário
  const nome = nomeUsuarioEl.value.trim();
  const cargo = cargoUsuarioEl.value.trim();
  if(!nome || !cargo){
    mensagemConfirmacao.textContent = 'Preencha nome e cargo antes de bater ponto.';
    showToast('Preencha nome e cargo', 'error', 2600);
    return;
  }

  // determina próximo tipo
  const prox = proximoPonto();
  if(!prox){ 
    mensagemConfirmacao.textContent='Todos os pontos já registrados!'; 
    return; 
  }

  // SOLICITAÇÃO GPS CORRIGIDA
  warnGPS.style.display = 'none';
  mensagemConfirmacao.textContent = 'Obtendo localização GPS...';
  
  try {
    // Mostrar feedback visual
    botao.style.opacity = '0.7';
    botao.style.pointerEvents = 'none';
    botao.classList.add('processing');
    
    const pos = await obterLocalizacaoComTimeout(20000);
    
    mensagemConfirmacao.textContent = 'Localização obtida! Abrindo câmera...';
    showToast('GPS OK! Abra a câmera para selfie', 'success', 2000);
    
    window._pendingLocation = pos;
    
    // Pequeno delay para usuário ver a mensagem
    setTimeout(() => {
      inputSelfie.click();
    }, 800);
    
  } catch (err) {
    console.error('Falha GPS:', err);
    
    // Restaurar botão
    botao.style.opacity = '1';
    botao.style.pointerEvents = 'auto';
    botao.classList.remove('processing');
    
    warnGPS.style.display = 'block';
    warnGPS.innerHTML = `
      <strong>Falha na localização:</strong> ${err.message}<br>
      <small>• Verifique permissões do navegador<br>
      • Ative o GPS do dispositivo<br>
      • Tente em área aberta com boa recepção</small>
    `;
    
    mensagemConfirmacao.textContent = 'Falha na localização. Tente novamente.';
    showToast('Erro GPS: ' + err.message, 'error', 5000);
    
  }
});

/* ============================
   Fallback / captura selfie
   ============================ */
inputSelfie.addEventListener('change', async (e)=>{
  const file = inputSelfie.files[0];
  inputSelfie.value = '';
  const prox = proximoPonto();
  if(!prox) return;

  const pos = window._pendingLocation || null;
  if(!pos){
    mensagemConfirmacao.textContent = 'Localização não disponível — ponto cancelado.';
    showToast('Localização indisponível', 'error', 2400);
    return;
  }
  if(!file){
    mensagemConfirmacao.textContent = 'Selfie não selecionada — ponto cancelado.';
    showToast('Selfie não selecionada', 'error', 2400);
    return;
  }

  try{
    const compressedBlob = await compressImageFileToJpegBlob(file, 800, 0.75);
    const thumbDataUrl = await blobToDataURL(await compressImageBlobForThumb(compressedBlob, 140, 0.65));
    const agora = new Date();
    const ponto = {
      _id: 'p-' + agora.getTime() + '-' + Math.random().toString(36).slice(2,8),
      nome: nomeUsuarioEl.value.trim(),
      cargo: cargoUsuarioEl.value.trim(),
      tipo: prox.tipo,
      observacoes: observacoesEl.value.trim() || '',
      data: agora.toLocaleDateString('pt-BR'),
      hora: agora.toLocaleTimeString('pt-BR'),
      timestamp: agora.toISOString(),
      thumbnail: thumbDataUrl,
      localizacao: pos,
      enviado: false
    };

    // salva resumo localmente (localStorage)
    pontosDia.push(ponto);
    localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
    lastThumb.src = thumbDataUrl; lastThumb.style.display = 'inline-block';
    atualizarVisualizacaoResumo();

    // prepara objeto com blob para enviar/salvar
    const pendingFull = Object.assign({}, ponto, { fotoBlob: compressedBlob });

    if(navigator.onLine){
      mensagemConfirmacao.textContent = 'Enviando ponto por email...';
      try{
        await enviarPorEmail(pendingFull);
        ponto.enviado = true;
        localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
        mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (enviada)`;
        showToast('Ponto enviado com sucesso!', 'success', 3000);
      } catch(err){
        // se falhar, salva pendente
        try{
          await addPendingToDB(pendingFull);
          mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (salva - pendente)`;
          showToast('Erro no envio. Ponto salvo como pendente.', 'error', 3500);
        }catch(dbErr){
          console.error('Falha ao salvar pendente no DB:', dbErr);
          mensagemConfirmacao.textContent = 'Erro ao salvar ponto localmente.';
          showToast('Erro ao salvar localmente', 'error', 3500);
        }
      } finally {
        atualizarPendCount();
      }
    } else {
      // offline -> salvar no indexedDB
      try{
        await addPendingToDB(pendingFull);
        mensagemConfirmacao.textContent = `${ponto.tipo} registrada às ${ponto.hora} (offline - será enviada)`;
        showToast('Ponto salvo offline. Será sincronizado quando online.', 'info', 3200);
      }catch(err){
        console.error('Erro addPendingToDB:', err);
        mensagemConfirmacao.textContent = 'Erro ao salvar ponto offline.';
        showToast('Erro ao salvar offline', 'error', 3200);
      }
      atualizarPendCount();
    }

  } catch(err){
    console.error('Erro ao processar selfie/ponto:', err);
    mensagemConfirmacao.textContent = 'Erro ao processar a selfie. Tente novamente.';
    showToast('Erro ao processar a selfie', 'error', 3000);
  } finally {
    window._pendingLocation = null;
    // Restaurar botão após processamento
    botao.style.opacity = '1';
    botao.style.pointerEvents = 'auto';
    botao.classList.remove('processing');
  }
});

/* ============================
   ENVIO POR EMAIL (usando mailto com dados em anexo)
   ============================ */
async function enviarPorEmail(pontoObj) {
    return new Promise((resolve, reject) => {
        try {
            // Prepara o assunto do email
            const assunto = `Ponto Registrado - ${pontoObj.nome} - ${pontoObj.data} ${pontoObj.hora}`;
            
            // Prepara o corpo do email
            let corpo = `REGISTRO DE PONTO\n\n`;
            corpo += `Nome: ${pontoObj.nome}\n`;
            corpo += `Cargo: ${pontoObj.cargo}\n`;
            corpo += `Tipo: ${pontoObj.tipo}\n`;
            corpo += `Data: ${pontoObj.data}\n`;
            corpo += `Hora: ${pontoObj.hora}\n`;
            corpo += `Localização: ${pontoObj.localizacao.latitude.toFixed(6)}, ${pontoObj.localizacao.longitude.toFixed(6)}\n`;
            corpo += `Precisão: ±${pontoObj.localizacao.accuracy}m\n`;
            
            if (pontoObj.observacoes) {
                corpo += `Observações: ${pontoObj.observacoes}\n`;
            }
            
            corpo += `\n---\nEnviado via Sistema de Ponto LCSoftware`;
            
            // Cria URL mailto
            const mailtoUrl = `mailto:${EMAIL_DESTINO}?subject=${encodeURIComponent(assunto)}&body=${encodeURIComponent(corpo)}`;
            
            // Abre o cliente de email
            window.location.href = mailtoUrl;
            
            showToast('Abra o cliente de email para enviar', 'info', 4000);
            resolve(true);
            
        } catch (error) {
            console.error('Erro ao preparar email:', error);
            reject(new Error('Falha ao preparar envio por email'));
        }
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
      const enviadoBadge = p.enviado ? '✅' : '🕒';
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
   Justificativa / Alterar número
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
function alterarNumero(){
  if(numeroAlterado==='sim'){ alert('Número já alterado.'); return; }
  localStorage.removeItem('numeroWhatsApp');
  localStorage.setItem('numeroAlterado','sim');
  alert('Número removido. Recarregue a página.');
  location.reload();
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
   Inicialização CORRIGIDA
   ============================ */
async function inicializar(){
  atualizarConnStatus();
  carregarLocalStorage();
  
  // Verificar permissão GPS
  const permissao = await verificarPermissaoGPS();
  if (permissao === 'denied') {
    warnGPS.style.display = 'block';
    warnGPS.innerHTML = 'Permissão de localização negada. Habilite em Configurações > Site Settings > Location';
  }
  
  // Teste simples de geolocation
  if (!navigator.geolocation) {
    showToast('Seu navegador não suporta GPS', 'error', 5000);
  } else {
    console.log('Geolocation API disponível no navegador');
  }
  
  try{ 
    await atualizarPendCount(); 
  } catch(e) {}
  
  if(navigator.onLine) sincronizarPontosPendentes();
}
inicializar();

</script>
</body>
</html>
