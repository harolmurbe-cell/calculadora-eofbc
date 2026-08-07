<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora Táctica de Combustible - EOFBC</title>
    
    <!-- PWA -->
    <link rel="manifest" href="./manifest.json">
    <meta name="theme-color" content="#0f172a">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">

    <style>
        :root {
            --bg-color: #0b0f19;
            --card-bg: #1e293b;
            --accent-green: #10b981;
            --accent-blue: #3b82f6;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --border-color: #334155;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            padding: 15px;
            display: flex;
            justify-content: center;
        }

        .container {
            width: 100%;
            max-width: 1100px;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        /* HEADER */
        .header {
            background: var(--card-bg);
            padding: 15px 20px;
            border-radius: 12px;
            border: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .header h1 {
            font-size: 1.2rem;
            color: var(--text-main);
        }

        .badge {
            background: var(--accent-green);
            color: #000;
            padding: 4px 10px;
            border-radius: 6px;
            font-weight: bold;
            font-size: 0.8rem;
        }

        /* GRID CONTROLES */
        .controls-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 15px;
            background: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
            border: 1px solid var(--border-color);
        }

        .field-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .field-group label {
            font-size: 0.85rem;
            color: var(--text-muted);
            font-weight: 600;
        }

        .input-box {
            display: flex;
            gap: 8px;
        }

        input, select {
            width: 100%;
            padding: 12px;
            background: #0f172a;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            color: var(--text-main);
            font-size: 1rem;
            outline: none;
        }

        input:focus, select:focus {
            border-color: var(--accent-blue);
        }

        .unit-tag {
            background: #334155;
            padding: 12px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 0.9rem;
        }

        /* METRIC CARDS */
        .metrics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 15px;
        }

        .metric-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
            border: 1px solid var(--border-color);
        }

        .metric-card .title {
            font-size: 0.8rem;
            color: var(--text-muted);
            text-transform: uppercase;
            font-weight: 700;
        }

        .metric-card .value {
            font-size: 1.8rem;
            font-weight: 800;
            color: var(--accent-green);
            margin: 8px 0;
        }

        .metric-card .subtext {
            font-size: 0.8rem;
            color: var(--text-muted);
        }

        /* TABLA DESGLOSE */
        .table-container {
            background: var(--card-bg);
            border-radius: 12px;
            border: 1px solid var(--border-color);
            overflow-x: auto;
            padding: 15px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
            min-width: 500px;
        }

        th {
            background: #0f172a;
            padding: 12px;
            color: var(--text-muted);
            font-size: 0.8rem;
            border-bottom: 1px solid var(--border-color);
        }

        td {
            padding: 14px 12px;
            border-bottom: 1px solid var(--border-color);
            font-size: 0.95rem;
        }

        .highlight-val {
            color: var(--accent-green);
            font-weight: bold;
        }

        /* RESPONSIVO CELULAR */
        @media (max-width: 600px) {
            .controls-grid {
                grid-template-columns: 1fr;
            }
            .metric-card .value {
                font-size: 1.5rem;
            }
        }
    </style>
</head>
<body>

<div class="container">

    <!-- CABECERA -->
    <div class="header">
        <h1>CONSOLIDADO: EOFBC 52-2</h1>
        <span class="badge">OPERATIVO</span>
    </div>

    <!-- PANEL DE PARÁMETROS -->
    <div class="controls-grid">
        
        <div class="field-group">
            <label for="distanciaInput">DISTANCIA A NAVEGAR:</label>
            <div class="input-box">
                <input type="number" id="distanciaInput" value="50" min="1">
                <span class="unit-tag">MN</span>
            </div>
        </div>

        <div class="field-group">
            <label for="precioGalonInput">PRECIO GALÓN (COP):</label>
            <input type="number" id="precioGalonInput" value="16000" step="500">
        </div>

        <div class="field-group">
            <label for="corrienteSelect">SENTIDO NAVEGACIÓN:</label>
            <select id="corrienteSelect">
                <option value="1.0" selected>Neutro (Aguas Calmas)</option>
                <option value="1.20">Contra Corriente (Aguas Arriba +20%)</option>
                <option value="0.85">A Favor de Corriente (Aguas Abajo -15%)</option>
            </select>
        </div>

        <div class="field-group">
            <label for="rpmSelect">RÉGIMEN DE MARCHA (RPM):</label>
            <select id="rpmSelect">
                <option value="0.80">2.500 RPM (Velocidad Mínima)</option>
                <option value="0.90" selected>4.000 RPM (Crucero Económico)</option>
                <option value="1.00">4.800 RPM (Crucero Táctico)</option>
                <option value="1.25">5.500 RPM (Máxima Potencia)</option>
            </select>
        </div>

    </div>

    <!-- TARJETAS DE MÉTRICAS -->
    <div class="metrics-grid">
        <div class="metric-card">
            <div class="title">COMB. REQUERIDO</div>
            <div class="value" id="totalGalones">0.0 Gln</div>
            <div class="subtext" id="totalKm">0 KM equiv.</div>
        </div>

        <div class="metric-card">
            <div class="title">COSTO MISIÓN</div>
            <div class="value" id="costoTotal">$0</div>
            <div class="subtext">Valor estimado flota</div>
        </div>

        <div class="metric-card">
            <div class="title">CAPACIDAD TOTAL</div>
            <div class="value" style="color: var(--accent-blue);" id="capacidadTanques">473 Gln</div>
            <div class="subtext">En tanques de flotilla</div>
        </div>
    </div>

    <!-- TABLA DESGLOSE -->
    <div class="table-container">
        <table>
            <thead>
                <tr>
                    <th>BOTE / MOTOR</th>
                    <th>CAP. TANQUE</th>
                    <th>CONSUMO ESTIMADO</th>
                    <th>COSTO (COP)</th>
                </tr>
            </thead>
            <tbody id="tablaBotes">
                <!-- Se llena dinámicamente con JavaScript -->
            </tbody>
        </table>
    </div>

</div>

<script>
    // LISTA DE BOTES Y SUS CONSUMOS BASE POR MILLA (GPM COMBINADO DE SUS MOTORES)
    const flotilla = [
        { nombre: "Bote 01", motor: "2x 115 HP", cap: 120, consBase: 0.80 },
        { nombre: "Bote 02", motor: "2x 90 HP",  cap: 100, consBase: 0.76 },
        { nombre: "Bote 03", motor: "2x 90 HP",  cap: 100, consBase: 0.76 },
        { nombre: "Bote 04", motor: "2x 200 HP", cap: 153, consBase: 1.70 }
    ];

    function calcular() {
        const distancia = parseFloat(document.getElementById('distanciaInput').value) || 0;
        const precioGalon = parseFloat(document.getElementById('precioGalonInput').value) || 0;
        const factorCorriente = parseFloat(document.getElementById('corrienteSelect').value) || 1.0;
        
        // Lee el valor del selector (que ahora es el FACTOR DECIMAL: 0.80, 0.90, 1.00, etc.)
        const factorRPM = parseFloat(document.getElementById('rpmSelect').value) || 1.0;

        let totalGln = 0;
        let capacidadTotal = 0;
        let tbodyHTML = "";

        flotilla.forEach(bote => {
            // CÁLCULO REAL CORRECTO
            let consumoBote = distancia * bote.consBase * factorCorriente * factorRPM;
            let costoBote = consumoBote * precioGalon;

            totalGln += consumoBote;
            capacidadTotal += bote.cap;

            tbodyHTML += `
                <tr>
                    <td><strong>${bote.nombre}</strong><br><small style="color:var(--text-muted)">${bote.motor}</small></td>
                    <td>${bote.cap} Gln</td>
                    <td class="highlight-val">${consumoBote.toFixed(1)} Gln</td>
                    <td>$${costoBote.toLocaleString('es-CO', {maximumFractionDigits: 0})}</td>
                </tr>
            `;
        });

        let costoMision = totalGln * precioGalon;
        let distanciaKM = distancia * 1.852;

        // ACTUALIZAR INTERFAZ
        document.getElementById('totalGalones').textContent = `${totalGln.toFixed(1)} Gln`;
        document.getElementById('totalKm').textContent = `${distanciaKM.toFixed(1)} KM equiv.`;
        document.getElementById('costoTotal').textContent = `$${costoMision.toLocaleString('es-CO', {maximumFractionDigits: 0})}`;
        document.getElementById('capacidadTanques').textContent = `${capacidadTotal} Gln`;
        document.getElementById('tablaBotes').innerHTML = tbodyHTML;
    }

    // REGISTRAR EVENTOS
    const inputs = ['distanciaInput', 'precioGalonInput', 'corrienteSelect', 'rpmSelect'];
    inputs.forEach(id => {
        document.getElementById(id).addEventListener('input', calcular);
        document.getElementById(id).addEventListener('change', calcular);
    });

    // ACTIVACIÓN SERVICE WORKER (PWA)
    if ('serviceWorker' in navigator) {
        window.addEventListener('load', () => {
            navigator.serviceWorker.register('./sw.js').catch(err => console.log(err));
        });
    }

    // CÁLCULO INICIAL
    calcular();
</script>

</body>
</html>
