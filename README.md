# Traduttore-di-parolacce-
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Traduttore parolacce IT → EN (MVP)</title>
<style>
  body{font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial;max-width:780px;margin:16px auto;padding:0 14px;color:#111}
  header{margin-bottom:12px}
  textarea{width:100%;min-height:120px;padding:10px;font-size:16px;border:1px solid #ddd;border-radius:6px}
  button{padding:10px 14px;margin-top:10px;font-size:16px;border-radius:6px}
  .output{margin-top:14px;padding:12px;background:#f6f6f6;border-radius:8px}
  .list{margin-top:8px}
  .warn{background:#fff3cd;border:1px solid #ffeeba;padding:8px;border-radius:6px;margin-bottom:8px}
  code{background:#eee;padding:2px 6px;border-radius:4px}
  @media (max-width:420px){textarea{min-height:110px;font-size:15px}}
</style>
</head>
<body>
<header>
  <h2>Traduttore parolacce IT → EN</h2>
  <div class="warn">Attenzione: il testo può contenere linguaggio offensivo. Nessuna censura.</div>
</header><label>Inserisci parola o frase (italiano):</label>
<textarea id="input" placeholder="Es.: Che stronzo, vaffanculo!"></textarea>
<br>
<button id="btn">Traduci</button><div class="output" id="result" aria-live="polite" style="display:none">
  <strong>Traduzione letterale:</strong>
  <div id="translatedLiteral" style="margin:8px 0;font-size:17px"></div><strong>Traduzione naturale / idiomatica:</strong>  <div id="translatedNatural" style="margin:8px 0;font-size:17px"></div><script>
// Dizionario ampliato: chiave (italiano) -> { literal, natural, tone }
const dict = {
  "vaffanculo":             {literal:"fuck you",            natural:"fuck off",        tone:"offensivo"},
  "vai a cagare":           {literal:"go shit",             natural:"go to hell",      tone:"offensivo"},
  "che stronzo":            {literal:"what an asshole",     natural:"what a bastard",   tone:"offensivo"},
  "stronzo":                {literal:"asshole",             natural:"jerk/asshole",    tone:"offensivo"},
  "figlio di puttana":      {literal:"son of a whore",      natural:"son of a bitch",  tone:"offensivo"},
  "puttana":                {literal:"whore",               natural:"bitch (slur)",    tone:"offensivo"},
  "troia":                  {literal:"whore/slut",          natural:"slut",            tone:"offensivo"},
  "bastardo":               {literal:"bastard",             natural:"son of a bitch",  tone:"offensivo"},
  "merda":                  {literal:"shit",                natural:"shit",            tone:"offensivo"},
  "cazzo":                  {literal:"fuck",                natural:"fuck/damn",       tone:"volgare"},
  "coglione":               {literal:"testicle",            natural:"dickhead",        tone:"offensivo"},
  "testa di cazzo":         {literal:"dick's head",         natural:"dickhead",        tone:"offensivo"},
  "fottiti":                {literal:"fuck yourself",       natural:"fuck off",        tone:"offensivo"},
  "minchia":                {literal:"fuck (vulgar)",       natural:"fuck",            tone:"volgare"},
  "stronzata":              {literal:"bullshit",            natural:"crap/nonsense",   tone:"volgare"},
  "vai a quel paese":       {literal:"go to that country",   natural:"get lost",        tone:"mild"},
  "pezzente":               {literal:"pauper",              natural:"bum/lowlife",     tone:"offensivo"},
  "imbranato":              {literal:"clumsy",              natural:"klutz",           tone:"insulto leggero"},
  "idiota":                 {literal:"idiot",               natural:"idiot",           tone:"offensivo"},
  "stupido":                {literal:"stupid",              natural:"stupid",          tone:"offensivo"},
  "schifo":                 {literal:"disgust",             natural:"gross",           tone:"volgare"},
  "vile":                   {literal:"cowardly",            natural:"coward",          tone:"insulto"},
  "stronza":                {literal:"bitch/asshole",       natural:"bitch",           tone:"offensivo"},
  "rincoglionito":          {literal:"turned into a testicle", natural:"out of one's mind", tone:"offensivo"},
  "baldracca":              {literal:"tart/whore",          natural:"prostitute (slur)", tone:"offensivo"},
  "zingaro" :               {literal:"gypsy",               natural:"gypsy (slur)",    tone:"razzista"},
  "sporco":                 {literal:"dirty",               natural:"dirty",           tone:"offensivo"},
  "peggio di niente":       {literal:"worse than nothing",   natural:"worthless",       tone:"offensivo"}
};

// escape regex special chars
function escapeRegExp(s){ return s.replace(/[.*+?^${}()|[\]\\]/g,'\\$&'); }

// replace preserving order: più lunghe prima
function translateText(text, mode){
  // mode: 'literal' or 'natural'
  const keys = Object.keys(dict).sort((a,b)=>b.length - a.length);
  let out = text;
  const found = [];
  for(const k of keys){
    const pattern = '\\b' + escapeRegExp(k) + '\\b';
    const re = new RegExp(pattern,'gi');
    out = out.replace(re, (match)=>{
      // registra la forma trovata (minuscola)
      const keyLower = k.toLowerCase();
      if(!found.includes(keyLower)) found.push(keyLower);
      // restituisci la traduzione scelta
      return dict[k][mode];
    });
  }
  return {out, found};
}

document.getElementById('btn').addEventListener('click', ()=>{
  const input = document.getElementById('input').value.trim();
  if(!input){ document.getElementById('result').style.display='none'; return; }
  const literal = translateText(input,'literal');
  const natural = translateText(input,'natural');
  document.getElementById('translatedLiteral').textContent = literal.out;
  document.getElementById('translatedNatural').textContent = natural.out;

  const foundUnion = Array.from(new Set([...literal.found, ...natural.found]));
  const foundDiv = document.getElementById('found');
  if(foundUnion.length===0){
    foundDiv.innerHTML = '<em>Nessuna parola trovata nel dizionario.</em>';
  } else {
    let html = '<strong>Parole trovate:</strong><ul>';
    for(const k of foundUnion){
      const entry = dict[k] || dict[Object.keys(dict).find(x=>x.toLowerCase()===k)];
      if(entry){
        html += `<li><code>${k}</code> → letterale: <strong>${entry.literal}</strong> ; naturale: <strong>${entry.natural}</strong> ; registro: <em>${entry.tone}</em></li>`;
      } else {
        html += `<li><code>${k}</code> → (trovata)</li>`;
      }
    }
    html += '</ul>';
    foundDiv.innerHTML = html;
  }
  document.getElementById('result').style.display='block';
});

// Invio rapido da tastiera (mobile: Ctrl/Cmd+Enter)
document.getElementById('input').addEventListener('keydown', (e)=>{
  if(e.key === 'Enter' && (e.ctrlKey || e.metaKey)){
    e.preventDefault();
    document.getElementById('btn').click();
  }
});
</script>  <div class="list" id="found"></div>
</div><!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Traduttore di Parolacce Buffe</title>
<style>
  body { font-family: Arial; text-align: center; padding: 50px; background: #fefbd8; }
  input, button { font-size: 16px; padding: 10px; margin: 10px; }
  .result { margin-top: 20px; font-weight: bold; color: #2e8b57; font-size: 18px; }
</style>
</head>
<body>
<h1>Traduttore di Parolacce Buffe</h1>
<input type="text" id="wordInput" placeholder="Scrivi qui...">
<button onclick="translateWord()">Traduci!</button>
<div class="result" id="result"></div>

<body>
<html>