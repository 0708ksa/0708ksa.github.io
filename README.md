<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>❤ 给我的宝贝</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: #0a0015;
    font-family: 'Georgia', 'STKaiti', 'KaiTi', '楷体', serif;
    cursor: pointer;
    overflow-x: hidden;
  }
  /* ── Fixed 3D canvas background ── */
  #bg-canvas {
    position: fixed; top: 0; left: 0;
    width: 100vw; height: 100vh;
    z-index: 0; pointer-events: none;
  }
  /* ── Scrollable content ── */
  #scroll-container {
    position: relative; z-index: 10;
    pointer-events: none;
  }
  /* ── Hero (first screen) ── */
  .hero {
    height: 100vh; display: flex;
    flex-direction: column; align-items: center; justify-content: center;
  }
  .hero .title {
    font-size: clamp(2.5rem, 7vw, 5rem);
    color: #fff;
    text-shadow: 0 0 40px #ff3366, 0 0 80px #ff3366, 0 0 140px #ff6699;
    animation: glowPulse 2s ease-in-out infinite;
    letter-spacing: 0.1em;
  }
  .hero .subtitle {
    font-size: clamp(1rem, 2.5vw, 1.6rem);
    color: #ffccdd;
    text-shadow: 0 0 20px #ff3366;
    animation: fadeUp 3s ease-in-out infinite;
    letter-spacing: 0.12em;
    margin-top: 0.4rem;
  }
  .hero .hearts-row {
    display: flex; gap: 1rem; margin-top: 2rem;
    animation: floatRow 2s ease-in-out infinite;
  }
  .hero .hearts-row span { font-size: 2.5rem; filter: drop-shadow(0 0 15px #ff3366); }
  .scroll-hint {
    position: absolute; bottom: 2.5rem; left: 50%; transform: translateX(-50%);
    color: rgba(255,255,255,0.6); font-size: 0.9rem;
    letter-spacing: 0.2em;
    animation: bounceDown 1.8s ease-in-out infinite;
  }
  .scroll-hint .arrow { display: block; text-align: center; font-size: 1.4rem; margin-top: 0.3rem; }
  /* ── Letter sections ── */
  .letter-section {
    min-height: 100vh; display: flex;
    align-items: center; justify-content: center;
    padding: 4rem 2rem;
  }
  .letter-card {
    max-width: 680px; width: 100%;
    background: rgba(20, 5, 40, 0.65);
    backdrop-filter: blur(18px);
    -webkit-backdrop-filter: blur(18px);
    border: 1px solid rgba(255, 100, 150, 0.25);
    border-radius: 24px;
    padding: 3rem 2.5rem;
    color: #f0d0dd;
    line-height: 2.2;
    font-size: clamp(1rem, 2vw, 1.25rem);
    letter-spacing: 0.05em;
    opacity: 0;
    transform: translateY(50px);
    transition: opacity 1.2s ease, transform 1.2s ease;
    box-shadow: 0 20px 60px rgba(255, 50, 100, 0.12), 0 0 120px rgba(200, 50, 100, 0.06);
  }
  .letter-card.visible {
    opacity: 1; transform: translateY(0);
  }
  .letter-card .stamp {
    text-align: center; font-size: 2rem; margin-bottom: 1.2rem;
    filter: drop-shadow(0 0 8px #ff6699);
  }
  .letter-card .date {
    text-align: right; color: rgba(255,180,200,0.6);
    font-size: 0.85rem; margin-bottom: 2rem;
    letter-spacing: 0.1em;
  }
  .letter-card h2 {
    text-align: center; color: #ff99bb;
    font-size: 1.8rem; margin-bottom: 1.8rem;
    text-shadow: 0 0 18px rgba(255,100,150,0.5);
    letter-spacing: 0.15em;
  }
  .letter-card p {
    text-indent: 2em; margin-bottom: 1.2rem;
  }
  .letter-card .signature {
    text-align: right; margin-top: 2.5rem;
    color: #ffaacc; font-size: 1.1rem;
    letter-spacing: 0.12em;
  }
  .letter-card .highlight {
    color: #ff99bb; font-weight: bold;
    text-shadow: 0 0 10px rgba(255,100,150,0.4);
  }
  /* ── Fireflies (DOM) ── */
  .firefly {
    position: fixed; pointer-events: none; z-index: 5;
    width: 4px; height: 4px; border-radius: 50%;
    background: #ffcc88;
    box-shadow: 0 0 10px 3px #ffaa44, 0 0 20px 6px #ff8833;
    animation: fireflyFloat 6s ease-in-out infinite;
  }
  /* ── Keyframes ── */
  @keyframes glowPulse {
    0%,100% { text-shadow: 0 0 40px #ff3366, 0 0 80px #ff3366, 0 0 140px #ff6699; }
    50%     { text-shadow: 0 0 60px #ff6699, 0 0 130px #ff3366, 0 0 200px #ff99bb; }
  }
  @keyframes fadeUp {
    0%,100% { opacity: 0.65; transform: translateY(0); }
    50%     { opacity: 1;     transform: translateY(-8px); }
  }
  @keyframes floatRow {
    0%,100% { transform: translateY(0); }
    50%     { transform: translateY(-12px); }
  }
  @keyframes bounceDown {
    0%,100% { opacity: 0.4; transform: translateX(-50%) translateY(0); }
    50%     { opacity: 1;   transform: translateX(-50%) translateY(10px); }
  }
  @keyframes fireflyFloat {
    0%,100% { transform: translate(0,0) scale(1); opacity: 0; }
    20%     { opacity: 0.9; }
    40%     { opacity: 0.3; }
    60%     { opacity: 1; }
    80%     { opacity: 0.4; }
  }
</style>
</head>
<body>
<canvas id="bg-canvas"></canvas>
<div id="scroll-container">
  <!-- Hero -->
  <section class="hero">
    <h1 class="title">我 爱 你</h1>
    <p class="subtitle">你是我生命中最美的意外 ✨</p>
    <div class="hearts-row">
      <span>💖</span><span>💕</span><span>💗</span><span>💕</span><span>💖</span>
    </div>
    <div class="scroll-hint">
      向下滚动，读一封写给你的信
      <span class="arrow">↓</span>
    </div>
  </section>
  <!-- Letter: Chapter 1 -->
  <section class="letter-section">
    <div class="letter-card" data-reveal>
      <div class="stamp">💌</div>
      <h2>遇见你，是一切美好的开始</h2>
      <p>亲爱的宝贝：</p>
      <p>写下这些字的时候，我的脑海里全是你的笑容。说不清楚是从哪一刻开始的——也许是那天你不经意的一个回眸，也许是某次聊天时你发来的那句晚安——总之，<span class="highlight">你就这样走进了我的世界</span>，然后，一切都变得不一样了。</p>
      <p>遇见你之前，我以为生活不过如此。遇见你之后，我才明白，原来每一天都可以被期待填满。你的出现，像是宇宙安排好的一场惊喜，不早不晚，刚好撞进我的心里。</p>
    </div>
  </section>
  <!-- Letter: Chapter 2 -->
  <section class="letter-section">
    <div class="letter-card" data-reveal>
      <div class="stamp">💕</div>
      <h2>和你在一起的每分每秒</h2>
      <p>我喜欢看你笑的样子，眼睛弯成月牙，比世界上任何风景都好看。我喜欢听你讲那些琐碎的小事——今天吃了什么、路上遇到了哪只猫——因为<span class="highlight">只要是你说的，我都觉得有趣</span>。</p>
      <p>和你在一起的时候，世界变得很安静，也很大。安静是因为心里有了归属，大是因为——我们还有那么多想去的地方、想吃的东西、想一起完成的事。每一个平凡的日子，因为有你在，都变成了值得纪念的时光。</p>
    </div>
  </section>
  <!-- Letter: Chapter 3 -->
  <section class="letter-section">
    <div class="letter-card" data-reveal>
      <div class="stamp">💗</div>
      <h2>你让我成为更好的自己</h2>
      <p>有人说，好的爱情不是互相凝视，而是一起朝同一个方向看。谢谢你，在我迷茫的时候给我力量，在我低落的时候给我拥抱。<span class="highlight">因为你，我想要成为一个更好的人</span>——不是为了配得上你，而是因为你值得这世间所有的美好。</p>
      <p>你教会了我耐心、温柔，还有无条件的信任。和你在一起之后，我开始期待未来，开始认真规划每一个明天。因为你，我对这个世界多了好多好多的热爱。</p>
    </div>
  </section>
  <!-- Letter: Chapter 4 -->
  <section class="letter-section">
    <div class="letter-card" data-reveal>
      <div class="stamp">💍</div>
      <h2>关于未来，我想对你说</h2>
      <p>我不知道未来会是什么样子，但我知道，<span class="highlight">我想和你一起去看</span>。想和你一起走过春夏秋冬，看花开花落、云卷云舒。想和你一起在厨房里手忙脚乱，在沙发上看无聊的电影，在清晨第一缕阳光里醒来。</p>
      <p>世界很大，大到我们可能一辈子都走不完。但没关系，因为最好的风景，我已经找到了——那就是你。</p>
      <p>从今以后，无论风雨还是晴天，我都想站在你身边。不是因为你完美，而是因为和你在一起，一切才完整。</p>
      <div class="signature">永远爱你的<br />—— 你的傻瓜 ❤</div>
    </div>
  </section>
  <!-- Letter: Final -->
  <section class="letter-section">
    <div class="letter-card" data-reveal>
      <div class="stamp">💫</div>
      <h2>写在最后</h2>
      <p>这封信很短，短到装不下我想对你说的所有话。但一辈子很长，长到我们可以慢慢说完。</p>
      <p style="text-align:center; text-indent:0; font-size:1.5rem; margin-top:2rem;">
        <span class="highlight">我爱你 ❤</span>
      </p>
      <p style="text-align:center; text-indent:0; color:rgba(255,180,200,0.7);">
        不只是今天，而是每一天。
      </p>
    </div>
  </section>
  <!-- Spacer for final visual -->
  <section style="height:40vh; display:flex; align-items:center; justify-content:center;">
    <p style="color:rgba(255,255,255,0.3); font-size:0.9rem; letter-spacing:0.2em;">
      ✨ 向上滑动，回到你的世界 ✨
    </p>
  </section>
</div>
<!-- Fireflies injected by JS -->
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.160.0/build/three.module.js"
  }
}
</script>
<script type="module">
import * as THREE from 'three';
// ── DOM Fireflies ───────────────────────────────────────
const fireflyCount = 25;
for (let i = 0; i < fireflyCount; i++) {
  const el = document.createElement('div');
  el.className = 'firefly';
  el.style.left = Math.random()*100 + '%';
  el.style.top  = Math.random()*100 + '%';
  el.style.animationDelay = Math.random()*6 + 's';
  el.style.animationDuration = (4 + Math.random()*8) + 's';
  document.body.appendChild(el);
}
// ── Scroll reveal ──────────────────────────────────────
const revealCards = document.querySelectorAll('[data-reveal]');
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) e.target.classList.add('visible');
  });
}, { threshold: 0.18, rootMargin: '0px 0px -40px 0px' });
revealCards.forEach(c => revealObserver.observe(c));
// ── 3D Scene ────────────────────────────────────────────
const canvas = document.getElementById('bg-canvas');
const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.3;
const scene = new THREE.Scene();
scene.fog = new THREE.FogExp2(0x0a0015, 0.00006);
const camera = new THREE.PerspectiveCamera(60, innerWidth/innerHeight, 0.1, 100);
camera.position.set(0, 1.2, 8);
camera.lookAt(0, 0, 0);
// ── Lights ──────────────────────────────────────────────
const ambient = new THREE.AmbientLight(0x331122, 2.2);
scene.add(ambient);
const keyLight = new THREE.PointLight(0xff6699, 80, 20, 1.5);
keyLight.position.set(0, 3, 4);
keyLight.castShadow = true;
keyLight.shadow.mapSize.set(512, 512);
scene.add(keyLight);
const fillLight = new THREE.PointLight(0xff3366, 40, 15, 2);
fillLight.position.set(-4, -1, 3);
scene.add(fillLight);
const rimLight = new THREE.PointLight(0xff99cc, 50, 18, 2);
rimLight.position.set(4, 1, -2);
scene.add(rimLight);
const topLight = new THREE.PointLight(0xffaacc, 30, 12, 2);
topLight.position.set(0, 5, 0);
scene.add(topLight);
// Extra color-shifting lights
const auroraLight1 = new THREE.PointLight(0xff66aa, 45, 14, 2);
auroraLight1.position.set(-5, 2, -1);
scene.add(auroraLight1);
const auroraLight2 = new THREE.PointLight(0xaa66ff, 35, 14, 2);
auroraLight2.position.set(5, 2, -1);
scene.add(auroraLight2);
// ── Ground ──────────────────────────────────────────────
const groundGeo = new THREE.PlaneGeometry(50, 50);
const groundMat = new THREE.MeshStandardMaterial({ color: 0x1a0028, roughness: 0.7, metalness: 0.3 });
const ground = new THREE.Mesh(groundGeo, groundMat);
ground.rotation.x = -Math.PI/2;
ground.position.y = -5.5;
ground.receiveShadow = true;
scene.add(ground);
// ── Heart geometry ──────────────────────────────────────
function heartShape(scale = 1) {
  const shape = new THREE.Shape();
  const s = scale * 0.18;
  shape.moveTo(0, -1.5 * s);
  shape.bezierCurveTo(-3*s, -3*s,  -4*s, 0.5*s,  0, 2.8*s);
  shape.bezierCurveTo(4*s, 0.5*s,  3*s, -3*s,  0, -1.5*s);
  return shape;
}
function createHeart3D(scale = 1, color = 0xff3366, matType = 'standard') {
  const shape = heartShape(scale);
  const extrudeSettings = {
    depth: scale*0.35, bevelEnabled: true,
    bevelThickness: scale*0.15, bevelSize: scale*0.08, bevelSegments: 3
  };
  const geo = new THREE.ExtrudeGeometry(shape, extrudeSettings);
  geo.center();
  let mat;
  if (matType === 'emissive') {
    mat = new THREE.MeshStandardMaterial({ color, roughness:0.25, metalness:0.1, emissive:color, emissiveIntensity:0.5 });
  } else if (matType === 'glass') {
    mat = new THREE.MeshPhysicalMaterial({ color, roughness:0.1, metalness:0.05, clearcoat:0.4, clearcoatRoughness:0.2, emissive:color, emissiveIntensity:0.3, transparent:true, opacity:0.85 });
  } else if (matType === 'gold') {
    mat = new THREE.MeshStandardMaterial({ color, roughness:0.2, metalness:0.9, emissive:color, emissiveIntensity:0.4 });
  } else {
    mat = new THREE.MeshStandardMaterial({ color, roughness:0.3, metalness:0.15, emissive:color, emissiveIntensity:0.2 });
  }
  const mesh = new THREE.Mesh(geo, mat);
  mesh.castShadow = true;
  mesh.receiveShadow = true;
  return mesh;
}
// ── Central Big Heart ───────────────────────────────────
const bigHeart = createHeart3D(2.8, 0xff2255, 'glass');
bigHeart.position.set(0, 0.2, 0);
bigHeart.rotation.x = Math.PI;
scene.add(bigHeart);
// ── Floating hearts ─────────────────────────────────────
const floatingHearts = [];
const heartColors = [0xff3366,0xff6699,0xff4477,0xff5588,0xff2266,0xff7799,0xff4466,0xff3355];
for (let i = 0; i < 60; i++) {
  const s = 0.25 + Math.random() * 1.3;
  const color = heartColors[Math.floor(Math.random()*heartColors.length)];
  const type = Math.random() < 0.25 ? 'emissive' : (Math.random() < 0.2 ? 'gold' : 'standard');
  const h = createHeart3D(s, color, type);
  const theta = Math.random()*Math.PI*2;
  const phi = Math.random()*Math.PI*0.7;
  const r = 2.5 + Math.random()*9;
  h.position.set(
    Math.cos(theta)*Math.cos(phi)*r,
    (Math.random()-0.5)*7,
    Math.sin(theta)*Math.cos(phi)*r - 2
  );
  h.rotation.set(Math.random()*Math.PI, Math.random()*Math.PI, Math.random()*Math.PI);
  h.userData = {
    speed: 0.15 + Math.random()*0.9,
    rotSpeed: { x:(Math.random()-0.5)*0.02, y:(Math.random()-0.5)*0.02, z:(Math.random()-0.5)*0.02 },
    floatAmp: 0.3 + Math.random()*1.8,
    floatOff: Math.random()*Math.PI*2,
    baseY: h.position.y,
    colorPhase: Math.random()*Math.PI*2
  };
  scene.add(h);
  floatingHearts.push(h);
}
// ── Sparkle particles ───────────────────────────────────
const particlesGeo = new THREE.BufferGeometry();
const particlesCount = 2500;
const pPositions = new Float32Array(particlesCount*3);
const pColors = new Float32Array(particlesCount*3);
for (let i = 0; i < particlesCount; i++) {
  const theta = Math.random()*Math.PI*2;
  const phi = Math.random()*Math.PI;
  const r = 3 + Math.random()*18;
  pPositions[i*3]   = Math.cos(theta)*Math.sin(phi)*r;
  pPositions[i*3+1] = Math.cos(phi)*r*0.6;
  pPositions[i*3+2] = Math.sin(theta)*Math.sin(phi)*r;
  const pick = Math.random();
  if (pick<0.5)      { pColors[i*3]=1; pColors[i*3+1]=0.2+Math.random()*0.3; pColors[i*3+2]=0.4+Math.random()*0.3; }
  else if (pick<0.8) { pColors[i*3]=1; pColors[i*3+1]=0.7+Math.random()*0.3; pColors[i*3+2]=0.5+Math.random()*0.3; }
  else               { pColors[i*3]=0.9+Math.random()*0.1; pColors[i*3+1]=0.9+Math.random()*0.1; pColors[i*3+2]=0.9+Math.random()*0.1; }
}
particlesGeo.setAttribute('position', new THREE.BufferAttribute(pPositions, 3));
particlesGeo.setAttribute('color', new THREE.BufferAttribute(pColors, 3));
const sparkCanvas = document.createElement('canvas');
sparkCanvas.width=64; sparkCanvas.height=64;
const sctx = sparkCanvas.getContext('2d');
const grad = sctx.createRadialGradient(32,32,0,32,32,32);
grad.addColorStop(0,'rgba(255,255,255,1)');
grad.addColorStop(0.08,'rgba(255,200,220,0.9)');
grad.addColorStop(0.25,'rgba(255,100,150,0.5)');
grad.addColorStop(0.6,'rgba(255,50,100,0.1)');
grad.addColorStop(1,'rgba(0,0,0,0)');
sctx.fillStyle=grad; sctx.fillRect(0,0,64,64);
const sparkTex = new THREE.CanvasTexture(sparkCanvas);
const particlesMat = new THREE.PointsMaterial({
  size:0.08, map:sparkTex, blending:THREE.AdditiveBlending,
  depthWrite:false, vertexColors:true, transparent:true, opacity:0.85
});
const particles = new THREE.Points(particlesGeo, particlesMat);
scene.add(particles);
// ── Roses ──────────────────────────────────────────────
function createRose(x, y, z, color = 0xff3366) {
  const group = new THREE.Group();
  const stemGeo = new THREE.CylinderGeometry(0.03,0.04,1.5,8);
  const stem = new THREE.Mesh(stemGeo, new THREE.MeshStandardMaterial({ color:0x2d5a1e, roughness:0.6 }));
  stem.position.y=-0.75; group.add(stem);
  for (let layer=0; layer<4; layer++) {
    const n = 5+layer*2;
    for (let i=0; i<n; i++) {
      const angle = (i/n)*Math.PI*2 + layer*0.3;
      const pg = new THREE.SphereGeometry(0.12+layer*0.02, 8, 6, 0, Math.PI*1.2, 0, Math.PI*0.6);
      pg.scale(0.5,1,0.5);
      const pm = new THREE.MeshStandardMaterial({
        color: new THREE.Color(color).offsetHSL(layer*0.03,0,0.05),
        roughness:0.4, emissive:color, emissiveIntensity:0.15
      });
      const petal = new THREE.Mesh(pg, pm);
      petal.position.set(Math.cos(angle)*(0.15+layer*0.06), layer*0.08, Math.sin(angle)*(0.15+layer*0.06));
      petal.rotation.set(Math.PI*0.4, angle, Math.cos(angle)*0.3);
      group.add(petal);
    }
  }
  group.position.set(x,y,z);
  group.scale.setScalar(0.7+Math.random()*0.6);
  return group;
}
const roses = [];
for (let i=0; i<8; i++) {
  const rose = createRose((Math.random()-0.5)*9, -2.2-Math.random()*1.8, (Math.random()-0.5)*7-2);
  rose.userData = { spinSpeed:(Math.random()-0.5)*0.005, floatAmp:0.15+Math.random()*0.45, floatOff:Math.random()*Math.PI*2, baseY:rose.position.y };
  scene.add(rose);
  roses.push(rose);
}
// ── Rings ──────────────────────────────────────────────
function createRing(radius, color, y) {
  const ringGeo = new THREE.TorusGeometry(radius, 0.025, 16, 120);
  const ringMat = new THREE.MeshStandardMaterial({ color, roughness:0.2, metalness:0.8, emissive:color, emissiveIntensity:0.6 });
  const ring = new THREE.Mesh(ringGeo, ringMat);
  ring.rotation.x = Math.PI/2;
  ring.position.y = y;
  return ring;
}
const ring1 = createRing(3.0, 0xff6699, 0);
const ring2 = createRing(3.5, 0xff3366, 0);
ring2.rotation.z = Math.PI/4;
scene.add(ring1); scene.add(ring2);
// ── Shooting stars ──────────────────────────────────────
const shootingStars = [];
function spawnShootingStar() {
  const geo = new THREE.SphereGeometry(0.06, 6, 6);
  const mat = new THREE.MeshBasicMaterial({ color:0xffffff });
  const head = new THREE.Mesh(geo, mat);
  // Trail
  const trailGeo = new THREE.CylinderGeometry(0.02, 0.06, 1.5, 6);
  const trailMat = new THREE.MeshBasicMaterial({ color:0xffddcc, transparent:true, opacity:0.7 });
  const trail = new THREE.Mesh(trailGeo, trailMat);
  trail.rotation.z = Math.PI/2;
  trail.position.x = -0.7;
  const group = new THREE.Group();
  group.add(head); group.add(trail);
  const startX = (Math.random()-0.5)*20;
  const startY = 5 + Math.random()*8;
  const startZ = -3 - Math.random()*6;
  group.position.set(startX, startY, startZ);
  const angle = -0.3 - Math.random()*0.5;
  group.rotation.z = angle;
  group.userData = {
    velocity: 0.08 + Math.random()*0.18,
    life: 1.0,
    decay: 0.003 + Math.random()*0.006
  };
  scene.add(group);
  shootingStars.push(group);
}
// ── Aurora ribbons ──────────────────────────────────────
const ribbons = [];
for (let r=0; r<3; r++) {
  const ribGeo = new THREE.PlaneGeometry(18, 0.6, 40, 1);
  const ribMat = new THREE.ShaderMaterial({
    uniforms: {
      uTime: { value: 0 },
      uColor1: { value: new THREE.Color().setHSL(0.93+r*0.04, 0.9, 0.55) },
      uColor2: { value: new THREE.Color().setHSL(0.88+r*0.04, 0.8, 0.5) },
      uOffset: { value: r * 1.7 }
    },
    vertexShader: `
      varying vec2 vUv; varying vec3 vPos;
      void main() {
        vUv = uv; vPos = position;
        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
      }`,
    fragmentShader: `
      varying vec2 vUv; varying vec3 vPos;
      uniform float uTime; uniform vec3 uColor1, uColor2; uniform float uOffset;
      void main() {
        float wave = sin(vPos.x*0.8 + uTime*0.7 + uOffset) * 0.5 + 0.5;
        wave += sin(vPos.x*1.5 - uTime*0.5 + uOffset*1.3) * 0.3;
        wave += sin(vPos.x*0.4 + uTime*1.1 + uOffset*0.6) * 0.2;
        float alpha = wave * 0.18 * (1.0 - abs(vUv.y-0.5)*2.0);
        vec3 col = mix(uColor1, uColor2, wave);
        gl_FragColor = vec4(col, alpha);
      }`,
    transparent: true, depthWrite: false, blending: THREE.AdditiveBlending
  });
  const ribbon = new THREE.Mesh(ribGeo, ribMat);
  ribbon.position.set(0, 5 + r*1.5, -5);
  ribbon.rotation.x = -0.3;
  scene.add(ribbon);
  ribbons.push(ribbon);
}
// ── Rising orbs (lanterns) ──────────────────────────────
const orbs = [];
function spawnOrb() {
  const geo = new THREE.SphereGeometry(0.08, 8, 8);
  const mat = new THREE.MeshBasicMaterial({ color: new THREE.Color().setHSL(0.93+Math.random()*0.07, 0.9, 0.5+Math.random()*0.4), transparent:true, opacity:0.8 });
  const orb = new THREE.Mesh(geo, mat);
  // Glow
  const glowGeo = new THREE.SphereGeometry(0.2, 8, 8);
  const glowMat = new THREE.MeshBasicMaterial({ color: mat.color, transparent:true, opacity:0.25, depthWrite:false });
  const glow = new THREE.Mesh(glowGeo, glowMat);
  orb.add(glow);
  orb.position.set((Math.random()-0.5)*10, -5-Math.random()*3, (Math.random()-0.5)*8-1);
  orb.userData = { speed:0.008+Math.random()*0.025, wobbleAmp:0.3+Math.random()*0.7, wobbleOff:Math.random()*Math.PI*2 };
  scene.add(orb);
  orbs.push(orb);
  return orb;
}
for (let i=0; i<25; i++) {
  const o = spawnOrb();
  o.position.y = (Math.random()-0.5)*12;
}
// ── Interaction ─────────────────────────────────────────
const mouse = new THREE.Vector2();
const targetCam = new THREE.Vector3(0, 1.2, 8);
const currentCam = new THREE.Vector3(0, 1.2, 8);
let scrollFrac = 0;
window.addEventListener('mousemove', (e) => {
  mouse.x = (e.clientX/innerWidth)*2-1;
  mouse.y = -(e.clientY/innerHeight)*2+1;
  targetCam.x = mouse.x*1.6;
  targetCam.y = 1.2 + mouse.y*0.9;
  targetCam.z = 8 - Math.abs(mouse.x)*1.6;
});
window.addEventListener('touchmove', (e) => {
  const t = e.touches[0];
  mouse.x = (t.clientX/innerWidth)*2-1;
  mouse.y = -(t.clientY/innerHeight)*2+1;
  targetCam.x = mouse.x*1.6;
  targetCam.y = 1.2 + mouse.y*0.9;
  targetCam.z = 8 - Math.abs(mouse.x)*1.6;
}, { passive:true });
// Scroll tracking
const scrollContainer = document.getElementById('scroll-container');
const maxScroll = () => scrollContainer.scrollHeight - innerHeight;
window.addEventListener('scroll', () => {
  const s = window.scrollY;
  const max = maxScroll();
  scrollFrac = max > 0 ? Math.min(1, Math.max(0, s/max)) : 0;
  // Camera pulls back as user scrolls
  targetCam.z = 8 + scrollFrac * 5;
  targetCam.y = 1.2 - scrollFrac * 0.6;
});
// Click hearts
window.addEventListener('click', (e) => {
  const mx = (e.clientX/innerWidth)*2-1;
  const my = -(e.clientY/innerHeight)*2+1;
  for (let i=0; i<10; i++) {
    const h = createHeart3D(0.25+Math.random()*0.5, heartColors[Math.floor(Math.random()*heartColors.length)], 'emissive');
    h.position.set(mx*5, my*3.5, 0);
    h.userData = {
      speed:1.5+Math.random()*2,
      rotSpeed:{ x:(Math.random()-0.5)*0.08, y:(Math.random()-0.5)*0.08, z:(Math.random()-0.5)*0.08 },
      floatAmp:0.5+Math.random()*1, floatOff:Math.random()*Math.PI*2, baseY:h.position.y,
      burst:true,
      burstVel: new THREE.Vector3((Math.random()-0.5)*0.18, 0.05+Math.random()*0.09, (Math.random()-0.5)*0.18),
      life:1.0, colorPhase:Math.random()*Math.PI*2
    };
    scene.add(h);
    floatingHearts.push(h);
  }
});
// Resize
window.addEventListener('resize', () => {
  camera.aspect = innerWidth/innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
// ── Animation ──────────────────────────────────────────
const clock = new THREE.Clock();
let lastStarSpawn = 0;
function animate() {
  requestAnimationFrame(animate);
  const t = clock.getElapsedTime();
  // Smooth camera
  currentCam.lerp(targetCam, 0.035);
  camera.position.copy(currentCam);
  camera.lookAt(0, 0.3, 0);
  // ── Big heart: beat + color shift ──
  const beat = 1 + Math.sin(t*2.5)*0.05 + Math.sin(t*5.0)*0.025;
  bigHeart.scale.setScalar(beat);
  bigHeart.rotation.z = Math.sin(t*1.3)*0.08;
  // Color shifts with scroll and time
  const hueShift = scrollFrac * 0.15 + Math.sin(t*0.4)*0.04;
  const heartColor = new THREE.Color().setHSL(0.95 + hueShift, 0.9, 0.55);
  bigHeart.material.color.copy(heartColor);
  bigHeart.material.emissive.copy(heartColor);
  // Also shift ring colors
  ring1.material.color.copy(new THREE.Color().setHSL(0.93+hueShift, 0.85, 0.55));
  ring2.material.color.copy(new THREE.Color().setHSL(0.95+hueShift, 0.9, 0.5));
  ring1.material.emissive.copy(ring1.material.color);
  ring2.material.emissive.copy(ring2.material.color);
  // ── Rings ──
  const ringPulse = 1 + Math.sin(t*1.8)*0.12;
  ring1.scale.setScalar(ringPulse);
  ring2.scale.setScalar(ringPulse*1.06);
  ring1.material.emissiveIntensity = 0.4 + Math.sin(t*2.2)*0.35;
  ring2.material.emissiveIntensity = 0.4 + Math.cos(t*2.4)*0.35;
  // ── Floating hearts ──
  for (let i=floatingHearts.length-1; i>=0; i--) {
    const h = floatingHearts[i];
    if (h.userData.burst) {
      h.position.x += h.userData.burstVel.x;
      h.position.y += h.userData.burstVel.y;
      h.position.z += h.userData.burstVel.z;
      h.userData.burstVel.y += 0.0012;
      h.userData.life -= 0.0035;
      if (h.material.opacity !== undefined) h.material.opacity = Math.max(0, h.userData.life);
      else h.material.transparent = true; h.material.opacity = Math.max(0, h.userData.life);
      if (h.userData.life <= 0) {
        scene.remove(h);
        floatingHearts.splice(i,1);
      }
    } else {
      h.position.y = h.userData.baseY + Math.sin(t*h.userData.speed + h.userData.floatOff)*h.userData.floatAmp;
      h.rotation.x += h.userData.rotSpeed.x;
      h.rotation.y += h.userData.rotSpeed.y;
      h.rotation.z += h.userData.rotSpeed.z;
      // Subtle color cycling
      if (h.userData.colorPhase && h.material.color) {
        const ch = new THREE.Color().setHSL(0.93+Math.sin(t*0.6+h.userData.colorPhase)*0.06, 0.85, 0.5+Math.sin(t*0.8+h.userData.colorPhase)*0.15);
        h.material.color.lerp(ch, 0.02);
        if (h.material.emissive) h.material.emissive.lerp(ch, 0.02);
      }
    }
  }
  // ── Roses ──
  for (const r of roses) {
    r.position.y = r.userData.baseY + Math.sin(t*0.8+r.userData.floatOff)*r.userData.floatAmp;
    r.rotation.y += r.userData.spinSpeed;
  }
  // ── Particles ──
  particles.rotation.y += 0.0004 + scrollFrac*0.0006;
  particles.rotation.x += 0.0002;
  // ── Shooting stars ──
  if (t - lastStarSpawn > (1.5 + Math.random()*3.5)) {
    spawnShootingStar();
    lastStarSpawn = t;
  }
  for (let i=shootingStars.length-1; i>=0; i--) {
    const s = shootingStars[i];
    s.position.x += Math.cos(s.rotation.z)*s.userData.velocity;
    s.position.y += Math.sin(s.rotation.z)*s.userData.velocity;
    s.userData.life -= s.userData.decay;
    s.children.forEach(c => { if (c.material&&c.material.opacity!==undefined) c.material.opacity = s.userData.life; });
    if (s.userData.life <= 0) { scene.remove(s); shootingStars.splice(i,1); }
  }
  // ── Aurora ribbons ──
  for (const rib of ribbons) {
    rib.material.uniforms.uTime.value = t;
    rib.position.y = 4.8 + scrollFrac*1.2;
  }
  // ── Rising orbs ──
  for (const o of orbs) {
    o.position.y += o.userData.speed;
    o.position.x += Math.sin(t*1.5+o.userData.wobbleOff)*o.userData.wobbleAmp*0.01;
    if (o.position.y > 7) o.position.y = -5 - Math.random()*2;
  }
  // ── Lights dance ──
  keyLight.intensity = 60 + Math.sin(t*1.5)*18 + scrollFrac*20;
  fillLight.intensity = 30 + Math.cos(t*1.7)*12;
  auroraLight1.intensity = 35 + Math.sin(t*0.7)*20 + scrollFrac*25;
  auroraLight2.intensity = 35 + Math.cos(t*0.8)*20 + scrollFrac*25;
  auroraLight1.color.setHSL(0.93+Math.sin(t*0.3)*0.06, 0.8, 0.55);
  auroraLight2.color.setHSL(0.78+Math.cos(t*0.35)*0.06, 0.8, 0.55);
  // ── Fog adapts to scroll ──
  scene.fog.density = 0.00006 - scrollFrac*0.00002;
  renderer.render(scene, camera);
}
animate();
console.log('💖 我爱你，宝贝！每一天都因为你而闪耀。');
console.log('💕 You are my everything.');
</script>
</body>
</html>
