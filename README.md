<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Futbolcu Tahmin Oyunu</title>
  <style>
    :root{
      --bg:#0b1020; --card:#121936; --muted:#aab2d5; --text:#eaf0ff; --accent:#6da8ff; --good:#35d49b; --bad:#ff6b6b; --warn:#ffd166;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font-family:system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Helvetica,Arial,sans-serif;
      background: radial-gradient(1200px 600px at 20% -10%, #1b2457 0, #0b1020 40%),
                  radial-gradient(900px 500px at 110% 10%, #0e1640 0, #0b1020 50%),
                  var(--bg);
      color:var(--text);
      display:flex; align-items:center; justify-content:center;
    }
    .app{width:min(1100px,96vw);}
    header{display:flex; align-items:center; justify-content:space-between; gap:12px; margin:18px 0 10px}
    h1{font-size:clamp(20px,3.2vw,34px); margin:0; letter-spacing:.5px}
    .meta{display:flex; gap:10px; flex-wrap:wrap}
    .pill{background:rgba(255,255,255,.06); color:var(--muted); padding:8px 12px; border-radius:14px; font-size:12px}

    .board{display:grid; grid-template-columns:1.2fr .8fr; gap:18px}
    @media (max-width:900px){.board{grid-template-columns:1fr}}

    .card{background:linear-gradient(0deg, rgba(255,255,255,.04), rgba(255,255,255,.02)); border:1px solid rgba(255,255,255,.08); box-shadow:0 10px 30px rgba(0,0,0,.25); border-radius:18px; overflow:hidden}
    .poster{position:relative; min-height:360px; display:grid; place-items:center; padding:22px}
    @media (max-width:520px){.poster{min-height:300px}}
    .poster .halo{position:absolute; inset:0; background:
      radial-gradient(400px 220px at 50% 30%, rgba(109,168,255,.25), transparent 60%),
      radial-gradient(260px 180px at 20% 80%, rgba(53,212,155,.18), transparent 60%),
      radial-gradient(260px 180px at 80% 80%, rgba(255,209,102,.18), transparent 60%);
      filter:blur(10px);
    }
    .silhouette{
      width:min(78%, 460px);
      aspect-ratio: 3/4;
      position:relative; z-index:1;
      border-radius:18px;
      background:
        linear-gradient(160deg, rgba(255,255,255,.07), rgba(255,255,255,.02) 45%, rgba(0,0,0,.12) 46%),
        radial-gradient(60% 80% at 50% 30%, rgba(0,0,0,.55), rgba(0,0,0,.85)),
        #0a0f1f;
      overflow:hidden;
    }
    .silhouette::before{ /* boy gölgesi */
      content:""; position:absolute; inset:0; background:
        radial-gradient(120% 100% at 50% -20%, rgba(255,255,255,.10), rgba(0,0,0,0) 40%),
        url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 420"><path fill="%23000" opacity="0.85" d="M155 25c20 0 40 14 47 33 6 17 6 32 1 48l-7 20 17 11c15 10 27 25 33 43l18 57c3 9-1 19-9 23-6 3-13 2-18-2l-21-17-1 39 31 91c4 11-2 23-13 26-8 2-17-2-21-9l-32-59-10 34 7 46c2 12-7 22-19 22-10 0-18-7-20-17l-13-59-13 59c-2 10-10 17-20 17-12 0-21-10-19-22l7-46-10-34-32 59c-4 7-13 11-21 9-11-3-17-15-13-26l31-91-1-39-21 17c-5 4-12 5-18 2-8-4-12-14-9-23l18-57c6-18 18-33 33-43l17-11-7-20c-5-16-5-31 1-48 7-19 27-33 47-33h12Z"/></svg>') center/contain no-repeat;
      mix-blend-mode:screen;
      filter:drop-shadow(0 40px 60px rgba(0,0,0,.6));
      transform:scale(var(--s,1)) translateY(var(--y,0));
    }
    .silhouette::after{ /* sahte ışık ve forma dokusu */
      content:""; position:absolute; inset:0; background:
        linear-gradient( to bottom, rgba(255,255,255,.12), transparent 30%),
        radial-gradient(100% 60% at 50% 0%, rgba(255,255,255,.10), transparent 50%),
        repeating-linear-gradient( 0deg, rgba(255,255,255,.04), rgba(255,255,255,.04) 2px, transparent 3px, transparent 6px);
      mix-blend-mode:overlay;
    }

    .content{padding:18px 18px 20px}
    .hints{display:flex; flex-direction:column; gap:10px; padding:16px}
    .hints li{list-style:none; background:rgba(255,255,255,.04); border:1px dashed rgba(255,255,255,.12); padding:10px 12px; border-radius:12px; color:var(--muted); font-size:14px}

    .right{display:flex; flex-direction:column; gap:16px}
    .inputRow{display:flex; gap:10px}
    input[type="text"]{flex:1; background:#0e1530; border:1px solid rgba(255,255,255,.14); color:var(--text); padding:12px 14px; border-radius:12px; outline:none; font-size:15px}
    .btn{cursor:pointer; border:0; border-radius:12px; padding:12px 14px; font-weight:600; letter-spacing:.2px; transition:transform .05s ease, box-shadow .2s ease; box-shadow:0 6px 16px rgba(0,0,0,.25)}
    .btn:active{transform:translateY(1px)}
    .primary{background:linear-gradient(180deg, var(--accent), #4e82f5); color:#0b1020}
    .ghost{background:rgba(255,255,255,.06); color:var(--text); border:1px solid rgba(255,255,255,.12)}
    .danger{background:linear-gradient(180deg, var(--bad), #e15252); color:#fff}

    .status{display:flex; gap:8px; align-items:center; color:var(--muted); font-size:14px}
    .dot{width:10px; height:10px; border-radius:50%; background:var(--warn); box-shadow:0 0 16px var(--warn)}

    .scorebar{display:grid; grid-template-columns:repeat(3,1fr); gap:10px}
    .scorebar .tile{background:rgba(255,255,255,.06); padding:14px; border-radius:14px; border:1px solid rgba(255,255,255,.1)}
    .scorebar .tile h3{margin:0; font-size:12px; color:var(--muted); font-weight:600}
    .scorebar .tile .v{font-size:20px; font-weight:800; margin-top:6px}

    .answer{font-weight:800}
    .toast{position:fixed; left:50%; top:14px; transform:translateX(-50%); background:#0e1530; border:1px solid rgba(255,255,255,.12); padding:12px 16px; border-radius:12px; color:var(--text); box-shadow:0 10px 30px rgba(0,0,0,.35); opacity:0; pointer-events:none; transition:opacity .2s ease, transform .2s ease}
    .toast.show{opacity:1; transform:translate(-50%, 0)}

    footer{margin:18px 0 28px; color:var(--muted); font-size:12px; text-align:center}
    .kbd{border:1px solid rgba(255,255,255,.2); padding:2px 6px; border-radius:6px; font-weight:700; color:#fff; background:rgba(255,255,255,.08)}
  </style>
</head>
<body>
  <div class="app">
    <header>
      <h1>⚽ Futbolcu Tahmin Oyunu</h1>
      <div class="meta">
        <div class="pill">Silüetlere bak, ipuçlarını oku, ismi yaz</div>
        <div class="pill">Mobil uyumlu · Tek dosya</div>
      </div>
    </header>

    <section class="board">
      <!-- SOL TARAF: Poster + İpuçları -->
      <article class="card">
        <div class="poster">
          <div class="halo"></div>
          <div id="silhouette" class="silhouette"></div>
        </div>
        <div class="content">
          <ul id="hints" class="hints"></ul>
        </div>
      </article>

      <!-- SAĞ TARAF: Oyun Kontrolleri + Skorlar -->
      <aside class="right">
        <div class="card" style="padding:16px">
          <div class="scorebar">
            <div class="tile"><h3>Skor</h3><div id="score" class="v">0</div></div>
            <div class="tile"><h3>Seri</h3><div id="streak" class="v">0</div></div>
            <div class="tile"><h3>En İyi</h3><div id="best" class="v">0</div></div>
          </div>
          <div style="height:10px"></div>
          <div class="status"><span class="dot" id="statusDot"></span><span id="statusText">Yeni tur hazır</span></div>
          <div style="height:10px"></div>
          <div class="inputRow">
            <input id="guess" type="text" placeholder="Tahminini yaz: Örn. Ronaldo" autocomplete="off" />
            <button id="btnGuess" class="btn primary">Tahmin Et</button>
          </div>
          <div style="display:flex; gap:10px; margin-top:10px">
            <button id="btnHint" class="btn ghost">Ek İpucu</button>
            <button id="btnSkip" class="btn danger">Pas Geç</button>
          </div>
          <div id="result" style="margin-top:12px; font-size:14px; color:var(--muted)"></div>
        </div>

        <div class="card" style="padding:16px">
          <details>
            <summary style="cursor:pointer; font-weight:700">Nasıl Oynanır?</summary>
            <ul style="margin:10px 0 0 18px; color:var(--muted); font-size:14px; line-height:1.5">
              <li>Silüet ve ipuçlarına göre futbolcuyu tahmin et.</li>
              <li><span class="kbd">Enter</span> ile hızlıca tahmin gönderebilirsin.</li>
              <li>Yalnızca soyadını da yazabilirsin (örn. <em>Messi</em>).</li>
              <li>Doğru cevap +100 puan. Her ek ipucu -15 puan.</li>
              <li>Pas geçmek doğru cevabı gösterir ve seri sıfırlanır.</li>
            </ul>
          </details>
        </div>
      </aside>
    </section>

    <footer>
      © 2025 – Tek dosyalık mini oyun. Klavye kısayolu: <span class="kbd">Enter</span> = Tahmin, <span class="kbd">Ctrl</span> + <span class="kbd">H</span> = Ek ipucu, <span class="kbd">Ctrl</span> + <span class="kbd">S</span> = Pas.
    </footer>
  </div>

  <div id="toast" class="toast">Mesaj</div>

  <script>
    // --- Basit veri tabanı: futbolcular ---
    const PLAYERS = [
      {
        name: "Cristiano Ronaldo",
        aliases: ["cristiano", "ronaldo", "cr7"],
        hints: [
          "Kulüpler: Sporting CP → Manchester United → Real Madrid → Juventus → Al Nassr",
          "Milli takım: Portekiz · 2003-",
          "Konum: Forvet / Sağ kanat",
          "Kariyer rekoru: 800+ resmi gol",
        ],
      },
      {
        name: "Lionel Messi",
        aliases: ["leo", "lionel", "messi", "leomessi"],
        hints: [
          "Kulüpler: Barcelona → Paris Saint‑Germain → Inter Miami",
          "Milli takım: Arjantin · 2005-",
          "Konum: Forvet / On numara",
          "Dünya Kupası: 2022 şampiyonu",
        ],
      },
      {
        name: "Neymar Jr",
        aliases: ["neymar", "neymar jr", "neymarjr"],
        hints: [
          "Kulüpler: Santos → Barcelona → Paris Saint‑Germain → Al Hilal",
          "Milli takım: Brezilya",
          "Konum: Sol kanat / Forvet",
          "Dripling ve çalım ustası",
        ],
      },
      {
        name: "Kylian Mbappé",
        aliases: ["mbappe", "kylian", "mbappee", "mbappé"],
        hints: [
          "Kulüpler: Monaco → Paris Saint‑Germain → Real Madrid",
          "Milli takım: Fransa",
          "Konum: Forvet",
          "Hızlı sprintleriyle ünlü",
        ],
      },
      {
        name: "Robert Lewandowski",
        aliases: ["lewandowski", "robert"],
        hints: [
          "Kulüpler: Lech Poznań → Dortmund → Bayern → Barcelona",
          "Milli takım: Polonya",
          "Konum: Santrfor",
          "Gol kralı unvanları",
        ],
      },
      {
        name: "Mohamed Salah",
        aliases: ["salah", "mo salah", "mosalah"],
        hints: [
          "Kulüpler: El Mokawloon → Basel → Chelsea → Fiorentina → Roma → Liverpool",
          "Milli takım: Mısır",
          "Konum: Sağ kanat",
          "Premier League yıldızı",
        ],
      },
      {
        name: "Kevin De Bruyne",
        aliases: ["de bruyne", "kevin", "kdb", "debruyne"],
        hints: [
          "Kulüpler: Genk → Chelsea → Werder Bremen (k) → Wolfsburg → Manchester City",
          "Milli takım: Belçika",
          "Konum: Orta saha (8/10)",
          "Pas yeteneği ve vizyon",
        ],
      },
      {
        name: "Luka Modrić",
        aliases: ["modric", "modriç", "luka"],
        hints: [
          "Kulüpler: Dinamo Zagreb → Tottenham → Real Madrid",
          "Milli takım: Hırvatistan",
          "Konum: Orta saha",
          "Ballon d'Or sahibi",
        ],
      },
      {
        name: "Erling Haaland",
        aliases: ["haaland", "erling"],
        hints: [
          "Kulüpler: Bryne → Molde → Salzburg → Dortmund → Manchester City",
          "Milli takım: Norveç",
          "Konum: Santrfor",
          "Fizik ve bitiricilik",
        ],
      },
      {
        name: "Harry Kane",
        aliases: ["kane", "harry"],
        hints: [
          "Kulüpler: Tottenham → Bayern Münih",
          "Milli takım: İngiltere",
          "Konum: Santrfor",
          "Gol ve oyun kurma",
        ],
      },
      {
        name: "Andrés Iniesta",
        aliases: ["iniesta", "andres", "andrés"],
        hints: [
          "Kulüpler: Barcelona → Vissel Kobe → Emirates Club",
          "Milli takım: İspanya",
          "Konum: Orta saha",
          "2010 Dünya Kupası final golü",
        ],
      },
      {
        name: "Xavi Hernández",
        aliases: ["xavi", "xavi hernandez", "xavi hernández"],
        hints: [
          "Kulüpler: Barcelona → Al Sadd",
          "Milli takım: İspanya",
          "Konum: Orta saha",
          "Pas üçgenlerinin efendisi",
        ],
      },
      {
        name: "Ronaldinho",
        aliases: ["ronaldinho", "gaucho"],
        hints: [
          "Kulüpler: Grêmio → Paris Saint‑Germain → Barcelona → Milan → Flamengo/Atlético‑MG → Querétaro → Fluminense",
          "Milli takım: Brezilya",
          "Konum: On numara / Sol kanat",
          "Frikik ve elastico ustası",
        ],
      },
      {
        name: "Zlatan Ibrahimović",
        aliases: ["zlatan", "ibrahimovic", "ibrahimović"],
        hints: [
          "Kulüpler: Malmö → Ajax → Juventus → Inter → Barcelona → Milan → PSG → Manchester United → LA Galaxy → Milan",
          "Milli takım: İsveç",
          "Konum: Santrfor",
          "Akrobatik goller",
        ],
      },
      {
        name: "Gareth Bale",
        aliases: ["bale", "gareth"],
        hints: [
          "Kulüpler: Southampton → Tottenham → Real Madrid → LAFC",
          "Milli takım: Galler",
          "Konum: Sağ/sol kanat",
          "Sürat ve uzaktan şut",
        ],
      },
    ];

    // --- Yardımcılar ---
    const $ = sel => document.querySelector(sel);
    const $$ = sel => [...document.querySelectorAll(sel)];
    const toast = (msg, t=2000)=>{const el=$('#toast'); el.textContent=msg; el.classList.add('show'); setTimeout(()=>el.classList.remove('show'), t)}
    const norm = s => (s||"").toString().toLowerCase().normalize('NFD').replace(/[\u0300-\u036f]/g,'').replace(/[^a-z0-9ğüşöçı\s]/gi,'').trim();

    // --- Oyun durumu ---
    let state = {
      order: [], idx: 0, revealed: 1,
      score: 0, streak: 0, best: Number(localStorage.getItem('bestScore')||0)
    };

    const setStatus = (txt, colorVar='--warn')=>{
      $('#statusText').textContent = txt;
      $('#statusDot').style.background = getComputedStyle(document.documentElement).getPropertyValue(colorVar);
      $('#statusDot').style.boxShadow = `0 0 16px ${getComputedStyle(document.documentElement).getPropertyValue(colorVar)}`;
    }

    const refreshScores = ()=>{
      $('#score').textContent = state.score;
      $('#streak').textContent = state.streak;
      $('#best').textContent = state.best;
    }

    const shuffle = arr => arr.map(v=>({v, r:Math.random()})).sort((a,b)=>a.r-b.r).map(o=>o.v);

    const nextRound = ()=>{
      state.revealed = 1;
      if (state.idx >= state.order.length) state.idx = 0;
      const p = PLAYERS[state.order[state.idx]];
      // Silüeti ufak varyasyonla göster
      const s = 0.96 + Math.random()*0.12; const y = (Math.random()*-6).toFixed(2)+'px';
      const sil = $('#silhouette'); sil.style.setProperty('--s', s); sil.style.setProperty('--y', y);
      // İpuçları
      const hints = $('#hints'); hints.innerHTML = '';
      p.hints.slice(0, state.revealed).forEach(h=>{
        const li = document.createElement('li'); li.textContent = h; hints.appendChild(li);
      });
      // Alanları temizle
      $('#guess').value=''; $('#guess').focus();
      $('#result').innerHTML = '';
      setStatus('Tahminini yaz!','--warn');
      state.idx++;
    }

    const addHint = ()=>{
      const p = PLAYERS[state.order[state.idx-1]]; // aktif oyuncu
      if (state.revealed >= p.hints.length){ toast('Tüm ipuçları zaten açık.'); return; }
      state.revealed++;
      const li = document.createElement('li'); li.textContent = p.hints[state.revealed-1];
      $('#hints').appendChild(li);
      state.score = Math.max(0, state.score - 15);
      refreshScores();
      toast('Ek ipucu açıldı (-15 puan)');
    }

    const checkGuess = ()=>{
      const p = PLAYERS[state.order[state.idx-1]];
      const g = norm($('#guess').value);
      if(!g){ toast('Bir şey yazmadın 🙂'); return; }
      const names = [p.name, ...(p.aliases||[])].map(norm);
      const ok = names.some(n => g && (n===g || n.split(' ').includes(g)) );
      if (ok){
        // Skor/seri
        const earned = Math.max(20, 100 - (state.revealed-1)*15);
        state.score += earned;
        state.streak += 1;
        state.best = Math.max(state.best, state.score);
        localStorage.setItem('bestScore', state.best);
        refreshScores();
        $('#result').innerHTML = `✅ Doğru! <span class="answer">${p.name}</span> (+${earned})`;
        setStatus('Harika tahmin!', '--good');
        toast('Doğru cevap!');
        setTimeout(nextRound, 900);
      } else {
        state.streak = 0; refreshScores();
        setStatus('Yanlış tahmin', '--bad');
        $('#result').textContent = '❌ Olmadı, tekrar dene!';
      }
    }

    const skip = ()=>{
      const p = PLAYERS[state.order[state.idx-1]];
      state.streak = 0; refreshScores();
      $('#result').innerHTML = `➡️ Pas geçildi. Cevap: <span class="answer">${p.name}</span>`;
      setStatus('Pas geçildi', '--bad');
      setTimeout(nextRound, 900);
    }

    const init = ()=>{
      state.order = shuffle([...PLAYERS.keys? [...PLAYERS.keys()] : PLAYERS.map((_,i)=>i)]);
      refreshScores();
      nextRound();
      // Events
      $('#btnGuess').addEventListener('click', checkGuess);
      $('#btnHint').addEventListener('click', addHint);
      $('#btnSkip').addEventListener('click', skip);
      $('#guess').addEventListener('keydown', e=>{ if(e.key==='Enter') checkGuess(); });
      window.addEventListener('keydown', e=>{
        if (e.ctrlKey && (e.key==='h' || e.key==='H')) { e.preventDefault(); addHint(); }
        if (e.ctrlKey && (e.key==='s' || e.key==='S')) { e.preventDefault(); skip(); }
      })
    }

    // Uygulamayı başlat
    document.addEventListener('DOMContentLoaded', init);
  </script>
</body>
</html>
