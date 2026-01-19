<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Conception Wheel — Fertility Guessing Game</title>
  <style>
    :root{
      --bg0: #0b1020;
      --bg1: #0b2a2d;
      --card: rgba(255,255,255,0.08);
      --card2: rgba(255,255,255,0.06);
      --text: rgba(255,255,255,0.92);
      --muted: rgba(255,255,255,0.72);
      --muted2: rgba(255,255,255,0.55);
      --border: rgba(255,255,255,0.15);

      --wheelSize: 360px;
      --guessSize: 190px;
      --hubSize: 104px;
    }

    *{ box-sizing: border-box; }
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji";
      color: var(--text);
      background:
        radial-gradient(1200px 600px at 20% 10%, rgba(155,231,255,0.15), transparent 65%),
        radial-gradient(900px 500px at 80% 0%, rgba(255,170,232,0.12), transparent 60%),
        linear-gradient(160deg, var(--bg0), var(--bg1));
      min-height: 100vh;
    }

    .app{
      max-width: 1180px;
      margin: 0 auto;
      padding: 18px 16px 40px;
    }

    header{
      display: grid;
      grid-template-columns: 1fr;
      gap: 10px;
      align-items: start;
      margin-bottom: 14px;
    }

    .titlebar{
      display:flex;
      align-items: baseline;
      justify-content: space-between;
      gap: 14px;
      flex-wrap: wrap;
    }

    h1{
      margin: 0;
      font-size: 20px;
      letter-spacing: 0.2px;
      font-weight: 750;
    }

    .subtitle{
      font-size: 13px;
      color: var(--muted2);
      line-height: 1.35;
      max-width: 78ch;
    }

    .stats{
      display:flex;
      flex-wrap: wrap;
      gap: 10px;
    }
    .pill{
      background: var(--card);
      border: 1px solid var(--border);
      padding: 8px 10px;
      border-radius: 999px;
      font-size: 13px;
      display:flex;
      gap: 6px;
      align-items: center;
      box-shadow: 0 8px 24px rgba(0,0,0,0.18);
    }
    .pill b{ font-weight: 800; }
    .pill .muted{ color: var(--muted2); font-weight: 650; }

    .message{
      background: var(--card2);
      border: 1px solid var(--border);
      border-radius: 14px;
      padding: 10px 12px;
      font-size: 14px;
      line-height: 1.35;
      box-shadow: 0 10px 26px rgba(0,0,0,0.15);
    }

    .message strong{ font-weight: 820; }
    .message.good{ border-color: rgba(62, 255, 171, 0.32); }
    .message.bad{ border-color: rgba(255, 87, 87, 0.35); }
    .message.neutral{ border-color: var(--border); }

    .message small{
      display:block;
      margin-top: 6px;
      color: rgba(255,255,255,0.62);
      font-size: 12px;
      line-height: 1.35;
    }

    .monthRow{
      margin-top: 10px;
      display:grid;
      grid-template-columns: repeat(12, 1fr);
      gap: 6px;
      background: rgba(0,0,0,0.18);
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 16px;
      padding: 10px 10px 12px;
      box-shadow: inset 0 0 0 1px rgba(0,0,0,0.15);
    }

    .monthSlot{
      aspect-ratio: 1 / 1;
      border-radius: 12px;
      border: 1px solid rgba(255,255,255,0.18);
      background: rgba(255,255,255,0.06);
      display:grid;
      place-items:center;
      position: relative;
      overflow: hidden;
    }
    .monthSlot::after{
      content: attr(data-label);
      position:absolute;
      inset: auto 0 2px 0;
      font-size: 9px;
      text-align: center;
      color: rgba(255,255,255,0.55);
      letter-spacing: 0.2px;
      pointer-events: none;
    }

    .monthSlot .icon{
      font-size: 18px;
      opacity: 0.92;
      transform: translateY(-4px);
    }

    .monthSlot .miniWrap{
      display:none;
      position:absolute;
      inset: 0;
      place-items:center;
    }

    .monthSlot.revealed .icon{ display:none; }
    .monthSlot.revealed .miniWrap{
      display:grid;
    }

    .miniWheel{
      width: 72%;
      height: 72%;
      border-radius: 50%;
      border: 1px solid rgba(255,255,255,0.20);
      box-shadow:
        inset 0 0 0 1px rgba(0,0,0,0.18),
        0 8px 18px rgba(0,0,0,0.25);
      background: rgba(255,255,255,0.08);
      transform: rotate(0deg);
      transform-origin: 50% 50%;
      position: relative;
      overflow: hidden;
    }

    .miniWheel::after{
      content:"";
      position:absolute;
      inset: 16%;
      border-radius: 50%;
      background: rgba(0,0,0,0.25);
      border: 1px solid rgba(255,255,255,0.10);
      box-shadow: inset 0 0 0 1px rgba(0,0,0,0.18);
      pointer-events:none;
    }

    .miniPointer{
      position:absolute;
      top: 6%;
      left: 50%;
      transform: translateX(-50%);
      width: 0; height: 0;
      border-left: 6px solid transparent;
      border-right: 6px solid transparent;
      border-bottom: 10px solid rgba(255,255,255,0.95);
      filter: drop-shadow(0 5px 6px rgba(0,0,0,0.45));
      z-index: 2;
      pointer-events: none;
      opacity: 0.95;
    }

    .monthSlot.played{
      border-color: rgba(155,231,255,0.35);
      background: rgba(155,231,255,0.08);
    }

    .monthSlot.revealed.good{
      background: rgba(62, 255, 171, 0.10);
      border-color: rgba(62, 255, 171, 0.55);
    }
    .monthSlot.revealed.bad{
      background: rgba(255, 87, 87, 0.10);
      border-color: rgba(255, 87, 87, 0.55);
    }

    main{
      margin-top: 14px;
      display:grid;
      grid-template-columns: 1.4fr 0.9fr;
      gap: 14px;
      align-items: start;
    }

    .panel{
      background: rgba(0,0,0,0.20);
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 18px;
      padding: 14px;
      box-shadow: 0 18px 46px rgba(0,0,0,0.20);
    }

    .wheelPanel{
      display:grid;
      grid-template-columns: 1fr;
      gap: 12px;
      align-items: center;
      justify-items: center;
    }

    .sideStack{
      display:grid;
      grid-template-columns: 1fr;
      gap: 14px;
      align-items: start;
    }

    .wheelShell{
      width: var(--wheelSize);
      height: var(--wheelSize);
      position: relative;
      display:grid;
      place-items: center;
      user-select: none;
    }

    .pointer{
      position:absolute;
      top: -8px;
      left: 50%;
      transform: translateX(-50%);
      width: 0; height: 0;
      border-left: 14px solid transparent;
      border-right: 14px solid transparent;
      border-bottom: 24px solid rgba(255,255,255,0.92);
      filter: drop-shadow(0 6px 8px rgba(0,0,0,0.45));
      z-index: 5;
    }

    .wheelFace{
      width: 100%;
      height: 100%;
      border-radius: 50%;
      border: 1px solid rgba(255,255,255,0.20);
      box-shadow:
        inset 0 0 0 2px rgba(0,0,0,0.15),
        0 20px 60px rgba(0,0,0,0.35);
      background: conic-gradient(#444 0 100%);
      position: relative;
      overflow: hidden;

      transform: rotate(0deg);
      transform-origin: 50% 50%;
      transition: transform 4.6s cubic-bezier(0.10, 0.80, 0.10, 1);
    }

    .wheelFace::before{
      content:"";
      position:absolute;
      inset: 0;
      background:
        radial-gradient(circle at 30% 25%, rgba(255,255,255,0.26), rgba(255,255,255,0.06) 25%, rgba(0,0,0,0.30) 72%, rgba(0,0,0,0.70) 100%),
        radial-gradient(circle at 50% 55%, transparent 0 58%, rgba(0,0,0,0.35) 61%, rgba(0,0,0,0.55) 70%, transparent 74%),
        radial-gradient(circle at 50% 50%, rgba(255,255,255,0.05), transparent 55%);
      mix-blend-mode: overlay;
      pointer-events: none;
      opacity: 0.9;
    }

    .wheelHub{
      width: var(--hubSize);
      height: var(--hubSize);
      border-radius: 50%;
      background:
        radial-gradient(circle at 30% 30%, rgba(255,255,255,0.25), rgba(255,255,255,0.06) 35%, rgba(0,0,0,0.35) 75%, rgba(0,0,0,0.75) 100%);
      border: 1px solid rgba(255,255,255,0.18);
      box-shadow:
        inset 0 0 0 1px rgba(0,0,0,0.25),
        0 10px 22px rgba(0,0,0,0.35);
      position:absolute;
      display:grid;
      place-items:center;
      z-index: 6;
      pointer-events: none;
    }
    .wheelHub .hubText{
      text-align: center;
      font-size: 12px;
      line-height: 1.1;
      color: rgba(255,255,255,0.85);
      padding: 0 10px;
      font-weight: 750;
      letter-spacing: 0.25px;
    }
    .wheelHub .hubText small{
      display:block;
      font-weight: 650;
      opacity: 0.8;
      margin-top: 2px;
    }

    /* Markers now live INSIDE the wheelFace so they rotate with the wheel.
       This guarantees alignment even as the wheel continues spinning after reveal. */
    .markers{
      position:absolute;
      inset: 0;
      pointer-events:none;
      opacity: 0;
      transition: opacity 240ms ease;
    }
    .markers.show{ opacity: 1; }

    .marker{
      position:absolute;
      left: 50%;
      top: 50%;
      width: 0;
      height: 0;
      transform: rotate(var(--a)) translateY(calc(-1 * var(--r)));
      filter: drop-shadow(0 4px 6px rgba(0,0,0,0.45));
    }
    .marker span{
      display:inline-grid;
      place-items:center;
      width: 22px;
      height: 22px;
      border-radius: 999px;
      background: rgba(255,255,255,0.96);
      color: rgba(0,0,0,0.88);
      border: 1px solid rgba(0,0,0,0.14);
      font-size: 12px;
      font-weight: 850;
      transform: translate(-50%, -50%);
    }
    .marker span.good{ outline: 2px solid rgba(62, 255, 171, 0.70); }
    .marker span.bad{ outline: 2px solid rgba(255, 87, 87, 0.72); }

    .controls{
      display:flex;
      gap: 10px;
      flex-wrap: wrap;
      justify-content: center;
      align-items: center;
      width: 100%;
    }

    button{
      border: 1px solid rgba(255,255,255,0.18);
      background: rgba(255,255,255,0.10);
      color: var(--text);
      padding: 10px 12px;
      border-radius: 12px;
      font-size: 14px;
      font-weight: 750;
      cursor: pointer;
      box-shadow: 0 14px 32px rgba(0,0,0,0.18);
      transition: transform 120ms ease, background 120ms ease, opacity 120ms ease;
    }
    button:hover{ transform: translateY(-1px); background: rgba(255,255,255,0.14); }
    button:active{ transform: translateY(0px); }
    button:disabled{
      cursor: not-allowed;
      opacity: 0.55;
      transform: none;
    }

    .btnPrimary{
      background: rgba(155,231,255,0.18);
      border-color: rgba(155,231,255,0.40);
    }
    .btnPrimary:hover{ background: rgba(155,231,255,0.24); }

    .btnDanger{
      background: rgba(255, 87, 87, 0.14);
      border-color: rgba(255, 87, 87, 0.35);
    }

    .guessPanel h2, .logPanel h2, .roundsPanel h2{
      margin: 0 0 10px 0;
      font-size: 14px;
      font-weight: 850;
      letter-spacing: 0.2px;
      color: rgba(255,255,255,0.9);
    }

    .fieldRow{
      width: 100%;
      display:flex;
      gap: 10px;
      align-items:center;
      justify-content: space-between;
      flex-wrap: wrap;
      margin-bottom: 10px;
      padding-bottom: 10px;
      border-bottom: 1px solid rgba(255,255,255,0.10);
    }

    .fieldRow label{
      font-size: 12px;
      color: rgba(255,255,255,0.70);
      font-weight: 800;
      letter-spacing: 0.2px;
    }

    select{
      background: rgba(0,0,0,0.25);
      border: 1px solid rgba(255,255,255,0.16);
      color: rgba(255,255,255,0.92);
      border-radius: 12px;
      padding: 8px 10px;
      font-size: 13px;
      font-weight: 750;
      outline: none;
      min-width: 190px;
    }
    select:focus{
      border-color: rgba(155,231,255,0.42);
      box-shadow: 0 0 0 4px rgba(155,231,255,0.12);
    }

    .guessLayout{
      display:grid;
      grid-template-columns: 1fr;
      gap: 10px;
      align-items: center;
      justify-items: center;
    }

    .guessDialShell{
      width: var(--guessSize);
      height: var(--guessSize);
      position: relative;
      display:grid;
      place-items:center;
      user-select: none;
      touch-action: none;
    }
    .guessDial{
      width: 100%;
      height: 100%;
      border-radius: 50%;
      border: 1px solid rgba(255,255,255,0.20);
      box-shadow:
        inset 0 0 0 2px rgba(0,0,0,0.16),
        0 16px 38px rgba(0,0,0,0.26);
      position: relative;
      overflow: hidden;

      --fillDeg: 0deg;

      background:
        radial-gradient(circle at 30% 25%, rgba(255,255,255,0.20), rgba(0,0,0,0.38) 72%, rgba(0,0,0,0.70) 100%),
        repeating-conic-gradient(
          rgba(255,255,255,0.14) 0 2deg,
          rgba(255,255,255,0.00) 2deg 8deg
        ),
        conic-gradient(
          rgba(155,231,255,0.95) 0 var(--fillDeg),
          rgba(255,255,255,0.07) var(--fillDeg) 360deg
        );
    }
    .guessDial::after{
      content:"";
      position:absolute;
      inset: 14px;
      border-radius: 50%;
      background: rgba(0,0,0,0.28);
      border: 1px solid rgba(255,255,255,0.10);
      box-shadow: inset 0 0 0 1px rgba(0,0,0,0.20);
      pointer-events:none;
    }

    .guessReadout{
      display:flex;
      align-items: baseline;
      gap: 6px;
    }
    .guessValue{
      font-size: 30px;
      font-weight: 900;
      letter-spacing: 0.2px;
    }
    .guessUnit{
      color: rgba(255,255,255,0.72);
      font-weight: 750;
    }
    .guessHint{
      font-size: 12px;
      color: rgba(255,255,255,0.60);
      text-align: center;
      line-height: 1.35;
      margin-top: 6px;
    }

    input[type="range"]{
      width: 100%;
      accent-color: rgba(155,231,255,0.95);
    }

    .guessControls{
      width: 100%;
      display:grid;
      grid-template-columns: 1fr;
      gap: 10px;
    }

    .legend{
      display:flex;
      gap: 10px;
      align-items: center;
      justify-content: center;
      flex-wrap: wrap;
      font-size: 12px;
      color: rgba(255,255,255,0.70);
      margin-top: 2px;
    }
    .chip{
      display:flex;
      gap: 8px;
      align-items:center;
      padding: 5px 8px;
      border-radius: 999px;
      border: 1px solid rgba(255,255,255,0.14);
      background: rgba(0,0,0,0.18);
    }
    .swatch{
      width: 10px;
      height: 10px;
      border-radius: 99px;
      display:inline-block;
      background: rgba(255,255,255,0.7);
    }
    .swatch.good{ background: rgba(62, 255, 171, 0.9); }
    .swatch.bad{ background: rgba(255, 87, 87, 0.9); }

    .logPanel{
      margin-top: 14px;
    }

    table{
      width: 100%;
      border-collapse: collapse;
      font-size: 13px;
      overflow: hidden;
      border-radius: 14px;
      border: 1px solid rgba(255,255,255,0.14);
    }
    thead th{
      text-align:left;
      padding: 10px 10px;
      color: rgba(255,255,255,0.78);
      background: rgba(0,0,0,0.30);
      border-bottom: 1px solid rgba(255,255,255,0.12);
      font-weight: 800;
    }
    tbody td{
      padding: 10px 10px;
      border-bottom: 1px solid rgba(255,255,255,0.10);
      color: rgba(255,255,255,0.86);
      vertical-align: top;
    }
    tbody tr:last-child td{ border-bottom: none; }
    tbody tr:hover td{ background: rgba(255,255,255,0.04); }

    .roundsPanel table{
      font-size: 12px;
    }
    .roundsPanel thead th{
      padding: 8px 10px;
    }
    .roundsPanel tbody td{
      padding: 8px 10px;
    }
    .roundRowFilled td{
      background: rgba(155,231,255,0.04);
    }
    .roundRowActive td{
      outline: 1px solid rgba(155,231,255,0.22);
      outline-offset: -1px;
    }

    .tag{
      display:inline-flex;
      align-items:center;
      gap: 6px;
      padding: 4px 8px;
      border-radius: 999px;
      font-size: 12px;
      font-weight: 800;
      border: 1px solid rgba(255,255,255,0.14);
      background: rgba(0,0,0,0.20);
    }
    .tag.good{ border-color: rgba(62, 255, 171, 0.55); background: rgba(62, 255, 171, 0.10); }
    .tag.bad{ border-color: rgba(255, 87, 87, 0.55); background: rgba(255, 87, 87, 0.10); }

    footer{
      margin-top: 14px;
      display:flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
    }
    .footNote{
      font-size: 12px;
      color: rgba(255,255,255,0.55);
      line-height: 1.35;
      max-width: 96ch;
    }

    @media (max-width: 980px){
      main{
        grid-template-columns: 1fr;
      }
      .sideStack{
        grid-template-columns: 1fr;
      }
    }

    @media (max-width: 520px){
      :root{ --wheelSize: 300px; --guessSize: 170px; --hubSize: 96px; }
      .monthRow{ grid-template-columns: repeat(8, 1fr); }
      h1{ font-size: 18px; }
      .miniPointer{ border-left-width: 5px; border-right-width: 5px; border-bottom-width: 9px; }
      select{ min-width: 0; width: 100%; }
      .fieldRow{ gap: 8px; }
    }
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="titlebar">
        <div>
          <h1>Conception Wheel</h1>
          <div class="subtitle">
            A “game” is <b>10 rounds</b>. Each round has a hidden monthly fertility (0–25%). Spin month-by-month outcomes, then lock a guess:
            <b>within ±5 percentage points</b> keeps your remaining points. Total score is the sum over the 10 rounds.
          </div>
        </div>
        <div class="stats">
          <div class="pill"><span class="muted">Round</span> <b id="roundNum">1</b><span class="muted">/10</span></div>
          <div class="pill"><span class="muted">Month</span> <b><span id="monthNum">0</span></b><span class="muted">/24</span></div>
          <div class="pill"><span class="muted" id="pointsLabel">Points left</span> <b id="pointsNum">24</b></div>
          <div class="pill"><span class="muted">Total score</span> <b id="scoreNum">0</b></div>
          <div class="pill" id="fertilityPill" style="display:none;"><span class="muted">True fertility</span> <b id="trueFertilityNum">—</b></div>
        </div>
      </div>
      <div id="message" class="message neutral">
        ✅ Ready (Round 1/10). Spin Month 1, or lock in a guess immediately (high risk, high reward).
        <small>(If nothing responds to clicks, your viewer is probably blocking JavaScript. Open the file in a real browser.)</small>
      </div>
    </header>

    <section id="monthRow" class="monthRow" aria-label="Month outcomes row"></section>

    <main>
      <section class="panel wheelPanel" aria-label="Main wheel">
        <div class="wheelShell" aria-hidden="false">
          <div class="pointer" aria-hidden="true"></div>
          <div id="wheelFace" class="wheelFace" aria-label="Main wheel (hidden outcomes)">
            <div id="markers" class="markers" aria-hidden="true"></div>
          </div>
          <div class="wheelHub" aria-hidden="true">
            <div class="hubText">
              SPIN
              <small id="hubSub">Month 1</small>
            </div>
          </div>
        </div>

        <div class="controls">
          <button id="spinBtn" class="btnPrimary" type="button">Spin Month 1</button>
          <button id="revealBtn" type="button" disabled title="Lock your guess to reveal.">Reveal (locks guess)</button>
        </div>

        <div class="legend" aria-hidden="true">
          <span class="chip"><span class="swatch good"></span> pregnant arc</span>
          <span class="chip"><span class="swatch bad"></span> not pregnant arc</span>
          <span class="chip"><span class="swatch" style="background: rgba(255,255,255,0.92);"></span> month landing</span>
        </div>
      </section>

      <aside class="sideStack" aria-label="Side panels">
        <section class="panel guessPanel" aria-label="Guess panel">
          <div class="fieldRow" aria-label="Age preset">
            <label for="ageSelect">Age preset</label>
            <select id="ageSelect" aria-label="Age preset selector">
              <option value="20_29">20–29 (easiest)</option>
              <option value="30_34" selected>30–34</option>
              <option value="35_37">35–37</option>
              <option value="38_40">38–40</option>
              <option value="41_42">41–42</option>
              <option value="43_44">43–44</option>
              <option value="45_plus">45+ (hardest)</option>
              <option value="original">Original (v3/v4)</option>
            </select>
          </div>

          <h2>Your fertility guess</h2>

          <div class="guessLayout">
            <div id="guessDialShell" class="guessDialShell" aria-label="Guess pie (drag to set guess)">
              <div id="guessDial" class="guessDial" aria-hidden="true"></div>
            </div>

            <div class="guessReadout" aria-label="Guess readout">
              <div class="guessValue"><span id="guessValue">10</span></div>
              <div class="guessUnit">%</div>
            </div>

            <div class="guessControls" aria-label="Guess controls">
              <input id="guessSlider" type="range" min="0" max="25" step="1" value="10" aria-label="Fertility guess slider (0–25%)" />
              <button id="lockBtn" class="btnPrimary" type="button">Lock in guess</button>
            </div>

            <div class="guessHint" id="ageHint">
              Age preset changes the <b>distribution</b> used when starting a round. If you change it mid-round, it applies to the <b>next</b> round.
            </div>
          </div>
        </section>

        <section class="panel roundsPanel" aria-label="Rounds summary">
          <h2>Rounds summary (10)</h2>
          <table aria-label="Rounds table">
            <thead>
              <tr>
                <th style="width:72px;">Round</th>
                <th style="width:80px;">Months</th>
                <th style="width:90px;">Fertility</th>
                <th style="width:80px;">Points</th>
              </tr>
            </thead>
            <tbody id="roundsBody"></tbody>
          </table>
          <div class="footNote" style="margin-top:10px;">
            A round is recorded when the simulation reaches its final outcome (<b>pregnancy</b> or <b>24 months</b>).
          </div>
        </section>
      </aside>
    </main>

    <section class="panel logPanel" aria-label="Month-by-month log">
      <h2>Month-by-month record</h2>
      <table aria-label="Results table">
        <thead>
          <tr>
            <th style="width:84px;">Month</th>
            <th>Outcome</th>
            <th style="width:140px;">Points</th>
          </tr>
        </thead>
        <tbody id="logBody">
          <tr>
            <td colspan="3" style="color: rgba(255,255,255,0.65);">No spins yet.</td>
          </tr>
        </tbody>
      </table>
    </section>

    <footer>
      <div class="footNote">
        <b>Note:</b> This is a simplified simulation, not medical advice. Real-world time‑to‑pregnancy varies widely with age, timing, and many other factors.
        After 10 rounds, click <b>Start new game</b> to reset the 10-round table + total score.
      </div>
      <div style="display:flex; gap:10px; flex-wrap:wrap;">
        <button id="nextRoundBtn" class="btnPrimary" type="button" disabled title="Finish the round first.">Next round</button>
        <button id="newGameBtn" class="btnDanger" type="button">Start new game</button>
      </div>
    </footer>
  </div>

  <script>
    "use strict";

    const MAX_MONTHS = 24;
    const MAX_GAME_ROUNDS = 10;

    const WEDGES = 100;        // 100 wedges => 1 wedge = 1 percentage point.
    const MIN_FERT = 0;        // 0%
    const MAX_FERT = 25;       // 25% (monthly max)
    const GUESS_MAX = 25;      // guess range 0–25%
    const TOLERANCE_PTS = 5;   // ±5 percentage points

    const $ = (sel) => document.querySelector(sel);

    const els = {
      roundNum: $("#roundNum"),
      monthNum: $("#monthNum"),
      pointsNum: $("#pointsNum"),
      pointsLabel: $("#pointsLabel"),
      scoreNum: $("#scoreNum"),
      fertilityPill: $("#fertilityPill"),
      trueFertilityNum: $("#trueFertilityNum"),
      message: $("#message"),

      monthRow: $("#monthRow"),

      wheelFace: $("#wheelFace"),
      markers: $("#markers"),
      hubSub: $("#hubSub"),

      spinBtn: $("#spinBtn"),
      revealBtn: $("#revealBtn"),

      ageSelect: $("#ageSelect"),
      ageHint: $("#ageHint"),

      guessDialShell: $("#guessDialShell"),
      guessDial: $("#guessDial"),
      guessSlider: $("#guessSlider"),
      guessValue: $("#guessValue"),
      lockBtn: $("#lockBtn"),

      logBody: $("#logBody"),

      roundsBody: $("#roundsBody"),
      nextRoundBtn: $("#nextRoundBtn"),
      newGameBtn: $("#newGameBtn"),
    };

    function setMessage(html, kind="neutral"){
      els.message.innerHTML = html;
      els.message.classList.remove("neutral","good","bad");
      els.message.classList.add(kind);
    }

    /** ----- RNG utilities (crypto-backed; Math.random fallback) ----- **/
    function getRandomU32(){
      if (globalThis.crypto && typeof globalThis.crypto.getRandomValues === "function"){
        const u32 = new Uint32Array(1);
        globalThis.crypto.getRandomValues(u32);
        return u32[0];
      }
      return (Math.floor(Math.random() * 0x100000000) >>> 0);
    }

    function randFloat(){
      return getRandomU32() / 0x100000000;
    }

    function randInt(maxExclusive){
      if (!Number.isInteger(maxExclusive) || maxExclusive <= 0 || maxExclusive > 2**32){
        throw new Error("randInt: bad maxExclusive");
      }
      const lim = Math.floor(0x100000000 / maxExclusive) * maxExclusive;
      while(true){
        const x = getRandomU32();
        if (x < lim) return x % maxExclusive;
      }
    }

    function randNormal(){
      let u = 0, v = 0;
      while (u === 0) u = randFloat();
      while (v === 0) v = randFloat();
      return Math.sqrt(-2.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
    }

    function randGamma(k){
      if (!(k > 0)) throw new Error("randGamma: shape must be > 0");
      if (k < 1){
        const u = randFloat();
        return randGamma(k + 1) * Math.pow(u, 1 / k);
      }
      const d = k - 1/3;
      const c = 1 / Math.sqrt(9 * d);
      while(true){
        const x = randNormal();
        let v = 1 + c * x;
        if (v <= 0) continue;
        v = v * v * v;
        const u = randFloat();
        if (u < 1 - 0.0331 * (x * x) * (x * x)) return d * v;
        if (Math.log(u) < 0.5 * x * x + d * (1 - v + Math.log(v))) return d * v;
      }
    }

    function randBeta(a, b){
      const x = randGamma(a);
      const y = randGamma(b);
      return x / (x + y);
    }

    /** ----- Age presets (Beta on [0,1], scaled to 0..25 and rounded) ----- **/
    const AGE_PRESETS = {
      "20_29":  { label: "20–29",   a: 8.0,  b: 2.0,  note: "Higher average fecundability" },
      "30_34":  { label: "30–34",   a: 6.5,  b: 3.5,  note: "High, but a bit lower" },
      "35_37":  { label: "35–37",   a: 5.0,  b: 5.0,  note: "Mid" },
      "38_40":  { label: "38–40",   a: 3.0,  b: 7.0,  note: "Lower" },
      "41_42":  { label: "41–42",   a: 2.0,  b: 8.0,  note: "Low" },
      "43_44":  { label: "43–44",   a: 1.2,  b: 8.8,  note: "Very low" },
      "45_plus":{ label: "45+",     a: 0.7,  b: 9.3,  note: "Extremely low" },
      "original":{label:"Original", a: 4.0,  b: 3.6,  note: "v3/v4 distribution" },
    };

    function currentAgePreset(){
      const key = els.ageSelect.value;
      return AGE_PRESETS[key] || AGE_PRESETS["30_34"];
    }

    function formatPresetHint(){
      const p = currentAgePreset();
      const meanPct = (MAX_FERT * (p.a / (p.a + p.b)));
      return `Age preset changes the <b>distribution</b> used when starting a round. ` +
             `This preset’s average fertility is about <b>${meanPct.toFixed(1)}%</b> (out of a 25% max).`;
    }

    function sampleTrueFertilityPct(){
      const p = currentAgePreset();
      const x = randBeta(p.a, p.b);
      const pct = Math.round(x * MAX_FERT);
      return Math.max(MIN_FERT, Math.min(MAX_FERT, pct));
    }

    /** ----- State ----- **/
    const state = {
      round: 1,
      totalScore: 0,

      trueFertilityPct: 10,
      wedgeOutcomes: new Array(WEDGES).fill(false),

      month: 0,
      pointsLeft: MAX_MONTHS,
      rotationDeg: 0,

      spins: [], // {month, wedgeIndex, pregnant, rotationDeg}

      guessPct: 10,
      guessLocked: false,
      revealed: false,

      conceived: false,
      conceivedMonth: null,

      spinning: false,
      autoSpinning: false,

      lockedPoints: null,   // points at the moment of locking the guess
      roundScore: null,     // points earned this round (lockedPoints or 0)

      roundSummaries: new Array(MAX_GAME_ROUNDS).fill(null),
      gameOver: false,

      sessionId: 0,         // increments when starting a new round/game to cancel async loops
    };

    function bumpSession(){
      state.sessionId += 1;
      return state.sessionId;
    }

    /** ----- Rounds table ----- **/
    function buildRoundsTable(){
      els.roundsBody.innerHTML = "";
      for (let r = 1; r <= MAX_GAME_ROUNDS; r++){
        const tr = document.createElement("tr");
        tr.dataset.round = String(r);
        tr.innerHTML = `
          <td><b>${r}</b></td>
          <td class="monthsCell">—</td>
          <td class="fertCell">—</td>
          <td class="pointsCell">—</td>
        `;
        els.roundsBody.appendChild(tr);
      }
    }

    function updateRoundsTable(){
      const rows = els.roundsBody.querySelectorAll("tr");
      rows.forEach((tr) => {
        tr.classList.remove("roundRowFilled", "roundRowActive");
        const r = Number(tr.dataset.round);
        const s = state.roundSummaries[r - 1];

        if (s){
          tr.classList.add("roundRowFilled");
          tr.querySelector(".monthsCell").textContent = s.conceived ? `${s.months} 👶` : String(s.months);
          tr.querySelector(".fertCell").textContent = `${s.fertilityPct}%`;
          tr.querySelector(".pointsCell").textContent = String(s.points);
        } else {
          tr.querySelector(".monthsCell").textContent = "—";
          tr.querySelector(".fertCell").textContent = "—";
          tr.querySelector(".pointsCell").textContent = "—";
        }

        if (r === state.round && !state.gameOver){
          tr.classList.add("roundRowActive");
        }
      });
    }

    function recordCurrentRoundSummary({monthsFinal, roundScore}){
      const idx = state.round - 1;
      state.roundSummaries[idx] = {
        months: monthsFinal,
        fertilityPct: state.trueFertilityPct,
        points: roundScore,
        conceived: !!state.conceived,
      };
      updateRoundsTable();
    }

    /** ----- Month row ----- **/
    function buildMonthRow(){
      els.monthRow.innerHTML = "";
      for (let m = 1; m <= MAX_MONTHS; m++){
        const slot = document.createElement("div");
        slot.className = "monthSlot";
        slot.dataset.month = String(m);
        slot.dataset.label = String(m);
        slot.title = `Month ${m}`;
        slot.innerHTML = `
          <div class="icon">○</div>
          <div class="miniWrap" aria-hidden="true">
            <div class="miniPointer"></div>
            <div class="miniWheel"></div>
          </div>
        `;
        els.monthRow.appendChild(slot);
      }
    }

    function resetMonthRow(){
      const slots = els.monthRow.querySelectorAll(".monthSlot");
      slots.forEach((slot) => {
        slot.classList.remove("played", "revealed", "good", "bad");
        slot.querySelector(".icon").textContent = "○";
        const mini = slot.querySelector(".miniWheel");
        if (mini){
          mini.style.background = "rgba(255,255,255,0.08)";
          mini.style.transform = "rotate(0deg)";
        }
      });
    }

    function updateMonthRowAfterSpin(month){
      const slot = els.monthRow.querySelector(`.monthSlot[data-month="${month}"]`);
      if (!slot) return;
      slot.classList.add("played");
      slot.querySelector(".icon").textContent = "●";
    }

    function wedgeDistanceToFertile(wedgeIndex){
      const p = state.trueFertilityPct;
      if (p <= 0) return WEDGES;
      if (wedgeIndex < p) return 0;
      const distBackToEdge = wedgeIndex - (p - 1);
      const distForwardToStart = (WEDGES - wedgeIndex);
      return Math.min(distBackToEdge, distForwardToStart);
    }

    function revealMonthRow(){
      const bg = buildWheelBackground(state.wedgeOutcomes, true);

      const slots = els.monthRow.querySelectorAll(".monthSlot");
      slots.forEach((slot) => {
        const m = Number(slot.dataset.month);
        const spin = state.spins.find(s => s.month === m);
        if (!spin){
          slot.classList.remove("played");
          slot.querySelector(".icon").textContent = "○";
          return;
        }

        slot.classList.add("revealed");
        if (spin.pregnant){
          slot.classList.add("good");
        } else {
          slot.classList.add("bad");
        }

        const mini = slot.querySelector(".miniWheel");
        if (mini){
          mini.style.background = bg;
          mini.style.transform = `rotate(${normalizeDeg(spin.rotationDeg)}deg)`;
        }

        const d = wedgeDistanceToFertile(spin.wedgeIndex);
        const detail = spin.pregnant
          ? `Pregnant (hit green)`
          : (state.trueFertilityPct === 0 ? `Not pregnant (0% this round)` : `Not pregnant (nearest green: ${d} wedge${d===1?"":"s"} away)`);
        slot.title = `Month ${m} — ${detail}`;
      });
    }

    function setFertilityRevealVisible(visible){
      els.fertilityPill.style.display = visible ? "flex" : "none";
    }

    /** ----- Log table ----- **/
    function resetLogTable(){
      els.logBody.innerHTML = `
        <tr>
          <td colspan="3" style="color: rgba(255,255,255,0.65);">No spins yet.</td>
        </tr>
      `;
    }

    function appendLogRow({month, pregnant, pointsText}){
      if (els.logBody.querySelector("tr td[colspan]")) els.logBody.innerHTML = "";
      const tr = document.createElement("tr");

      const tag = pregnant
        ? `<span class="tag good">👶 Pregnant</span>`
        : `<span class="tag bad">— Not pregnant</span>`;

      tr.innerHTML = `
        <td>${month}</td>
        <td>${tag}</td>
        <td>${pointsText}</td>
      `;
      els.logBody.appendChild(tr);
    }

    /** ----- Wheel rendering ----- **/
    function buildWheelBackground(outcomes, revealed){
      const seg = 360 / outcomes.length;
      const parts = [];
      for (let i = 0; i < outcomes.length; i++){
        let c;
        if (!revealed){
          c = (i % 2 === 0) ? "hsl(0 0% 33%)" : "hsl(0 0% 28%)";
        } else {
          c = outcomes[i] ? "hsl(145 70% 45%)" : "hsl(0 75% 55%)";
        }
        const a0 = (i * seg).toFixed(4);
        const a1 = ((i + 1) * seg).toFixed(4);
        parts.push(`${c} ${a0}deg ${a1}deg`);
      }

      const radial = revealed
        ? "radial-gradient(circle at 30% 25%, rgba(255,255,255,0.20), rgba(0,0,0,0.38) 72%, rgba(0,0,0,0.70) 100%)"
        : "radial-gradient(circle at 30% 25%, rgba(255,255,255,0.14), rgba(0,0,0,0.45) 72%, rgba(0,0,0,0.75) 100%)";

      return `${radial}, conic-gradient(${parts.join(",")})`;
    }

    function renderWheelHidden(){
      els.wheelFace.style.background = buildWheelBackground(state.wedgeOutcomes, false);
      els.markers.classList.remove("show");
      els.markers.innerHTML = "";
    }

    function renderWheelRevealed(){
      els.wheelFace.style.background = buildWheelBackground(state.wedgeOutcomes, true);

      els.markers.innerHTML = "";

      const byWedge = new Map();
      for (const s of state.spins){
        const key = s.wedgeIndex;
        if (!byWedge.has(key)) byWedge.set(key, []);
        byWedge.get(key).push(s);
      }

      const seg = 360 / WEDGES;
      const baseR = Math.min(
        148,
        (parseInt(getComputedStyle(document.documentElement).getPropertyValue("--wheelSize")) || 360) / 2 - 32
      );

      for (const [wedgeIndex, spins] of byWedge.entries()){
        spins.sort((a,b) => a.month - b.month);
        const angle = (wedgeIndex * seg + seg / 2);

        for (let k = 0; k < spins.length; k++){
          const s = spins[k];
          const marker = document.createElement("div");
          marker.className = "marker";
          marker.style.setProperty("--a", `${angle}deg`);
          marker.style.setProperty("--r", `${baseR - k * 24}px`);
          const cls = s.pregnant ? "good" : "bad";
          marker.innerHTML = `<span class="${cls}" title="Month ${s.month} landed here">${s.month}</span>`;
          els.markers.appendChild(marker);
        }
      }

      els.markers.classList.add("show");
    }

    /** ----- Guess UI ----- **/
    function setGuessPct(pct){
      const clamped = Math.max(0, Math.min(GUESS_MAX, Math.round(pct)));
      state.guessPct = clamped;

      els.guessSlider.value = String(clamped);
      els.guessValue.textContent = String(clamped);

      const fillDeg = (clamped / 100) * 360;
      els.guessDial.style.setProperty("--fillDeg", `${fillDeg.toFixed(2)}deg`);

      els.guessDialShell.setAttribute("role", "slider");
      els.guessDialShell.setAttribute("aria-valuemin", "0");
      els.guessDialShell.setAttribute("aria-valuemax", String(GUESS_MAX));
      els.guessDialShell.setAttribute("aria-valuenow", String(clamped));
      els.guessDialShell.setAttribute("tabindex", state.guessLocked ? "-1" : "0");
    }

    /** ----- Round finalization ----- **/
    function monthsFinalForSummary(){
      // After our auto-spin fix, by the time we finalize a round we are either:
      //  - conceived, with conceivedMonth set, OR
      //  - hit 24 months without pregnancy
      if (state.conceived && Number.isFinite(state.conceivedMonth)) return state.conceivedMonth;
      return Math.min(MAX_MONTHS, Math.max(0, state.month));
    }

    function finalizeRoundUI(){
      // Called when the round’s "months until outcome" is determined.
      const monthsFinal = monthsFinalForSummary();
      const roundScore = (state.roundScore ?? 0);

      recordCurrentRoundSummary({ monthsFinal, roundScore });

      // Enable next round / game over
      if (state.round < MAX_GAME_ROUNDS){
        els.nextRoundBtn.disabled = false;
        els.nextRoundBtn.title = "Start the next round.";
      } else {
        state.gameOver = true;
        els.nextRoundBtn.disabled = true;
        els.nextRoundBtn.title = "Game complete. Start a new game.";
      }

      const outcomeText = state.conceived
        ? `Pregnancy achieved by <b>Month ${state.conceivedMonth}</b>.`
        : `No pregnancy by <b>Month ${monthsFinal}</b>.`;

      const scoreText = (Math.abs(state.guessPct - state.trueFertilityPct) <= TOLERANCE_PTS)
        ? `You kept <b>${roundScore}</b> points.`
        : `Round score <b>0</b>.`;

      const tail = state.gameOver
        ? `<small>✅ Game complete (10/10). Click <b>Start new game</b> to play again.</small>`
        : `<small>Click <b>Next round</b> to continue (Round ${state.round + 1}/10).</small>`;

      setMessage(
        `${outcomeText} ${scoreText} Total score <b>${state.totalScore}</b>.` + tail,
        (roundScore > 0 ? "good" : "bad")
      );
    }

    /** ----- Auto-spin after locking ----- **/
    function sleep(ms){
      return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function autoSpinToOutcome(sessionAtStart){
      // Auto-spin until pregnancy OR until MAX_MONTHS, while the wheel remains revealed.
      state.autoSpinning = true;

      // Keep next-round disabled until we're done.
      els.nextRoundBtn.disabled = true;
      els.nextRoundBtn.title = "Auto-spinning to outcome…";

      // If fertility is 0%, we will never conceive. Still spin to month 24 so months-to-outcome is correct.
      while (state.sessionId === sessionAtStart && !state.conceived && state.month < MAX_MONTHS){
        await spinOnce({ auto: true, sessionAtStart });
        // small pause so the user can see the wheel settle
        await sleep(140);
      }

      if (state.sessionId !== sessionAtStart) return; // cancelled by new game/round

      state.autoSpinning = false;

      finalizeRoundUI();
    }

    /** ----- Lock & reveal ----- **/
    function lockGuessAndReveal(){
      if (state.guessLocked) return;
      if (state.gameOver) return;
      if (state.spinning) return;

      state.guessLocked = true;
      state.revealed = true;

      // Freeze points at the moment of locking (score uses this).
      state.lockedPoints = state.pointsLeft;
      els.pointsLabel.textContent = "Points locked";

      els.lockBtn.disabled = true;
      els.revealBtn.disabled = true;
      els.spinBtn.disabled = true;
      els.guessSlider.disabled = true;
      els.guessDialShell.style.opacity = "0.70";

      const diff = Math.abs(state.guessPct - state.trueFertilityPct);
      const correct = diff <= TOLERANCE_PTS;
      state.roundScore = correct ? state.lockedPoints : 0;

      state.totalScore += state.roundScore;
      els.scoreNum.textContent = String(state.totalScore);

      setFertilityRevealVisible(true);
      els.trueFertilityNum.textContent = `${state.trueFertilityPct}%`;

      // Reveal immediately, then possibly continue spinning automatically.
      renderWheelRevealed();
      revealMonthRow();

      // If already conceived (or already at 24), we can finalize immediately.
      // Otherwise, auto-spin until we reach the outcome.
      const scoreLine = correct
        ? `Locked <b>${state.guessPct}%</b> (true: <b>${state.trueFertilityPct}%</b>, diff: ${diff}). You keep <b>${state.roundScore}</b> points.`
        : `Locked <b>${state.guessPct}%</b> (true: <b>${state.trueFertilityPct}%</b>, diff: ${diff}). Outside ±${TOLERANCE_PTS} ⇒ <b>score 0</b>.`;

      if (state.conceived || state.month >= MAX_MONTHS){
        setMessage(scoreLine, correct ? "good" : "bad");
        finalizeRoundUI();
      } else {
        // Start auto-spinning to pregnancy (or 24 months).
        setMessage(
          scoreLine + `<small>Auto-spinning to find the month of pregnancy (or Month 24 if it never happens)…</small>`,
          correct ? "good" : "bad"
        );
        const sessionAtStart = state.sessionId;
        autoSpinToOutcome(sessionAtStart);
      }
    }

    /** ----- Round setup / reset ----- **/
    function normalizeDeg(deg){
      return ((deg % 360) + 360) % 360;
    }

    function setWheelRotationInstant(deg){
      els.wheelFace.style.transitionDuration = "0s";
      els.wheelFace.style.transform = `rotate(${deg}deg)`;
      void els.wheelFace.offsetWidth;
      els.wheelFace.style.transitionDuration = "";
    }

    function startRound(){
      bumpSession();

      state.month = 0;
      state.pointsLeft = MAX_MONTHS;
      state.rotationDeg = 0;
      state.spins = [];
      state.guessLocked = false;
      state.revealed = false;
      state.conceived = false;
      state.conceivedMonth = null;
      state.spinning = false;
      state.autoSpinning = false;
      state.lockedPoints = null;
      state.roundScore = null;

      // Points label
      els.pointsLabel.textContent = "Points left";

      state.trueFertilityPct = sampleTrueFertilityPct();

      const outcomes = new Array(WEDGES).fill(false);
      for (let i = 0; i < state.trueFertilityPct; i++) outcomes[i] = true;
      state.wedgeOutcomes = outcomes;

      // Randomize where arc sits on the wheel via an initial rotation.
      const seg = 360 / WEDGES;
      const offsetIndex = randInt(WEDGES);
      state.rotationDeg = offsetIndex * seg;
      setWheelRotationInstant(state.rotationDeg);

      els.monthNum.textContent = "0";
      els.pointsNum.textContent = String(MAX_MONTHS);
      els.hubSub.textContent = "Month 1";
      els.spinBtn.textContent = "Spin Month 1";
      els.spinBtn.disabled = false;

      els.revealBtn.disabled = false;
      els.lockBtn.disabled = false;
      els.guessSlider.disabled = false;
      els.guessDialShell.style.opacity = "1";

      els.nextRoundBtn.disabled = true;
      els.nextRoundBtn.title = "Finish the round first.";

      resetMonthRow();
      resetLogTable();
      renderWheelHidden();

      setFertilityRevealVisible(false);
      setGuessPct(10);

      setMessage(`✅ Ready (Round ${state.round}/10). Spin Month 1, or lock in a guess immediately.`, "neutral");
      updateTopStats();
      updateRoundsTable();

      els.ageHint.innerHTML = formatPresetHint();
    }

    function startNewGame(){
      bumpSession();

      state.round = 1;
      state.totalScore = 0;
      state.roundSummaries = new Array(MAX_GAME_ROUNDS).fill(null);
      state.gameOver = false;

      els.scoreNum.textContent = "0";
      buildRoundsTable();
      updateRoundsTable();

      startRound();
    }

    function updateTopStats(){
      els.roundNum.textContent = String(state.round);
      els.monthNum.textContent = String(state.month);
      els.pointsNum.textContent = String(state.pointsLeft);
      els.scoreNum.textContent = String(state.totalScore);
    }

    /** ----- Spin logic ----- **/
    function computeRotationToLandOn(wedgeIndex){
      // Conic gradients in CSS are measured from the top (12 o'clock) and increase clockwise.
      // Our marker geometry matches that. The wheel transform is also clockwise for +deg.
      // The center of wedge i is at: i*seg + seg/2.
      // To land that center at the pointer (top, 0deg), wheel rotation modulo 360 must be:
      //   rotation ≡ -center (mod 360) == 360 - center.
      const seg = 360 / WEDGES;
      const center = (wedgeIndex * seg + seg / 2);
      const desiredMod = (360 - center) % 360;
      const currentMod = normalizeDeg(state.rotationDeg);
      const delta = (desiredMod - currentMod + 360) % 360;
      return delta;
    }

    function canSpinNow({auto}){
      if (state.spinning) return false;
      if (state.gameOver) return false;
      if (state.month >= MAX_MONTHS) return false;
      if (state.conceived) return false;
      if (!auto && state.guessLocked) return false;
      return true;
    }

    function spinOnce({auto=false, sessionAtStart=null} = {}){
      return new Promise((resolve) => {
        const mySession = (sessionAtStart ?? state.sessionId);

        if (!canSpinNow({auto})) { resolve(null); return; }
        state.spinning = true;

        // For auto-spins, we keep controls disabled.
        if (!auto){
          els.spinBtn.disabled = true;
          els.lockBtn.disabled = true;
          els.revealBtn.disabled = true;
        }

        const nextMonth = state.month + 1;
        const wedgeIndex = randInt(WEDGES);

        const extraSpins = auto ? (2 + randInt(3)) : (4 + randInt(4)); // auto: 2..4, manual: 4..7
        const delta = computeRotationToLandOn(wedgeIndex);
        const finalRotation = state.rotationDeg + extraSpins * 360 + delta;

        const dur = auto
          ? (0.85 + randFloat() * 0.55)  // 0.85..1.40s
          : (4.2 + randFloat() * 1.2);   // 4.2..5.4s

        els.wheelFace.style.transitionDuration = `${dur.toFixed(2)}s`;
        els.wheelFace.style.transform = `rotate(${finalRotation}deg)`;

        const onDone = (ev) => {
          if (ev.propertyName !== "transform") return;
          els.wheelFace.removeEventListener("transitionend", onDone);

          // If the session changed, ignore this spin's result.
          if (state.sessionId !== mySession){
            state.spinning = false;
            resolve(null);
            return;
          }

          state.rotationDeg = finalRotation;
          state.month = nextMonth;

          // Points only decrease BEFORE you lock. After locking, points are frozen ("Points locked").
          if (!state.guessLocked){
            state.pointsLeft = Math.max(0, MAX_MONTHS - state.month);
          }

          const pregnant = !!state.wedgeOutcomes[wedgeIndex];
          state.spins.push({ month: nextMonth, wedgeIndex, pregnant, rotationDeg: finalRotation });

          // UI updates
          if (state.revealed){
            renderWheelRevealed();  // refresh marker layer to include the new month
            revealMonthRow();
          } else {
            updateMonthRowAfterSpin(nextMonth);
          }

          // Log: show points remaining until lock; after lock, show "—" (score already fixed).
          const pointsText = state.guessLocked ? "—" : String(state.pointsLeft);
          appendLogRow({ month: nextMonth, pregnant, pointsText });

          updateTopStats();

          if (pregnant){
            state.conceived = true;
            state.conceivedMonth = nextMonth;
          }

          if (!state.revealed){
            // Only update instructional messages during manual play (pre-lock).
            if (pregnant){
              setMessage(`Month <b>${nextMonth}</b>: <b>Pregnancy achieved</b>. You can lock your fertility guess now to score points.`, "good");
            } else if (state.month >= MAX_MONTHS){
              setMessage("You’ve reached <b>24 months</b>. You can still lock a guess, but no points remain.", "neutral");
            } else {
              setMessage(`Month <b>${nextMonth}</b>: Not pregnant. Spin again for Month <b>${nextMonth + 1}</b>, or lock your guess.`, "neutral");
            }
          }

          if (!state.revealed){
            if (!state.conceived && state.month < MAX_MONTHS){
              els.spinBtn.textContent = `Spin Month ${state.month + 1}`;
              els.spinBtn.disabled = false;
            } else {
              els.spinBtn.disabled = true;
            }

            els.hubSub.textContent = state.conceived
              ? `Conceived (M${state.conceivedMonth})`
              : (state.month < MAX_MONTHS ? `Month ${state.month + 1}` : "Done");

            els.lockBtn.disabled = false;
            els.revealBtn.disabled = false;
          } else {
            // During reveal/auto mode, keep hubSub updated.
            els.hubSub.textContent = state.conceived
              ? `Conceived (M${state.conceivedMonth})`
              : (state.month < MAX_MONTHS ? `Auto Month ${state.month + 1}` : "Done");
          }

          state.spinning = false;
          resolve({ month: nextMonth, wedgeIndex, pregnant });
        };

        els.wheelFace.addEventListener("transitionend", onDone);
      });
    }

    function doSpin(){
      spinOnce({auto:false});
    }

    /** ----- Guess dial dragging ----- **/
    function angleFromCenter(clientX, clientY, element){
      const rect = element.getBoundingClientRect();
      const cx = rect.left + rect.width / 2;
      const cy = rect.top + rect.height / 2;
      const dx = clientX - cx;
      const dy = clientY - cy;
      const rad = Math.atan2(dy, dx);
      const degFromRight = rad * 180 / Math.PI;
      const degFromTop = (degFromRight + 90 + 360) % 360;
      return degFromTop;
    }

    function pctFromAngle(angleDeg){
      return Math.round((angleDeg / 360) * GUESS_MAX);
    }

    let dragging = false;

    function onDialPointerDown(ev){
      if (state.guessLocked || state.gameOver) return;
      dragging = true;
      try { els.guessDialShell.setPointerCapture(ev.pointerId); } catch(_){}
      ev.preventDefault();

      const angle = angleFromCenter(ev.clientX, ev.clientY, els.guessDialShell);
      setGuessPct(pctFromAngle(angle));
    }

    function onDialPointerMove(ev){
      if (!dragging || state.guessLocked || state.gameOver) return;
      ev.preventDefault();
      const angle = angleFromCenter(ev.clientX, ev.clientY, els.guessDialShell);
      setGuessPct(pctFromAngle(angle));
    }

    function onDialPointerUp(ev){
      if (!dragging) return;
      dragging = false;
      try { els.guessDialShell.releasePointerCapture(ev.pointerId); } catch(_){}
    }

    function onDialKeyDown(ev){
      if (state.guessLocked || state.gameOver) return;
      if (ev.key === "ArrowLeft" || ev.key === "ArrowDown"){
        ev.preventDefault();
        setGuessPct(state.guessPct - 1);
      } else if (ev.key === "ArrowRight" || ev.key === "ArrowUp"){
        ev.preventDefault();
        setGuessPct(state.guessPct + 1);
      } else if (ev.key === "Enter" || ev.key === " "){
        ev.preventDefault();
        lockGuessAndReveal();
      }
    }

    /** ----- Wire up events ----- **/
    function init(){
      buildMonthRow();
      buildRoundsTable();
      updateRoundsTable();

      els.ageHint.innerHTML = formatPresetHint();
      els.ageSelect.addEventListener("change", () => {
        els.ageHint.innerHTML = formatPresetHint();
        setMessage(
          `Age preset set to <b>${currentAgePreset().label}</b>. This will be used when the next round starts.`,
          "neutral"
        );
      });

      startNewGame();

      els.spinBtn.addEventListener("click", doSpin);
      els.lockBtn.addEventListener("click", lockGuessAndReveal);
      els.revealBtn.addEventListener("click", lockGuessAndReveal);

      els.nextRoundBtn.addEventListener("click", () => {
        if (state.gameOver) return;
        if (!state.guessLocked) return;
        if (state.round >= MAX_GAME_ROUNDS) return;
        if (state.autoSpinning || state.spinning) return;

        state.round += 1;
        startRound();
      });

      els.newGameBtn.addEventListener("click", startNewGame);

      els.guessSlider.addEventListener("input", (ev) => {
        if (state.guessLocked || state.gameOver) return;
        setGuessPct(Number(ev.target.value));
      });

      if ("PointerEvent" in window){
        els.guessDialShell.addEventListener("pointerdown", onDialPointerDown);
        els.guessDialShell.addEventListener("pointermove", onDialPointerMove);
        els.guessDialShell.addEventListener("pointerup", onDialPointerUp);
        els.guessDialShell.addEventListener("pointercancel", onDialPointerUp);
      } else {
        els.guessDialShell.addEventListener("mousedown", (e) => {
          if (state.guessLocked || state.gameOver) return;
          dragging = true;
          const move = (ev) => {
            const angle = angleFromCenter(ev.clientX, ev.clientY, els.guessDialShell);
            setGuessPct(pctFromAngle(angle));
          };
          const up = () => {
            dragging = false;
            document.removeEventListener("mousemove", move);
            document.removeEventListener("mouseup", up);
          };
          document.addEventListener("mousemove", move);
          document.addEventListener("mouseup", up);
          move(e);
        });
      }

      els.guessDialShell.addEventListener("keydown", onDialKeyDown);
      document.addEventListener("dragstart", (e) => e.preventDefault());
    }

    // Make failures visible.
    try {
      init();
    } catch (err){
      console.error(err);
      const msg = err && err.message ? err.message : String(err);
      setMessage(
        `❌ JavaScript error: <b>${msg}</b><br /><small>Try opening this file in a regular browser (Chrome/Firefox/Safari) instead of an in-app preview. If you are already in a browser, open DevTools → Console for details.</small>`,
        "bad"
      );
      try {
        els.spinBtn.disabled = true;
        els.lockBtn.disabled = true;
        els.revealBtn.disabled = true;
        els.guessSlider.disabled = true;
        els.nextRoundBtn.disabled = true;
      } catch(_){}
    }
  </script>
</body>
</html>
