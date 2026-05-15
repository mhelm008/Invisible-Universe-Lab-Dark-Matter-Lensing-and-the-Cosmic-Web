<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Invisible Universe Lab</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#030610;--panel:rgba(5,10,28,.92);--border:rgba(100,150,255,.18);
  --accent:#3d6fff;--cyan:#7df9ff;--pink:#ff6ef7;--gold:#ffd966;
  --text:#dce8ff;--muted:#7a93cc;--soft:#b0c4ee;
  --mono:'Space Mono',monospace;--display:'Syne',sans-serif;
}
html,body{width:100%;height:100%;background:var(--bg);color:var(--text);font-family:var(--mono);overflow:hidden}
canvas{display:block;position:fixed;top:0;left:0;z-index:0}

/* ── HEADER ── */
#hdr{
  position:fixed;top:0;left:0;right:0;z-index:30;height:50px;
  display:flex;align-items:center;justify-content:space-between;padding:0 18px;
  background:rgba(3,6,16,.82);backdrop-filter:blur(14px);
  border-bottom:1px solid var(--border);gap:10px;
}
.logo{font-family:var(--display);font-weight:800;font-size:15px;letter-spacing:.03em;
  color:#fff;display:flex;align-items:center;gap:9px;white-space:nowrap}
.dot{width:8px;height:8px;border-radius:50%;background:var(--cyan);
  box-shadow:0 0 10px var(--cyan);animation:pulse 2.4s ease-in-out infinite;flex-shrink:0}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.35;transform:scale(.65)}}
.tagline{font-size:9px;color:var(--muted);letter-spacing:.12em;text-transform:uppercase;white-space:nowrap}
.hdr-toggles{display:flex;gap:6px;flex-shrink:0}
.hdr-btn{
  padding:5px 10px;border-radius:6px;font-family:var(--mono);font-size:9px;
  letter-spacing:.1em;text-transform:uppercase;cursor:pointer;
  border:1px solid var(--border);background:rgba(255,255,255,.04);color:var(--muted);
  transition:all .15s;white-space:nowrap;
}
.hdr-btn:hover,.hdr-btn.active{background:rgba(61,111,255,.22);color:#adc8ff;border-color:rgba(61,111,255,.45)}

/* ── PANELS SHARED ── */
.side-panel{
  position:fixed;top:66px;z-index:20;width:282px;
  background:var(--panel);backdrop-filter:blur(18px);
  border:1px solid var(--border);border-radius:16px;
  padding:16px 15px 14px;
  transition:opacity .3s, transform .3s;
}
.side-panel.hidden{opacity:0;pointer-events:none}
#panel{left:18px;animation:slideInL .5s cubic-bezier(.22,1,.36,1) both}
#panel.hidden{transform:translateX(-14px)}
#glossary{
  right:18px;width:308px;
  max-height:calc(100vh - 88px);overflow-y:auto;
  animation:slideInR .5s .1s cubic-bezier(.22,1,.36,1) both;
  scrollbar-width:thin;scrollbar-color:rgba(100,140,255,.2) transparent;
}
#glossary.hidden{transform:translateX(14px)}
#glossary::-webkit-scrollbar{width:4px}
#glossary::-webkit-scrollbar-thumb{background:rgba(100,140,255,.2);border-radius:2px}
@keyframes slideInL{from{opacity:0;transform:translateX(-18px)}to{opacity:1;transform:translateX(0)}}
@keyframes slideInR{from{opacity:0;transform:translateX(18px)}to{opacity:1;transform:translateX(0)}}

/* ── CONTROLS ── */
.sec{font-size:9px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);
  margin-bottom:11px;padding-bottom:5px;border-bottom:1px solid var(--border)}
.mt{margin-top:14px}
.ctrl{margin-bottom:11px}
.ctrl label{display:flex;justify-content:space-between;align-items:center;
  font-size:10.5px;color:var(--text);margin-bottom:4px;letter-spacing:.03em}
.ctrl label .v{font-size:10px;min-width:30px;text-align:right;color:var(--cyan)}

input[type=range]{-webkit-appearance:none;width:100%;height:3px;
  background:rgba(100,140,255,.15);border-radius:2px;outline:none;cursor:pointer}
input[type=range]::-webkit-slider-thumb{
  -webkit-appearance:none;width:13px;height:13px;border-radius:50%;cursor:pointer;transition:transform .12s}
input[type=range]::-webkit-slider-thumb:hover{transform:scale(1.35)}
#rVis::-webkit-slider-thumb {background:#8fc4ff;box-shadow:0 0 8px #8fc4ff}
#rDm::-webkit-slider-thumb  {background:var(--accent);box-shadow:0 0 8px var(--accent)}
#rLens::-webkit-slider-thumb{background:var(--pink);box-shadow:0 0 8px var(--pink)}
#rAbun::-webkit-slider-thumb{background:var(--cyan);box-shadow:0 0 8px var(--cyan)}
#rTime::-webkit-slider-thumb{background:var(--gold);box-shadow:0 0 8px var(--gold)}

.btns{display:flex;gap:8px;margin-top:13px}
button.ctrl-btn{flex:1;padding:8px 0;border-radius:8px;font-family:var(--mono);
  font-size:11px;letter-spacing:.08em;cursor:pointer;
  transition:background .18s,box-shadow .18s;border:1px solid rgba(100,150,255,.28)}
#bPlay{background:rgba(61,111,255,.22);color:#adc8ff;border-color:rgba(61,111,255,.45)}
#bPlay:hover{background:rgba(61,111,255,.42);box-shadow:0 0 14px rgba(61,111,255,.4)}
#bReset{background:rgba(255,255,255,.04);color:var(--muted)}
#bReset:hover{background:rgba(255,255,255,.1);color:var(--text)}

/* CDG-2 preset button */
#bCdg{
  width:100%;margin-top:8px;padding:7px 0;border-radius:8px;font-family:var(--mono);
  font-size:10px;letter-spacing:.08em;cursor:pointer;
  background:rgba(255,110,247,.1);color:var(--pink);
  border:1px solid rgba(255,110,247,.3);
  transition:background .18s,box-shadow .18s;
}
#bCdg:hover{background:rgba(255,110,247,.22);box-shadow:0 0 12px rgba(255,110,247,.3)}

#stats{margin-top:13px;padding-top:11px;border-top:1px solid var(--border);
  display:grid;grid-template-columns:1fr 1fr 1fr;gap:5px}
.sb{background:rgba(255,255,255,.03);border:1px solid var(--border);
  border-radius:8px;padding:6px 4px;text-align:center;cursor:pointer;
  transition:border-color .15s}
.sb:hover{border-color:rgba(125,249,255,.4)}
.sv{font-family:var(--display);font-weight:700;font-size:13px;color:var(--cyan);display:block}
.sk{font-size:8px;letter-spacing:.1em;text-transform:uppercase;color:var(--muted);margin-top:2px;display:block}

/* ── GLOSSARY ── */
.g-title{font-family:var(--display);font-weight:800;font-size:14px;color:#fff;margin-bottom:3px}
.g-sub{font-size:9px;letter-spacing:.16em;text-transform:uppercase;color:var(--muted);
  margin-bottom:13px;padding-bottom:11px;border-bottom:1px solid var(--border)}

.acc-item{border:1px solid var(--border);border-radius:10px;margin-bottom:5px;overflow:hidden;transition:border-color .2s}
.acc-item.open{border-color:rgba(125,249,255,.28)}
.acc-head{display:flex;align-items:center;gap:9px;padding:9px 11px;cursor:pointer;user-select:none;transition:background .15s}
.acc-head:hover{background:rgba(255,255,255,.04)}
.acc-dot{width:7px;height:7px;border-radius:50%;flex-shrink:0}
.acc-label{font-size:10.5px;font-weight:700;color:var(--text);flex:1;letter-spacing:.02em}
.acc-sublabel{font-size:8.5px;color:var(--muted);letter-spacing:.07em;text-transform:uppercase}
.acc-arrow{font-size:9px;color:var(--muted);transition:transform .25s cubic-bezier(.22,1,.36,1);flex-shrink:0}
.acc-item.open .acc-arrow{transform:rotate(180deg)}
.acc-body{max-height:0;overflow:hidden;transition:max-height .38s cubic-bezier(.22,1,.36,1)}
.acc-item.open .acc-body{max-height:700px}
.acc-content{padding:0 11px 12px;font-size:10.5px;line-height:1.72;color:var(--soft)}
.acc-content p{margin-bottom:7px}
.acc-content p:last-child{margin-bottom:0}
.acc-content strong{color:var(--text);font-weight:700}
.acc-content em{color:var(--cyan);font-style:normal}
.acc-content a{color:var(--cyan);text-decoration:none;border-bottom:1px solid rgba(125,249,255,.3)}
.acc-content a:hover{border-color:var(--cyan)}
.tag{display:inline-block;padding:1px 6px;border-radius:4px;font-size:8.5px;
  letter-spacing:.1em;text-transform:uppercase;margin-right:3px;margin-top:5px}
.tag.sim{background:rgba(255,110,247,.08);color:var(--pink);border:1px solid rgba(255,110,247,.2)}
.tag.data{background:rgba(61,111,255,.12);color:#88b0ff;border:1px solid rgba(61,111,255,.22)}
.tag.real{background:rgba(125,249,255,.08);color:var(--cyan);border:1px solid rgba(125,249,255,.2)}
.acc-divider{height:1px;background:var(--border);margin:8px 0}

/* ── CREDITS ── */
#credits{
  position:fixed;bottom:22px;left:18px;z-index:20;
  background:var(--panel);backdrop-filter:blur(14px);
  border:1px solid var(--border);border-radius:10px;padding:10px 13px;
  animation:fadeUp .7s .65s cubic-bezier(.22,1,.36,1) both;
}
@keyframes fadeUp{from{opacity:0;transform:translateY(14px)}to{opacity:1;transform:translateY(0)}}
.cr-name{font-family:var(--display);font-weight:700;font-size:12px;color:var(--text)}
.cr-role{font-size:9px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);margin-top:1px;margin-bottom:7px}
.cr-links{display:flex;flex-direction:column;gap:3px}
.cr-links a{font-size:10px;color:var(--muted);text-decoration:none;display:flex;align-items:center;gap:6px;transition:color .15s}
.cr-links a:hover{color:var(--cyan)}
.cr-icon{width:13px;height:13px;opacity:.55;flex-shrink:0}

/* ── HINT ── */
#hint{position:fixed;bottom:22px;left:50%;transform:translateX(-50%);z-index:20;
  font-size:10px;color:rgba(150,180,255,.35);letter-spacing:.16em;text-transform:uppercase;
  pointer-events:none;animation:hf 4.5s 1.2s ease-in-out forwards;white-space:nowrap}
@keyframes hf{0%{opacity:0}20%{opacity:1}75%{opacity:1}100%{opacity:0}}

/* time display */
#timebox{
  position:fixed;bottom:22px;right:18px;z-index:20;
  background:var(--panel);backdrop-filter:blur(14px);
  border:1px solid var(--border);border-radius:10px;padding:8px 13px;
  font-size:10px;color:var(--muted);text-align:center;
  animation:fadeUp .7s .8s cubic-bezier(.22,1,.36,1) both;
}
#timebox .tval{font-family:var(--display);font-weight:700;font-size:16px;color:var(--gold);display:block;margin-bottom:2px}
#timebox .tlabel{font-size:8px;letter-spacing:.14em;text-transform:uppercase}
</style>
</head>
<body>

<!-- ── HEADER ── -->
<div id="hdr">
  <div class="logo"><span class="dot"></span>Invisible Universe Lab</div>
  <div class="tagline">COSMOS-Web · CDG-2 · Large-Scale Structure</div>
  <div class="hdr-toggles">
    <button class="hdr-btn active" id="tglControls" onclick="togglePanel('panel','tglControls')">Controls</button>
    <button class="hdr-btn active" id="tglGlossary" onclick="togglePanel('glossary','tglGlossary')">What am I looking at?</button>
  </div>
</div>

<!-- ── LEFT: CONTROLS ── -->
<div id="panel" class="side-panel">
  <div class="sec">Layer Visibility</div>

  <div class="ctrl">
    <label>Visible Matter <span class="v" id="vVis">1.00</span></label>
    <input id="rVis" type="range" min="0" max="1" step="0.01" value="1">
  </div>
  <div class="ctrl">
    <label>Dark Matter Halos <span class="v" id="vDm">1.00</span></label>
    <input id="rDm" type="range" min="0" max="2" step="0.01" value="1">
  </div>
  <div class="ctrl">
    <label>Lensing Signal <span class="v" id="vLens">1.00</span></label>
    <input id="rLens" type="range" min="0" max="1" step="0.01" value="1">
  </div>

  <div class="sec mt">Sandbox — Dial Dark Matter</div>

  <div class="ctrl">
    <label>DM Abundance <span class="v" id="vAbun">1.00</span></label>
    <input id="rAbun" type="range" min="0" max="3" step="0.01" value="1">
  </div>
  <div class="ctrl">
    <label>Cosmic Time <span class="v" id="vTime">0.550</span></label>
    <input id="rTime" type="range" min="0" max="1" step="0.001" value="0.55">
  </div>

  <div class="btns">
    <button class="ctrl-btn" id="bPlay">▶ Play</button>
    <button class="ctrl-btn" id="bReset">↺ Reset</button>
  </div>

  <!-- CDG-2 preset -->
  <button id="bCdg" onclick="setCDG2()">⬡ Load CDG-2 Scenario</button>

  <div id="stats">
    <div class="sb" onclick="openGlossary('acc-formation')">
      <span class="sv" id="sGal">—</span><span class="sk">Formation</span>
    </div>
    <div class="sb" onclick="openGlossary('acc-lensing-stat')">
      <span class="sv" id="sLen">—</span><span class="sk">Lensing</span>
    </div>
    <div class="sb" onclick="openGlossary('acc-web-stat')">
      <span class="sv" id="sWeb">—</span><span class="sk">Web Ctrst</span>
    </div>
  </div>
</div>

<!-- ── RIGHT: GLOSSARY ── -->
<div id="glossary" class="side-panel">
  <div class="g-title">What am I looking at?</div>
  <div class="g-sub">Click any entry to expand · click again to close</div>

  <!-- WHAT IS THIS SIMULATION -->
  <div class="acc-item" id="acc-sim">
    <div class="acc-head" onclick="toggle('acc-sim')">
      <span class="acc-dot" style="background:#ffd966;box-shadow:0 0 5px #ffd966"></span>
      <span class="acc-label">What is this simulation?</span>
      <span class="acc-sublabel">Start here</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>This is an <strong>interactive educational simulation</strong> of how cosmologists understand the invisible structure of the universe, inspired by two real discoveries made in early 2026.</p>
      <p>The simulation is <strong>not</strong> showing the universe as it looks in a telescope. It is showing what scientists <em>infer</em> exists — dark matter halos, gravitational lensing effects, and the cosmic web — based on the gravitational influence that invisible matter has on light and on galaxies we can see.</p>
      <p>The two real-world jumping-off points are:<br>
      — <strong>Jan 26, 2026:</strong> NASA/JWST published the highest-resolution dark matter map ever made (COSMOS-Web field).<br>
      — <strong>Feb 18, 2026:</strong> Hubble + ESA Euclid confirmed CDG-2, a galaxy that is ~99% dark matter.</p>
      <p>Use the <strong>Sandbox</strong> sliders on the left to dial dark matter abundance up or down and watch what happens to galaxy formation, lensing strength, and the large-scale web.</p>
      <span class="tag sim">Simulation</span><span class="tag real">Inspired by real data</span>
    </div></div>
  </div>

  <!-- NASA SOURCE -->
  <div class="acc-item" id="acc-nasa">
    <div class="acc-head" onclick="toggle('acc-nasa')">
      <span class="acc-dot" style="background:#3d6fff;box-shadow:0 0 5px #3d6fff"></span>
      <span class="acc-label">Source 1 — NASA/JWST Dark Matter Map</span>
      <span class="acc-sublabel">Jan 26, 2026 · Nature Astronomy</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>On January 26, 2026, a team led by <strong>Dr. Diana Scognamiglio</strong> (NASA Jet Propulsion Laboratory), Durham University, and EPFL published in <em>Nature Astronomy</em> the highest-resolution dark matter map ever made.</p>
      <p>The map covers a patch of sky called the <strong>COSMOS field</strong> (constellation Sextans), just 0.54 square degrees — about 2½ times the size of the full Moon. Inside that tiny patch: <strong>~800,000 galaxies</strong>, imaged by JWST's NIRCam over 255 hours of total exposure time.</p>
      <p><strong>How they found dark matter:</strong> they used <em>weak gravitational lensing</em> — measuring the very slight distortions in the shapes of hundreds of thousands of background galaxies caused by dark matter bending the light on its way to us. No individual galaxy's distortion is detectable; statistically across millions of galaxies, the signal emerges.</p>
      <p>The result is twice as sharp as any previous map and reveals individual dark matter clumps, filaments connecting clusters, and empty voids — exactly what you see simulated here.</p>
      <p><a href="https://www.nasa.gov/missions/webb/nasa-reveals-new-details-about-dark-matters-influence-on-universe/" target="_blank">→ Full NASA release</a><br>
      <a href="https://www.nature.com/articles/s41550-025-02763-9" target="_blank">→ Nature Astronomy paper (DOI: 10.1038/s41550-025-02763-9)</a></p>
      <span class="tag data">NASA/JWST</span><span class="tag data">COSMOS-Web</span>
    </div></div>
  </div>

  <!-- CDG-2 SOURCE -->
  <div class="acc-item" id="acc-cdg2">
    <div class="acc-head" onclick="toggle('acc-cdg2')">
      <span class="acc-dot" style="background:#ff6ef7;box-shadow:0 0 5px #ff6ef7"></span>
      <span class="acc-label">Source 2 — CDG-2, The Dark Galaxy</span>
      <span class="acc-sublabel">Feb 18, 2026 · Astrophys. Journal Letters</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p><strong>CDG-2 (Candidate Dark Galaxy 2)</strong> is a specific real galaxy, not the universe as a whole. It sits inside the <strong>Perseus galaxy cluster</strong>, 300 million light-years away. It was discovered and confirmed on February 18, 2026, by a team using Hubble Space Telescope, ESA's Euclid mission, and the ground-based Subaru Telescope in Hawaii.</p>
      <p>What makes CDG-2 extraordinary: it appears to be <strong>~99% dark matter</strong>. It contains only 4 globular star clusters (the Milky Way has 150+) and shines with the light of only ~6 million Suns. Most normal galaxies shine with billions. It is so faint it is essentially invisible — it was found not by its stars, but by detecting those 4 globular clusters and inferring a galaxy must underlie them. It is the <strong>first galaxy ever detected solely through its globular cluster population.</strong></p>
      <p>Scientists think CDG-2's normal matter (hydrogen gas needed for star formation) was <strong>stripped away</strong> by gravitational tidal forces inside the dense Perseus cluster, leaving behind a skeleton of dark matter.</p>
      <p>In this simulation, <strong>CDG-2 is what you get when you drag Visible Matter to near-zero and DM Abundance to maximum</strong>. Try the preset button. It is an extreme real-world example of what this sandbox is letting you explore.</p>
      <p><a href="https://science.nasa.gov/missions/hubble/nasas-hubble-identifies-one-of-darkest-known-galaxies/" target="_blank">→ NASA Hubble release</a><br>
      <a href="https://esahubble.org/news/heic2605/" target="_blank">→ ESA Hubble release</a></p>
      <span class="tag data">Hubble/Euclid/Subaru</span><span class="tag real">Real galaxy</span>
    </div></div>
  </div>

  <!-- VISIBLE GALAXIES -->
  <div class="acc-item" id="acc-galaxies">
    <div class="acc-head" onclick="toggle('acc-galaxies')">
      <span class="acc-dot" style="background:#8fc4ff;box-shadow:0 0 5px #8fc4ff"></span>
      <span class="acc-label">Visible Galaxies</span>
      <span class="acc-sublabel">Blue &amp; gold spheres · Visible Matter slider</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>The <strong>blue-white and gold spheres</strong> represent galaxies — gravitationally bound systems of stars, gas, dust, and dark matter. Blue galaxies are younger, actively star-forming. Gold ones are older ellipticals that have largely stopped forming stars.</p>
      <p><strong>Why do they grow as you raise DM Abundance?</strong> Dark matter halos form first, then their gravity pulls in normal hydrogen gas, which collapses and forms stars. More dark matter scaffolding means more gravitational wells, which means larger, more massive galaxies. This is called <strong>hierarchical structure formation</strong> and is the dominant theory of how galaxies came to be.</p>
      <p><strong>What does lowering Visible Matter to zero represent?</strong> You are hiding the normal matter component and seeing the dark matter skeleton alone — the situation astronomers must work in, because dark matter is what they cannot directly see. Dragging Visible Matter to nearly zero while keeping DM Abundance high approximates something like CDG-2: a structure that is mostly dark matter with only a whisper of visible stars.</p>
      <span class="tag sim">Simulation object</span>
    </div></div>
  </div>

  <!-- DARK MATTER HALOS -->
  <div class="acc-item" id="acc-dm">
    <div class="acc-head" onclick="toggle('acc-dm')">
      <span class="acc-dot" style="background:#3d6fff;box-shadow:0 0 5px #3d6fff"></span>
      <span class="acc-label">Dark Matter Halos</span>
      <span class="acc-sublabel">Blue wireframe shells · DM Halos slider</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>The <strong>semi-transparent wireframe icosahedra</strong> represent dark matter halos — invisible spheroidal clouds of dark matter that envelop every galaxy and galaxy cluster. They contain no atoms and emit no light, yet account for roughly <strong>27% of the total energy content of the universe</strong> and about <strong>85% of all matter</strong>.</p>
      <p>We know they exist because galaxies rotate far too fast for their visible mass to hold them together (the galaxy rotation curve problem, first noted by Vera Rubin in the 1970s). Something massive and invisible must extend far beyond the visible disk. The January 2026 JWST map confirms that wherever there is a cluster of galaxies, there is an equal concentration of dark matter in exactly the same place — not a coincidence, but direct evidence of dark matter's gravitational pull organizing normal matter.</p>
      <p>The <strong>wireframe</strong> aesthetic is intentional: these have no surface, no edge, and are not objects in the conventional sense — they are diffuse, extended distributions of matter we can only map statistically.</p>
      <p><strong>Raising DM Abundance</strong> makes the halos larger and more opaque, representing a universe where dark matter is denser. <strong>Lowering it toward zero</strong> causes galaxies to shrink and the web to dissolve, showing how dependent visible structure is on invisible scaffolding.</p>
      <span class="tag sim">Simulation object</span>
    </div></div>
  </div>

  <!-- GRAVITATIONAL LENSING -->
  <div class="acc-item" id="acc-lens">
    <div class="acc-head" onclick="toggle('acc-lens')">
      <span class="acc-dot" style="background:#ff6ef7;box-shadow:0 0 5px #ff6ef7"></span>
      <span class="acc-label">Gravitational Lensing Arcs</span>
      <span class="acc-sublabel">Cyan ring + pink arcs · Lensing Signal slider</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>Einstein's General Relativity predicts that mass warps spacetime, and light follows the curvature of spacetime. When an extremely massive object — a galaxy cluster or a dark matter halo — sits between Earth and a distant light source, the mass acts as a <strong>gravitational lens</strong>, bending and magnifying the light from objects behind it.</p>
      <p><strong>The cyan Einstein ring</strong> is what happens with near-perfect alignment: light from a source directly behind the lens spreads into a complete ring. <strong>The pink arcs</strong> are what you see with slight misalignment — the background galaxy gets stretched into an arc. Both are real phenomena routinely observed by Hubble and JWST.</p>
      <p><strong>Two types of lensing:</strong><br>
      — <em>Strong lensing</em> (visible to the naked eye in images): produces the dramatic arcs and Einstein rings shown here. Used to measure the mass of individual clusters.<br>
      — <em>Weak lensing</em> (statistical, not visible in individual images): the technique used by the January 2026 JWST team to build the dark matter map. Each background galaxy's shape is distorted by less than 1%, but across 800,000 galaxies the cumulative signal reconstructs where mass is concentrated.</p>
      <p>The <strong>Lensing Signal slider</strong> controls the visibility of these arcs. A value of 0 means no massive foreground concentration. A value of 1 represents a region like a massive cluster with detectable strong lensing. <strong>Raising DM Abundance increases lensing strength</strong> because more mass = more spacetime curvature = more bending of light.</p>
      <span class="tag sim">Simulation object</span><span class="tag real">Real phenomenon</span>
    </div></div>
  </div>

  <!-- COSMIC WEB -->
  <div class="acc-item" id="acc-web">
    <div class="acc-head" onclick="toggle('acc-web')">
      <span class="acc-dot" style="background:#7a8cff;box-shadow:0 0 5px #7a8cff"></span>
      <span class="acc-label">Cosmic Web Filaments</span>
      <span class="acc-sublabel">Blue thread lines · driven by DM Halos slider</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>On the very largest scales, matter is not spread uniformly. It forms a <strong>cosmic web</strong>: a sponge-like network of dense filaments (threads of dark matter and gas), sheets (walls), and nodes (clusters) surrounding enormous empty <em>voids</em>. Galaxies form along the filaments, like beads on a string. This large-scale structure is exactly what the January 2026 JWST map confirmed with unprecedented resolution, describing it as the "invisible scaffolding of the universe."</p>
      <p><strong>Why do the threads fade when you advance Cosmic Time?</strong> In the early universe, matter was nearly smooth with only tiny density variations. Gravity amplified those differences over billions of years, pulling matter into filaments and evacuating voids. But in the far future (high Cosmic Time values), <em>dark energy</em> drives accelerated expansion that increasingly stretches filaments apart, reducing the contrast between filled filaments and empty voids.</p>
      <p><strong>Reduce DM Abundance to zero</strong> and the web disappears entirely. This reflects the scientific consensus: without dark matter providing early gravitational seeds, the web we observe would never have formed. Normal matter alone could not have collapsed fast enough to build the structures we see.</p>
      <span class="tag sim">Simulation object</span><span class="tag real">Confirmed structure</span>
    </div></div>
  </div>

  <!-- FORMATION INDEX -->
  <div class="acc-item" id="acc-formation">
    <div class="acc-head" onclick="toggle('acc-formation')">
      <span class="acc-dot" style="background:var(--cyan);box-shadow:0 0 5px var(--cyan)"></span>
      <span class="acc-label">Formation Index stat</span>
      <span class="acc-sublabel">What the number means · click stat box to jump here</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>The <strong>Formation Index</strong> is a relative measure of how actively galaxies are assembling in the current simulation state. It is computed from DM Abundance and Cosmic Time: <em>max(0.15, 0.35 + 0.55 × abundance × (0.25 + time))</em></p>
      <p>It is <strong>not a real physical unit</strong> — it is a dimensionless index, like a dial from "sparse early universe" (~0.2) to "mature, highly structured universe" (~2.5+). Think of it as a rough proxy for the <strong>galaxy star formation rate density</strong> — a quantity cosmologists measure by counting how many solar masses of new stars form per year per cubic megaparsec of space.</p>
      <p>In reality, the cosmic star formation rate peaked roughly <strong>10 billion years ago</strong> (about 3–4 billion years after the Big Bang) and has been declining ever since as gas gets consumed or expelled. More dark matter in early models accelerates the onset of this peak. Try setting DM Abundance to 3.0 and watching how fast and large galaxies grow — then dial it back to 0.2 and see how the universe remains sparse and underdeveloped.</p>
      <span class="tag sim">Simulated index</span>
    </div></div>
  </div>

  <!-- LENSING STRENGTH STAT -->
  <div class="acc-item" id="acc-lensing-stat">
    <div class="acc-head" onclick="toggle('acc-lensing-stat')">
      <span class="acc-dot" style="background:var(--pink);box-shadow:0 0 5px var(--pink)"></span>
      <span class="acc-label">Lensing Strength stat</span>
      <span class="acc-sublabel">What the number means · click stat box to jump here</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>The <strong>Lensing Strength</strong> value estimates how powerfully the mass in this simulated region bends background light: <em>max(0.05, abundance × (0.35 + 0.8 × time))</em></p>
      <p>It rises with both dark matter abundance (more mass = stronger lens) and cosmic time (structures collapse into denser concentrations over time, making stronger lenses). In real observational cosmology, this is measured via the <strong>Einstein radius</strong> — the angular size of the ring — and the <strong>shear field</strong>, the pattern of distortions across background galaxy shapes.</p>
      <p>The January 2026 JWST map was built by measuring the shear field across 800,000 background galaxies. Each galaxy's shape was distorted by less than 1% — undetectable individually — but statistically, the signal reconstructed where dark matter concentrations must be to produce those patterns. A Lensing Strength of ~1.5+ in this simulation corresponds roughly to a region like a rich galaxy cluster (e.g., Abell 2744), which produces some of the most dramatic Einstein arcs ever photographed.</p>
      <span class="tag sim">Simulated index</span>
    </div></div>
  </div>

  <!-- WEB CONTRAST STAT -->
  <div class="acc-item" id="acc-web-stat">
    <div class="acc-head" onclick="toggle('acc-web-stat')">
      <span class="acc-dot" style="background:#7a8cff;box-shadow:0 0 5px #7a8cff"></span>
      <span class="acc-label">Web Contrast stat</span>
      <span class="acc-sublabel">What the number means · click stat box to jump here</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p><strong>Web Contrast</strong> measures how sharply defined the cosmic web filaments are versus surrounding voids: <em>max(0.10, 0.2 + 0.75 × abundance × (1 − time × 0.7))</em></p>
      <p>Notice it <em>decreases</em> as cosmic time advances. In the early-to-middle universe, gravity is building the web — density contrast between filaments and voids grows quickly. In the far future, dark energy (which makes up ~68% of the universe's energy content) drives accelerated expansion that begins stretching and thinning filaments, reducing this contrast on the largest scales.</p>
      <p>In real cosmology, web contrast is measured via the <strong>matter power spectrum</strong> — a Fourier decomposition of how clumpy matter is at different length scales. The σ8 and S8 parameters describe the overall amplitude of this clumpiness. Some recent surveys (including Euclid-related results) have found the universe may be <em>slightly smoother</em> at small scales than the standard ΛCDM (Lambda Cold Dark Matter) model predicts — a mild tension that the CDG-2 discovery adds texture to: if even some galaxies are far more dark-matter-dominated than expected, it may point to processes that smooth out small-scale structure.</p>
      <span class="tag sim">Simulated index</span>
    </div></div>
  </div>

  <!-- COSMIC TIME -->
  <div class="acc-item" id="acc-time">
    <div class="acc-head" onclick="toggle('acc-time')">
      <span class="acc-dot" style="background:#ffd966;box-shadow:0 0 5px #ffd966"></span>
      <span class="acc-label">Cosmic Time slider</span>
      <span class="acc-sublabel">0 = Big Bang &rarr; 0.5 = today &rarr; 1 = far future</span>
      <span class="acc-arrow">▼</span>
    </div>
    <div class="acc-body"><div class="acc-content">
      <p>The slider maps 0 → 1 onto the full history (and projected future) of the universe. <strong>0</strong> is the Big Bang, 13.8 billion years ago. <strong>~0.5</strong> is roughly the present day. <strong>1</strong> represents the far future — perhaps 100+ billion years from now, long after the Milky Way and Andromeda have merged and most stars have burned out.</p>
      <p>As you advance time you will see: galaxies <strong>grow and merge</strong> (hierarchical assembly); the cosmic web <strong>sharpens then fades</strong> (gravity builds structure, then dark energy dilutes it); lensing signals <strong>strengthen then plateau</strong> (denser clusters form, then expansion disperses them); the background stars <strong>dim</strong> (older, cooler stellar populations dominate).</p>
      <p>When <strong>▶ Play</strong> is active, time advances at ~0.00035 per frame (60fps), so one full traversal from 0 to 1 takes about 6–7 minutes of real time — representing 13+ billion years of cosmic evolution compressed into a coffee break.</p>
      <span class="tag sim">Simulation parameter</span>
    </div></div>
  </div>

</div><!-- /glossary -->

<!-- ── CREDITS ── -->
<div id="credits">
  <div class="cr-name">Michael Helms</div>
  <div class="cr-role">Student · UC Riverside</div>
  <div class="cr-links">
    <a href="mailto:you@example.com">
      <svg class="cr-icon" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg"><rect x="1" y="3" width="14" height="10" rx="1.5" stroke="currentColor" stroke-width="1.4"/><path d="M1 4.5l7 5 7-5" stroke="currentColor" stroke-width="1.4" stroke-linecap="round"/></svg>
      mhelm008@ucr.edu · mhelms987@gmail.com
    </a>
    <a href="https://github.com/yourusername" target="_blank">
      <svg class="cr-icon" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0016 8c0-4.42-3.58-8-8-8z"/></svg>
      github.com/mhelm008
    </a>
  </div>
</div>

<div id="hint">Drag to orbit · Scroll to zoom</div>

<div id="timebox">
  <span class="tval" id="tboxVal">t = 0.550</span>
  <span class="tlabel">Cosmic Time</span>
</div>

<!-- ── THREE.JS ── -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
/* ── Inline OrbitControls ─────────────────────────────────────────────── */
THREE.OrbitControls=function(camera,domElement){
  this.camera=camera;this.domElement=domElement;this.enabled=true;
  this.target=new THREE.Vector3();this.enableDamping=false;this.dampingFactor=0.05;
  this.enableZoom=true;this.zoomSpeed=1.0;this.enableRotate=true;this.rotateSpeed=1.0;
  this.enablePan=true;this.panSpeed=1.0;this.minDistance=0;this.maxDistance=Infinity;
  this.minPolarAngle=0;this.maxPolarAngle=Math.PI;
  var scope=this;
  var spherical=new THREE.Spherical(),sphericalDelta=new THREE.Spherical();
  var scale=1,panOffset=new THREE.Vector3();
  var rotateStart=new THREE.Vector2(),rotateEnd=new THREE.Vector2(),rotateDelta=new THREE.Vector2();
  var panStart=new THREE.Vector2(),panEnd=new THREE.Vector2(),panDelta=new THREE.Vector2();
  var STATE={NONE:-1,ROTATE:0,PAN:1};var state=STATE.NONE;
  function getZoomScale(){return Math.pow(0.95,scope.zoomSpeed)}
  function rotateLeft(a){sphericalDelta.theta-=a}function rotateUp(a){sphericalDelta.phi-=a}
  var panLeft=(function(){var v=new THREE.Vector3();return function(d,m){v.setFromMatrixColumn(m,0);v.multiplyScalar(-d);panOffset.add(v);}})();
  var panUp=(function(){var v=new THREE.Vector3();return function(d,m){v.setFromMatrixColumn(m,1);v.multiplyScalar(d);panOffset.add(v);}})();
  function doPan(dx,dy){var el=scope.domElement;var td=scope.camera.position.clone().sub(scope.target).length()*Math.tan(scope.camera.fov/2*Math.PI/180);panLeft(2*dx*td/el.clientHeight,scope.camera.matrix);panUp(2*dy*td/el.clientHeight,scope.camera.matrix);}
  this.update=(function(){var offset=new THREE.Vector3();var quat=new THREE.Quaternion().setFromUnitVectors(camera.up,new THREE.Vector3(0,1,0));var qi=quat.clone().invert();return function(){var pos=scope.camera.position;offset.copy(pos).sub(scope.target);offset.applyQuaternion(quat);spherical.setFromVector3(offset);if(scope.enableDamping){spherical.theta+=sphericalDelta.theta*scope.dampingFactor;spherical.phi+=sphericalDelta.phi*scope.dampingFactor;}else{spherical.theta+=sphericalDelta.theta;spherical.phi+=sphericalDelta.phi;}spherical.phi=Math.max(scope.minPolarAngle,Math.min(scope.maxPolarAngle,spherical.phi));spherical.makeSafe();spherical.radius*=scale;spherical.radius=Math.max(scope.minDistance,Math.min(scope.maxDistance,spherical.radius));if(scope.enableDamping){scope.target.addScaledVector(panOffset,scope.dampingFactor);}else{scope.target.add(panOffset);}offset.setFromSpherical(spherical);offset.applyQuaternion(qi);pos.copy(scope.target).add(offset);scope.camera.lookAt(scope.target);if(scope.enableDamping){sphericalDelta.theta*=(1-scope.dampingFactor);sphericalDelta.phi*=(1-scope.dampingFactor);panOffset.multiplyScalar(1-scope.dampingFactor);}else{sphericalDelta.set(0,0,0);panOffset.set(0,0,0);}scale=1;};})();
  function onDown(e){if(!scope.enabled)return;if(e.button===0){state=STATE.ROTATE;rotateStart.set(e.clientX,e.clientY);}else if(e.button===2){state=STATE.PAN;panStart.set(e.clientX,e.clientY);}document.addEventListener('mousemove',onMove);document.addEventListener('mouseup',onUp);}
  function onMove(e){if(!scope.enabled)return;if(state===STATE.ROTATE){rotateEnd.set(e.clientX,e.clientY);rotateDelta.subVectors(rotateEnd,rotateStart).multiplyScalar(scope.rotateSpeed);var el=scope.domElement;rotateLeft(2*Math.PI*rotateDelta.x/el.clientHeight);rotateUp(2*Math.PI*rotateDelta.y/el.clientHeight);rotateStart.copy(rotateEnd);scope.update();}else if(state===STATE.PAN){panEnd.set(e.clientX,e.clientY);panDelta.subVectors(panEnd,panStart).multiplyScalar(scope.panSpeed);doPan(panDelta.x,panDelta.y);panStart.copy(panEnd);scope.update();}}
  function onUp(){document.removeEventListener('mousemove',onMove);document.removeEventListener('mouseup',onUp);state=STATE.NONE;}
  function onWheel(e){if(!scope.enabled||!scope.enableZoom)return;e.preventDefault();if(e.deltaY<0)scale*=1/getZoomScale();else scale*=getZoomScale();scope.update();}
  domElement.addEventListener('mousedown',onDown);
  domElement.addEventListener('wheel',onWheel,{passive:false});
  domElement.addEventListener('contextmenu',function(e){e.preventDefault();});
  this.update();
};
</script>

<script>
/* ══════════════════════════════════════════════════════════
   UI LOGIC
══════════════════════════════════════════════════════════ */
function togglePanel(panelId, btnId) {
  var panel = document.getElementById(panelId);
  var btn   = document.getElementById(btnId);
  panel.classList.toggle('hidden');
  btn.classList.toggle('active');
}

function toggle(id) {
  var item = document.getElementById(id);
  var wasOpen = item.classList.contains('open');
  document.querySelectorAll('.acc-item').forEach(function(el){ el.classList.remove('open'); });
  if (!wasOpen) item.classList.add('open');
}

function openGlossary(id) {
  // Make sure glossary is visible
  var glossary = document.getElementById('glossary');
  var btn = document.getElementById('tglGlossary');
  if (glossary.classList.contains('hidden')) {
    glossary.classList.remove('hidden');
    btn.classList.add('active');
  }
  // Close all, open target
  document.querySelectorAll('.acc-item').forEach(function(el){ el.classList.remove('open'); });
  var target = document.getElementById(id);
  if (target) {
    target.classList.add('open');
    setTimeout(function(){ target.scrollIntoView({behavior:'smooth',block:'nearest'}); }, 50);
  }
}

// CDG-2 scenario: 99% dark matter galaxy
function setCDG2() {
  set('rVis', 0.04);  set('rDm', 2.8);
  set('rLens', 0.6);  set('rAbun', 2.6);
  set('rTime', 0.45);
  updateLabels();
  openGlossary('acc-cdg2');
}

function set(id, val) { document.getElementById(id).value = val; }

function updateLabels() {
  ['Vis','Dm','Lens','Abun'].forEach(function(n){
    var el = document.getElementById('r'+n);
    document.getElementById('v'+n).textContent = parseFloat(el.value).toFixed(2);
  });
  var t = parseFloat(document.getElementById('rTime').value);
  document.getElementById('vTime').textContent = t.toFixed(3);
}

/* ══════════════════════════════════════════════════════════
   THREE.JS SCENE
══════════════════════════════════════════════════════════ */
var scene = new THREE.Scene();
scene.background = new THREE.Color(0x030610);
var camera = new THREE.PerspectiveCamera(55, window.innerWidth/window.innerHeight, 0.1, 2000);
camera.position.set(0, 28, 72);
var renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);
var controls = new THREE.OrbitControls(camera, renderer.domElement);
controls.enableDamping=true; controls.dampingFactor=0.07;
controls.minDistance=15; controls.maxDistance=300;

// Lights
scene.add(new THREE.AmbientLight(0x9bb7ff, 0.65));
var dl=new THREE.DirectionalLight(0xffffff,1.2); dl.position.set(20,35,10); scene.add(dl);

// Stars
(function(){
  var geo=new THREE.BufferGeometry(),n=4000,buf=new Float32Array(n*3);
  for(var i=0;i<n;i++){
    var r=900*Math.random(),th=Math.acos(2*Math.random()-1),ph=2*Math.PI*Math.random();
    buf[i*3]=r*Math.sin(th)*Math.cos(ph);buf[i*3+1]=r*Math.cos(th);buf[i*3+2]=r*Math.sin(th)*Math.sin(ph);
  }
  geo.setAttribute('position',new THREE.BufferAttribute(buf,3));
  window._stars=new THREE.Points(geo,new THREE.PointsMaterial({color:0xffffff,size:0.55,sizeAttenuation:true}));
  scene.add(window._stars);
})();

// Groups
var visGroup=new THREE.Group(); scene.add(visGroup);
var dmGroup=new THREE.Group();  scene.add(dmGroup);
var lensGroup=new THREE.Group();scene.add(lensGroup);
var webGroup=new THREE.Group(); scene.add(webGroup);

// Galaxies
for(var i=0;i<90;i++){
  var gm=new THREE.Mesh(
    new THREE.SphereGeometry(1.1*(0.4+Math.random()*1.7),18,18),
    new THREE.MeshStandardMaterial({color:i%3?0x8fc0ff:0xf7d5a2,roughness:.9,metalness:0,emissive:0x101020,transparent:true,opacity:1})
  );
  gm.position.set((Math.random()-.5)*64,(Math.random()-.5)*18,(Math.random()-.5)*64);
  visGroup.add(gm);
}

// DM halos
for(var i=0;i<16;i++){
  var hm=new THREE.Mesh(
    new THREE.IcosahedronGeometry(8+Math.random()*18,1),
    new THREE.MeshBasicMaterial({color:0x2f77ff,transparent:true,opacity:.32,wireframe:true})
  );
  hm.position.set((Math.random()-.5)*42,(Math.random()-.5)*12,(Math.random()-.5)*42);
  dmGroup.add(hm);
}

// Lensing
var lr=new THREE.Mesh(new THREE.TorusGeometry(14,.25,10,120),new THREE.MeshBasicMaterial({color:0x7df9ff,transparent:true,opacity:.8}));
lr.rotation.x=Math.PI/2; lensGroup.add(lr);
for(var i=0;i<8;i++){
  var arc=new THREE.Mesh(new THREE.TorusGeometry(22+Math.sin(i)*2,.08,8,80,1.8),new THREE.MeshBasicMaterial({color:0xff8bd4,transparent:true,opacity:.45}));
  arc.rotation.x=Math.PI/2; arc.rotation.z=i/8*Math.PI*2; lensGroup.add(arc);
}

// Web
for(var i=0;i<140;i++){
  var pts=[],len=6+Math.random()*20;
  var x0=(Math.random()-.5)*72,y0=(Math.random()-.5)*24,z0=(Math.random()-.5)*72;
  for(var j=0;j<10;j++) pts.push(new THREE.Vector3(x0+(Math.random()-.5)*len,y0+(Math.random()-.5)*len*.25,z0+(Math.random()-.5)*len));
  webGroup.add(new THREE.Line(new THREE.BufferGeometry().setFromPoints(pts),new THREE.LineBasicMaterial({color:0x7a8cff,transparent:true,opacity:.28})));
}

/* ── PLAYBACK ── */
var playing = false; // starts paused — user presses Play
var playBtn = document.getElementById('bPlay');
playBtn.textContent = '▶ Play';

playBtn.onclick = function(){
  playing = !playing;
  playBtn.textContent = playing ? '⏸ Pause' : '▶ Play';
  if (playing) playBtn.style.background = 'rgba(61,111,255,.42)';
  else playBtn.style.background = 'rgba(61,111,255,.22)';
};

document.getElementById('bReset').onclick = function(){
  playing = false;
  playBtn.textContent = '▶ Play';
  playBtn.style.background = 'rgba(61,111,255,.22)';
  set('rVis',1); set('rDm',1); set('rLens',1); set('rAbun',1); set('rTime',0.55);
  updateLabels();
};

// Live labels
['Vis','Dm','Lens','Abun'].forEach(function(n){
  document.getElementById('r'+n).addEventListener('input',function(){
    document.getElementById('v'+n).textContent=parseFloat(this.value).toFixed(2);
  });
});
document.getElementById('rTime').addEventListener('input',function(){
  document.getElementById('vTime').textContent=parseFloat(this.value).toFixed(3);
  document.getElementById('tboxVal').textContent='t = '+parseFloat(this.value).toFixed(3);
});

/* ── RENDER LOOP ── */
function gv(id){return parseFloat(document.getElementById(id).value);}
function gt(id){return document.getElementById(id);}

function animate(){
  requestAnimationFrame(animate);

  if(playing){
    var t=(gv('rTime')+0.00035)%1;
    gt('rTime').value=t;
    gt('vTime').textContent=t.toFixed(3);
    gt('tboxVal').textContent='t = '+t.toFixed(3);
  }

  var vis=gv('rVis'), dm=gv('rDm'), lens=gv('rLens'), abun=gv('rAbun'), t=gv('rTime');

  visGroup.visible  = vis  > 0.01;
  dmGroup.visible   = dm   > 0.01;
  lensGroup.visible = lens > 0.01;
  webGroup.visible  = dm   > 0.02;

  for(var i=0;i<visGroup.children.length;i++){
    var m=visGroup.children[i];
    m.scale.setScalar((0.45+abun*(0.15+t))*(0.7+(i%7)/8));
    m.material.opacity=0.12+0.88*vis;
  }
  for(var i=0;i<dmGroup.children.length;i++){
    var m=dmGroup.children[i];
    m.material.opacity=0.04+0.28*dm;
    m.scale.setScalar(0.75+abun*0.65+0.08*Math.sin(i+t*6));
    m.rotation.x+=0.002+0.001*abun;
    m.rotation.y+=0.0015+0.0008*abun;
  }
  for(var i=0;i<lensGroup.children.length;i++){
    var m=lensGroup.children[i];
    m.material.opacity=0.06+0.7*lens*abun;
    m.scale.setScalar(0.6+abun*0.7+t*0.3);
    m.rotation.z+=0.002;
  }
  for(var i=0;i<webGroup.children.length;i++){
    webGroup.children[i].material.opacity=(0.04+0.25*dm)*(0.35+(1-t)*0.95);
  }

  window._stars.material.opacity=0.5+0.4*(1-t);
  scene.rotation.y=Math.sin(t*Math.PI*2)*0.15;

  gt('sGal').textContent=Math.max(0.15,0.35+0.55*abun*(0.25+t)).toFixed(2);
  gt('sLen').textContent=Math.max(0.05,abun*(0.35+0.8*t)).toFixed(2);
  gt('sWeb').textContent=Math.max(0.10,0.2+0.75*abun*(1-t*0.7)).toFixed(2);

  controls.update();
  renderer.render(scene,camera);
}
animate();

window.addEventListener('resize',function(){
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth,window.innerHeight);
});
</script>
</body>
</html>
