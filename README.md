<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Calculadora Táctica EOFBC</title>
    <style>
        * { -webkit-tap-highlight-color: transparent; box-sizing: border-box; margin: 0; padding: 0; font-family: system-ui, -apple-system, sans-serif; }
        body { background-color: #020617 !important; color: #f8fafc !important; padding: 12px; min-height: 100vh; }
        .container { max-width: 700px; margin: 0 auto; display: flex; flex-direction: column; gap: 12px; }
        
        header { background: #0f172a !important; border: 1px solid #1e293b; padding: 14px; border-radius: 12px; }
        h1 { font-size: 1rem; color: #ffffff !important; text-transform: uppercase; font-weight: 800; }
        p.sub { font-size: 0.7rem; color: #94a3b8 !important; margin-top: 2px; }
        
        .card { background: #0f172a !important; border: 1px solid #1e293b; border-radius: 12px; padding: 14px; margin-bottom: 4px; }
        .card-title { font-size: 0.75rem; font-weight: bold; color: #34d399 !important; text-transform: uppercase; margin-bottom: 8px; border-bottom: 1px solid #1e293b; padding-bottom: 4px; }
        
        .field { margin-bottom: 10px; display: flex; flex-direction: column; gap: 4px; }
        label { font-size: 0.65rem; font-weight: bold; color: #cbd5e1 !important; text-transform: uppercase; }
        input, select { background: #020617 !important; border: 1px solid #334155; color: #ffffff !important; padding: 10px; border-radius: 8px; font-size: 0.85rem; font-weight: 600; width: 100%; }
        
        .btn-grid { display: flex; gap: 6px; }
        .btn-mode { flex: 1; padding: 10px; border: 1px solid #334155; background: #020617 !important; color: #94a3b8 !important; font-size: 0.75rem; font-weight: bold; border-radius: 8px; cursor: pointer; }
        .btn-mode.active { background: #059669 !important; color: #ffffff !important; border-color: #10b981; }
        
        .summary-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
        @media (min-width: 480px) { .summary-grid { grid-template-columns: repeat(4, 1fr); } }
        
        .sum-box { background-color: #0f172a !important; border: 1px solid #1e293b; padding: 12px; border-radius: 10px; display: flex; flex-direction: column; }
        .sum-title { font-size: 0.6rem; color: #94a3b8 !important; text-transform: uppercase; font-weight: bold; }
        .sum-val { font-size: 1.05rem; font-weight: 900; color: #34d399 !important; font-family: monospace; margin-top: 2px; }
        
        .table-container { overflow-x: auto; background-color: #0f172a !important; border-radius: 8px; }
        table { width: 100%; border-collapse: collapse; font-size: 0.7rem; background-color: #0f172a !important; }
        th { color: #94a3b8 !important; border-bottom: 1px solid #1e293b; padding: 8px 4px; text-align: left; font-size: 0.6rem; background-color: #0f172a !important; text-transform: uppercase; }
        td { padding: 8px 4px; border-bottom: 1px solid #1e293b; font-family: monospace; color: #f8fafc !important; background-color: #0f172a !important; }
        
        .bar-bg { width: 100%; background: #020617 !important; height: 5px; border-radius: 3px; overflow: hidden; margin-top: 3px; border: 1px solid #1e293b; }
        .bar-fill { height: 100%; background: #10b981; }
        .hidden { display: none !important; }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Calculadora Táctica EOFBC</h1>
            <p class="sub">Batallón Fluvial de Infantería de Marina — Control Operativo</p>
        </header>

        <div class="card">
            <div class="card-title">Configuración de Misión</div>
            
            <div class="field">
                <label>Modo de Cálculo:</label>
                <div class="btn-grid">
                    <button type="button" id="mGrp" class="btn-mode active">Consolidado</button>
                    <button type="button" id="mInd" class="btn-mode">Individual</button>
                </div>
            </div>

            <div class="field">
                <label for="uSel">Unidad Operativa:</label>
                <select id="uSel">
                    <option value="EOFBC_52_2">EOFBC 52-2 (4 Botes)</option>
                    <option value="EOFBC_52_1">EOFBC 52-1 (4 Botes)</option>
                </select>
            </div>

            <div id="bIndBox" class="field hidden">
                <label for="bSel">Seleccionar Bote:</label>
                <select id="bSel"></select>
            </div>

            <div class="field">
                <label for="dInp">Distancia a Navegar:</label>
                <div style="display: flex; gap: 6px;">
                    <input type="number" id="dInp" value="50" min="0" step="any" inputmode="decimal">
                    <select id="uDist" style="width: 85px;">
                        <option value="MN">MN</option>
                        <option value="KM">KM</option>
                        <option value="MI">MI</option>
                    </select>
                </div>
            </div>

            <div class="field">
                <label for="pInp">Precio Galón (COP):</label>
                <input type="number" id="pInp" value="16000" min="0" step="100" inputmode="numeric">
            </div>
        </div>

        <div class="summary-grid">
            <div class="sum-box">
                <div class="sum-title">Combustible Req.</div>
                <div id="resCons" class="sum-val">0 Gln</div>
            </div>
            <div class="sum-box">
                <div class="sum-title">Costo Total</div>
                <div id="resCost" class="sum-val" style="color: #a7f3d0 !important;">$0</div>
            </div>
            <div class="sum-box">
                <div class="sum-title">Comb. Restante</div>
                <div id="resRest" class="sum-val" style="color: #22d3ee !important;">0 Gln</div>
            </div>
            <div class="sum-box">
                <div class="sum-title">Autonomía (Res. 30%)</div>
                <div id="resAuto" class="sum-val" style="color: #fbbf24 !important;">0 MN</div>
            </div>
        </div>

        <div class="card" style="margin-top: 8px;">
            <div class="card-title" id="detTitle">Detalle de Flotilla</div>
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>Bote</th>
                            <th>Consumo</th>
                            <th>Costo</th>
                            <th style="text-align: right;">Autonomía</th>
                        </tr>
                    </thead>
                    <tbody id="tbBody"></tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        (function() {
            var data = {
                "EOFBC_52_2": {
                    nombre: "EOFBC 52-2",
                    botes: [
                        { nombre: "Bote 01", motor: "2x 115 HP", cap: 120, cons: 0.80 },
                        { nombre: "Bote 02", motor: "2x 90 HP", cap: 100, cons: 0.76 },
                        { nombre: "Bote 03", motor: "2x 90 HP", cap: 100, cons: 0.76 },
                        { nombre: "Bote 04", motor: "2x 200 HP", cap: 153, cons: 1.70 }
                    ]
                },
                "EOFBC_52_1": {
                    nombre: "EOFBC 52-1",
                    botes: [
                        { nombre: "Bote 01", motor: "2x 90 HP", cap: 120, cons: 0.76 },
                        { nombre: "Bote 02", motor: "2x 90 HP", cap: 120, cons: 0.76 },
                        { nombre: "Bote 03", motor: "2x 90 HP", cap: 120, cons: 0.76 },
                        { nombre: "Bote 04", motor: "2x 200 HP", cap: 220, cons: 1.70 }
                    ]
                }
            };

            var modo = 'grupo';

            function getEl(id) { return document.getElementById(id); }

            function fmtCOP(val) {
                return "$" + Math.round(val).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
            }

            function updateBotes() {
                var uKey = getEl('uSel').value;
                var bSel = getEl('bSel');
                var botes = data[uKey].botes;
                bSel.innerHTML = "";
                for (var i = 0; i < botes.length; i++) {
                    var opt = document.createElement('option');
                    opt.value = i;
                    opt.text = botes[i].nombre + " (" + botes[i].motor + ")";
                    bSel.appendChild(opt);
                }
            }

            function calc() {
                var uKey = getEl('uSel').value || "EOFBC_52_2";
                var uInfo = data[uKey];
                var dVal = parseFloat(getEl('dInp').value) || 0;
                var uDist = getEl('uDist').value;
                var pVal = parseFloat(getEl('pInp').value) || 0;

                var dMN = dVal;
                if (uDist === "KM") dMN = dVal / 1.852;
                if (uDist === "MI") dMN = dVal / 1.15078;

                var list = [];
                if (modo === 'grupo') {
                    list = uInfo.botes;
                    getEl('detTitle').innerText = "Consolidado: " + uInfo.nombre;
                } else {
                    var idx = parseInt(getEl('bSel').value) || 0;
                    list = [uInfo.botes[idx] || uInfo.botes[0]];
                    getEl('detTitle').innerText = "Detalle: " + list[0].nombre;
                }

                var totCap = 0, totCons = 0, html = "";

                for (var i = 0; i < list.length; i++) {
                    var b = list[i];
                    var cBote = dMN * b.cons;
                    var pct = b.cap > 0 ? Math.min(100, (cBote / b.cap) * 100) : 0;
                    var cost = cBote * pVal;
                    var aMax = b.cons > 0 ? (b.cap / b.cons) : 0;
                    var aRes = aMax * 0.70;

                    totCap += b.cap;
                    totCons += cBote;

                    var col = "#10b981";
                    if (pct > 80) col = "#f43f5e";
                    else if (pct > 60) col = "#f59e0b";

                    html += '<tr>' +
                        '<td><b style="color:#ffffff !important;">' + b.nombre + '</b><br><span style="color:#34d399 !important;font-size:0.6rem">' + b.motor + '</span></td>' +
                        '<td><span style="color:#34d399 !important;font-weight:bold;">' + cBote.toFixed(1) + ' Gln</span><div class="bar-bg"><div class="bar-fill" style="width:' + pct + '%;background:' + col + '"></div></div></td>' +
                        '<td style="color:#ffffff !important;font-weight:bold;">' + fmtCOP(cost) + '</td>' +
                        '<td style="text-align:right;"><b style="color:#fbbf24 !important;">' + aRes.toFixed(0) + ' MN</b><br><span style="color:#64748b !important;font-size:0.6rem">' + (aRes * 1.852).toFixed(0) + ' KM</span></td>' +
                        '</tr>';
                        /* FIX DE TABLA - FONDO OSCURO TÁCTICO */
table {
    width: 100%;
    border-collapse: collapse;
    background-color: #0b1329 !important; /* Mismo fondo oscuro de la tarjeta */
    border-radius: 8px;
    overflow: hidden;
}

/* ENCABEZADOS DE TABLA */
th {
    background-color: #0b1329 !important;
    color: #94a3b8 !important; /* Gris claro legible */
    border-bottom: 1px solid #1e293b !important;
    padding: 10px 8px;
    font-size: 0.7rem;
    text-transform: uppercase;
}

/* CELDAS Y DATOS */
td {
    background-color: #0b1329 !important;
    color: #f8fafc !important; /* Blanco suave */
    border-bottom: 1px solid #1e293b !important;
    padding: 12px 8px;
    font-family: monospace;
}

/* TEXTOS ESPECÍFICOS DENTRO DE LA TABLA */
td .nombre-bote {
    color: #ffffff !important;
    font-weight: bold;
}

td .capacidad, td .costo {
    color: #cbd5e1 !important; /* Gris claro visible sobre fondo oscuro */
    font-weight: 600;
}
                }

                var totRest = Math.max(0, totCap - totCons);
                var consProm = 0;
                for (var j = 0; j < list.length; j++) consProm += list[j].cons;
                var aResGrp = consProm > 0 ? ((totCap / consProm) * 0.70) : 0;

                getEl('resCons').innerText = totCons.toFixed(1) + " Gln";
                getEl('resCost').innerText = fmtCOP(totCons * pVal);
                getEl('resRest').innerText = totRest.toFixed(1) + " Gln";
                getEl('resAuto').innerText = aResGrp.toFixed(0) + " MN";
                getEl('tbBody').innerHTML = html;
            }

            function setupEvents() {
                getEl('mGrp').onclick = function() {
                    modo = 'grupo';
                    this.className = "btn-mode active";
                    getEl('mInd').className = "btn-mode";
                    getEl('bIndBox').className = "field hidden";
                    calc();
                };

                getEl('mInd').onclick = function() {
                    modo = 'individual';
                    this.className = "btn-mode active";
                    getEl('mGrp').className = "btn-mode";
                    getEl('bIndBox').className = "field";
                    updateBotes();
                    calc();
                };

                var ids = ['uSel', 'bSel', 'dInp', 'uDist', 'pInp'];
                for (var i = 0; i < ids.length; i++) {
                    var el = getEl(ids[i]);
                    if (el) {
                        el.onchange = function(e) { if(e.target.id === 'uSel') updateBotes(); calc(); };
                        el.oninput = function() { calc(); };
                    }
                }
            }

            setupEvents();
            updateBotes();
            calc();
        })();
    </script>
</body>
</html>
