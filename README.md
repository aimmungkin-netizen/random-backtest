<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Generator 2 Digit + Random Ajaib</title>

<style>
body {
  font-family: Arial, sans-serif;
  background: #f4f4f4;
  padding: 20px;
}
.box {
  max-width: 700px;
  margin: auto;
  background: #fff;
  padding: 16px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
textarea {
  width: 100%;
  height: 150px;
  padding: 10px;
  font-family: monospace;
}
button {
  padding: 6px 12px;
  margin-top: 6px;
  cursor: pointer;
}
.range {
  margin: 10px 0;
  font-size: 14px;
}
.range input { width: 70px; }
.warning {
  color: red;
  font-weight: bold;
  margin-top: 8px;
}
.result-box {
  border: 1px solid #ccc;
  padding: 8px;
  background: #fafafa;
  font-weight: bold;
  margin-top: 10px;
}
.result-box textarea {
  height: 45px;
  font-weight: bold;
}
.section-title {
  margin-top: 16px;
  font-size: 14px;
  color: #444;
}
.bt-row {
  font-family: monospace;
  font-size: 13px;
}
.hit { color: green; font-weight: bold; }
.miss { color: red; font-weight: bold; }
.summary {
  margin-top: 8px;
  font-weight: bold;
}
</style>
</head>

<body>
<div class="box">

<h3>Input Histori 4 Digit</h3>
<textarea id="input"></textarea>

<div class="range">
Ambil histori dari urutan
<input type="number" id="startIdx" value="9">
sampai
<input type="number" id="endIdx" value="58">
(dari histori TERBARU)
</div>

<button onclick="proses()">Proses</button>
<button onclick="resetAll()">Reset</button>
<button onclick="backtest()">🔍 Backtest 100 Hari</button>

<div id="warning" class="warning"></div>

<div class="section-title">Hasil Utama</div>
<div id="utama"></div>

<button onclick="acakAjaib()">🔀 Random</button>

<div class="section-title">Hasil Random</div>
<div id="random"></div>

<div class="section-title">Hasil Backtest</div>
<div id="backtest"></div>

</div>

<script>
function proses() {
  const input = document.getElementById("input").value.trim();
  const warning = document.getElementById("warning");
  const utama = document.getElementById("utama");
  const random = document.getElementById("random");
  warning.textContent = "";
  utama.innerHTML = "";
  random.innerHTML = "";

  let raw = input.split(/\s+/).filter(x => /^\d{4}$/.test(x));
  let start = parseInt(startIdx.value);
  let end = parseInt(endIdx.value);

  if (raw.length < end) {
    warning.textContent = "⚠️ Histori kurang dari batas range.";
    return;
  }

  let rev = [...raw].reverse();
  let pool = [];

  for (let i = start - 1; i <= end - 1; i++) {
    let d2 = rev[i].slice(2);
    if (!pool.includes(d2)) pool.push(d2);
  }

  while (pool.length < 50) {
    let r = Math.floor(Math.random() * 100).toString().padStart(2, "0");
    if (!pool.includes(r)) pool.push(r);
  }

  tampilkan(pool, utama);
}

function tampilkan(arr, target) {
  target.innerHTML = "";
  for (let i = 0; i < 5; i++) {
    let slice = arr.slice(i * 10, i * 10 + 10).join("*");
    let div = document.createElement("div");
    div.className = "result-box";
    div.innerHTML = `
      <textarea readonly>${slice}</textarea>
      <button onclick="copy(this)">Salin</button>
    `;
    target.appendChild(div);
  }
}

function acakAjaib() {
  let texts = document.querySelectorAll("#utama textarea");
  let pool = [];

  texts.forEach(t => {
    t.value.split("*").forEach(x => {
      if (/^\d{2}$/.test(x) && !pool.includes(x)) pool.push(x);
    });
  });

  while (pool.length < 50) {
    let r = Math.floor(Math.random() * 100).toString().padStart(2, "0");
    if (!pool.includes(r)) pool.push(r);
  }

  pool.sort(() => Math.random() - 0.5);
  tampilkan(pool, document.getElementById("random"));
}

function backtest() {
  const input = document.getElementById("input").value.trim();
  const out = document.getElementById("backtest");
  out.innerHTML = "";

  let raw = input.split(/\s+/).filter(x => /^\d{4}$/.test(x));
  let start = parseInt(startIdx.value);
  let end = parseInt(endIdx.value);

  if (raw.length < end + 100) {
    out.innerHTML = "<b>⚠️ Histori tidak cukup untuk backtest 100 hari</b>";
    return;
  }

  let HIT = 0, MISS = 0;

  for (let day = 1; day <= 100; day++) {
    let testHist = raw.slice(0, raw.length - day);
    let target = raw[raw.length - day].slice(2);

    let rev = [...testHist].reverse();
    let pool = [];

    for (let i = start - 1; i <= end - 1; i++) {
      let d2 = rev[i].slice(2);
      if (!pool.includes(d2)) pool.push(d2);
    }

    let kena = pool.includes(target);
    if (kena) HIT++; else MISS++;

    let div = document.createElement("div");
    div.className = "bt-row " + (kena ? "hit" : "miss");
    div.textContent = `Hari ${day} | Target ${target} | ${kena ? "HIT" : "MISS"}`;
    out.appendChild(div);
  }

  let sum = document.createElement("div");
  sum.className = "summary";
  sum.innerHTML = `
    <hr>
    Total HIT  : ${HIT}<br>
    Total MISS : ${MISS}<br>
    Akurasi   : ${((HIT / 100) * 100).toFixed(2)}%
  `;
  out.appendChild(sum);
}

function copy(btn) {
  navigator.clipboard.writeText(btn.previousElementSibling.value);
}

function resetAll() {
  input.value = "";
  utama.innerHTML = "";
  random.innerHTML = "";
  backtest.innerHTML = "";
  warning.textContent = "";
}
</script>

</body>
</html>
