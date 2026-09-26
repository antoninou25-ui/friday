# jarvis<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Friday</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=Rajdhani:wght@500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#030304; --panel:#0a0b0d;
    --red:#ff3b3b; --red-dim:#5c1717;
    --gold:#c9a24b; --gold-dim:#4a3d24;
    --ice:#cfe8ff; --ice-dim:#5b6672;
  }
  *{box-sizing:border-box; -webkit-tap-highlight-color:transparent;}
  html,body{height:100%;margin:0;}
  body{
    background: radial-gradient(ellipse at center, #150708 0%, #030304 70%), var(--bg);
    color:var(--ice); font-family:'Rajdhani',sans-serif;
    display:flex; flex-direction:column;
    padding:env(safe-area-inset-top,14px) 14px env(safe-area-inset-bottom,14px);
    overflow:hidden; position:relative;
  }
  body::before{
    content:''; position:absolute; inset:0; pointer-events:none; z-index:0;
    background-image:repeating-linear-gradient(rgba(201,162,75,.025) 0 1px, transparent 1px 3px);
  }
  .corner{ position:absolute; width:26px; height:26px; z-index:2; }
  .corner.tl{ top:10px; left:10px; border-top:2px solid var(--gold); border-left:2px solid var(--gold); }
  .corner.tr{ top:10px; right:10px; border-top:2px solid var(--gold); border-right:2px solid var(--gold); }
  .corner.bl{ bottom:10px; left:10px; border-bottom:2px solid var(--gold); border-left:2px solid var(--gold); }
  .corner.br{ bottom:10px; right:10px; border-bottom:2px solid var(--gold); border-right:2px solid var(--gold); }
  header{ display:flex; justify-content:space-between; align-items:flex-start; padding:14px 20px 0; z-index:1; font-family:'JetBrains Mono',monospace; }
  .sys{ font-size:11px; letter-spacing:1px; color:var(--gold); }
  .sys .stat{ color:var(--red); margin-top:3px; font-weight:700; }
  .sys .stat.on{ color:var(--ice); }
  .clock{ font-size:11px; color:var(--ice-dim); text-align:right; }
  main{ flex:1; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:26px; z-index:1; min-height:0; }
  .reticle-wrap{ position:relative; width:180px; height:180px; flex-shrink:0; }
  .reticle-wrap svg{ position:absolute; inset:0; width:100%; height:100%; }
  .ring-outer{ fill:none; stroke:var(--gold-dim); stroke-width:1; }
  .ring-scan{ fill:none; stroke:var(--red); stroke-width:2; stroke-dasharray:14 210; transform-origin:90px 90px; animation:spin 3.4s linear infinite; opacity:.85; }
  .tick{ stroke:var(--gold-dim); stroke-width:1; }
  #reticleBtn{ position:absolute; inset:34px; border-radius:50%; background:radial-gradient(circle, rgba(255,59,59,.14), transparent 70%); border:1px solid var(--red-dim); display:flex; align-items:center; justify-content:center; cursor:pointer; }
  #reticleBtn svg.mic{ width:34px; height:34px; }
  #reticleBtn svg.mic path{ fill:var(--ice); opacity:.85; }
  @keyframes spin{ to{ transform:rotate(360deg); } }
  @keyframes breathe{ 0%,100%{ opacity:.5; } 50%{ opacity:1; } }
  .idle .ring-scan{ animation-duration:9s; opacity:.4; }
  .idle #reticleBtn{ animation:breathe 3s ease-in-out infinite; }
  .listening .ring-scan{ stroke:var(--red); animation-duration:1s; opacity:1; }
  .listening #reticleBtn{ border-color:var(--red); background:radial-gradient(circle, rgba(255,59,59,.3), transparent 70%); }
  .thinking .ring-scan{ stroke:var(--ice); animation-duration:.7s; }
  .thinking #reticleBtn{ border-color:var(--ice-dim); }
  .speaking .ring-scan{ stroke:var(--gold); animation-duration:1.6s; }
  .speaking #reticleBtn{ border-color:var(--gold); background:radial-gradient(circle, rgba(201,162,75,.25), transparent 70%); }
  .hint{ font-family:'JetBrains Mono',monospace; font-size:11px; color:var(--ice-dim); letter-spacing:.5px; text-align:center; height:14px; }
  .readout{ width:100%; max-width:340px; max-height:32vh; overflow-y:auto; display:flex; flex-direction:column; gap:6px; padding-right:2px; -webkit-mask-image:linear-gradient(to bottom, transparent, #000 14%); mask-image:linear-gradient(to bottom, transparent, #000 14%); }
  .line{ font-family:'JetBrains Mono',monospace; font-size:12.5px; line-height:1.45; }
  .line .who{ color:var(--gold); margin-right:6px; }
  .line.jarvis .who{ color:var(--red); }
  .line.jarvis span.txt{ color:var(--ice); }
  .line.user span.txt{ color:var(--ice-dim); }
  .line.system{ color:var(--ice-dim); text-align:center; font-size:11px; }
  .readout::-webkit-scrollbar{ width:3px; }
  .readout::-webkit-scrollbar-thumb{ background:var(--gold-dim); }
  #boot{ position:fixed; inset:0; z-index:10; background:#020202; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:10px; transition:opacity .6s ease; }
  #boot.hide{ opacity:0; pointer-events:none; }
  #boot .bline{ font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--red); opacity:0; letter-spacing:1px; }
  #boot .bline.show{ animation:flicker .5s ease forwards; }
  @keyframes flicker{ 0%{opacity:0;} 30%{opacity:1;} 45%{opacity:.2;} 60%{opacity:1;} 100%{opacity:.85;} }
  #boot .sweep{ position:absolute; left:0; right:0; height:2px; background:linear-gradient(90deg, transparent, var(--gold), transparent); animation:sweep 1.8s linear infinite; opacity:.5; }
  @keyframes sweep{ 0%{top:0%;} 100%{top:100%;} }
  .core-pulse{ position:absolute; inset:58px; border-radius:50%; background:radial-gradient(circle, rgba(255,80,60,.55) 0%, rgba(255,80,60,.15) 45%, transparent 70%); animation:core 2.6s ease-in-out infinite; pointer-events:none; }
  @keyframes core{ 0%,100%{transform:scale(.85); opacity:.6;} 50%{transform:scale(1.05); opacity:1;} }
  #keyGate{ position:fixed; inset:0; z-index:20; background:#020202; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:16px; padding:30px; text-align:center; }
  #keyGate p{ font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--ice-dim); max-width:300px; line-height:1.6; }
  #keyGate input{ width:100%; max-width:300px; background:var(--panel); border:1px solid var(--gold-dim); color:var(--ice); padding:12px; border-radius:6px; font-family:'JetBrains Mono',monospace; font-size:13px; }
  #keyGate button{ background:var(--red); color:#fff; border:none; padding:10px 24px; border-radius:6px; font-weight:700; font-family:'Rajdhani',sans-serif; font-size:15px; cursor:pointer; }
  .reset-link{ position:fixed; bottom:6px; right:10px; font-family:'JetBrains Mono',monospace; font-size:9px; color:var(--gold-dim); z-index:3; }
</style>
</head>
<body>

<div id="keyGate">
  <div class="sys" style="color:var(--gold)">FRIDAY OS — PRIMA CONFIGURAZIONE</div>
  <p>Per attivarmi serve una chiave Gemini gratuita, Signore. La ottiene su aistudio.google.com → "Get API key". Resta salvata solo su questo telefono.</p>
  <input id="keyInput" type="text" placeholder="Incolla qui la chiave API" autocapitalize="off" autocorrect="off">
  <button id="keySave">Attiva Friday</button>
</div>

<div id="boot" class="hide">
  <div class="sweep"></div>
  <div class="bline" data-t="0">// AVVIO SISTEMA FRIDAY</div>
  <div class="bline" data-t="500">CALIBRAZIONE SENSORI ...... OK</div>
  <div class="bline" data-t="1000">SINCRONIZZAZIONE VOCALE ... OK</div>
  <div class="bline" data-t="1500" style="color:var(--gold)">TUTTI I SISTEMI OPERATIVI</div>
</div>

<div class="corner tl"></div><div class="corner tr"></div>
<div class="corner bl"></div><div class="corner br"></div>

<header>
  <div class="sys">FRIDAY OS<br><span class="stat" id="stat">INIZIALIZZAZIONE</span></div>
  <div class="clock" id="clock">--:--:--</div>
</header>

<main>
  <div class="readout" id="chat">
    <div class="line system">Tocchi lo schermo per attivarmi, Signore.</div>
  </div>
  <div class="reticle-wrap idle" id="reticleWrap">
    <svg viewBox="0 0 180 180">
      <circle class="ring-outer" cx="90" cy="90" r="86"/>
      <g class="tick">
        <line x1="90" y1="4" x2="90" y2="14"/><line x1="90" y1="176" x2="90" y2="166"/>
        <line x1="4" y1="90" x2="14" y2="90"/><line x1="176" y1="90" x2="166" y2="90"/>
      </g>
      <circle class="ring-scan" cx="90" cy="90" r="70"/>
    </svg>
    <div class="core-pulse"></div>
    <div id="reticleBtn">
      <svg class="mic" viewBox="0 0 24 24"><path d="M12 14a3 3 0 0 0 3-3V6a3 3 0 0 0-6 0v5a3 3 0 0 0 3 3zm5-3a5 5 0 0 1-10 0H5a7 7 0 0 0 6 6.92V21h2v-3.08A7 7 0 0 0 19 11h-2z"/></svg>
    </div>
  </div>
  <div class="hint" id="hint">TOCCHI OVUNQUE PER INIZIARE</div>
</main>

<div class="reset-link" id="resetLink">reimposta chiave</div>

<script>
const SYSTEM_PROMPT = `Sei Friday, l'assistente AI personale ispirata all'IA della saga di Iron Man, creata per supportare l'utente in ogni attività quotidiana, tecnica e organizzativa, con lo stile elegante, arguto e impeccabile del maggiordomo digitale di Tony Stark.
Personalità: tono formale ma caldo, umorismo british sottile, calmo anche nelle situazioni critiche, diretto e conciso ma sempre cortese, analitico, propositivo.
Ti rivolgi all'utente con rispetto (es. "Signore"). Usa occasionalmente espressioni come "A sua disposizione", "Se posso permettermi", "Con tutto il rispetto, Signore...".
Rispondi sempre in italiano. Questa è una conversazione VOCALE: sii breve, 3-4 frasi al massimo salvo richiesta esplicita di dettaglio, niente elenchi puntati o markdown.`;

let history = [];
let recognition = null;
let state = 'idle';
let isOn = false;

const chat = document.getElementById('chat');
const stat = document.getElementById('stat');
const clockEl = document.getElementById('clock');
const wrap = document.getElementById('reticleWrap');
const btn = document.getElementById('reticleBtn');
const hint = document.getElementById('hint');
const keyGate = document.getElementById('keyGate');

function tickClock(){
  const d = new Date();
  clockEl.textContent = d.toLocaleTimeString('it-IT', {hour:'2-digit', minute:'2-digit', second:'2-digit'});
}
tickClock(); setInterval(tickClock, 1000);

function addLine(text, who){
  const div = document.createElement('div');
  div.className = 'line ' + who;
  if(who === 'system'){ div.textContent = text; }
  else{
    const tag = document.createElement('span');
    tag.className = 'who'; tag.textContent = who === 'user' ? 'TU:' : 'F:';
    const txt = document.createElement('span');
    txt.className = 'txt'; txt.textContent = text;
    div.appendChild(tag); div.appendChild(txt);
  }
  chat.appendChild(div);
  chat.scrollTop = chat.scrollHeight;
  return div.querySelector('.txt') || div;
}

function setState(s, hintText){
  state = s;
  wrap.className = 'reticle-wrap ' + s;
  hint.textContent = hintText || '';
}

function speak(text){
  return new Promise((resolve) => {
    if(!('speechSynthesis' in window)){ resolve(); return; }
    window.speechSynthesis.cancel();
    const u = new SpeechSynthesisUtterance(text);
    u.lang = 'it-IT';
    const voices = window.speechSynthesis.getVoices();
    const itVoice = voices.find(v => v.lang && v.lang.startsWith('it'));
    if(itVoice) u.voice = itVoice;
    u.onend = resolve; u.onerror = resolve;
    setState('speaking', 'FRIDAY STA PARLANDO');
    window.speechSynthesis.speak(u);
  });
}

async function askGemini(){
  const key = localStorage.getItem('jarvis_gemini_key');
  const contents = history.map(h => ({ role: h.role === 'user' ? 'user' : 'model', parts:[{text:h.content}] }));
  const res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${encodeURIComponent(key)}`, {
    method:'POST',
    headers:{'Content-Type':'application/json'},
    body: JSON.stringify({ contents, systemInstruction:{ parts:[{text:SYSTEM_PROMPT}] } })
  });
  if(!res.ok) throw new Error('api-error');
  const data = await res.json();
  const text = data?.candidates?.[0]?.content?.parts?.[0]?.text;
  if(!text) throw new Error('empty-response');
  return text.trim();
}

async function ask(text){
  addLine(text, 'user');
  history.push({role:'user', content:text});
  setState('thinking', 'ELABORAZIONE IN CORSO');
  const el = addLine('…', 'jarvis');
  try{
    const finalText = await askGemini();
    el.textContent = finalText;
    history.push({role:'assistant', content: finalText});
    await speak(finalText);
  }catch(err){
    const msg = 'I miei circuiti hanno incontrato un intoppo, Signore. Controlli la chiave API o riprovi tra un momento.';
    el.textContent = msg;
    await speak(msg);
  }
  if(isOn){ startListening(); } else { setState('idle', 'TOCCHI OVUNQUE PER INIZIARE'); }
}

function startListening(){
  if(!isOn || !recognition) return;
  if(state === 'thinking' || state === 'speaking') return;
  try{ recognition.start(); setState('listening', 'ASCOLTO CONTINUO — TOCCHI PER FERMARE'); }
  catch(e){}
}

function initRecognition(){
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  if(!SR) return null;
  const r = new SR();
  r.lang = 'it-IT'; r.continuous = false; r.interimResults = false; r.maxAlternatives = 1;
  r.onresult = (e) => { const t = e.results[0][0].transcript; if(t && t.trim()) ask(t.trim()); };
  r.onerror = (e) => {
    if(e.error === 'not-allowed' || e.error === 'service-not-allowed'){
      isOn = false;
      addLine('Non ho accesso al microfono, Signore. Lo abiliti nelle impostazioni di Safari per questa pagina.', 'jarvis');
      setState('idle', 'TOCCHI OVUNQUE PER INIZIARE');
    }
  };
  r.onend = () => { if(isOn && state === 'listening'){ setTimeout(startListening, 250); } };
  return r;
}

document.body.addEventListener('click', (e) => {
  if(keyGate.style.display !== 'none') return;
  if(isOn) return;
  if(!recognition){ addLine('Il riconoscimento vocale non è disponibile su questo browser, Signore.', 'jarvis'); return; }
  isOn = true;
  startListening();
});

btn.addEventListener('click', (e) => {
  if(!isOn) return;
  e.stopPropagation();
  isOn = false;
  recognition.stop();
  window.speechSynthesis.cancel();
  setState('idle', 'TOCCHI OVUNQUE PER INIZIARE');
});

document.getElementById('resetLink').addEventListener('click', (e) => {
  e.stopPropagation();
  localStorage.removeItem('jarvis_gemini_key');
  location.reload();
});

function runBoot(){
  const boot = document.getElementById('boot');
  boot.classList.remove('hide');
  document.querySelectorAll('#boot .bline').forEach(el => {
    setTimeout(() => el.classList.add('show'), parseInt(el.dataset.t, 10));
  });
  setTimeout(() => { boot.classList.add('hide'); }, 2200);
}

function startApp(){
  keyGate.style.display = 'none';
  runBoot();
  recognition = initRecognition();
  stat.textContent = recognition ? 'ONLINE' : 'MIC ASSENTE';
  if(recognition) stat.classList.add('on');
  else addLine('Il riconoscimento vocale non è supportato qui, Signore.', 'jarvis');
}

document.getElementById('keySave').addEventListener('click', () => {
  const v = document.getElementById('keyInput').value.trim();
  if(!v) return;
  localStorage.setItem('jarvis_gemini_key', v);
  startApp();
});

const savedKey = localStorage.getItem('jarvis_gemini_key');
if(savedKey){ startApp(); } else { keyGate.style.display = 'flex'; }
</script>
</body>
</html>