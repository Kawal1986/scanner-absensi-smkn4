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

<script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>

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
  min-height: 260px;
  overflow: hidden;
  border-radius: 16px;
  background: #000;
  border: 3px solid #2563eb;
}

#reader video {
  width: 100% !important;
  height: auto !important;
  object-fit: cover;
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

.btn:active {
  transform: scale(.98);
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
  margin: 0 0 8px;
  font-size: 20px;
}

.student p {
  margin: 5px 0;
  font-size: 13px;
  color: #64748b;
}

.result-badge {
  display: inline-block;
  margin-top: 10px;
  padding: 8px 15px;
  border-radius: 999px;
  font-size: 13px;
  font-weight: bold;
  background: #dbeafe;
  color: #1d4ed8;
}

.info {
  font-size: 12px;
  color: #64748b;
  line-height: 1.7;
  text-align: center;
}

.api-status {
  text-align: center;
  font-size: 11px;
  color: #64748b;
  margin-top: 10px;
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
      type="button"
      class="btn btn-camera"
      onclick="startScanner()">

      📷 BUKA KAMERA

    </button>


    <button
      type="button"
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
      type="button"
      onclick="prosesManual()">

      ABSEN

    </button>

  </div>


  <div
    id="apiStatus"
    class="api-status">

    Mengecek koneksi server...

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
    <strong id="studentNIS">-</strong>
  </p>

  <p>
    Kelas:
    <strong id="studentKelas">-</strong>
  </p>

  <p>
    Jurusan:
    <strong id="studentJurusan">-</strong>
  </p>

  <div
    id="resultBadge"
    class="result-badge">

    -

  </div>

</section>


<section class="card">

  <div class="info">

    <strong>ALUR ABSENSI</strong>

    <br><br>

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

    <br><br>

    <strong>
      Scanner terhubung langsung ke Google Sheets.
    </strong>

  </div>

</section>


</main>


<script>

/* =====================================================
   KONFIGURASI API
===================================================== */

const API_URL =
  "https://script.google.com/macros/s/AKfycbzDJxMaAGQrLbrCzBqBlwx3Dc2Uh2HLNJXwEAZ3cOGAkABZcTcRDHbjtW_9C3VkkMbNpw/exec";


const DEVICE_NAME =
  "Scanner Kamera HP";


/* =====================================================
   VARIABEL
===================================================== */

let scanner = null;

let scannerAktif = false;

let sedangMemproses = false;

let barcodeTerakhir = "";

let waktuScanTerakhir = 0;


/* =====================================================
   STATUS
===================================================== */

function setStatus(pesan, tipe = "") {

  const el =
    document.getElementById("status");

  el.className =
    "status " + tipe;

  el.innerHTML =
    pesan;
}


/* =====================================================
   BUKA KAMERA
===================================================== */

async function startScanner() {

  if (scannerAktif) {

    setStatus(
      "📷 Kamera sudah aktif.<br>" +
      "Silakan arahkan kamera ke QR Code siswa.",
      "success"
    );

    return;
  }


  try {

    setStatus(
      "⏳ Membuka kamera belakang HP...",
      ""
    );


    /*
     * Buat objek scanner baru.
     * Ini membuat proses buka/tutup kamera
     * lebih stabil setelah scan.
     */

    scanner =
      new Html5Qrcode("reader");


    const config = {

      fps: 10,

      qrbox: {
        width: 280,
        height: 180
      },

      aspectRatio: 1.7777778,

      disableFlip: false

    };


    await scanner.start(

      {
        facingMode: "environment"
      },

      config,

      function(decodedText) {

        suksesScan(decodedText);

      },

      function(errorMessage) {

        /*
         * Kesalahan pembacaan frame
         * tidak perlu ditampilkan.
         */

      }

    );


    scannerAktif = true;


    setStatus(
      "🟢 KAMERA AKTIF<br>" +
      "Arahkan kamera belakang HP ke QR Code siswa.",
      "success"
    );

  }

  catch(error) {

    console.error(
      "Gagal membuka kamera:",
      error
    );


    scannerAktif = false;


    let pesan =
      "❌ Kamera tidak dapat dibuka.";


    if (
      location.protocol !== "https:" &&
      location.hostname !== "localhost"
    ) {

      pesan +=
        "<br><br>" +
        "Halaman scanner harus dibuka melalui HTTPS.";

    } else {

      pesan +=
        "<br><br>" +
        "Pastikan izin kamera browser sudah diberikan.";

    }


    setStatus(
      pesan,
      "error"
    );

  }

}


/* =====================================================
   HASIL SCAN
===================================================== */

async function suksesScan(decodedText) {

  if (sedangMemproses) {
    return;
  }


  const barcode =
    String(
      decodedText || ""
    ).trim();


  if (!barcode) {
    return;
  }


  const sekarang =
    Date.now();


  /*
   * Cegah QR yang sama terbaca berkali-kali
   */

  if (

    barcode === barcodeTerakhir &&

    (
      sekarang -
      waktuScanTerakhir
    ) < 4000

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

    /*
     * Matikan kamera sementara
     */

    await stopScanner();


    setStatus(
      "🔎 Barcode terbaca:<br>" +
      "<strong>" +
      escapeHTML(barcode) +
      "</strong>" +
      "<br><br>" +
      "⏳ Memproses absensi...",
      ""
    );


    const hasil =
      await kirimBarcode(barcode);


    tampilkanHasil(hasil);

  }

  catch(error) {

    console.error(
      "Kesalahan scan:",
      error
    );


    setStatus(
      "❌ Gagal menghubungi server.<br><br>" +
      escapeHTML(
        error.message ||
        String(error)
      ),
      "error"
    );

    bunyiGagal();

  }

  finally {

    sedangMemproses =
      false;


    /*
     * Kamera otomatis aktif kembali
     * setelah 2 detik.
     */

    setTimeout(
      function() {

        if (!scannerAktif) {

          startScanner();

        }

      },
      2000
    );

  }

}


/* =====================================================
   KIRIM BARCODE KE GOOGLE APPS SCRIPT
   MENGGUNAKAN JSONP
===================================================== */

function kirimBarcode(barcode) {

  return new Promise(
    function(resolve, reject) {

      const callbackName =
        "scannerCallback_" +
        Date.now() +
        "_" +
        Math.floor(
          Math.random() * 100000
        );


      const script =
        document.createElement("script");


      const url =
        new URL(API_URL);


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


      let selesai = false;


      const timer =
        setTimeout(
          function() {

            if (selesai) {
              return;
            }


            selesai = true;


            cleanup();


            reject(
              new Error(
                "Server tidak merespons dalam 15 detik."
              )
            );

          },
          15000
        );


      window[callbackName] =
        function(data) {

          if (selesai) {
            return;
          }


          selesai = true;


          clearTimeout(timer);


          cleanup();


          resolve(data);

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


          selesai = true;


          clearTimeout(timer);


          cleanup();


          reject(
            new Error(
              "Tidak dapat terhubung ke Google Apps Script."
            )
          );

        };


      document.body.appendChild(
        script
      );


      function cleanup() {

        try {

          delete window[
            callbackName
          ];

        }

        catch(e) {

          window[
            callbackName
          ] = undefined;

        }


        if (
          script &&
          script.parentNode
        ) {

          script.parentNode.removeChild(
            script
          );

        }

      }

    }
  );

}


/* =====================================================
   TAMPILKAN HASIL
===================================================== */

function tampilkanHasil(res) {

  if (!res) {

    setStatus(
      "❌ Server tidak memberikan respon.",
      "error"
    );

    bunyiGagal();

    return;
  }


  /*
   * ABSENSI BERHASIL
   */

  if (res.sukses) {

    let tipeHasil =
      res.tipe || "BERHASIL";


    let jam =
      res.jam
      ? "<br>🕐 Jam: " +
        escapeHTML(res.jam)
      : "";


    setStatus(

      "✅ <strong>" +
      escapeHTML(
        res.pesan ||
        "Absensi berhasil."
      ) +
      "</strong>" +

      jam,

      "success"

    );


    if (res.siswa) {

      tampilkanSiswa(
        res.siswa
      );

    }


    tampilkanBadge(
      tipeHasil
    );


    bunyiSukses();


    return;

  }


  /*
   * ABSENSI DITOLAK
   */

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


  if (res.siswa) {

    tampilkanSiswa(
      res.siswa
    );

  }


  tampilkanBadge(
    res.tipe ||
    "DITOLAK"
  );


  bunyiGagal();

}


/* =====================================================
   TAMPILKAN DATA SISWA
===================================================== */

function tampilkanSiswa(siswa) {

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
    siswa.nama || "-";


  document.getElementById(
    "studentNIS"
  ).textContent =
    siswa.nis || "-";


  document.getElementById(
    "studentKelas"
  ).textContent =
    siswa.kelas || "-";


  document.getElementById(
    "studentJurusan"
  ).textContent =
    siswa.jurusan || "-";

}


/* =====================================================
   BADGE HASIL
===================================================== */

function tampilkanBadge(tipe) {

  const badge =
    document.getElementById(
      "resultBadge"
    );


  let teks =
    tipe;


  switch(tipe) {

    case "MASUK":
      teks = "🟢 ABSEN MASUK";
      break;

    case "PULANG":
      teks = "🔵 ABSEN PULANG";
      break;

    case "STATUS_MANUAL":
      teks = "⚠️ STATUS MANUAL";
      break;

    case "BELUM_WAKTU_PULANG":
      teks = "⏰ BELUM WAKTU PULANG";
      break;

    case "SUDAH_ABSEN":
      teks = "ℹ️ SUDAH ABSEN";
      break;

    case "DUPLIKAT":
      teks = "⚠️ DUPLIKAT";
      break;

    default:
      teks =
        "ℹ️ " +
        tipe;

  }


  badge.textContent =
    teks;

}


/* =====================================================
   ABSEN MANUAL
===================================================== */

function prosesManual() {

  const input =
    document.getElementById(
      "manualBarcode"
    );


  const barcode =
    String(
      input.value || ""
    ).trim();


  if (!barcode) {

    setStatus(
      "⚠️ Masukkan barcode atau NIS terlebih dahulu.",
      "warning"
    );

    input.focus();

    return;
  }


  if (sedangMemproses) {
    return;
  }


  sedangMemproses =
    true;


  setStatus(
    "⏳ Memproses barcode manual...",
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


      bunyiGagal();

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
   ENTER
===================================================== */

function manualEnter(event) {

  if (
    event.key === "Enter"
  ) {

    event.preventDefault();

    prosesManual();

  }

}


/* =====================================================
   TUTUP KAMERA
===================================================== */

async function stopScanner() {

  if (!scanner) {

    scannerAktif =
      false;

    return;

  }


  try {

    if (scannerAktif) {

      await scanner.stop();

    }

  }

  catch(error) {

    console.warn(
      "Stop scanner:",
      error
    );

  }


  try {

    await scanner.clear();

  }

  catch(error) {

    console.warn(
      "Clear scanner:",
      error
    );

  }


  scanner = null;

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
    document.createElement("script");


  const url =
    new URL(API_URL);


  url.searchParams.set(
    "api",
    "ping"
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


        selesai = true;


        cleanup();


        tampilkanStatusAPI(
          false,
          "Server tidak merespons."
        );

      },
      10000
    );


  window[callbackName] =
    function(data) {

      if (selesai) {
        return;
      }


      selesai = true;


      clearTimeout(
        timer
      );


      tampilkanStatusAPI(
        true,
        data.pesan ||
        "API aktif."
      );


      cleanup();

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


      selesai = true;


      clearTimeout(
        timer
      );


      tampilkanStatusAPI(
        false,
        "Tidak dapat menghubungi API."
      );


      cleanup();

    };


  document.body.appendChild(
    script
  );


  function cleanup() {

    try {

      delete window[
        callbackName
      ];

    }

    catch(e) {}


    if (
      script &&
      script.parentNode
    ) {

      script.parentNode.removeChild(
        script
      );

    }

  }

}


/* =====================================================
   STATUS API
===================================================== */

function tampilkanStatusAPI(
  aktif,
  pesan
) {

  const el =
    document.getElementById(
      "apiStatus"
    );


  if (aktif) {

    el.innerHTML =
      "🟢 Server terhubung • " +
      escapeHTML(pesan);

    el.style.color =
      "#166534";

  }

  else {

    el.innerHTML =
      "🔴 Server bermasalah • " +
      escapeHTML(pesan);

    el.style.color =
      "#991b1b";

  }

}


/* =====================================================
   BUNYI SUKSES
===================================================== */

function bunyiSukses() {

  bunyi(
    1000,
    0.12
  );

}


/* =====================================================
   BUNYI GAGAL
===================================================== */

function bunyiGagal() {

  bunyi(
    400,
    0.20
  );

}


/* =====================================================
   AUDIO
===================================================== */

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


  }

  catch(e) {

    console.warn(
      "Audio:",
      e
    );

  }

}


/* =====================================================
   ESCAPE HTML
===================================================== */

function escapeHTML(value) {

  return String(
    value || ""
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
   SAAT HALAMAN DIBUKA
===================================================== */

window.addEventListener(
  "load",
  function() {

    /*
     * Cek koneksi API
     */

    testAPI();


    /*
     * Fokus ke input manual
     */

    const input =
      document.getElementById(
        "manualBarcode"
      );


    if (input) {

      input.focus();

    }

  }
);


/* =====================================================
   SAAT HALAMAN DITUTUP
===================================================== */

window.addEventListener(
  "beforeunload",
  function() {

    if (scanner) {

      scanner.stop()
        .catch(
          function() {}
        );

    }

  }
);

</script>

</body>

</html>
