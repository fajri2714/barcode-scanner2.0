<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Scan Barcode Project</title>
  <script src="https://unpkg.com/html5-qrcode@2.3.8/minified/html5-qrcode.min.js"></script>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      padding: 20px;
      background: #f8f8f8;
    }
    #reader {
      width: 300px;
      margin: auto;
      display: none;
    }
    #result-box {
      margin-top: 20px;
      display: none;
    }
    #start-btn, #copy-btn {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 20px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h2>Scan Barcode Project</h2>
  <p>Tekan tombol di bawah untuk mulai scan.</p>
  <button id="start-btn">Mulai Scan</button>
  <div id="reader"></div>

  <div id="result-box">
    <p><strong>Hasil Scan:</strong> <span id="result-text"></span></p>
    <button id="copy-btn">Salin & Buka Link</button>
  </div>

  <script>
    let scannedText = "";
    let html5QrCode;

    document.getElementById("start-btn").addEventListener("click", async function () {
      const readerDiv = document.getElementById("reader");
      readerDiv.style.display = "block";
      this.style.display = "none";

      try {
        // Minta akses kamera eksplisit terlebih dahulu
        await navigator.mediaDevices.getUserMedia({ video: true });

        html5QrCode = new Html5Qrcode("reader");

        html5QrCode.start(
          { facingMode: "environment" },
          {
            fps: 15,
            qrbox: 300,
            disableFlip: true,
            formatsToSupport: [
              Html5QrcodeSupportedFormats.QR_CODE,
              Html5QrcodeSupportedFormats.CODE_128,
              Html5QrcodeSupportedFormats.CODE_39,
              Html5QrcodeSupportedFormats.EAN_13,
              Html5QrcodeSupportedFormats.UPC_A
            ]
          },
          (decodedText) => {
            if (!scannedText) {
              scannedText = decodedText;
              document.getElementById("result-text").textContent = scannedText;
              document.getElementById("result-box").style.display = "block";

              // Salin hasil barcode ke clipboard
              navigator.clipboard.writeText(scannedText).catch(() => {});
              
              html5QrCode.stop(); // stop scanner
            }
          },
          (errorMessage) => {
            // silent scan error
          }
        );
      } catch (err) {
        alert("Gagal mengakses kamera: " + err);
      }
    });

    document.getElementById("copy-btn").addEventListener("click", () => {
      if (scannedText) {
        navigator.clipboard.writeText(scannedText)
          .then(() => {
            const targetURL = `http://52.74.69.49/admin/#/admin/orderprojectscan?code=${encodeURIComponent(scannedText)}`;
            window.location.href = targetURL;
          })
          .catch(err => {
            alert("Gagal menyalin ke clipboard: " + err);
          });
      }
    });
  </script>
</body>
</html>
