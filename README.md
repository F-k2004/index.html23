<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<title>Cyberpunk Liquid Grid</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
  body {
    margin: 0;
    overflow: hidden;
    background: #05010f;
  }
  canvas {
    display: block;
    filter: brightness(1.3) blur(1.5px);
  }
</style>
</head>

<body>
<canvas id="canvas"></canvas>

<scrit>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

let w, h;
function resize() {
  w = canvas.width = window.innerWidth;
  h = canvas.height = window.innerHeight;
}
window.addEventListener("resize", resize);
resize();

// Grid resolution
const COLS = 170;
const ROWS = 100;

let current = [];
let previous = [];

function init() {
  for (let y = 0; y < ROWS; y++) {
    current[y] = [];
    previous[y] = [];
    for (let x = 0; x < COLS; x++) {
      current[y][x] = 0;
      previous[y][x] = 0;
    }
  }
}
init();

let damping = 0.988;
let hueShift = 0;

// سایبرپانکی: موج با موس
canvas.addEventListener("mousemove", (e) => {
  let x = Math.floor((e.clientX / w) * COLS);
  let y = Math.floor((e.clientY / h) * ROWS);

  if (x > 1 && x < COLS - 1 && y > 1 && y < ROWS - 1) {
    current[y][x] = 180;
  }
});

// آپدیت موج
function update() {
  for (let y = 1; y < ROWS - 1; y++) {
    for (let x = 1; x < COLS - 1; x++) {
      current[y][x] =
        ((previous[y - 1][x] +
          previous[y + 1][x] +
          previous[y][x - 1] +
          previous[y][x + 1]) /
          2 -
          current[y][x]) *
        damping;
    }
  }
  let temp = previous;
  previous = current;
  current = temp;

  hueShift += 0.6;
}

// رسم نئون سایبرپانکی
function draw() {
  let cw = w / COLS;
  let ch = h / ROWS;

  ctx.clearRect(0, 0, w, h);

  for (let y = 1; y < ROWS - 1; y++) {
    for (let x = 1; x < COLS - 1; x++) {
      let v = current[y][x];

      // رنگ‌پردازی سایبرپانکی
      let hue = (200 + hueShift + x * 0.8 + y * 0.3 + v * 1.2) % 360;
      let light = 40 + v * 0.25;
      let sat = 90;

      ctx.fillStyle = `hsl(${hue}, ${sat}%, ${light}%)`;
      ctx.fillRect(x * cw, y * ch, cw + 1, ch + 1);
    }
  }

  // گرید نئونی
  ctx.strokeStyle = "rgba(0,255,255,0.07)";
  ctx.lineWidth = 1;

  for (let x = 0; x < w; x += w / 20) {
    ctx.beginPath();
    ctx.moveTo(x, 0);
    ctx.lineTo(x, h);
    ctx.stroke();
  }

  for (let y = 0; y < h; y += h / 15) {
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(w, y);
    ctx.stroke();
  }
}

// حلقه اصلی
function animate() {
  update();
  draw();
  requestAnimationFrame(animate);
}

animate();
</script>
</body>
</html>
