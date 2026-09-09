<!DOCTYPE html>
<html lang="id">

<head>

  <meta charset="UTF-8">

  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  >

  <meta
    name="theme-color"
    content="#0f3c68"
  >

  <title>Scanner Absensi Siswa</title>


  <script
    src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js">
  </script>


  <style>

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      background: #f1f5f9;
      color: #1e293b;
    }

    .header {
      background: linear-gradient(
        135deg,
        #0f3c68,
        #1769aa
      );
      color: white;
      padding: 18px;
      text-align: center;
    }

    .header h1 {
      margin: 0;
      font-size: 21px;
    }

    .header p {
      margin: 6px 0 0;
      font-size: 13px;
      opacity: .9;
    }

    .container {
      max-width: 560px;
      margin: auto;
      padding: 18px 12px 35px;
    }

    .card {
      background: white;
      border-radius: 18px;
      padding: 18px;
      box-shadow:
        0 4px 18px rgba(0,0,0,.08);
      margin-bottom: 15px;
    }

    .status {
      padding: 13px;
      border-radius: 12px;
      text-align: center;
      font-size: 13px;
      font-weight: bold;
      line-height: 1.5;
      margin-bottom: 15px;
      background: #eff6ff;
      color: #1e40af;
      border: 1px solid #bfdbfe;
    }

    .status.success {
      background: #dcfce7;
      color: #166534;
      border-color: #86efac;
    }

    .status.error {
      background: #fee2e2;
      color: #991b1b;
      border-color: #fca5a5;
    }

    .status.warning {
      background: #fef3c7;
      color: #92400e;
      border-color: #fcd34d;
    }

    #reader {
      width: 100%;
      overflow: hidden;
      border-radius: 16px;
      background: #000;
      border: 3px solid #2563eb;
    }

    #reader video {
      width: 100% !important;
      height: auto !important;
    }

    #reader__dashboard {
      display: none !important;
    }

    #reader__scan_region {
      min-height: 260px;
    }

    .buttons {
      display: flex;
      gap: 10px;
      margin-top: 15px;
    }

    .btn {
      flex: 1;
      border: none;
      border-radius: 12px;
      padding: 14px;
      font-size: 14px;
      font-weight: bold;
      cursor: pointer;
    }

    .btn-camera {
      background: #2563eb;
      color: white;
    }

    .btn-stop {
      background: #dc2626;
      color: white;
    }

    .btn-test {
      background: #e2e8f0;
      color: #334155;
    }

    .manual {
      display: flex;
      gap: 8px;
      margin-top: 15px;
    }

    .manual input {
      flex: 1;
      min-width: 0;
      border: 2px solid #cbd5e1;
      border-radius: 12px;
      padding: 13px;
      font-size: 15px;
      outline: none;
    }

    .manual input:focus {
      border-color: #2563eb;
    }

    .manual button {
      border: none;
      border-radius: 12px;
      padding: 13px 16px;
      background: #16a34a;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }

    .student {
      display: none;
      margin-top: 15px;
      background: #f8fafc;
      border-radius: 14px;
      padding: 15px;
      text-align: center;
    }

    .student.show {
      display: block;
    }

    .student h2 {
      margin: 0 0 6px;
      font-size: 20px;
    }

    .student p {
      margin: 4px 0;
      font-size: 13px;
      color: #64748b;
    }

    .info {
      font-size: 12px;
      color: #64748b;
      line-height: 1.6;
      text-align: center;
    }

    @media(max-width: 480px) {

      .buttons {
        flex-direction: column;
      }

      .manual {
        display: block;
      }

      .manual input {
        width: 100%;
        margin-bottom: 8px;
      }

      .manual button {
        width: 100%;
      }
    }

  </style>

</head>


<body>


<header class="header">

  <h1>
    📷 Scanner Absensi Siswa
  </h1>

  <p>
    SMKN 4 Buton Tengah
  </p>

</header>


<main class="container">


<section class="card">


  <div
    id="status"
    class="status">

    Scanner siap.
    Tekan tombol BUKA KAMERA.

  </div>


  <div id="reader"></div>


  <div class="buttons">

    <button
      class="btn btn-camera"
      onclick="startScanner()">

      📷 BUKA KAMERA

    </button>


    <button
      class="btn btn-stop"
      onclick="stopScanner()">

      ⏹ TUTUP KAMERA

    </button>

  </div>


  <div class="manual">

    <input
      id="manualBarcode"
      type="text"
      placeholder="Masukkan barcode / NIS"
      autocomplete="off"
      autocapitalize="off"
      spellcheck="false"
      onkeydown="manualEnter(event)"
    >


    <button
      onclick="prosesManual()">

      ABSEN

    </button>

  </div>

</section>


<section
  id="student"
  class="card student">

  <h2 id="studentNama">
    -
  </h2>

  <p>
    NIS:
    <span id="studentNIS">
      -
    </span>
  </p>

  <p>
    Kelas:
    <span id="studentKelas">
      -
    </span>
  </p>

  <p>
    Jurusan:
    <span id="studentJurusan">
      -
    </span>
  </p>

</section>


<section class="card">

  <div class="info">

    <strong>
      Alur Absensi
    </strong>

    <br>

    Scan pertama →
    <strong>MASUK</strong>

    <br>

    Scan kedua mulai pukul 14:00 →
    <strong>PULANG</strong>

    <br>

    Sebelum pukul 14:00 →
    <strong>DITOLAK</strong>

    <br>

    Pukul 14:00–14:59 →
    <strong>PULANG LEBIH AWAL</strong>

    <br>

    Pukul 15:00 ke atas →
    <strong>PULANG</strong>

  </div>

</section>


</main>


<script>


/* =====================================================
   KONFIGURASI
===================================================== */

const API_URL =
  "https://script.google.com/macros/s/AKfycbz9YLdUl9m6A-3OChKowjsFJKTNndmRLp9RwMbj2bXBNVe2E2s0GpKIe2SDrlfnHj8g/exec";


const DEVICE_NAME =
  "Scanner Kamera HP";


let scanner = null;

let scannerAktif = false;

let sedangMemproses = false;

let barcodeTerakhir = "";

let waktuScanTerakhir = 0;


/* =====================================================
   STATUS
===================================================== */

function setStatus(
  pesan,
  tipe
) {

  const el =
    document.getElementById(
      "status"
    );


  el.className =
    "status " +
    (
      tipe || ''
    );


  el.innerHTML =
    pesan;
}


/* =====================================================
   BUKA KAMERA
===================================================== */

async function startScanner() {

  if (
    scannerAktif
  ) {

    setStatus(
      "📷 Kamera sudah aktif. Silakan arahkan ke barcode.",
      ""
    );

    return;
  }


  try {

    setStatus(
      "⏳ Membuka kamera belakang HP...",
      ""
    );


    if (!scanner) {

      scanner =
        new Html5Qrcode(
          "reader"
        );
    }


    const config = {

      fps: 10,

      qrbox: {
        width: 280,
        height: 180
      },

      aspectRatio:
        1.7777778,

      disableFlip:
        false

    };


    await scanner.start(

      {
        facingMode:
          "environment"
      },

      config,

      function(decodedText) {

        suksesScan(
          decodedText
        );

      },

      function(errorMessage) {

        /*
         * Error pembacaan frame
         * tidak perlu ditampilkan.
         */

      }

    );


    scannerAktif =
      true;


    setStatus(
      "🟢 Kamera aktif. Arahkan kamera belakang HP ke QR Code / Barcode siswa.",
      "success"
    );


  } catch (error) {

    console.error(
      error
    );


    scannerAktif =
      false;


    setStatus(
      "❌ Kamera tidak dapat dibuka.<br><br>" +
      "Pastikan halaman dibuka melalui HTTPS dan izin kamera sudah diberikan pada browser.",
      "error"
    );
  }
}


/* =====================================================
   HASIL SCAN
===================================================== */

async function suksesScan(
  decodedText
) {

  if (
    sedangMemproses
  ) {
    return;
  }


  const barcode =
    String(
      decodedText || ''
    ).trim();


  if (!barcode) {
    return;
  }


  const sekarang =
    Date.now();


  /*
   * CEGAH SCAN BERULANG
   */

  if (

    barcode ===
    barcodeTerakhir &&

    (
      sekarang -
      waktuScanTerakhir
    ) < 3000

  ) {

    return;
  }


  barcodeTerakhir =
    barcode;

  waktuScanTerakhir =
    sekarang;


  sedangMemproses =
    true;


  try {

    await stopScanner();


    setStatus(
      "⏳ Barcode terbaca: <strong>" +
      escapeHTML(
        barcode
      ) +
      "</strong><br>Memproses absensi...",
      ""
    );


    const hasil =
      await kirimBarcode(
        barcode
      );


    tampilkanHasil(
      hasil
    );


  } catch (error) {

    console.error(
      error
    );


    setStatus(
      "❌ Gagal mengirim data ke server.<br>" +
      escapeHTML(
        error.message ||
        String(error)
      ),
      "error"
    );


  } finally {

    sedangMemproses =
      false;


    /*
     * Kamera dapat dibuka
     * kembali setelah 2 detik.
     */

    setTimeout(
      function() {

        if (
          !scannerAktif
        ) {

          startScanner();
        }

      },
      2000
    );
  }
}


/* =====================================================
   KIRIM BARCODE
   JSONP
===================================================== */

function kirimBarcode(
  barcode
) {

  return new Promise(
    function(resolve, reject) {

      const callbackName =
        "scannerCallback_" +
        Date.now() +
        "_" +
        Math.floor(
          Math.random() *
          10000
        );


      const script =
        document.createElement(
          "script"
        );


      const url =
        new URL(
          API_URL
        );


      url.searchParams.set(
        "api",
        "scan"
      );


      url.searchParams.set(
        "barcode",
        barcode
      );


      url.searchParams.set(
        "device",
        DEVICE_NAME
      );


      url.searchParams.set(
        "callback",
        callbackName
      );


      let selesai =
        false;


      const timer =
        setTimeout(
          function() {

            if (selesai) {
              return;
            }


            selesai =
              true;


            cleanup();


            reject(
              new Error(
                "Server tidak merespons dalam waktu 15 detik."
              )
            );

          },
          15000
        );


      window[
        callbackName
      ] =
        function(data) {

          if (selesai) {
            return;
          }


          selesai =
            true;


          clearTimeout(
            timer
          );


          cleanup();


          resolve(
            data
          );
        };


      script.src =
        url.toString();


      script.async =
        true;


      script.onerror =
        function() {

          if (selesai) {
            return;
          }


          selesai =
            true;


          clearTimeout(
            timer
          );


          cleanup();


          reject(
            new Error(
              "Tidak dapat terhubung ke Google Apps Script."
            )
          );
        };


      document.body
        .appendChild(
          script
        );


      function cleanup() {

        try {

          delete window[
            callbackName
          ];

        } catch (e) {

          window[
            callbackName
          ] = undefined;
        }


        if (
          script &&
          script.parentNode
        ) {

          script.parentNode
            .removeChild(
              script
            );
        }
      }

    }
  );
}


/* =====================================================
   HASIL DARI SERVER
===================================================== */

function tampilkanHasil(
  res
) {

  if (!res) {

    setStatus(
      "❌ Tidak ada respon dari server.",
      "error"
    );

    return;
  }


  if (
    res.sukses
  ) {

    setStatus(
      "✅ <strong>" +
      escapeHTML(
        res.pesan ||
        "Absensi berhasil."
      ) +
      "</strong>",
      "success"
    );


    if (
      res.siswa
    ) {

      tampilkanSiswa(
        res.siswa
      );
    }


    bunyiSukses();


  } else {

    let tipe =
      "error";


    if (
      res.tipe ===
      "BELUM_WAKTU_PULANG"
    ) {

      tipe =
        "warning";
    }


    setStatus(
      "⚠️ <strong>" +
      escapeHTML(
        res.pesan ||
        "Absensi ditolak."
      ) +
      "</strong>",
      tipe
    );


    if (
      res.siswa
    ) {

      tampilkanSiswa(
        res.siswa
      );
    }


    bunyiGagal();
  }
}


/* =====================================================
   SISWA
===================================================== */

function tampilkanSiswa(
  siswa
) {

  const el =
    document.getElementById(
      "student"
    );


  el.classList.add(
    "show"
  );


  document.getElementById(
    "studentNama"
  ).textContent =
    siswa.nama ||
    "-";


  document.getElementById(
    "studentNIS"
  ).textContent =
    siswa.nis ||
    "-";


  document.getElementById(
    "studentKelas"
  ).textContent =
    siswa.kelas ||
    "-";


  document.getElementById(
    "studentJurusan"
  ).textContent =
    siswa.jurusan ||
    "-";
}


/* =====================================================
   MANUAL
===================================================== */

function prosesManual() {

  const input =
    document.getElementById(
      "manualBarcode"
    );


  const barcode =
    String(
      input.value || ''
    ).trim();


  if (!barcode) {

    setStatus(
      "⚠️ Masukkan barcode atau NIS terlebih dahulu.",
      "warning"
    );

    return;
  }


  if (
    sedangMemproses
  ) {

    return;
  }


  sedangMemproses =
    true;


  setStatus(
    "⏳ Memproses absensi...",
    ""
  );


  kirimBarcode(
    barcode
  )

  .then(
    function(res) {

      tampilkanHasil(
        res
      );

      input.value =
        "";

    }
  )

  .catch(
    function(error) {

      setStatus(
        "❌ " +
        escapeHTML(
          error.message ||
          String(error)
        ),
        "error"
      );

    }
  )

  .finally(
    function() {

      sedangMemproses =
        false;

      input.focus();

    }
  );
}


/* =====================================================
   ENTER MANUAL
===================================================== */

function manualEnter(
  event
) {

  if (
    event.key ===
    "Enter"
  ) {

    event.preventDefault();

    prosesManual();
  }
}


/* =====================================================
   TUTUP KAMERA
===================================================== */

async function stopScanner() {

  try {

    if (
      scanner &&
      scannerAktif
    ) {

      await scanner.stop();
    }

  } catch (error) {

    console.warn(
      "Stop scanner:",
      error
    );

  }


  try {

    if (
      scanner
    ) {

      scanner.clear();
    }

  } catch (error) {

    console.warn(
      "Clear scanner:",
      error
    );
  }


  scannerAktif =
    false;
}


/* =====================================================
   TEST API
===================================================== */

function testAPI() {

  const callbackName =
    "pingCallback_" +
    Date.now();


  const script =
    document.createElement(
      "script"
    );


  const url =
    new URL(
      API_URL
    );


  url.searchParams.set(
    "api",
    "ping"
  );


  url.searchParams.set(
    "callback",
    callbackName
  );


  window[
    callbackName
  ] =
    function(data) {

      setStatus(
        "🟢 " +
        escapeHTML(
          data.pesan ||
          "API aktif."
        ),
        "success"
      );


      cleanup();
    };


  script.src =
    url.toString();


  script.onerror =
    function() {

      setStatus(
        "🔴 API Google Apps Script tidak dapat dihubungi.",
        "error"
      );


      cleanup();
    };


  document.body
    .appendChild(
      script
    );


  function cleanup() {

    try {

      delete window[
        callbackName
      ];

    } catch (e) {}


    if (
      script.parentNode
    ) {

      script.parentNode
        .removeChild(
          script
        );
    }
  }
}


/* =====================================================
   BUNYI
===================================================== */

function bunyiSukses() {

  bunyi(
    1000,
    0.12
  );
}


function bunyiGagal() {

  bunyi(
    400,
    0.20
  );
}


function bunyi(
  frequency,
  duration
) {

  try {

    const AudioContext =
      window.AudioContext ||
      window.webkitAudioContext;


    if (!AudioContext) {
      return;
    }


    const ctx =
      new AudioContext();


    const oscillator =
      ctx.createOscillator();


    const gain =
      ctx.createGain();


    oscillator.frequency.value =
      frequency;


    gain.gain.value =
      0.15;


    oscillator.connect(
      gain
    );


    gain.connect(
      ctx.destination
    );


    oscillator.start();


    oscillator.stop(
      ctx.currentTime +
      duration
    );

  } catch (e) {}
}


/* =====================================================
   ESCAPE HTML
===================================================== */

function escapeHTML(
  value
) {

  return String(
    value || ''
  )
  .replace(
    /&/g,
    "&amp;"
  )
  .replace(
    /</g,
    "&lt;"
  )
  .replace(
    />/g,
    "&gt;"
  )
  .replace(
    /"/g,
    "&quot;"
  )
  .replace(
    /'/g,
    "&#039;"
  );
}


/* =====================================================
   CEK API SAAT HALAMAN DIBUKA
===================================================== */

window.addEventListener(
  "load",
  function() {

    testAPI();

    document
      .getElementById(
        "manualBarcode"
      )
      .focus();

  }
);


/* =====================================================
   SAAT HALAMAN DITUTUP
===================================================== */

window.addEventListener(
  "beforeunload",
  function() {

    stopScanner();

  }
);


</script>

</body>
</html>
