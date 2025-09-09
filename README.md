<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Simulador E-FLEX</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background: #fff;
      color: #333;
    }
    h1 {
      text-align: center;
      color: #222;
      margin-bottom: 30px;
    }
    h2 {
      text-align: center;
      margin-top: 40px;
      color: #333;
      border-bottom: 2px solid #eee;
      padding-bottom: 5px;
    }
    .container {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 2px;
      row-gap: 2px;    /* 👈 espacio vertical independiente */
    }
    .box {
      padding: 15px;
      border-radius: 10px;
      text-align: center;
      font-weight: bold;
      color: white;
      min-width: 150px;   /* ancho mínimo fijo */
      min-height: 120px;  /* alto fijo para todos */
      display: flex;
      flex-direction: column;
     justify-content: center;
    }
    .blue { background: #0057b7; }
    .purple { background: #6a0dad; }
    .lightpurple { background: #9370db; }
    .result {
      font-size: 22px;
      margin-top: 10px;
    }
    .form-box {
      background: #f4f4f4;
      padding: 15px;
      border-radius: 10px;
      grid-column: span 1;
    }
    label {
      display: block;
      margin-top: 8px;
      font-size: 14px;
    }
    input {
      width: 100%;
      padding: 5px;
      margin-top: 3px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    button {
      margin-top: 20px;
      padding: 10px;
      width: 100%;
      background: #0057b7;
      color: white;
      border: none;
      font-size: 16px;
      font-weight: bold;
      cursor: pointer;
      border-radius: 8px;
    }
    button:hover {
      background: #003f7f;
    }
    .footer {
      grid-column: span 3;
      display: flex;
      justify-content: space-around;
      margin-top: 20px;
    }
    .footer-box {
      flex: 1;
      margin: 0 10px;
      padding: 15px;
      text-align: center;
      font-size: 20px;
      border-radius: 10px;
      color: white;
    }
    .pyramid {
      grid-column: span 3;
      text-align: center;
      margin-top: 20px;
    }

    .docentes-grupos {
  display: grid;
  grid-template-columns: 1fr 1fr; /* dos columnas abajo */
  grid-template-rows: auto auto; /* una fila arriba, otra abajo */
  gap: 2px;
  row-gap: 2px;    /* 👈 espacio vertical independiente */
  grid-column: span 1; /* opcional: para que ocupe dos columnas del grid padre */
}

.box.full {
  grid-column: span 2; /* Docentes ocupa toda la fila */
  height: 50px;
}

.box.half {
  height: 100px;
}

.yellow {
  background: #d4af37;
  color: #000;
}

    .wide { width: 250px; margin: 10px auto; }
    .wider { width: 350px; margin: 10px auto; }
    .widest { width: 450px; margin: 10px auto; }
    table {
      margin: 20px auto;
      border-collapse: collapse;
      text-align: center;
      width: 80%;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 8px 12px;
    }
    th {
      background: #0057b7;
      color: white;
    }
    tr:nth-child(even) { background: #f9f9f9; }
    tr:last-child { font-weight: bold; }
  </style>
</head>
<body>

  <h1>SIMULADOR E-FLEX</h1>

  <div class="container">
    <!-- Valores de entrada -->
    <div class="form-box">
      <label>Valor Promedio Curso</label>
      <input type="number" id="avgCourseValue" value="590000">

      <label>Promedio Matriculados x Salón</label>
      <input type="number" id="avgEnrolled" value="35">

      <label>Docente Medio Tiempo</label>
      <input type="number" id="docenteMT" value="40">

      <label>Docente Tiempo Completo</label>
      <input type="number" id="docenteTC" value="40">

      <label>Horas Semanales MT (Max 20)</label>
      <input type="number" id="horasMT" value="20">

      <label>Horas Semanales TC (Max 40)</label>
      <input type="number" id="horasTC" value="40">

        <label>Presupuesto Total</label>
        <input type="number" id="Presupuesto" value="8489392000">

      <label>Matrículas vs Certificados (%)</label>
      <input type="number" id="percCertificados" value="60">

      <label>Gestionados vs Matriculados (%)</label>
      <input type="number" id="percGestionados" value="70">

      <button onclick="simulate()">SIMULAR</button>
    </div>

    <!-- Bloques de datos básicos -->
    <div class="docentes-grupos">
    <div class="box blue full">
        <div>Docentes</div>
        <div class="result" id="docentes">0</div>
    </div>
    <div class="box blue half">
        <div>Grupos MT</div>
        <div class="result" id="gruposMT">0</div>
    </div>
    <div class="box yellow half">
        <div>Grupos TC</div>
        <div class="result" id="gruposTC">0</div>
    </div>
    </div>

    <!-- Pirámide de resultados -->
    <div class="pyramid">
      <div class="box purple wide">
        <div>Certificados</div>
        <div class="result" id="certificados">0</div>
      </div>
      <div class="box purple wider">
        <div>Matriculados</div>
        <div class="result" id="matriculados">0</div>
      </div>
      <div class="box lightpurple widest">
        <div>Gestionados</div>
        <div class="result" id="gestionados">0</div>
      </div>
    </div>

    <!-- Resultados finales -->
    <div class="footer">
      <div class="footer-box blue">
        Presupuesto <br> <span id="presupuesto">$0</span>
      </div>
      <div class="footer-box blue">
        Ejecución Presupuestada <br> <span id="ejecucion">$0</span>
      </div>
    </div>
  </div>

  <!-- Tabla de días hábiles -->
  <h2>Días hábiles por período de facturación 2025</h2>
  <table>
    <tr>
      <th>Tipo Día</th>
      <th>Septiembre</th>
      <th>Octubre</th>
      <th>Noviembre</th>
      <th>Diciembre</th>
      <th>Total</th>
    </tr>
    <tr><td>Hábil</td><td>7</td><td>21</td><td>19</td><td>18</td><td>65</td></tr>
    <tr><td>No hábil</td><td>1</td><td>5</td><td>7</td><td>4</td><td>17</td></tr>
    <tr><td>Sábado</td><td>1</td><td>4</td><td>5</td><td>3</td><td>13</td></tr>
    <tr><td>Total</td><td>9</td><td>30</td><td>31</td><td>25</td><td>95</td></tr>
  </table>

  <script>
    function simulate() {
      // Entradas
      let avgCourseValue = parseFloat(document.getElementById("avgCourseValue").value);
      let avgEnrolled = parseFloat(document.getElementById("avgEnrolled").value);
      let docenteMT = parseInt(document.getElementById("docenteMT").value);
      let docenteTC = parseInt(document.getElementById("docenteTC").value);
      let horasMT = parseFloat(document.getElementById("horasMT").value);
      let horasTC = parseFloat(document.getElementById("horasTC").value);
      let presupuesto = parseFloat(document.getElementById("Presupuesto").value);
      let percCertificados = parseFloat(document.getElementById("percCertificados").value) / 100;
      let percGestionados = parseFloat(document.getElementById("percGestionados").value) / 100;
      

      // Cálculos básicos
      let docentes = docenteMT + docenteTC;
      let gruposMT = docenteMT * 3; // sup: 1 grupo por 20h
      let gruposTC = docenteTC * 6; // sup: 1 grupo por 40h
      let certificados = avgEnrolled * (gruposMT + gruposTC);
      let matriculados = certificados / percCertificados;
      let gestionados = matriculados / percGestionados;
      let ejecucion = certificados * avgCourseValue;

      // Mostrar resultados
      document.getElementById("docentes").innerText = docentes.toLocaleString();
      document.getElementById("gruposMT").innerText = gruposMT.toLocaleString();
      document.getElementById("gruposTC").innerText = gruposTC.toLocaleString();
      document.getElementById("certificados").innerText = certificados.toLocaleString();
      document.getElementById("matriculados").innerText = matriculados.toLocaleString();
      document.getElementById("gestionados").innerText = gestionados.toLocaleString();
      document.getElementById("presupuesto").innerText = "$" + presupuesto.toLocaleString();
      document.getElementById("ejecucion").innerText = "$" + ejecucion.toLocaleString();
    }
  </script>

</body>
</html>
