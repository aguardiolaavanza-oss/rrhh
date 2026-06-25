# rrhh
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Formulario Big Five - Proceso de selección</title>
<style>
  * { box-sizing: border-box; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    background: #f5f4f0;
    color: #1a1a1a;
    margin: 0;
    padding: 2rem 1rem;
  }
  .wrap { max-width: 720px; margin: 0 auto; }
  .card {
    background: #fff;
    border: 1px solid #e0ded4;
    border-radius: 12px;
    padding: 1.5rem 1.75rem;
    margin-bottom: 1.5rem;
  }
  h1 { font-size: 22px; font-weight: 600; margin: 0 0 .25rem; }
  .subtitle { color: #666; font-size: 14px; margin-bottom: 1.5rem; }
  label { font-size: 13px; color: #555; display: block; margin-bottom: 4px; }
  input[type="text"] {
    width: 100%; padding: 8px 10px; font-size: 14px;
    border: 1px solid #ccc; border-radius: 8px; margin-bottom: 1rem;
  }
  .item { padding: 14px 0; border-bottom: 1px solid #ececec; }
  .item:last-child { border-bottom: none; }
  .item p { margin: 0 0 8px; font-size: 15px; }
  .likert { display: flex; gap: 6px; }
  .likert button {
    flex: 1; padding: 8px 0; font-size: 13px; cursor: pointer;
    border: 1px solid #d5d3c8; border-radius: 8px; background: #fff;
    transition: all .12s;
  }
  .likert button:hover { background: #f0f0f0; }
  .likert button.selected { background: #2563eb; color: #fff; border-color: #2563eb; }
  .footer-row {
    display: flex; justify-content: space-between; align-items: center;
    margin-top: 1rem; padding-top: 1rem; border-top: 1px solid #ececec;
  }
  .progress { font-size: 13px; color: #666; }
  button.primary {
    background: #2563eb; color: #fff; border: none; border-radius: 8px;
    padding: 10px 18px; font-size: 14px; cursor: pointer; font-weight: 500;
  }
  button.primary:hover { background: #1d4ed8; }
  button.secondary {
    background: #fff; border: 1px solid #ccc; border-radius: 8px;
    padding: 8px 14px; font-size: 13px; cursor: pointer; margin-right: 8px;
  }
  button.secondary:hover { background: #f0f0f0; }
  .results-grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 10px; margin-top: 1rem;
  }
  .factor-card {
    border-radius: 8px; padding: 12px 14px;
  }
  .factor-card p.label { font-size: 12px; margin: 0 0 4px; opacity: .85; }
  .factor-card p.value { font-size: 22px; font-weight: 600; margin: 0; }
  table { width: 100%; border-collapse: collapse; font-size: 13px; margin-top: 1rem; }
  th, td { text-align: left; padding: 8px 6px; border-bottom: 1px solid #ececec; }
  th { color: #666; font-weight: 500; }
  .note { font-size: 12px; color: #888; margin-top: .75rem; }
  .empty { font-size: 13px; color: #888; padding: 1rem 0; }
</style>
</head>
<body>
<div class="wrap">

  <div class="card">
    <h1>Evaluación de personalidad — Modelo Big Five</h1>
    <p class="subtitle">Indica tu grado de acuerdo con cada afirmación (1 = totalmente en desacuerdo, 5 = totalmente de acuerdo). Tus respuestas se guardan solo en este navegador.</p>

    <label for="candidateName">Nombre del candidato/a</label>
    <input type="text" id="candidateName" placeholder="Nombre y apellidos" />

    <div id="formItems"></div>

    <div class="footer-row">
      <span class="progress" id="progress">0 / 44 respondidas</span>
      <button class="primary" id="submitBtn">Calcular y guardar resultados</button>
    </div>
  </div>

  <div class="card" id="thankYouCard" style="display:none; text-align:center;">
    <h1 style="font-size:18px;">Gracias por completar el cuestionario</h1>
    <p class="subtitle" style="margin-bottom:0;">Tus respuestas se han registrado correctamente. El equipo de selección se pondrá en contacto contigo si es necesario.</p>
  </div>

  <div class="card" id="adminGateCard">
    <div style="display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;">
      <p style="font-size:13px; color:#666; margin:0;">Acceso restringido al equipo de selección</p>
      <div style="display:flex; gap:8px;">
        <input type="password" id="adminPassword" placeholder="Contraseña" style="width:140px; margin-bottom:0;" />
        <button class="secondary" id="adminLoginBtn" style="margin-right:0;">Ver resultados</button>
      </div>
    </div>
  </div>

  <div class="card" id="resultsCard" style="display:none;">
    <h1 id="resultsTitle" style="font-size:18px;"></h1>
    <div class="results-grid" id="resultsGrid"></div>
    <p class="note">Porcentaje relativo dentro de cada factor (0% = polo bajo, 100% = polo alto). Cuestionario basado en ítems de dominio público (IPIP); no sustituye a una prueba psicométrica validada para decisiones de contratación.</p>
  </div>

  <div class="card" id="candidatesCard" style="display:none;">
    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:1rem;">
      <h1 style="font-size:18px; margin:0;">Candidatos evaluados</h1>
      <div>
        <button class="secondary" id="exportBtn">Exportar CSV</button>
        <button class="secondary" id="clearBtn">Borrar todo</button>
      </div>
    </div>
    <div id="candidatesTable"><p class="empty">Todavía no hay candidatos guardados.</p></div>
  </div>

</div>

<script>
const items = [
  {t:"Soy el alma de la fiesta.", d:"E"},
  {t:"Siento poca preocupación por los demás.", d:"A", r:true},
  {t:"Siempre estoy preparado/a.", d:"C"},
  {t:"Me estreso fácilmente.", d:"N"},
  {t:"Tengo un vocabulario muy rico.", d:"O"},
  {t:"No hablo mucho.", d:"E", r:true},
  {t:"Me intereso por la vida de los demás.", d:"A"},
  {t:"Dejo mis pertenencias por cualquier sitio.", d:"C", r:true},
  {t:"Me relajo la mayor parte del tiempo.", d:"N", r:true},
  {t:"Tengo dificultad para entender ideas abstractas.", d:"O", r:true},
  {t:"Me siento cómodo/a entre la gente.", d:"E"},
  {t:"Insulto a las personas.", d:"A", r:true},
  {t:"Presto atención a los detalles.", d:"C"},
  {t:"Me preocupo por las cosas.", d:"N"},
  {t:"Tengo una imaginación viva.", d:"O"},
  {t:"Me mantengo en un segundo plano.", d:"E", r:true},
  {t:"Tengo buenos sentimientos hacia casi todo el mundo.", d:"A"},
  {t:"Hago las tareas de inmediato.", d:"C"},
  {t:"Me siento fácilmente alterado/a.", d:"N"},
  {t:"Tengo excelentes ideas.", d:"O"},
  {t:"Hablo con muchas personas distintas en las reuniones.", d:"E"},
  {t:"No me interesan los problemas de los demás.", d:"A", r:true},
  {t:"Sigo un horario establecido.", d:"C"},
  {t:"Me altero con facilidad ante imprevistos.", d:"N"},
  {t:"No tengo mucha imaginación.", d:"O", r:true},
  {t:"No me gusta atraer la atención.", d:"E", r:true},
  {t:"Me tomo tiempo para los demás.", d:"A"},
  {t:"Soy meticuloso/a en mi trabajo.", d:"C"},
  {t:"Cambio de ánimo con frecuencia.", d:"N"},
  {t:"Soy rápido/a para entender las cosas.", d:"O"},
  {t:"Estoy callado/a con personas que no conozco.", d:"E", r:true},
  {t:"Siento poca empatía por los demás.", d:"A", r:true},
  {t:"Dejo las cosas inacabadas.", d:"C", r:true},
  {t:"Me siento mal con frecuencia sin razón aparente.", d:"N"},
  {t:"Uso palabras complejas con naturalidad.", d:"O"},
  {t:"No me importa ser el centro de atención.", d:"E"},
  {t:"Siento simpatía por las personas que pasan dificultades.", d:"A"},
  {t:"Sigo un plan de trabajo claro.", d:"C"},
  {t:"Me irrito con facilidad.", d:"N"},
  {t:"Paso tiempo reflexionando sobre las cosas.", d:"O"},
  {t:"Inicio las conversaciones.", d:"E"},
  {t:"No estoy realmente interesado/a en los demás.", d:"A", r:true},
  {t:"Cometo errores por descuido.", d:"C", r:true},
  {t:"A menudo me siento inseguro/a.", d:"N"}
];

const dims = {
  E:{name:"Extraversión", color:"#fde7e0", text:"#7a2e10"},
  A:{name:"Amabilidad", color:"#e0f5ec", text:"#0c5c44"},
  C:{name:"Responsabilidad", color:"#eae7fb", text:"#3a2f7a"},
  N:{name:"Estabilidad emocional", color:"#e3f0fb", text:"#0d4a85"},
  O:{name:"Apertura", color:"#fcf0db", text:"#7a4e08"}
};

const STORAGE_KEY = "bigfive_candidates";

const formItems = document.getElementById('formItems');
items.forEach((item, idx) => {
  const row = document.createElement('div');
  row.className = "item";
  row.innerHTML = `
    <p>${idx+1}. ${item.t}</p>
    <div class="likert" data-idx="${idx}">
      ${[1,2,3,4,5].map(v => `<button type="button" data-idx="${idx}" data-val="${v}">${v}</button>`).join('')}
    </div>
  `;
  formItems.appendChild(row);
});

const answers = new Array(items.length).fill(null);

formItems.addEventListener('click', (e) => {
  const btn = e.target.closest('button');
  if (!btn) return;
  const idx = parseInt(btn.dataset.idx);
  const val = parseInt(btn.dataset.val);
  answers[idx] = val;
  const group = formItems.querySelectorAll(`.likert[data-idx="${idx}"] button`);
  group.forEach(b => b.classList.toggle('selected', parseInt(b.dataset.val) === val));
  updateProgress();
});

function updateProgress() {
  const answered = answers.filter(a => a !== null).length;
  document.getElementById('progress').textContent = `${answered} / ${items.length} respondidas`;
}

function computeScores() {
  const sums = {E:0,A:0,C:0,N:0,O:0};
  const counts = {E:0,A:0,C:0,N:0,O:0};
  items.forEach((item, idx) => {
    let v = answers[idx];
    if (item.r) v = 6 - v;
    sums[item.d] += v;
    counts[item.d] += 1;
  });
  const pct = {};
  Object.keys(dims).forEach(k => {
    const avg = sums[k] / counts[k];
    pct[k] = Math.round(((avg - 1) / 4) * 100);
  });
  return pct;
}

function loadCandidates() {
  try {
    return JSON.parse(localStorage.getItem(STORAGE_KEY)) || [];
  } catch (e) {
    return [];
  }
}

function saveCandidates(list) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(list));
}

function renderCandidatesTable() {
  const list = loadCandidates();
  const container = document.getElementById('candidatesTable');
  if (list.length === 0) {
    container.innerHTML = '<p class="empty">Todavía no hay candidatos guardados.</p>';
    return;
  }
  let html = '<table><thead><tr><th>Candidato</th><th>Fecha</th>';
  Object.keys(dims).forEach(k => html += `<th>${dims[k].name}</th>`);
  html += '</tr></thead><tbody>';
  list.forEach(c => {
    html += `<tr><td>${c.name}</td><td>${c.date}</td>`;
    Object.keys(dims).forEach(k => html += `<td>${c.scores[k]}%</td>`);
    html += '</tr>';
  });
  html += '</tbody></table>';
  container.innerHTML = html;
}

const ADMIN_PASSWORD = "rrhh2026"; // cámbiala por la que prefieras
let isAdmin = false;

document.getElementById('submitBtn').addEventListener('click', () => {
  const answered = answers.filter(a => a !== null).length;
  if (answered < items.length) {
    alert(`Faltan ${items.length - answered} preguntas por responder.`);
    return;
  }
  const name = document.getElementById('candidateName').value.trim() || 'Candidato sin nombre';
  const scores = computeScores();
  const date = new Date().toLocaleDateString('es-ES');

  const list = loadCandidates();
  list.push({ name, date, scores });
  saveCandidates(list);

  // Ocultar el formulario y mostrar solo agradecimiento; el candidato no ve sus resultados
  document.querySelector('.card').style.display = 'none';
  document.getElementById('thankYouCard').style.display = 'block';

  if (isAdmin) {
    showResultsForCandidate(name, scores);
    renderCandidatesTable();
  }

  document.getElementById('thankYouCard').scrollIntoView({ behavior: 'smooth' });
});

function showResultsForCandidate(name, scores) {
  const resultsCard = document.getElementById('resultsCard');
  resultsCard.style.display = 'block';
  document.getElementById('resultsTitle').textContent = `Resultados de ${name}`;
  const grid = document.getElementById('resultsGrid');
  grid.innerHTML = '';
  Object.keys(dims).forEach(k => {
    const div = document.createElement('div');
    div.className = 'factor-card';
    div.style.background = dims[k].color;
    div.style.color = dims[k].text;
    div.innerHTML = `<p class="label">${dims[k].name}</p><p class="value">${scores[k]}%</p>`;
    grid.appendChild(div);
  });
}

document.getElementById('adminLoginBtn').addEventListener('click', () => {
  const pass = document.getElementById('adminPassword').value;
  if (pass === ADMIN_PASSWORD) {
    isAdmin = true;
    document.getElementById('adminGateCard').style.display = 'none';
    document.getElementById('candidatesCard').style.display = 'block';
    renderCandidatesTable();
  } else {
    alert('Contraseña incorrecta.');
  }
});

document.getElementById('exportBtn').addEventListener('click', () => {
  const list = loadCandidates();
  if (list.length === 0) {
    alert('No hay candidatos guardados para exportar.');
    return;
  }
  const headers = ['Candidato', 'Fecha', ...Object.values(dims).map(d => d.name)];
  const rows = list.map(c => [c.name, c.date, ...Object.keys(dims).map(k => c.scores[k])]);
  let csv = headers.join(';') + '\n';
  rows.forEach(r => csv += r.join(';') + '\n');
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'candidatos_big_five.csv';
  a.click();
  URL.revokeObjectURL(url);
});

document.getElementById('clearBtn').addEventListener('click', () => {
  if (confirm('¿Seguro que quieres borrar todos los candidatos guardados? Esta acción no se puede deshacer.')) {
    localStorage.removeItem(STORAGE_KEY);
    renderCandidatesTable();
  }
});

// La tabla de candidatos solo se carga tras el login de administrador
</script>
</body>
</html>
