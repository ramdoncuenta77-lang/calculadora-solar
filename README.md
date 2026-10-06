<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Simulador Solar Fotovoltaico</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: 'Segoe UI', Arial, sans-serif;
    background: linear-gradient(135deg, #1e3c72, #2a5298);
    color: #fff;
    min-height: 100vh;
    padding: 20px;
  }
  .container { max-width: 1300px; margin: 0 auto; }
  h1 { text-align: center; margin-bottom: 10px; font-size: 2em; }
  .subtitle { text-align: center; opacity: 0.8; margin-bottom: 30px; }
  .panel {
    background: rgba(255,255,255,0.1);
    backdrop-filter: blur(10px);
    border-radius: 15px;
    padding: 20px;
    margin-bottom: 20px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.3);
    border: 1px solid rgba(255,255,255,0.2);
  }
  .panel h2 { margin-bottom: 15px; color: #ffd700; font-size: 1.3em; }
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
    gap: 15px;
  }
  label { display: block; margin-bottom: 5px; font-size: 0.9em; opacity: 0.9; }
  input, select {
    width: 100%;
    padding: 10px;
    border-radius: 8px;
    border: 1px solid rgba(255,255,255,0.3);
    background: rgba(0,0,0,0.3);
    color: #fff;
    font-size: 1em;
  }
  input:focus, select:focus { outline: none; border-color: #ffd700; }
  button {
    background: linear-gradient(135deg, #f093fb, #f5576c);
    color: white;
    border: none;
    padding: 12px 30px;
    font-size: 1em;
    border-radius: 10px;
    cursor: pointer;
    font-weight: bold;
    transition: transform 0.2s, box-shadow 0.2s;
  }
  button:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 20px rgba(245,87,108,0.5);
  }
  button.secondary {
    background: linear-gradient(135deg, #3498db, #2980b9);
  }
  button.secondary:hover {
    box-shadow: 0 8px 20px rgba(52,152,219,0.5);
  }
  .button-row {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    justify-content: center;
    margin: 20px 0;
  }
  .results {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 15px;
    margin-bottom: 20px;
  }
  .card {
    background: rgba(0,0,0,0.3);
    border-radius: 10px;
    padding: 15px;
    text-align: center;
    border-left: 4px solid #ffd700;
  }
  .card .value {
    font-size: 1.6em;
    font-weight: bold;
    color: #ffd700;
    display: block;
    margin-top: 5px;
  }
  .card .label { font-size: 0.85em; opacity: 0.8; }
  canvas { max-height: 400px; background: rgba(255,255,255,0.05); border-radius: 10px; padding: 10px; }
  .chart-container { margin-bottom: 25px; }
  .chart-title { font-weight: bold; margin-bottom: 10px; color: #ffd700; }

  /* Techo */
  .techo-wrapper {
    background: rgba(0,0,0,0.3);
    border-radius: 12px;
    padding: 15px;
    overflow: auto;
  }
  .techo-wrapper svg {
    display: block;
    margin: 0 auto;
    cursor: pointer;
    background: #2c1810;
    border-radius: 6px;
  }
  .techo-info {
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
    margin-top: 15px;
    font-size: 0.9em;
    justify-content: center;
  }
  .techo-info span { color: #ffd700; font-weight: bold; }

  .alerta {
    padding: 12px 15px;
    border-radius: 8px;
    margin-top: 15px;
    font-weight: bold;
  }
  .alerta-ok { background: rgba(46, 204, 113, 0.3); border-left: 4px solid #2ecc71; }
  .alerta-error { background: rgba(231, 76, 60, 0.3); border-left: 4px solid #e74c3c; }
  .alerta-warn { background: rgba(241, 196, 15, 0.3); border-left: 4px solid #f1c40f; }

  .info-tecnica {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
    gap: 10px;
    margin-top: 15px;
    font-size: 0.9em;
  }
  .info-tecnica div {
    background: rgba(0,0,0,0.2);
    padding: 8px 12px;
    border-radius: 6px;
  }
  .info-tecnica span { color: #ffd700; font-weight: bold; }

  .switch {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 0.9em;
  }
  .switch input { width: auto; }
</style>
</head>
<body>
<div class="container">
  <h1>🔆 Simulador Solar Fotovoltaico</h1>
  <p class="subtitle">Diseña tu sistema sobre el techo real y simula la producción</p>

  <!-- CONFIGURACIÓN -->
  <div class="panel">
    <h2>⚙️ Configuración del sistema</h2>
    <div class="grid">
      <div>
        <label>Potencia del panel (W)</label>
        <input type="number" id="potencia_panel" value="550" min="50" max="1000">
      </div>
      <div>
        <label>Ancho del panel (m)</label>
        <input type="number" id="panel_ancho" value="1.13" step="0.01">
      </div>
      <div>
        <label>Alto del panel (m)</label>
        <input type="number" id="panel_alto" value="2.28" step="0.01">
      </div>
      <div>
        <label>Voc del panel (V)</label>
        <input type="number" id="voc_panel" value="49.5" step="0.1">
      </div>
      <div>
        <label>Isc del panel (A)</label>
        <input type="number" id="isc_panel" value="14.0" step="0.1">
      </div>
      <div>
        <label>Voc máx. del inversor (V)</label>
        <input type="number" id="voc_inversor" value="500" step="10">
      </div>
      <div>
        <label>Inclinación (°)</label>
        <input type="number" id="inclinacion" value="15" min="0" max="90">
      </div>
      <div>
        <label>Orientación</label>
        <select id="orientacion">
          <option value="180">Sur (óptimo)</option>
          <option value="135">Sureste</option>
          <option value="225">Suroeste</option>
          <option value="90">Este</option>
          <option value="270">Oeste</option>
        </select>
      </div>
      <div>
        <label>Latitud</label>
        <input type="number" id="latitud" value="6.25" step="0.01">
      </div>
      <div>
        <label>Día del año</label>
        <input type="number" id="dia" value="172" min="1" max="365">
      </div>
      <div>
        <label>Capacidad batería (kWh)</label>
        <input type="number" id="bateria" value="10" min="0" step="0.5">
      </div>
      <div>
        <label>Consumo diario (kWh)</label>
        <input type="number" id="consumo" value="12" min="0" step="0.5">
      </div>
    </div>
  </div>

  <!-- DISEÑO DEL TECHO -->
  <div class="panel">
    <h2>🏠 Diseño del techo</h2>
    <div class="grid">
      <div>
        <label>Ancho del techo (m)</label>
        <input type="number" id="techo_ancho" value="10" step="0.5" min="1">
      </div>
      <div>
        <label>Alto del techo (m)</label>
        <input type="number" id="techo_alto" value="6" step="0.5" min="1">
      </div>
      <div>
        <label>Separación entre paneles (m)</label>
        <input type="number" id="separacion" value="0.05" step="0.01" min="0">
      </div>
      <div>
        <label>Margen de seguridad (m)</label>
        <input type="number" id="margen" value="0.3" step="0.05" min="0">
      </div>
      <div>
        <label>Agrupar strings por</label>
        <select id="modo_strings">
          <option value="filas">Filas horizontales</option>
          <option value="columnas">Columnas verticales</option>
          <option value="auto">Automático (menos strings)</option>
        </select>
      </div>
      <div>
        <label>Paneles por string</label>
        <input type="number" id="paneles_serie" value="4" min="1" max="30">
      </div>
    </div>

    <div class="button-row">
      <button onclick="autoLlenarTecho()">⚡ Auto-llenar techo</button>
      <button class="secondary" onclick="limpiarTecho()">🗑 Limpiar techo</button>
      <button class="secondary" onclick="recalcularStrings()">🔗 Recalcular strings</button>
    </div>

    <div class="techo-wrapper">
      <svg id="techo" viewBox="0 0 900 540" xmlns="http://www.w3.org/2000/svg">
        <!-- Se genera con JS -->
      </svg>
    </div>

    <div class="techo-info" id="techo-info"></div>

    <p style="margin-top: 12px; font-size: 0.85em; opacity: 0.75; text-align: center;">
      💡 Haz clic en una celda para colocar o quitar un panel. Los strings se recolorean automáticamente.
    </p>
  </div>

  <!-- DIAGRAMA ELÉCTRICO -->
  <div class="panel">
    <h2>🔌 Diagrama eléctrico</h2>
    <div class="techo-wrapper">
      <svg id="diagrama" viewBox="0 0 1000 600" xmlns="http://www.w3.org/2000/svg"></svg>
    </div>
    <div id="alerta" class="alerta alerta-ok">Sistema configurado correctamente</div>
    <div class="info-tecnica" id="info-tecnica"></div>
  </div>

  <!-- RESULTADOS -->
  <div class="panel">
    <h2>📊 Resultados del día</h2>
    <div class="results" id="resultados"></div>
  </div>

  <!-- GRÁFICAS -->
  <div class="panel">
    <h2>📈 Gráficas</h2>
    <div class="chart-container">
      <div class="chart-title">Producción fotovoltaica y consumo (kW)</div>
      <canvas id="grafica1"></canvas>
    </div>
    <div class="chart-container">
      <div class="chart-title">Estado de carga de la batería (%)</div>
      <canvas id="grafica2"></canvas>
    </div>
  </div>
</div>

<script>
// ============================================================
// ESTADO GLOBAL
// ============================================================

let paneles = [];        // Array de {fila, col, string}
let gridCols = 0;
let gridRows = 0;
let stringsAsignados = [];

const NS = 'http://www.w3.org/2000/svg';

// Colores para strings
const COLORES_STRING = [
  '#e74c3c', '#3498db', '#2ecc71', '#f39c12', '#9b59b6',
  '#1abc9c', '#e67e22', '#34495e', '#c0392b', '#16a085',
  '#8e44ad', '#d35400', '#27ae60', '#2980b9', '#f1c40f'
];

// ============================================================
// DIBUJAR TECHO
// ============================================================

function dibujarTecho() {
  const svg = document.getElementById('techo');
  svg.innerHTML = '';

  const techoAncho = parseFloat(document.getElementById('techo_ancho').value);
  const techoAlto = parseFloat(document.getElementById('techo_alto').value);
  const panelAncho = parseFloat(document.getElementById('panel_ancho').value);
  const panelAlto = parseFloat(document.getElementById('panel_alto').value);
  const separacion = parseFloat(document.getElementById('separacion').value);
  const margen = parseFloat(document.getElementById('margen').value);

  // Calcular cuántos paneles caben
  const anchoUtil = techoAncho - 2 * margen;
  const altoUtil = techoAlto - 2 * margen;
  gridCols = Math.max(1, Math.floor((anchoUtil + separacion) / (panelAncho + separacion)));
  gridRows = Math.max(1, Math.floor((altoUtil + separacion) / (panelAlto + separacion)));

  // Calcular escala de dibujo
  const SVG_W = 900, SVG_H = 540;
  const escalaX = (SVG_W - 80) / techoAncho;
  const escalaY = (SVG_H - 80) / techoAlto;
  const escala = Math.min(escalaX, escalaY);

  const offsetX = (SVG_W - techoAncho * escala) / 2;
  const offsetY = (SVG_H - techoAlto * escala) / 2;

  // Fondo del techo (área total)
  const techoRect = document.createElementNS(NS, 'rect');
  techoRect.setAttribute('x', offsetX);
  techoRect.setAttribute('y', offsetY);
  techoRect.setAttribute('width', techoAncho * escala);
  techoRect.setAttribute('height', techoAlto * escala);
  techoRect.setAttribute('fill', '#3a2318');
  techoRect.setAttribute('stroke', '#8b6f47');
  techoRect.setAttribute('stroke-width', 3);
  techoRect.setAttribute('rx', 6);
  svg.appendChild(techoRect);

  // Área útil (con margen)
  const utilRect = document.createElementNS(NS, 'rect');
  utilRect.setAttribute('x', offsetX + margen * escala);
  utilRect.setAttribute('y', offsetY + margen * escala);
  utilRect.setAttribute('width', (techoAncho - 2 * margen) * escala);
  utilRect.setAttribute('height', (techoAlto - 2 * margen) * escala);
  utilRect.setAttribute('fill', 'none');
  utilRect.setAttribute('stroke', '#f39c12');
  utilRect.setAttribute('stroke-width', 1.5);
  utilRect.setAttribute('stroke-dasharray', '8,4');
  svg.appendChild(utilRect);

  // Etiqueta del área útil
  const etiquetaUtil = document.createElementNS(NS, 'text');
  etiquetaUtil.setAttribute('x', offsetX + margen * escala + 5);
  etiquetaUtil.setAttribute('y', offsetY + margen * escala - 5);
  etiquetaUtil.setAttribute('fill', '#f39c12');
  etiquetaUtil.setAttribute('font-size', 10);
  etiquetaUtil.textContent = `Área útil: ${anchoUtil.toFixed(1)} × ${altoUtil.toFixed(1)} m`;
  svg.appendChild(etiquetaUtil);

  // Cuadrícula de celdas donde puede ir un panel
  for (let r = 0; r < gridRows; r++) {
    for (let c = 0; c < gridCols; c++) {
      const x = offsetX + margen * escala + c * (panelAncho + separacion) * escala;
      const y = offsetY + margen * escala + r * (panelAlto + separacion) * escala;
      const w = panelAncho * escala;
      const h = panelAlto * escala;

      const tieneePanel = paneles.some(p => p.fila === r && p.col === c);
      const panelActual = paneles.find(p => p.fila === r && p.col === c);

      const rect = document.createElementNS(NS, 'rect');
      rect.setAttribute('x', x);
      rect.setAttribute('y', y);
      rect.setAttribute('width', w);
      rect.setAttribute('height', h);
      rect.setAttribute('rx', 3);

      if (tienePanel) {
        const color = COLORES_STRING[panelActual.string % COLORES_STRING.length];
        rect.setAttribute('fill', color);
        rect.setAttribute('fill-opacity', 0.35);
        rect.setAttribute('stroke', color);
        rect.setAttribute('stroke-width', 3);
      } else {
        rect.setAttribute('fill', 'rgba(255,255,255,0.03)');
        rect.setAttribute('stroke', 'rgba(255,255,255,0.15)');
        rect.setAttribute('stroke-width', 1);
        rect.setAttribute('stroke-dasharray', '3,3');
      }

      rect.style.cursor = 'pointer';
      rect.addEventListener('click', () => togglePanel(r, c));
      svg.appendChild(rect);

      // Número del panel si está colocado
      if (tienePanel) {
        // Líneas de células
        for (let i = 1; i < 3; i++) {
          const linea = document.createElementNS(NS, 'line');
          linea.setAttribute('x1', x + (w / 3) * i);
          linea.setAttribute('y1', y + 4);
          linea.setAttribute('x2', x + (w / 3) * i);
          linea.setAttribute('y2', y + h - 4);
          linea.setAttribute('stroke', '#fff');
          linea.setAttribute('stroke-width', 0.5);
          linea.setAttribute('opacity', 0.4);
          svg.appendChild(linea);
        }

        const texto = document.createElementNS(NS, 'text');
        texto.setAttribute('x', x + w / 2);
        texto.setAttribute('y', y + h / 2 + 4);
        texto.setAttribute('text-anchor', 'middle');
        texto.setAttribute('fill', '#fff');
        texto.setAttribute('font-size', 12);
        texto.setAttribute('font-weight', 'bold');
        texto.setAttribute('pointer-events', 'none');
        texto.textContent = `S${panelActual.string + 1}`;
        svg.appendChild(texto);
      }
    }
  }

  // Etiquetas de dimensiones del techo
  const dimAncho = document.createElementNS(NS, 'text');
  dimAncho.setAttribute('x', SVG_W / 2);
  dimAncho.setAttribute('y', offsetY + techoAlto * escala + 25);
  dimAncho.setAttribute('text-anchor', 'middle');
  dimAncho.setAttribute('fill', '#fff');
  dimAncho.setAttribute('font-size', 13);
  dimAncho.textContent = `${techoAncho} m`;
  svg.appendChild(dimAncho);

  const dimAlto = document.createElementNS(NS, 'text');
  dimAlto.setAttribute('x', offsetX - 15);
  dimAlto.setAttribute('y', SVG_H / 2);
  dimAlto.setAttribute('text-anchor', 'middle');
  dimAlto.setAttribute('fill', '#fff');
  dimAlto.setAttribute('font-size', 13);
  dimAlto.setAttribute('transform', `rotate(-90, ${offsetX - 15}, ${SVG_H / 2})`);
  dimAlto.textContent = `${techoAlto} m`;
  svg.appendChild(dimAlto);

  actualizarInfoTecho();
}

// ============================================================
// INFO DEL TECHO
// ============================================================

function actualizarInfoTecho() {
  const totalCeldas = gridRows * gridCols;
  const panelesColocados = paneles.length;
  const ocupacion = totalCeldas > 0 ? (panelesColocados / totalCeldas * 100) : 0;
  const potenciaTotal = panelesColocados * parseFloat(document.getElementById('potencia_panel').value);

  document.getElementById('techo-info').innerHTML = `
    <div>Capacidad del techo: <span>${gridRows} × ${gridCols} = ${totalCeldas} paneles</span></div>
    <div>Paneles colocados: <span>${panelesColocados}</span></div>
    <div>Ocupación: <span>${ocupacion.toFixed(0)}%</span></div>
    <div>Potencia pico: <span>${(potenciaTotal / 1000).toFixed(2)} kWp</span></div>
    <div>Strings: <span>${new Set(paneles.map(p => p.string)).size}</span></div>
  `;
}

// ============================================================
// AUTO-LLENAR TECHO
// ============================================================

function autoLlenarTecho() {
  // Primero recalcular gridCols/gridRows
  const techoAncho = parseFloat(document.getElementById('techo_ancho').value);
  const techoAlto = parseFloat(document.getElementById('techo_alto').value);
  const panelAncho = parseFloat(document.getElementById('panel_ancho').value);
  const panelAlto = parseFloat(document.getElementById('panel_alto').value);
  const separacion = parseFloat(document.getElementById('separacion').value);
  const margen = parseFloat(document.getElementById('margen').value);

  const anchoUtil = techoAncho - 2 * margen;
  const altoUtil = techoAlto - 2 * margen;
  gridCols = Math.max(1, Math.floor((anchoUtil + separacion) / (panelAncho + separacion)));
  gridRows = Math.max(1, Math.floor((altoUtil + separacion) / (panelAlto + separacion)));

  paneles = [];
  for (let r = 0; r < gridRows; r++) {
    for (let c = 0; c < gridCols; c++) {
      paneles.push({ fila: r, col: c, string: 0 });
    }
  }

  asignarStrings();
  dibujarTecho();
  simular();
}

function limpiarTecho() {
  paneles = [];
  dibujarTecho();
  simular();
}

function togglePanel(r, c) {
  const idx = paneles.findIndex(p => p.fila === r && p.col === c);
  if (idx >= 0) {
    paneles.splice(idx, 1);
  } else {
    paneles.push({ fila: r, col: c, string: 0 });
  }
  asignarStrings();
  dibujarTecho();
  simular();
}

function recalcularStrings() {
  asignarStrings();
  dibujarTecho();
  simular();
}

// ============================================================
// ASIGNAR STRINGS AUTOMÁTICAMENTE
// ============================================================

function asignarStrings() {
  if (paneles.length === 0) return;

  const modo = document.getElementById('modo_strings').value;
  const panelesSerie = parseInt(document.getElementById('paneles_serie').value);

  // Ordenar paneles
  if (modo === 'filas') {
    paneles.sort((a, b) => a.fila - b.fila || a.col - b.col);
  } else if (modo === 'columnas') {
    paneles.sort((a, b) => a.col - b.col || a.fila - b.fila);
  } else {
    // Auto: agrupar por filas (suele dar menos strings si el techo es ancho)
    paneles.sort((a, b) => a.fila - b.fila || a.col - b.col);
  }

  // Asignar string secuencialmente
  let stringActual = 0;
  let contador = 0;
  for (const p of paneles) {
    p.string = stringActual;
    contador++;
    if (contador >= panelesSerie) {
      stringActual++;
      contador = 0;
    }
  }

  stringsAsignados = [];
  const maxString = Math.max(...paneles.map(p => p.string));
  for (let s = 0; s <= maxString; s++) {
    const enString = paneles.filter(p => p.string === s);
    if (enString.length > 0) {
      stringsAsignados.push({ id: s, paneles: enString });
    }
  }
}

// ============================================================
// DIBUJAR DIAGRAMA ELÉCTRICO
// ============================================================

function dibujarDiagrama() {
  const svg = document.getElementById('diagrama');
  svg.innerHTML = '';

  const vocPanel = parseFloat(document.getElementById('voc_panel').value);
  const iscPanel = parseFloat(document.getElementById('isc_panel').value);
  const vocInversor = parseFloat(document.getElementById('voc_inversor').value);
  const capacidadBateria = parseFloat(document.getElementById('bateria').value);

  const numStrings = stringsAsignados.length;

  if (numStrings === 0) {
    const t = document.createElementNS(NS, 'text');
    t.setAttribute('x', 500);
    t.setAttribute('y', 300);
    t.setAttribute('text-anchor', 'middle');
    t.setAttribute('fill', '#fff');
    t.setAttribute('font-size', 16);
    t.textContent = 'Coloca paneles en el techo para ver el diagrama';
    svg.appendChild(t);
    return;
  }

  // Layout
  const PANEL_W = 50;
  const PANEL_H = 35;
  const GAP_X = 8;
  const START_X = 40;
  const START_Y = 70;
  const ESPACIO_STRING = 60;

  // Título
  const titulo = document.createElementNS(NS, 'text');
  titulo.setAttribute('x', 500);
  titulo.setAttribute('y', 25);
  titulo.setAttribute('text-anchor', 'middle');
  titulo.setAttribute('fill', '#ffd700');
  titulo.setAttribute('font-size', 15);
  titulo.setAttribute('font-weight', 'bold');
  titulo.textContent = `${paneles.length} paneles distribuidos en ${numStrings} string(s)`;
  svg.appendChild(titulo);

  // Dibujar cada string como una fila
  const stringCoords = [];

  stringsAsignados.forEach((str, idx) => {
    const y = START_Y + idx * (PANEL_H + ESPACIO_STRING);
    const color = COLORES_STRING[str.id % COLORES_STRING.length];

    // Etiqueta del string
    const etiqueta = document.createElementNS(NS, 'text');
    etiqueta.setAttribute('x', 15);
    etiqueta.setAttribute('y', y + PANEL_H / 2 + 5);
    etiqueta.setAttribute('fill', color);
    etiqueta.setAttribute('font-size', 12);
    etiqueta.setAttribute('font-weight', 'bold');
    etiqueta.textContent = `S${str.id + 1}`;
    svg.appendChild(etiqueta);

    // Paneles del string
    str.paneles.forEach((p, pIdx) => {
      const x = START_X + pIdx * (PANEL_W + GAP_X);

      const rect = document.createElementNS(NS, 'rect');
      rect.setAttribute('x', x);
      rect.setAttribute('y', y);
      rect.setAttribute('width', PANEL_W);
      rect.setAttribute('height', PANEL_H);
      rect.setAttribute('fill', color);
      rect.setAttribute('fill-opacity', 0.3);
      rect.setAttribute('stroke', color);
      rect.setAttribute('stroke-width', 2);
      rect.setAttribute('rx', 3);
      svg.appendChild(rect);

      const num = document.createElementNS(NS, 'text');
      num.setAttribute('x', x + PANEL_W / 2);
      num.setAttribute('y', y + PANEL_H / 2 + 4);
      num.setAttribute('text-anchor', 'middle');
      num.setAttribute('fill', '#fff');
      num.setAttribute('font-size', 10);
      num.textContent = `${p.fila + 1}.${p.col + 1}`;
      svg.appendChild(num);
    });

    // Cableado en serie
    for (let p = 0; p < str.paneles.length - 1; p++) {
      const x1 = START_X + p * (PANEL_W + GAP_X) + PANEL_W;
      const x2 = START_X + (p + 1) * (PANEL_W + GAP_X);
      const yMid = y + PANEL_H / 2;
      const linea = document.createElementNS(NS, 'line');
      linea.setAttribute('x1', x1);
      linea.setAttribute('y1', yMid);
      linea.setAttribute('x2', x2);
      linea.setAttribute('y2', yMid);
      linea.setAttribute('stroke', '#e74c3c');
      linea.setAttribute('stroke-width', 2.5);
      svg.appendChild(linea);
    }

    const xFin = START_X + str.paneles.length * PANEL_W + (str.paneles.length - 1) * GAP_X;
    stringCoords.push({ y, xInicio: START_X, xFin, id: str.id });
  });

  // Bus DC
  const busX = 520;
  const busYInicio = START_Y + 10;
  const busYFin = START_Y + (numStrings - 1) * (PANEL_H + ESPACIO_STRING) + PANEL_H - 10;

  const busPos = document.createElementNS(NS, 'line');
  busPos.setAttribute('x1', busX); busPos.setAttribute('y1', busYInicio);
  busPos.setAttribute('x2', busX); busPos.setAttribute('y2', busYFin);
  busPos.setAttribute('stroke', '#e74c3c'); busPos.setAttribute('stroke-width', 4);
  svg.appendChild(busPos);

  const busNeg = document.createElementNS(NS, 'line');
  busNeg.setAttribute('x1', busX + 15); busNeg.setAttribute('y1', busYInicio);
  busNeg.setAttribute('x2', busX + 15); busNeg.setAttribute('y2', busYFin);
  busNeg.setAttribute('stroke', '#3498db'); busNeg.setAttribute('stroke-width', 4);
  svg.appendChild(busNeg);

  // Etiquetas + y -
  const mas = document.createElementNS(NS, 'text');
  mas.setAttribute('x', busX); mas.setAttribute('y', busYInicio - 5);
  mas.setAttribute('text-anchor', 'middle');
  mas.setAttribute('fill', '#e74c3c'); mas.setAttribute('font-size', 12);
  mas.setAttribute('font-weight', 'bold'); mas.textContent = '+';
  svg.appendChild(mas);

  const menos = document.createElementNS(NS, 'text');
  menos.setAttribute('x', busX + 15); menos.setAttribute('y', busYInicio - 5);
  menos.setAttribute('text-anchor', 'middle');
  menos.setAttribute('fill', '#3498db'); menos.setAttribute('font-size', 12);
  menos.setAttribute('font-weight', 'bold'); menos.textContent = '−';
  svg.appendChild(menos);

  // Conexiones string → bus
  stringCoords.forEach((sc, i) => {
    const yConecta = sc.y + PANEL_H / 2;
    const lineaPos = document.createElementNS(NS, 'line');
    lineaPos.setAttribute('x1', sc.xFin);
    lineaPos.setAttribute('y1', yConecta);
    lineaPos.setAttribute('x2', busX);
    lineaPos.setAttribute('y2', yConecta);
    lineaPos.setAttribute('stroke', '#e74c3c');
    lineaPos.setAttribute('stroke-width', 2);
    svg.appendChild(lineaPos);

    const lineaNeg = document.createElementNS(NS, 'line');
    lineaNeg.setAttribute('x1', sc.xInicio);
    lineaNeg.setAttribute('y1', sc.y + PANEL_H - 5);
    lineaNeg.setAttribute('x2', busX + 15);
    lineaNeg.setAttribute('y2', busYFin + 15 + i * 6);
    lineaNeg.setAttribute('stroke', '#3498db');
    lineaNeg.setAttribute('stroke-width', 2);
    svg.appendChild(lineaNeg);
  });

  // Inversor
  const invX = 640;
  const invY = (busYInicio + busYFin) / 2 - 50;
  const invW = 110;
  const invH = 100;

  const invRect = document.createElementNS(NS, 'rect');
  invRect.setAttribute('x', invX);
  invRect.setAttribute('y', invY);
  invRect.setAttribute('width', invW);
  invRect.setAttribute('height', invH);
  invRect.setAttribute('fill', '#2c3e50');
  invRect.setAttribute('stroke', '#ffd700');
  invRect.setAttribute('stroke-width', 3);
  invRect.setAttribute('rx', 8);
  svg.appendChild(invRect);

  const invTxt1 = document.createElementNS(NS, 'text');
  invTxt1.setAttribute('x', invX + invW / 2);
  invTxt1.setAttribute('y', invY + 30);
  invTxt1.setAttribute('text-anchor', 'middle');
  invTxt1.setAttribute('fill', '#ffd700');
  invTxt1.setAttribute('font-size', 14);
  invTxt1.setAttribute('font-weight', 'bold');
  invTxt1.textContent = 'INVERSOR';
  svg.appendChild(invTxt1);

  const invTxt2 = document.createElementNS(NS, 'text');
  invTxt2.setAttribute('x', invX + invW / 2);
  invTxt2.setAttribute('y', invY + 50);
  invTxt2.setAttribute('text-anchor', 'middle');
  invTxt2.setAttribute('fill', '#fff');
  invTxt2.setAttribute('font-size', 11);
  invTxt2.textContent = 'DC → AC';
  svg.appendChild(invTxt2);

  const onda = document.createElementNS(NS, 'path');
  onda.setAttribute('d', `M ${invX + 30} ${invY + 70} q 10 -12 20 0 q 10 12 20 0`);
  onda.setAttribute('fill', 'none');
  onda.setAttribute('stroke', '#2ecc71');
  onda.setAttribute('stroke-width', 2.5);
  svg.appendChild(onda);

  // Bus → inversor
  const busMidY = (busYInicio + busYFin) / 2;
  const linPos = document.createElementNS(NS, 'line');
  linPos.setAttribute('x1', busX);
  linPos.setAttribute('y1', busMidY);
  linPos.setAttribute('x2', invX);
  linPos.setAttribute('y2', invY + invH / 2);
  linPos.setAttribute('stroke', '#e74c3c');
  linPos.setAttribute('stroke-width', 3);
  svg.appendChild(linPos);

  const linNeg = document.createElementNS(NS, 'line');
  linNeg.setAttribute('x1', busX + 15);
  linNeg.setAttribute('y1', busMidY + 10);
  linNeg.setAttribute('x2', invX);
  linNeg.setAttribute('y2', invY + invH / 2 + 10);
  linNeg.setAttribute('stroke', '#3498db');
  linNeg.setAttribute('stroke-width', 3);
  svg.appendChild(linNeg);

  // Batería
  const batX = invX + invW + 50;
  const batY = invY - 30;
  const batW = 80;
  const batH = 60;

  if (capacidadBateria > 0) {
    const batRect = document.createElementNS(NS, 'rect');
    batRect.setAttribute('x', batX);
    batRect.setAttribute('y', batY);
    batRect.setAttribute('width', batW);
    batRect.setAttribute('height', batH);
    batRect.setAttribute('fill', '#1a3a1a');
    batRect.setAttribute('stroke', '#2ecc71');
    batRect.setAttribute('stroke-width', 3);
    batRect.setAttribute('rx', 6);
    svg.appendChild(batRect);

    const terminal = document.createElementNS(NS, 'rect');
    terminal.setAttribute('x', batX + batW / 2 - 10);
    terminal.setAttribute('y', batY - 5);
    terminal.setAttribute('width', 20);
    terminal.setAttribute('height', 8);
    terminal.setAttribute('fill', '#2ecc71');
    svg.appendChild(terminal);

    const batTxt = document.createElementNS(NS, 'text');
    batTxt.setAttribute('x', batX + batW / 2);
    batTxt.setAttribute('y', batY + batH / 2 + 5);
    batTxt.setAttribute('text-anchor', 'middle');
    batTxt.setAttribute('fill', '#2ecc71');
    batTxt.setAttribute('font-size', 12);
    batTxt.setAttribute('font-weight', 'bold');
    batTxt.textContent = `${capacidadBateria} kWh`;
    svg.appendChild(batTxt);

    const conBat = document.createElementNS(NS, 'line');
    conBat.setAttribute('x1', invX + invW);
    conBat.setAttribute('y1', invY + invH / 2);
    conBat.setAttribute('x2', batX);
    conBat.setAttribute('y2', batY + batH / 2);
    conBat.setAttribute('stroke', '#2ecc71');
    conBat.setAttribute('stroke-width', 2.5);
    conBat.setAttribute('stroke-dasharray', '5,3');
    svg.appendChild(conBat);
  }

  // Cargas (casa)
  const casaX = batX + batW + 60;
  const casaY = invY + 10;
  const casaW = 90;
  const casaH = 80;

  const casaRect = document.createElementNS(NS, 'rect');
  casaRect.setAttribute('x', casaX);
  casaRect.setAttribute('y', casaY);
  casaRect.setAttribute('width', casaW);
  casaRect.setAttribute('height', casaH);
  casaRect.setAttribute('fill', '#3d2c1a');
  casaRect.setAttribute('stroke', '#f39c12');
  casaRect.setAttribute('stroke-width', 3);
  casaRect.setAttribute('rx', 6);
  svg.appendChild(casaRect);

  const techo = document.createElementNS(NS, 'polygon');
  techo.setAttribute('points', `${casaX - 8},${casaY} ${casaX + casaW / 2},${casaY - 25} ${casaX + casaW + 8},${casaY}`);
  techo.setAttribute('fill', '#c0392b');
  techo.setAttribute('stroke', '#f39c12');
  techo.setAttribute('stroke-width', 2);
  svg.appendChild(techo);

  const casaTxt = document.createElementNS(NS, 'text');
  casaTxt.setAttribute('x', casaX + casaW / 2);
  casaTxt.setAttribute('y', casaY + casaH / 2 + 5);
  casaTxt.setAttribute('text-anchor', 'middle');
  casaTxt.setAttribute('fill', '#f39c12');
  casaTxt.setAttribute('font-size', 13);
  casaTxt.setAttribute('font-weight', 'bold');
  casaTxt.textContent = 'CARGAS';
  svg.appendChild(casaTxt);

  const conCasa = document.createElementNS(NS, 'line');
  conCasa.setAttribute('x1', invX + invW);
  conCasa.setAttribute('y1', invY + invH / 2 + 20);
  conCasa.setAttribute('x2', casaX);
  conCasa.setAttribute('y2', casaY + casaH / 2);
  conCasa.setAttribute('stroke', '#f39c12');
  conCasa.setAttribute('stroke-width', 3);
  svg.appendChild(conCasa);
}

// ============================================================
// VALIDACIONES TÉCNICAS
// ============================================================

function validarSistema() {
  const vocPanel = parseFloat(document.getElementById('voc_panel').value);
  const iscPanel = parseFloat(document.getElementById('isc_panel').value);
  const vocInversor = parseFloat(document.getElementById('voc_inversor').value);

  if (stringsAsignados.length === 0) {
    document.getElementById('alerta').className = 'alerta alerta-warn';
    document.getElementById('alerta').innerHTML = '⚠️ No hay paneles colocados. Coloca paneles en el techo.';
    document.getElementById('info-tecnica').innerHTML = '';
    return;
  }

  const maxPanelesPorString = Math.max(...stringsAsignados.map(s => s.paneles.length));
  const vocStringMax = vocPanel * maxPanelesPorString;
  const vocStringFrio = vocStringMax * 1.15;
  const iscTotal = iscPanel * stringsAsignados.length;

  let alertaHTML = '';
  let claseAlerta = 'alerta-ok';

  if (vocStringFrio > vocInversor) {
    alertaHTML = `⚠️ <strong>PELIGRO:</strong> Voc en frío del string más largo (${vocStringFrio.toFixed(0)} V) supera el máximo del inversor (${vocInversor} V). Reduce paneles por string.`;
    claseAlerta = 'alerta-error';
  } else if (vocStringFrio > vocInversor * 0.9) {
    alertaHTML = `⚡ <strong>Atención:</strong> Voc del string (${vocStringFrio.toFixed(0)} V) cerca del límite del inversor (${vocInversor} V).`;
    claseAlerta = 'alerta-warn';
  } else if (vocStringMax < 150) {
    alertaHTML = `ℹ️ El Voc del string (${vocStringMax.toFixed(0)} V) puede ser bajo para el MPPT. Considera más paneles en serie.`;
    claseAlerta = 'alerta-warn';
  } else {
    alertaHTML = `✅ Sistema correcto. Voc string más largo: ${vocStringMax.toFixed(0)} V (STC) / ${vocStringFrio.toFixed(0)} V (frío).`;
  }

  document.getElementById('alerta').className = 'alerta ' + claseAlerta;
  document.getElementById('alerta').innerHTML = alertaHTML;

  const potenciaTotal = paneles.length * parseFloat(document.getElementById('potencia_panel').value);

  document.getElementById('info-tecnica').innerHTML = `
    <div>Strings: <span>${stringsAsignados.length}</span></div>
    <div>Paneles por string: <span>${stringsAsignados.map(s => s.paneles.length).join(' / ')}</span></div>
    <div>Voc string mayor: <span>${vocStringMax.toFixed(1)} V</span></div>
    <div>Voc en frío: <span>${vocStringFrio.toFixed(1)} V</span></div>
    <div>Isc total: <span>${iscTotal.toFixed(1)} A</span></div>
    <div>Potencia pico: <span>${(potenciaTotal/1000).toFixed(2)} kWp</span></div>
  `;
}

// ============================================================
// SIMULACIÓN ENERGÉTICA
// ============================================================

function simular() {
  const pPanel = parseFloat(document.getElementById('potencia_panel').value);
  const numPaneles = paneles.length;
  const inclinacion = parseFloat(document.getElementById('inclinacion').value);
  const orientacion = parseFloat(document.getElementById('orientacion').value);
  const latitud = parseFloat(document.getElementById('latitud').value);
  const diaAnio = parseInt(document.getElementById('dia').value);
  const capacidadBateria = parseFloat(document.getElementById('bateria').value);
  const consumoDiario = parseFloat(document.getElementById('consumo').value);

  const potenciaTotal = pPanel * numPaneles;

  validarSistema();
  dibujarDiagrama();

  if (numPaneles === 0) {
    document.getElementById('resultados').innerHTML = '<p style="text-align:center;opacity:0.7">Coloca paneles para ver los resultados</p>';
    return;
  }

  const declinacion = 23.45 * Math.sin((2 * Math.PI * (284 + diaAnio)) / 365) * Math.PI / 180;
  const puntos = [];

  for (let i = 0; i < 96; i++) {
    const hora = i * 0.25;
    const anguloHorario = (hora - 12) * 15 * Math.PI / 180;
    const cosCenital =
      Math.sin(latitud * Math.PI / 180) * Math.sin(declinacion) +
      Math.cos(latitud * Math.PI / 180) * Math.cos(declinacion) * Math.cos(anguloHorario);
    const cenital = Math.acos(Math.max(-1, Math.min(1, cosCenital)));

    const Gsc = 1367;
    const irradianciaExtraterrestre = Gsc * (1 + 0.033 * Math.cos(2 * Math.PI * diaAnio / 365));

    const anguloIncidencia =
      Math.cos(cenital) * Math.cos(inclinacion * Math.PI / 180) +
      Math.sin(cenital) * Math.sin(inclinacion * Math.PI / 180) *
      Math.cos((orientacion - 180) * Math.PI / 180);

    let irradiancia = 0;
    if (cosCenital > 0) {
      const masaAire = 1 / (cosCenital + 0.0001);
      const transmitancia = 0.7 * Math.pow(0.678, Math.pow(masaAire, 0.5));
      irradiancia = irradianciaExtraterrestre * cosCenital * transmitancia;
    }

    let irradianciaPanel = 0;
    if (anguloIncidencia > 0) {
      irradianciaPanel = irradiancia * Math.max(0, anguloIncidencia);
    }

    const temperaturaCelda = 25 + (irradianciaPanel / 800) * 25;
    const coefTemp = 1 - 0.004 * (temperaturaCelda - 25);

    const potenciaDC = (potenciaTotal / 1000) * (irradianciaPanel / 1000) * coefTemp;
    const potenciaAC = potenciaDC * 0.96 * 0.98;

    puntos.push({
      hora,
      potenciaDC: Math.max(0, potenciaDC),
      potenciaAC: Math.max(0, potenciaAC),
    });
  }

  const perfilConsumo = [
    0.2,0.2,0.2,0.2,0.2,0.3,0.5,0.8,0.7,0.5,0.4,0.4,
    0.5,0.5,0.4,0.4,0.6,1.0,1.3,1.2,0.9,0.6,0.4,0.3,
    0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.3,0.4,0.4,0.4,
    0.5,0.5,0.5,0.5,0.5,0.6,0.7,0.8,0.8,0.9,1.0,1.1,
    1.1,1.0,0.9,0.8,0.7,0.7,0.7,0.7,0.8,0.9,1.0,1.2,
    1.4,1.6,1.7,1.6,1.4,1.2,1.1,1.0,0.9,0.8,0.7,0.6,
    0.5,0.5,0.4,0.4,0.4,0.4,0.3,0.3,0.3,0.3,0.3,0.3,
    0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,0.2,
  ];
  const sumaPerfil = perfilConsumo.reduce((a, b) => a + b, 0);
  const factor = (consumoDiario * 4) / sumaPerfil;
  const consumoPuntos = perfilConsumo.map(p => p * factor);

  const socInicial = 0.5, socMin = 0.2, socMax = 0.95;
  let soc = socInicial;
  const socHistorial = [];
  let energiaGenerada = 0, energiaConsumida = 0;
  let energiaExportada = 0, energiaImportada = 0;

  for (let i = 0; i < 96; i++) {
    const dt = 0.25;
    const pFV = puntos[i].potenciaAC;
    const pConsumo = consumoPuntos[i];
    energiaGenerada += pFV * dt;
    energiaConsumida += pConsumo * dt;
    const balance = (pFV - pConsumo) * dt;

    if (balance > 0) {
      const espacio = (socMax - soc) * capacidadBateria;
      const cargado = Math.min(balance * 0.95, espacio);
      soc += capacidadBateria > 0 ? cargado / capacidadBateria : 0;
      energiaExportada += balance - cargado / 0.95;
    } else {
      const disponible = (soc - socMin) * capacidadBateria;
      const descargado = Math.min(-balance / 0.95, disponible);
      soc -= capacidadBateria > 0 ? descargado / capacidadBateria : 0;
      energiaImportada += -balance - descargado * 0.95;
    }
    soc = Math.max(socMin, Math.min(socMax, soc));
    socHistorial.push(soc * 100);
  }

  const autoconsumo = energiaGenerada - energiaExportada;
  const cobertura = energiaGenerada > 0 ? (autoconsumo / energiaConsumida * 100) : 0;

  document.getElementById('resultados').innerHTML = `
    <div class="card"><div class="label">Energía generada</div><span class="value">${energiaGenerada.toFixed(1)} kWh</span></div>
    <div class="card"><div class="label">Consumo total</div><span class="value">${energiaConsumida.toFixed(1)} kWh</span></div>
    <div class="card"><div class="label">Autoconsumo</div><span class="value">${autoconsumo.toFixed(1)} kWh</span></div>
    <div class="card"><div class="label">Exportado a red</div><span class="value">${energiaExportada.toFixed(1)} kWh</span></div>
    <div class="card"><div class="label">Importado de red</div><span class="value">${energiaImportada.toFixed(1)} kWh</span></div>
    <div class="card"><div class="label">Cobertura solar</div><span class="value">${cobertura.toFixed(0)}%</span></div>
  `;

  const horasLabel = puntos.map(p => {
    const h = Math.floor(p.hora);
    const m = Math.round((p.hora - h) * 60);
    return `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}`;
  });

  const ctx1 = document.getElementById('grafica1').getContext('2d');
  if (window.chart1) window.chart1.destroy();
  window.chart1 = new Chart(ctx1, {
    type: 'line',
    data: {
      labels: horasLabel.filter((_, i) => i % 4 === 0),
      datasets: [
        { label: 'Producción AC (kW)', data: puntos.map(p => p.potenciaAC).filter((_, i) => i % 4 === 0),
          borderColor: '#ffd700', backgroundColor: 'rgba(255,215,0,0.2)', fill: true, tension: 0.4 },
        { label: 'Consumo (kW)', data: consumoPuntos.filter((_, i) => i % 4 === 0),
          borderColor: '#f5576c', backgroundColor: 'rgba(245,87,108,0.2)', fill: true, tension: 0.4 },
      ],
    },
    options: { responsive: true, plugins: { legend: { labels: { color: '#fff' } } },
      scales: { x: { ticks: { color: '#fff' }, grid: { color: 'rgba(255,255,255,0.1)' } },
        y: { ticks: { color: '#fff' }, grid: { color: 'rgba(255,255,255,0.1)' } } } },
  });

  const ctx2 = document.getElementById('grafica2').getContext('2d');
  if (window.chart2) window.chart2.destroy();
  window.chart2 = new Chart(ctx2, {
    type: 'line',
    data: {
      labels: horasLabel.filter((_, i) => i % 4 === 0),
      datasets: [
        { label: 'Estado de carga (%)', data: socHistorial.filter((_, i) => i % 4 === 0),
          borderColor: '#00d4ff', backgroundColor: 'rgba(0,212,255,0.2)', fill: true, tension: 0.4 },
      ],
    },
    options: { responsive: true, plugins: { legend: { labels: { color: '#fff' } } },
      scales: { x: { ticks: { color: '#fff' }, grid: { color: 'rgba(255,255,255,0.1)' } },
        y: { min: 0, max: 100, ticks: { color: '#fff' }, grid: { color: 'rgba(255,255,255,0.1)' } } } },
  });
}

// ============================================================
// INICIALIZACIÓN
// ============================================================

window.addEventListener('DOMContentLoaded', () => {
  autoLlenarTecho();
  // Re-simular cuando cambian parámetros clave
  ['potencia_panel', 'panel_ancho', 'panel_alto', 'voc_panel', 'isc_panel',
   'voc_inversor', 'separacion', 'margen', 'modo_strings', 'paneles_serie',
   'bateria', 'consumo', 'latitud', 'dia', 'inclinacion', 'orientacion',
   'techo_ancho', 'techo_alto'].forEach(id => {
    document.getElementById(id).addEventListener('change', () => {
      if (['panel_ancho', 'panel_alto', 'separacion', 'margen', 'techo_ancho', 'techo_alto'].includes(id)) {
        // Si cambia geometría, recalcular todo
        autoLlenarTecho();
      } else if (['modo_strings', 'paneles_serie'].includes(id)) {
        asignarStrings();
        dibujarTecho();
        simular();
      } else {
        simular();
      }
    });
  });
});
</script>
</body>
</html>
