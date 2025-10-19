<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Reptile Interactive Cursor</title>
<style>
  body {
    margin: 0;
    background: #000;
    overflow: hidden;
    cursor: none;
  }
  canvas {
    display: block;
  }
</style>
</head>
<body>
<canvas id="canvas"></canvas>

<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
canvas.width = innerWidth;
canvas.height = innerHeight;

const mouse = { x: canvas.width / 2, y: canvas.height / 2 };
document.addEventListener("mousemove", e => {
  mouse.x = e.clientX;
  mouse.y = e.clientY;
});

// --- CONFIGURATION ---
const SEGMENTS = 40;
const SEG_LENGTH = 14;
const BODY_COLOR = "#33ff33";
const LEG_COLOR = "#00cc66";
const LEG_LENGTH = 10;

// --- SEGMENT OBJECT ---
class Segment {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.angle = 0;
  }
}

const body = [];
for (let i = 0; i < SEGMENTS; i++) {
  body.push(new Segment(canvas.width / 2, canvas.height / 2));
}

// --- FOLLOW ALGORITHM ---
function follow(i, targetX, targetY) {
  const dx = targetX - body[i].x;
  const dy = targetY - body[i].y;
  body[i].angle = Math.atan2(dy, dx);
  body[i].x = targetX - Math.cos(body[i].angle) * SEG_LENGTH;
  body[i].y = targetY - Math.sin(body[i].angle) * SEG_LENGTH;
}

// --- DRAW BODY ---
function drawSegment(x, y, angle, i) {
  ctx.save();
  ctx.translate(x, y);
  ctx.rotate(angle);

  // Draw main body line
  ctx.beginPath();
  ctx.moveTo(0, 0);
  ctx.lineTo(SEG_LENGTH, 0);
  ctx.strokeStyle = BODY_COLOR;
  ctx.lineWidth = 2;
  ctx.stroke();

  // Draw legs (zig-zag style)
  if (i % 3 === 0) {
    ctx.beginPath();
    ctx.moveTo(0, 0);
    ctx.lineTo(0, LEG_LENGTH);
    ctx.moveTo(0, 0);
    ctx.lineTo(0, -LEG_LENGTH);
    ctx.strokeStyle = LEG_COLOR;
    ctx.lineWidth = 1.5;
    ctx.stroke();
  }

  ctx.restore();
}

// --- ANIMATION LOOP ---
function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Head follows mouse
  follow(0, mouse.x, mouse.y);
  drawSegment(body[0].x, body[0].y, body[0].angle, 0);

  // Body follows previous
  for (let i = 1; i < SEGMENTS; i++) {
    follow(i, body[i - 1].x, body[i - 1].y);
    drawSegment(body[i].x, body[i].y, body[i].angle, i);
  }

  requestAnimationFrame(animate);
}

animate();

// --- HANDLE RESIZE ---
window.addEventListener("resize", () => {
  canvas.width = innerWidth;
  canvas.height = innerHeight;
});
</script>
</body>
</html>
