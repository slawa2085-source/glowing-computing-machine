# glowing-computing-machine
<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>EduAI Google Style</title>

  <!-- PWA -->
  <link rel="manifest" href="manifest.json">
  <meta name="theme-color" content="#4285F4">

  <!-- OCR -->
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>

  <style>
    body { margin:0; font-family: Arial, sans-serif; background:#fff; }
    .container { min-height:100vh; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:20px; }
    .logo { font-size:42px; margin-bottom:20px; }
    .logo span:nth-child(1){color:#4285F4}
    .logo span:nth-child(2){color:#DB4437}
    .logo span:nth-child(3){color:#F4B400}
    .logo span:nth-child(4){color:#4285F4}
    .logo span:nth-child(5){color:#0F9D58}
    .logo span:nth-child(6){color:#DB4437}

    input, textarea { width:90%; max-width:500px; padding:14px 18px; font-size:16px; border-radius:24px; border:1px solid #ddd; margin-bottom:10px; }

    .buttons { display:flex; flex-wrap:wrap; justify-content:center; }
    button { margin:6px; padding:10px 18px; border:none; border-radius:20px; background:#4285F4; color:white; font-size:14px; cursor:pointer; }

    .results { max-width:700px; width:90%; margin-top:30px; }
    .card { background:#f1f3f4; border-radius:16px; padding:16px; margin-bottom:20px; }
    .title { font-weight:bold; margin-bottom:8px; }
    small { color:#555; }
  </style>
</head>
<body>
  <div class="container">
    <div class="logo">
      <span>E</span><span>d</span><span>u</span><span>A</span><span>I</span><span>+</span>
    </div>

    <textarea id="query" rows="2" placeholder="Zadaj pytanie lub wklej treść zadania..."></textarea>

    <input type="file" accept="image/*" capture="environment" onchange="doOCR(event)">
    <small>📸 Zrób zdjęcie zadania – tekst zostanie rozpoznany</small>

    <div class="buttons">
      <button onclick="wikiSearch()">🔍 Szukaj</button>
      <button onclick="aiAnswer()">🤖 AI – odpowiedź</button>
      <button onclick="aiSteps()">🧮 Krok po kroku</button>
    </div>

    <div class="results" id="results"></div>
  </div>

<script>
/* ---------------- OCR ---------------- */
function doOCR(event){
  const file = event.target.files[0];
  if(!file) return;
  const results = document.getElementById('results');
  results.innerHTML = '<div class="card">📖 Rozpoznawanie tekstu...</div>';

  Tesseract.recognize(file, 'pol').then(({ data: { text } }) => {
    document.getElementById('query').value = text.trim();
    results.innerHTML = '<div class="card">✅ Tekst rozpoznany. Możesz użyć AI.</div>';
  });
}

/* ---------------- Wikipedia ---------------- */
async function wikiSearch(){
  const q = query.value.trim();
  if(!q) return;
  results.innerHTML = 'Szukam...';
  const url = `https://pl.wikipedia.org/w/api.php?action=query&list=search&srsearch=${encodeURIComponent(q)}&format=json&origin=*`;
  const res = await fetch(url);
  const data = await res.json();
  results.innerHTML = '';
  data.query.search.slice(0,5).forEach(item => {
    results.innerHTML += `<div class="card"><b>${item.title}</b><p>${item.snippet}...</p></div>`;
  });
}

/* ---------------- AI (ChatGPT API placeholder) ---------------- */
async function aiAnswer(){
  const q = query.value.trim();
  if(!q) return;
  results.innerHTML = `<div class="card"><div class="title">🤖 Odpowiedź AI</div><p>Tu podłączysz prawdziwe ChatGPT API (OpenAI).<br>AI odpowiada prostym językiem szkolnym.</p><p><b>Pytanie:</b> ${q}</p></div>`;
}

async function aiSteps(){
  const q = query.value.trim();
  if(!q) return;
  results.innerHTML = `<div class="card"><div class="title">🧮 Rozwiązanie krok po kroku</div><ol><li>Zrozumienie treści zadania</li><li>Wybór metody</li><li>Obliczenia</li><li>Odpowiedź końcowa</li></ol><p>(Tryb szkolny – do połączenia z ChatGPT)</p></div>`;
}

/* ---------------- Service Worker ---------------- */
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('sw.js');
}
</script>
</body>
</html>
