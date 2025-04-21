<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Generador QR PRO</title>
  <script src="https://cdn.jsdelivr.net/npm/qrcodejs/qrcode.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f9f9f9;
      color: #333;
      padding: 30px;
      text-align: center;
      transition: background 0.3s, color 0.3s;
    }
    body.dark {
      background: #1e1e1e;
      color: #f0f0f0;
    }
    input, select, button {
      margin: 10px;
      padding: 10px;
      font-size: 16px;
      border-radius: 5px;
    }
    input[type="file"] {
      border: none;
    }
    #qrcode {
      margin-top: 20px;
      position: relative;
      display: inline-block;
    }
    canvas {
      border: 1px solid #ccc;
      border-radius: 10px;
    }
    .controls {
      margin: 20px 0;
    }
    .dark-mode-toggle {
      position: absolute;
      top: 20px;
      right: 20px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="dark-mode-toggle">
    <label>
      🌙 <input type="checkbox" id="darkToggle"> Modo oscuro
    </label>
  </div>

  <h1>🎯 Generador de Código QR PRO</h1>

  <div class="controls">
    <input type="text" id="text" placeholder="Introduce texto o URL" size="40"><br>

    <label>Color QR:</label>
    <input type="color" id="qrColor" value="#000000">

    <label>Tamaño:</label>
    <select id="qrSize">
      <option value="128">128x128</option>
      <option value="256" selected>256x256</option>
      <option value="512">512x512</option>
    </select><br>

    <label>Logo (opcional):</label>
    <input type="file" id="logoUpload" accept="image/*"><br>

    <button onclick="generateQR()">Generar QR</button>
    <button onclick="downloadQR()">Descargar PNG</button>
  </div>

  <div id="qrcode"></div>

  <script>
    const toggle = document.getElementById('darkToggle');
    toggle.addEventListener('change', () => {
      document.body.classList.toggle('dark');
    });

    let qrCanvas;

    function generateQR() {
      const text = document.getElementById("text").value;
      const color = document.getElementById("qrColor").value;
      const size = parseInt(document.getElementById("qrSize").value);
      const logoFile = document.getElementById("logoUpload").files[0];

      const qrContainer = document.getElementById("qrcode");
      qrContainer.innerHTML = "";

      // Crear canvas base
      const canvas = document.createElement("canvas");
      canvas.width = size;
      canvas.height = size;
      qrContainer.appendChild(canvas);

      const qr = new QRCode(canvas, {
        text: text,
        width: size,
        height: size,
        colorDark: color,
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.H
      });

      // Espera a que el QR se renderice para agregar logo
      setTimeout(() => {
        const ctx = canvas.getContext("2d");
        if (logoFile) {
          const reader = new FileReader();
          reader.onload = function (e) {
            const img = new Image();
            img.onload = function () {
              const logoSize = size * 0.2;
              const x = (size - logoSize) / 2;
              const y = (size - logoSize) / 2;
              ctx.drawImage(img, x, y, logoSize, logoSize);
            };
            img.src = e.target.result;
          };
          reader.readAsDataURL(logoFile);
        }
        qrCanvas = canvas;
      }, 500);
    }

    function downloadQR() {
      if (!qrCanvas) return alert("Primero genera un QR");
      const link = document.createElement("a");
      link.href = qrCanvas.toDataURL("image/png");
      link.download = "codigo-qr.png";
      link.click();
    }
  </script>
</body>
</html>

