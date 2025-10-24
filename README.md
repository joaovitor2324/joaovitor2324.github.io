<html lang="pt-BR">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
    <title>Registro de Ponto</title>
    
    <!-- Forçar HTTPS para funcionamento do GPS no Android -->
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

    /* Estados do botão */
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

    /* Indicador de GPS */
    .gps-status {
        display: flex;
        align-items: center;
        gap: 8px;
        font-size: 12px;
        margin-top: 5px;
    }
    .gps-dot {
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background: #ff9800;
        animation: gps-pulse 2s infinite;
    }
    .gps-dot.active {
        background: #4caf50;
    }
    @keyframes gps-pulse {
        0% { transform: scale(1); opacity: 1; }
        50% { transform: scale(1.5); opacity: 0.7; }
        100% { transform: scale(1); opacity: 1; }
    }

    /* small screens adjustments */
    @media (max-width:420px){
      .row{flex-direction:column;}
      .botao-circular{width:140px;height:140px;}
    }
    </style>
</head>
<body>

<!-- Tela de Cadastro -->
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
  <button onclick="exportarDados()">Exportar Dados</button>
  <button onclick="sincronizarPendentes()">Sincronizar Pendentes</button>
  <button onclick="testarGPS()">Testar GPS</button>
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

    <!-- Indicador de Status GPS -->
    <div class="gps-status" id="gpsStatus" style="display: none;">
      <div class="gps-dot" id="gpsDot"></div>
      <span id="gpsText">Obtendo localização...</span>
    </div>

    <div style="text-align:center;margin-top:6px;">
      <div class="botao-circular" id="baterPontoBtn">BATER PONTO</div>
    </div>

    <div class="mensagem-confirmacao" id="mensagemConfirmacao"></div>

    <div class="statusBar" id="statusBar">
      <div>Pontos Hoje: <span id="pontosHojeCount">0/8</span></div>
      <div>Status: <span id="statusEnvio">Pronto</span></div>
      <img id="lastThumb" class="thumbnail" style="display:none" alt="thumb">
    </div>

    <div id="warnGPS" class="warn" style="display:none"></div>
    <div id="warnTempo" class="warn" style="display:none"></div>
    <div id="infoSync" class="warn" style="display:none; background:#81c784; color:#1b5e20;"></div>
    
    <!-- Instruções para Android -->
    <div id="androidHelp" class="warn" style="display:none; background:#bbdefb; color:#0d47a1;">
      <strong>📱 Dicas para Android:</strong><br>
      • Toque em "PERMITIR" quando pedir localização<br>
      • Use em área aberta para melhor precisão<br>
      • Ative o GPS do dispositivo
    </div>
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
// ============================
// CONFIGURAÇÃO DO WEBAPP
// ============================
const WEBAPP_URL = 'https://script.google.com/macros/s/AKfycbwjWH6lr0_jOyBL2u8oRgr630XVcC9eKrOIdQp0m1V74ZB9xoImy3Tdc7_5MdMSSwI8_A/exec';
const WEBAPP_SECRET = 'Pontobh#4';

// ============================
// CONFIGURAÇÃO LOCAL
// ============================
const KEY_PONTOS_DIA = 'pontosDia_v13';
const KEY_CADASTRO = 'cadastroUsuario_v4';
const KEY_PENDENTES = 'pontosPendentes_v1';

// ============================
// Variáveis globais
// ============================
let pontosDia = [];
let pontosPendentes = [];
let usuarioCadastrado = null;
let deviceId = null;
let cameraStream = null;
let isProcessing = false;
let gpsWatchId = null;

// ============================
// Elementos DOM
// ============================
const nomeUsuarioEl = document.getElementById('nomeUsuario');
const cargoUsuarioEl = document.getElementById('cargoUsuario');
const observacoesEl = document.getElementById('observacoes');
const mensagemConfirmacao = document.getElementById('mensagemConfirmacao');
const botao = document.getElementById('baterPontoBtn');
const relogioEl = document.getElementById('relogio');
const visualizacaoPontos = document.getElementById('visualizacaoPontos');
const pontosHojeCountEl = document.getElementById('pontosHojeCount');
const statusEnvioEl = document.getElementById('statusEnvio');
const warnGPS = document.getElementById('warnGPS');
const warnTempo = document.getElementById('warnTempo');
const infoSync = document.getElementById('infoSync');
const androidHelp = document.getElementById('androidHelp');
const gpsStatus = document.getElementById('gpsStatus');
const gpsDot = document.getElementById('gpsDot');
const gpsText = document.getElementById('gpsText');
const lastThumb = document.getElementById('lastThumb');
const toastContainer = document.getElementById('toastContainer');
const telaCadastro = document.getElementById('telaCadastro');
const modalCamera = document.getElementById('modalCamera');
const videoCamera = document.getElementById('videoCamera');
const capturarFotoBtn = document.getElementById('capturarFotoBtn');
const fecharCameraBtn = document.getElementById('fecharCameraBtn');

// ============================
// Detectar Android e mostrar ajuda
// ============================
function detectarAndroid() {
    const isAndroid = /Android/i.test(navigator.userAgent);
    if (isAndroid) {
        androidHelp.style.display = 'block';
    }
    return isAndroid;
}

// ============================
// Sistema de Device ID
// ============================
function gerarDeviceId() {
    const KEY_DEVICE_ID = 'ponto_device_id';
    let deviceId = localStorage.getItem(KEY_DEVICE_ID);
    
    if (!deviceId) {
        deviceId = 'device_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9);
        localStorage.setItem(KEY_DEVICE_ID, deviceId);
    }
    
    return deviceId;
}

// ============================
// Sistema de Cadastro
// ============================
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

// ============================
// Sistema de Câmera
// ============================
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
                reject(new Error('Não foi possível acessar a câmera: ' + err.message));
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

// ============================
// Toast System
// ============================
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

// ============================
// Sistema de Pontos
// ============================
function carregarLocalStorage(){
    try{
        const raw = localStorage.getItem(KEY_PONTOS_DIA);
        pontosDia = raw ? JSON.parse(raw) : [];
        
        const rawPendentes = localStorage.getItem(KEY_PENDENTES);
        pontosPendentes = rawPendentes ? JSON.parse(rawPendentes) : [];
        
        atualizarContadorPontosHoje();
        atualizarStatusEnvio();
    }catch(e){ 
        pontosDia = []; 
        pontosPendentes = [];
    }
    atualizarVisualizacaoResumo();
}

function proximoPonto(){
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

function atualizarContadorPontosHoje(){
    const hoje = new Date().toLocaleDateString('pt-BR');
    const pontosHoje = pontosDia.filter(p => p.data === hoje).length;
    pontosHojeCountEl.textContent = `${pontosHoje}/8`;
}

function atualizarStatusEnvio() {
    const pendentesCount = pontosPendentes.length;
    if (pendentesCount > 0) {
        statusEnvioEl.textContent = `${pendentesCount} pendente(s)`;
        statusEnvioEl.style.color = '#ffd54f';
        infoSync.style.display = 'block';
        infoSync.innerHTML = `<strong>${pendentesCount} ponto(s) aguardando sincronização</strong>`;
    } else {
        statusEnvioEl.textContent = 'Sincronizado';
        statusEnvioEl.style.color = '#00c853';
        infoSync.style.display = 'none';
    }
}

// ============================
// SISTEMA GPS MELHORADO PARA ANDROID
// ============================
function atualizarStatusGPS(mensagem, sucesso = false) {
    gpsStatus.style.display = 'flex';
    gpsText.textContent = mensagem;
    
    if (sucesso) {
        gpsDot.classList.add('active');
        gpsDot.style.background = '#4caf50';
    } else {
        gpsDot.classList.remove('active');
        gpsDot.style.background = '#ff9800';
    }
}

function pararGPS() {
    if (gpsWatchId !== null) {
        navigator.geolocation.clearWatch(gpsWatchId);
        gpsWatchId = null;
    }
    gpsStatus.style.display = 'none';
}

function obterLocalizacaoAndroid() {
    return new Promise((resolve, reject) => {
        if (!navigator.geolocation) {
            reject(new Error('Geolocalização não suportada pelo navegador'));
            return;
        }

        // Mostrar status do GPS
        atualizarStatusGPS('Buscando sinal GPS...', false);
        
        let positionReceived = false;
        const startTime = Date.now();
        const timeout = 30000; // 30 segundos

        const options = {
            enableHighAccuracy: true,
            timeout: 30000,
            maximumAge: 0
        };

        gpsWatchId = navigator.geolocation.watchPosition(
            (position) => {
                if (positionReceived) return;
                positionReceived = true;
                
                pararGPS();
                
                const coords = position.coords;
                const accuracy = coords.accuracy;
                const tempoDecorrido = Date.now() - startTime;
                
                console.log('GPS obtido:', {
                    latitude: coords.latitude,
                    longitude: coords.longitude,
                    accuracy: accuracy,
                    tempo: tempoDecorrido + 'ms'
                });

                // Verificar precisão
                if (accuracy > 50) {
                    atualizarStatusGPS(`GPS com baixa precisão (${Math.round(accuracy)}m)`, true);
                    showToast(`GPS com baixa precisão (${Math.round(accuracy)}m). Melhore o sinal.`, 'warning', 4000);
                } else {
                    atualizarStatusGPS(`GPS OK (${Math.round(accuracy)}m)`, true);
                }

                const locationData = {
                    latitude: coords.latitude,
                    longitude: coords.longitude,
                    accuracy: accuracy,
                    timestamp: position.timestamp
                };

                resolve(locationData);
            },
            (error) => {
                if (positionReceived) return;
                
                pararGPS();
                
                let errorMessage = 'Erro no GPS: ';
                switch(error.code) {
                    case error.PERMISSION_DENIED:
                        errorMessage = 'Permissão de localização negada. ';
                        errorMessage += 'Toque em "PERMITIR" quando o navegador pedir acesso à localização.';
                        break;
                    case error.POSITION_UNAVAILABLE:
                        errorMessage = 'Localização indisponível. ';
                        errorMessage += 'Verifique se o GPS está ativado no dispositivo.';
                        break;
                    case error.TIMEOUT:
                        errorMessage = 'Tempo esgotado. ';
                        errorMessage += 'Saia para uma área aberta e tente novamente.';
                        break;
                    default:
                        errorMessage += error.message;
                }
                
                reject(new Error(errorMessage));
            },
            options
        );

        // Timeout adicional de segurança
        setTimeout(() => {
            if (!positionReceived) {
                pararGPS();
                reject(new Error('Tempo máximo de espera do GPS excedido. Verifique se está em área aberta.'));
            }
        }, timeout);
    });
}

// Função de teste do GPS
function testarGPS() {
    if (!navigator.geolocation) {
        showToast('GPS não suportado neste navegador', 'error', 4000);
        return;
    }

    showToast('Testando GPS...', 'info', 2000);
    
    obterLocalizacaoAndroid()
        .then((location) => {
            showToast(`GPS funcionando! Precisão: ${Math.round(location.accuracy)}m`, 'success', 5000);
        })
        .catch((error) => {
            showToast(error.message, 'error', 5000);
        });
}

// ============================
// WEBAPP - Envio para Google Sheets
// ============================
async function enviarParaWebApp(pontoObj) {
    try {
        statusEnvioEl.textContent = 'Enviando...';
        statusEnvioEl.style.color = '#ff9800';
        
        // Converter blob para base64
        const base64 = await blobToDataURL(pontoObj.fotoBlob);

        // Preparar payload CORRETO
        const payload = {
            secret: WEBAPP_SECRET,
            nome: pontoObj.nome,
            cargo: pontoObj.cargo,
            telefone: pontoObj.telefone, // Usar telefone sem formatação
            tipo: pontoObj.tipo,
            numero: pontoObj.numero,
            data: pontoObj.data,
            hora: pontoObj.hora,
            observacoes: pontoObj.observacoes,
            localizacao: {
                latitude: pontoObj.localizacao.latitude,
                longitude: pontoObj.localizacao.longitude,
                accuracy: pontoObj.localizacao.accuracy
            },
            deviceId: pontoObj._id,
            selfieBase64: base64
        };

        console.log('Enviando para WebApp:', payload);

        // Fazer requisição com timeout
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), 10000); // 10 segundos

        const response = await fetch(WEBAPP_URL, {
            method: 'POST',
            body: JSON.stringify(payload),
            headers: { 
                'Content-Type': 'application/json'
            },
            signal: controller.signal
        });
        
        clearTimeout(timeoutId);
        
        const result = await response.json();
        console.log('Resposta do WebApp:', result);
        
        if (result.success) {
            showToast('✅ Ponto enviado para planilha!', 'success', 3000);
            return true;
        } else {
            throw new Error(result.msg || 'Erro desconhecido do WebApp');
        }
        
    } catch (error) {
        console.error('Erro ao enviar para WebApp:', error);
        
        if (error.name === 'AbortError') {
            throw new Error('SEM_CONEXAO: Timeout na conexão');
        } else if (error.message.includes('Failed to fetch') || error.message.includes('Network')) {
            throw new Error('SEM_CONEXAO: Sem conexão com internet');
        } else {
            throw new Error('ERRO_ENVIO: ' + error.message);
        }
    }
}

// ============================
// Sistema de Sincronização
// ============================
async function sincronizarPendentes() {
    if (pontosPendentes.length === 0) {
        showToast('Nenhum ponto pendente para sincronizar', 'info', 3000);
        return;
    }

    if (!navigator.onLine) {
        showToast('📵 Sem conexão com internet', 'error', 3000);
        return;
    }

    infoSync.innerHTML = '<strong>🔄 Sincronizando pontos pendentes...</strong>';
    botao.disabled = true;
    
    let sucessos = 0;
    let erros = 0;
    const pontosParaRemover = [];

    for (let i = 0; i < pontosPendentes.length; i++) {
        const ponto = pontosPendentes[i];
        
        try {
            console.log(`Sincronizando ponto ${i + 1}/${pontosPendentes.length}:`, ponto.tipo);
            await enviarParaWebApp(ponto);
            
            // Marcar para remover
            pontosParaRemover.push(ponto._id);
            sucessos++;
            
            // Atualiza o ponto na lista principal
            const pontoIndex = pontosDia.findIndex(p => p._id === ponto._id);
            if (pontoIndex !== -1) {
                pontosDia[pontoIndex].enviado = true;
            }
            
            showToast(`✅ ${ponto.tipo} sincronizado!`, 'success', 2000);
            
        } catch (error) {
            console.error(`❌ Erro ao sincronizar ponto ${ponto._id}:`, error);
            erros++;
            
            if (!error.message.includes('SEM_CONEXAO')) {
                // Se não for erro de conexão, marca para remover
                pontosParaRemover.push(ponto._id);
            }
        }
        
        // Pequena pausa entre envios
        await new Promise(resolve => setTimeout(resolve, 1000));
    }

    // Remover pontos processados da lista de pendentes
    pontosPendentes = pontosPendentes.filter(p => !pontosParaRemover.includes(p._id));

    // Salvar alterações
    localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
    localStorage.setItem(KEY_PENDENTES, JSON.stringify(pontosPendentes));
    
    atualizarVisualizacaoResumo();
    atualizarStatusEnvio();
    botao.disabled = false;
    
    if (sucessos > 0) {
        showToast(`🎉 ${sucessos} ponto(s) sincronizado(s) com sucesso!`, 'success', 5000);
    }
    if (erros > 0) {
        showToast(`⚠️ ${erros} ponto(s) falharam na sincronização`, 'warning', 5000);
    }
    
    if (pontosPendentes.length === 0) {
        infoSync.style.display = 'none';
    }
}

// Sincronização automática quando online
window.addEventListener('online', () => {
    showToast('Conexão restaurada - sincronizando...', 'info', 3000);
    setTimeout(sincronizarPendentes, 2000);
});

// ============================
// Processamento de Imagem
// ============================
function compressImageFileToJpegBlob(file, maxWidth = 800, quality = 0.75){
    return new Promise((resolve,reject)=>{
        const reader = new FileReader();
        reader.onload = () => {
            const img = new Image();
            img.onload = () => {
                const ratio = img.width / img.height;
                const width = img.width > maxWidth ? maxWidth : img.width;
                const height = Math.round(width / ratio);
                const canvas = document.createElement('canvas');
                canvas.width = width;
                canvas.height = height;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(img, 0, 0, width, height);
                canvas.toBlob((blob) => {
                    if(blob) resolve(blob);
                    else reject(new Error('Falha ao comprimir imagem'));
                }, 'image/jpeg', quality);
            };
            img.onerror = (e)=> reject(e);
            img.src = reader.result;
        };
        reader.onerror = (e)=> reject(e);
        reader.readAsDataURL(file);
    });
}

function blobToDataURL(blob){
    return new Promise((resolve,reject)=>{
        const reader = new FileReader();
        reader.onload = ()=> resolve(reader.result);
        reader.onerror = ()=> reject(new Error('Falha ao converter imagem'));
        reader.readAsDataURL(blob);
    });
}

// ============================
// Bater Ponto - Fluxo Principal
// ============================
botao.addEventListener('click', async ()=>{
    if (isProcessing) {
        showToast('Aguarde o processamento atual', 'error', 3000);
        return;
    }

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

    isProcessing = true;
    botao.disabled = true;
    botao.classList.add('processing');
    warnGPS.style.display = 'none';
    mensagemConfirmacao.textContent = '';

    try {
        // 1. Obter localização (SISTEMA GPS MELHORADO)
        mensagemConfirmacao.textContent = 'Obtendo localização GPS...';
        const pos = await obterLocalizacaoAndroid();
        
        // 2. Tirar foto
        mensagemConfirmacao.textContent = 'Abrindo câmera...';
        const selfieFile = await abrirCameraManual();
        
        // 3. Processar dados
        mensagemConfirmacao.textContent = 'Processando...';
        
        const compressedBlob = await compressImageFileToJpegBlob(selfieFile);
        const thumbDataUrl = await blobToDataURL(compressedBlob);
        
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
            enviado: false, // Inicialmente não enviado
            fotoBlob: compressedBlob
        };

        // 4. Tentar enviar para WebApp
        let enviadoComSucesso = false;
        
        if (navigator.onLine) {
            try {
                await enviarParaWebApp(ponto);
                ponto.enviado = true;
                enviadoComSucesso = true;
            } catch (error) {
                if (error.message.includes('SEM_CONEXAO')) {
                    // Salva como pendente se sem conexão
                    pontosPendentes.push(ponto);
                    showToast('Ponto salvo localmente (sem conexão)', 'info', 3000);
                } else {
                    // Outros erros também salvam como pendente
                    pontosPendentes.push(ponto);
                    showToast('Ponto salvo (erro no envio)', 'warning', 3000);
                }
            }
        } else {
            // Sem internet, salva como pendente
            pontosPendentes.push(ponto);
            showToast('Ponto salvo offline', 'info', 3000);
        }

        // Salvar localmente
        pontosDia.push(ponto);
        localStorage.setItem(KEY_PONTOS_DIA, JSON.stringify(pontosDia));
        localStorage.setItem(KEY_PENDENTES, JSON.stringify(pontosPendentes));
        
        // Atualizar UI
        lastThumb.src = thumbDataUrl; 
        lastThumb.style.display = 'inline-block';
        atualizarVisualizacaoResumo();
        atualizarContadorPontosHoje();
        atualizarStatusEnvio();

        observacoesEl.value = '';
        
        if (enviadoComSucesso) {
            mensagemConfirmacao.textContent = `${ponto.tipo} registrada e enviada!`;
        } else {
            mensagemConfirmacao.textContent = `${ponto.tipo} salva (pendente envio)`;
        }

    } catch (err) {
        console.error('Erro no processo:', err);
        
        if (err.message.includes('câmera')) {
            warnGPS.style.display = 'block';
            warnGPS.innerHTML = `
                <strong>Erro na câmera:</strong> ${err.message}<br>
                <small>• Verifique as permissões da câmera</small>
            `;
        } else if (err.message.includes('GPS') || err.message.includes('localização')) {
            warnGPS.style.display = 'block';
            warnGPS.innerHTML = `
                <strong>Falha na localização:</strong> ${err.message}<br>
                <small>• Toque em "PERMITIR" quando pedir acesso<br>
                • Ative o GPS do dispositivo<br>
                • Use em área aberta</small>
            `;
        } else if (err.message.includes('cancelada')) {
            mensagemConfirmacao.textContent = 'Selfie cancelada';
        } else {
            mensagemConfirmacao.textContent = 'Falha no processo. Tente novamente.';
            showToast('Erro: ' + err.message, 'error', 5000);
        }
    } finally {
        isProcessing = false;
        botao.disabled = false;
        botao.classList.remove('processing');
        pararGPS(); // Garantir que o GPS seja parado
    }
});

// ============================
// Visualização UI
// ============================
function atualizarVisualizacaoResumo(){
    let html = '<h3 style="margin:8px 0 6px 0;">Pontos Registrados</h3>';
    if(pontosDia.length===0) {
        html += '<div style="padding:8px;background:rgba(255,255,255,0.03);border-radius:8px;">Nenhum ponto registrado ainda.</div>';
    } else {
        html += '<ul style="padding-left:14px; margin:0;">';
        pontosDia.slice().reverse().forEach(p=>{
            const coord = p.localizacao ? ` • ${p.localizacao.latitude.toFixed(5)},${p.localizacao.longitude.toFixed(5)}` : '';
            const enviadoBadge = p.enviado ? '✅' : '⏳';
            const isPendente = pontosPendentes.some(pendente => pendente._id === p._id);
            const statusText = p.enviado ? 'Enviado' : (isPendente ? 'Pendente' : 'Salvo');
            
            html += `<li style="font-size:14px;margin-bottom:8px;background:rgba(255,255,255,0.03);padding:8px;border-radius:8px;display:flex;align-items:center;justify-content:space-between">
                <div>
                <strong>${p.tipo}</strong><br>
                <small>${p.data} ${p.hora}${coord}</small><br>
                <small style="color:#ffd;">${p.observacoes || ''}</small><br>
                <small style="color:#${p.enviado ? '4caf50' : 'ff9800'};">${statusText}</small>
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

// ============================
// Funções do Menu
// ============================
function mostrarPontoDia(){ 
    atualizarVisualizacaoResumo(); 
}

function mostrarPontoMes(){
    let html = '<h3>Ponto do Mês</h3>';
    if(pontosDia.length===0) {
        html += '<div>Nenhum ponto registrado.</div>';
    } else {
        html += '<ul style="padding-left:14px;">';
        pontosDia.slice().reverse().forEach(p => {
            html += `<li style="margin-bottom:6px;">${p.data} ${p.hora} — ${p.tipo}</li>`;
        });
        html += '</ul>';
    }
    visualizacaoPontos.innerHTML = html;
}

function abrirJustificativa(){ 
    document.getElementById('modalJustificativa').style.display='flex'; 
}

function fecharJustificativa(){ 
    document.getElementById('modalJustificativa').style.display='none'; 
    document.getElementById('justText').value = '';
}

function enviarJustificativa(){
    const txt = document.getElementById('justText').value.trim();
    if(txt===''){ 
        showToast('Digite uma justificativa.', 'error', 3000); 
        return; 
    }
    
    // Simular envio (substituir por sua lógica real)
    showToast('Justificativa enviada com sucesso!', 'success', 3000);
    document.getElementById('justText').value='';
    fecharJustificativa();
}

function exportarDados() {
    if (pontosDia.length === 0) {
        showToast('Nenhum dado para exportar', 'info', 3000);
        return;
    }
    
    const dados = {
        usuario: usuarioCadastrado,
        pontos: pontosDia,
        pendentes: pontosPendentes,
        exportadoEm: new Date().toISOString()
    };
    
    const blob = new Blob([JSON.stringify(dados, null, 2)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `pontos_${usuarioCadastrado.nome}_${new Date().toLocaleDateString('pt-BR')}.json`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
    
    showToast('Dados exportados com sucesso!', 'success', 3000);
}

// ============================
// Menu toggle
// ============================
const menuBtn = document.getElementById('menuBtn');
const menuOptions = document.getElementById('menuOptions');
menuBtn.addEventListener('click', (e)=>{
    e.stopPropagation();
    const visible = menuOptions.style.display === 'flex';
    menuOptions.style.display = visible ? 'none' : 'flex';
});
document.addEventListener('click', (e)=>{
    if(!menuOptions.contains(e.target) && e.target !== menuBtn){
        menuOptions.style.display = 'none';
    }
});

// ============================
// Relógio
// ============================
function atualizarRelogio(){
    const agora = new Date();
    relogioEl.textContent = `${String(agora.getHours()).padStart(2,'0')}:${String(agora.getMinutes()).padStart(2,'0')}:${String(agora.getSeconds()).padStart(2,'0')}`;
}
setInterval(atualizarRelogio,1000);
atualizarRelogio();

// ============================
// Inicialização
// ============================
function inicializar(){
    detectarAndroid();
    verificarCadastro();
    carregarLocalStorage();
    
    if (!navigator.geolocation) {
        showToast('Seu navegador não suporta GPS', 'error', 5000);
    }
    
    // Sincronização automática a cada 2 minutos
    setInterval(() => {
        if (navigator.onLine && pontosPendentes.length > 0) {
            sincronizarPendentes();
        }
    }, 120000);
}

inicializar();
</script>
</body>
</html>
