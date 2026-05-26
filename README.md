[love.html](https://github.com/user-attachments/files/28267067/love.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, viewport-fit=cover">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <title>小宝宝</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html, body {
      width: 100%;
      height: 100%;
      height: 100dvh;
      overflow: hidden;
      font-family: "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif;
      background: linear-gradient(160deg, #fff5f7 0%, #ffe4ec 45%, #ffd6e8 100%);
      cursor: default;
      user-select: none;
      -webkit-user-select: none;
      -webkit-touch-callout: none;
      touch-action: manipulation;
      overscroll-behavior: none;
    }

    body.started {
      touch-action: none;
    }

    .intro {
      position: fixed;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 10;
      transition: opacity 0.6s ease, transform 0.6s ease;
      cursor: pointer;
      -webkit-tap-highlight-color: transparent;
      padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
    }

    .intro.hidden {
      opacity: 0;
      pointer-events: none;
      transform: scale(1.15);
    }

    .title {
      display: block;
      border: none;
      background: none;
      font: inherit;
      font-size: clamp(3rem, 12vw, 5.5rem);
      font-weight: 600;
      letter-spacing: 0.35em;
      padding: 0.5em 0.8em;
      padding-left: calc(0.8em + 0.35em);
      color: #e85d8a;
      text-shadow: 0 4px 24px rgba(232, 93, 138, 0.35);
      cursor: pointer;
      -webkit-tap-highlight-color: transparent;
      touch-action: manipulation;
      animation: breathe 2.4s ease-in-out infinite;
    }

    @media (hover: hover) {
      .title:hover {
        transform: scale(1.06);
      }
    }

    .title:active {
      transform: scale(0.98);
    }

    .hint {
      position: absolute;
      bottom: 18%;
      font-size: 0.95rem;
      color: rgba(232, 93, 138, 0.55);
      letter-spacing: 0.2em;
      animation: fadeHint 2s ease-in-out infinite;
    }

    @keyframes breathe {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.04); }
    }

    @supports (-webkit-touch-callout: none) {
      .float-heart,
      .burst-heart {
        filter: none;
      }
    }

    @keyframes fadeHint {
      0%, 100% { opacity: 0.35; }
      50% { opacity: 0.85; }
    }

    #canvas {
      position: fixed;
      inset: 0;
      z-index: 1;
      pointer-events: none;
    }

    .heart-layer {
      position: fixed;
      inset: 0;
      z-index: 2;
      pointer-events: none;
      overflow: hidden;
    }

    .float-heart {
      position: absolute;
      bottom: -60px;
      animation: rise linear forwards;
      filter: drop-shadow(0 2px 6px rgba(232, 93, 138, 0.25));
    }

    @keyframes rise {
      0% {
        transform: translateY(0) rotate(0deg) scale(1);
        opacity: 0;
      }
      8% { opacity: 1; }
      90% { opacity: 0.85; }
      100% {
        transform: translateY(-110vh) rotate(var(--spin)) scale(var(--end-scale));
        opacity: 0;
      }
    }

    .burst-heart {
      position: absolute;
      animation: burst ease-out forwards;
      filter: drop-shadow(0 0 8px rgba(255, 105, 150, 0.5));
    }

    @keyframes burst {
      0% {
        transform: translate(0, 0) scale(0) rotate(0deg);
        opacity: 1;
      }
      100% {
        transform: translate(var(--tx), var(--ty)) scale(1) rotate(var(--rot));
        opacity: 0;
      }
    }

    .glow {
      position: fixed;
      inset: 0;
      z-index: 0;
      opacity: 0;
      background: radial-gradient(circle at 50% 50%, rgba(255, 182, 210, 0.5), transparent 70%);
      transition: opacity 1s ease;
      pointer-events: none;
    }

    .glow.active {
      opacity: 1;
    }

    .love-screen {
      position: fixed;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 5;
      pointer-events: none;
      opacity: 0;
      transform: scale(0.9);
      transition: opacity 0.8s ease 0.3s, transform 0.8s ease 0.3s;
    }

    .love-screen.visible {
      opacity: 1;
      transform: scale(1);
    }

    .love-text {
      font-size: clamp(2.5rem, 10vw, 4.5rem);
      font-weight: 600;
      letter-spacing: 0.4em;
      padding-left: 0.4em;
      color: #fff;
      text-shadow:
        0 0 20px rgba(255, 105, 150, 0.8),
        0 4px 32px rgba(232, 93, 138, 0.6);
      animation: lovePulse 2.2s ease-in-out infinite;
    }

    @keyframes lovePulse {
      0%, 100% {
        transform: scale(1);
        text-shadow:
          0 0 20px rgba(255, 105, 150, 0.8),
          0 4px 32px rgba(232, 93, 138, 0.6);
      }
      50% {
        transform: scale(1.05);
        text-shadow:
          0 0 36px rgba(255, 150, 180, 1),
          0 4px 40px rgba(232, 93, 138, 0.85);
      }
    }
  </style>
</head>
<body>
  <div class="glow" id="glow"></div>
  <canvas id="canvas"></canvas>
  <div class="heart-layer" id="heartLayer"></div>

  <div class="intro" id="intro">
    <button type="button" class="title" id="title">小宝宝</button>
    <p class="hint">点一下试试 ♥</p>
  </div>

  <div class="love-screen" id="loveScreen">
    <span class="love-text">我爱你</span>
  </div>

  <script>
    const intro = document.getElementById("intro");
    const loveScreen = document.getElementById("loveScreen");
    const title = document.getElementById("title");
    const heartLayer = document.getElementById("heartLayer");
    const glow = document.getElementById("glow");
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    let started = false;
    let animId = null;
    let lastInteract = 0;
    const particles = [];
    const isTouchDevice = "ontouchstart" in window || navigator.maxTouchPoints > 0;
    const floatInterval = isTouchDevice ? 320 : 180;
    const maxHearts = isTouchDevice ? 80 : 150;

    const colors = ["#ff6b9d", "#ff8fab", "#ffb3c6", "#e85d8a", "#ff4d8d", "#ffc2d4", "#f72585"];

    function getCoords(e) {
      if (e.changedTouches && e.changedTouches.length) {
        const t = e.changedTouches[0];
        return { x: t.clientX, y: t.clientY };
      }
      if (e.touches && e.touches.length) {
        const t = e.touches[0];
        return { x: t.clientX, y: t.clientY };
      }
      return { x: e.clientX, y: e.clientY };
    }

    function interact(x, y) {
      const now = Date.now();
      if (now - lastInteract < 60) return;
      lastInteract = now;
      startLove(x, y);
    }

    function heartPath(ctx, size) {
      const s = size / 2;
      ctx.beginPath();
      ctx.moveTo(0, s * 0.3);
      ctx.bezierCurveTo(0, -s * 0.5, -s, -s * 0.5, -s, s * 0.1);
      ctx.bezierCurveTo(-s, s * 0.7, 0, s, 0, s * 1.2);
      ctx.bezierCurveTo(0, s, s, s * 0.7, s, s * 0.1);
      ctx.bezierCurveTo(s, -s * 0.5, 0, -s * 0.5, 0, s * 0.3);
      ctx.closePath();
    }

    function resize() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      const w = window.innerWidth;
      const h = window.innerHeight;
      canvas.width = w * dpr;
      canvas.height = h * dpr;
      canvas.style.width = w + "px";
      canvas.style.height = h + "px";
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    }
    window.addEventListener("resize", resize);
    resize();

    function randomColor() {
      return colors[Math.floor(Math.random() * colors.length)];
    }

    function createFloatHeart() {
      if (heartLayer.children.length >= maxHearts) return;

      const el = document.createElement("div");
      el.className = "float-heart";
      const size = 14 + Math.random() * 36;
      const left = Math.random() * 100;
      const duration = 4 + Math.random() * 5;
      const spin = (Math.random() - 0.5) * 720 + "deg";
      const endScale = 0.6 + Math.random() * 0.8;

      el.innerHTML = `<svg width="${size}" height="${size}" viewBox="-12 -10 24 22"><path fill="${randomColor()}" d="M0 4 C0 -6 -10 -6 -10 2 C-10 8 0 12 0 14 C0 12 10 8 10 2 C10 -6 0 -6 0 4Z"/></svg>`;
      el.style.left = left + "%";
      el.style.setProperty("--spin", spin);
      el.style.setProperty("--end-scale", endScale);
      el.style.animationDuration = duration + "s";
      el.style.animationDelay = Math.random() * 0.5 + "s";

      heartLayer.appendChild(el);
      setTimeout(() => el.remove(), (duration + 1) * 1000);
    }

    function burst(x, y, count = 24) {
      for (let i = 0; i < count; i++) {
        const el = document.createElement("div");
        el.className = "burst-heart";
        const size = 10 + Math.random() * 22;
        const angle = (Math.PI * 2 * i) / count + Math.random() * 0.4;
        const dist = 80 + Math.random() * 160;
        const tx = Math.cos(angle) * dist + "px";
        const ty = Math.sin(angle) * dist + "px";
        const rot = (Math.random() - 0.5) * 540 + "deg";
        const duration = 0.8 + Math.random() * 0.6;

        el.innerHTML = `<svg width="${size}" height="${size}" viewBox="-12 -10 24 22"><path fill="${randomColor()}" d="M0 4 C0 -6 -10 -6 -10 2 C-10 8 0 12 0 14 C0 12 10 8 10 2 C10 -6 0 -6 0 4Z"/></svg>`;
        el.style.left = x + "px";
        el.style.top = y + "px";
        el.style.setProperty("--tx", tx);
        el.style.setProperty("--ty", ty);
        el.style.setProperty("--rot", rot);
        el.style.animationDuration = duration + "s";

        heartLayer.appendChild(el);
        setTimeout(() => el.remove(), duration * 1000 + 100);
      }
    }

    function spawnParticle(x, y) {
      const count = 8 + Math.floor(Math.random() * 10);
      for (let i = 0; i < count; i++) {
        const angle = Math.random() * Math.PI * 2;
        const speed = 2 + Math.random() * 6;
        particles.push({
          x,
          y,
          vx: Math.cos(angle) * speed,
          vy: Math.sin(angle) * speed - 2,
          size: 6 + Math.random() * 14,
          color: randomColor(),
          life: 1,
          decay: 0.012 + Math.random() * 0.02,
          rotation: Math.random() * Math.PI * 2,
          spin: (Math.random() - 0.5) * 0.15
        });
      }
    }

    function tick() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      for (let i = particles.length - 1; i >= 0; i--) {
        const p = particles[i];
        p.x += p.vx;
        p.y += p.vy;
        p.vy += 0.08;
        p.vx *= 0.99;
        p.life -= p.decay;
        p.rotation += p.spin;

        if (p.life <= 0) {
          particles.splice(i, 1);
          continue;
        }

        ctx.save();
        ctx.translate(p.x, p.y);
        ctx.rotate(p.rotation);
        ctx.globalAlpha = p.life;
        ctx.fillStyle = p.color;
        heartPath(ctx, p.size);
        ctx.fill();
        ctx.restore();
      }

      animId = requestAnimationFrame(tick);
    }

    function startLove(x, y) {
      if (!started) {
        started = true;
        document.body.classList.add("started");
        intro.classList.add("hidden");
        loveScreen.classList.add("visible");
        glow.classList.add("active");
        tick();

        const interval = setInterval(createFloatHeart, floatInterval);
        window._heartInterval = interval;
      }

      burst(x, y, 28);
      spawnParticle(x, y);
    }

    function onFirstTap(e) {
      if (started) return;
      e.preventDefault();
      e.stopPropagation();
      const { x, y } = getCoords(e);
      interact(x, y);
    }

    function onScreenTap(e) {
      if (!started) return;
      e.preventDefault();
      const { x, y } = getCoords(e);
      interact(x, y);
    }

    if (isTouchDevice) {
      intro.addEventListener("touchend", onFirstTap, { passive: false });
      document.addEventListener("touchend", onScreenTap, { passive: false });
    } else {
      intro.addEventListener("click", onFirstTap);
      document.addEventListener("click", onScreenTap);
    }
  </script>
</body>
</html>
