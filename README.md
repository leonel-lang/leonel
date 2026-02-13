<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Árbol del Amor 💖</title>
  <style>
    body{
      margin:0;
      background:#f3e7de;
      font-family: Arial, sans-serif;
      overflow:hidden;
    }

    canvas{
      display:block;
      margin:0 auto;
    }

    .text-left{
      position:absolute;
      top:80px;
      left:60px;
      color:#333;
      font-size:20px;
      line-height:1.4;
      width:40%;
    }

    .text-left span{
      display:block;
      margin-top:12px;
      font-size:28px;
      font-weight:bold;
      color:#222;
    }

    .counter{
      position:absolute;
      bottom:40px;
      left:60px;
      color:#444;
      font-size:18px;
    }

    .counter strong{
      font-size:22px;
      color:#222;
    }

    .small{
      font-size:14px;
      opacity:0.8;
      margin-bottom:5px;
    }
  </style>
</head>
<body>

  <div class="text-left" id="dedicatoria" style="display:none;">
    Para el amor de mi vida:
    <span id="nombre"></span>
  </div>

  <div class="counter" id="contador" style="display:none;">
    <div class="small">Mi amor por ti comenzó hace...</div>
    <strong id="timeText"></strong>
  </div>

  <canvas id="canvas"></canvas>

<script>
/* ===========================
   CONFIGURACIÓN (EDITA AQUÍ)
=========================== */
const NOMBRE = "S"; // <-- Cambia por el nombre (Ej: "Sofía")
const FECHA_INICIO = "2025-02-23T00:00:00"; // <-- Cambia tu fecha real (Año-Mes-Día)

/* ===========================
   CANVAS SETUP
=========================== */
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

function resize(){
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
}
window.addEventListener("resize", resize);
resize();

/* ===========================
   VARIABLES DEL ÁRBOL
=========================== */
let hearts = [];
let fallingHearts = [];
let stage = 0; // 0: punto, 1: árbol, 2: corazón lleno, 3: texto + lluvia

const centerX = canvas.width * 0.70;
const groundY = canvas.height * 0.78;

function rand(min,max){ return Math.random()*(max-min)+min; }

/* ===========================
   DIBUJAR CORAZÓN (FORMA)
=========================== */
function drawHeart(x, y, size, color, alpha=1){
  ctx.save();
  ctx.globalAlpha = alpha;
  ctx.fillStyle = color;
  ctx.beginPath();

  const s = size;
  ctx.moveTo(x, y);
  ctx.bezierCurveTo(x - s, y - s, x - 2*s, y + s/3, x, y + 2*s);
  ctx.bezierCurveTo(x + 2*s, y + s/3, x + s, y - s, x, y);

  ctx.fill();
  ctx.restore();
}

/* ===========================
   DIBUJAR TRONCO + RAMAS
=========================== */
function drawTree(){
  // tronco
  ctx.fillStyle = "#8b5a2b";
  ctx.beginPath();
  ctx.moveTo(centerX - 25, groundY);
  ctx.lineTo(centerX + 25, groundY);
  ctx.lineTo(centerX + 12, groundY - 260);
  ctx.lineTo(centerX - 12, groundY - 260);
  ctx.closePath();
  ctx.fill();

  // ramas
  ctx.strokeStyle = "#5c3b1f";
  ctx.lineWidth = 5;
  ctx.lineCap = "round";

  function branch(x1,y1,x2,y2){
    ctx.beginPath();
    ctx.moveTo(x1,y1);
    ctx.lineTo(x2,y2);
    ctx.stroke();
  }

  const topY = groundY - 260;

  branch(centerX, topY+40, centerX-90, topY-30);
  branch(centerX, topY+50, centerX+90, topY-40);

  branch(centerX-30, topY+80, centerX-130, topY+20);
  branch(centerX+30, topY+80, centerX+140, topY+10);

  branch(centerX-10, topY+120, centerX-80, topY+90);
  branch(centerX+10, topY+120, centerX+80, topY+90);
}

/* ===========================
   SUELO
=========================== */
function drawGround(){
  ctx.strokeStyle = "#222";
  ctx.lineWidth = 3;
  ctx.beginPath();
  ctx.moveTo(0, groundY);
  ctx.lineTo(canvas.width, groundY);
  ctx.stroke();
}

/* ===========================
   GENERAR CORAZONES DEL ÁRBOL
=========================== */
function generateTreeHearts(){
  hearts = [];

  // corazones alrededor de la copa
  for(let i=0;i<120;i++){
    hearts.push({
      x: centerX + rand(-140, 140),
      y: groundY - 260 + rand(-120, 120),
      size: rand(5, 12),
      color: randomHeartColor(),
      alpha: 0,
      appearSpeed: rand(0.01, 0.03)
    });
  }
}

function randomHeartColor(){
  const colors = ["#ff1f4b","#ff4d6d","#ff758f","#ff0033","#ff8fab"];
  return colors[Math.floor(Math.random()*colors.length)];
}

/* ===========================
   GENERAR CORAZÓN GRANDE
=========================== */
function generateBigHeart(){
  hearts = [];

  // relleno con corazones formando un corazón gigante
  for(let i=0;i<700;i++){
    // fórmula del corazón (normalizada)
    let t = Math.random() * Math.PI * 2;
    let r = Math.random();

    // coordenadas base
    let x = 16*Math.pow(Math.sin(t),3);
    let y = 13*Math.cos(t)-5*Math.cos(2*t)-2*Math.cos(3*t)-Math.cos(4*t);

    // escalado
    x *= 12;
    y *= 12;

    // dispersión
    x *= r;
    y *= r;

    hearts.push({
      x: centerX + x,
      y: groundY - 240 - y,
      size: rand(5, 12),
      color: randomHeartColor(),
      alpha: 0,
      appearSpeed: rand(0.02, 0.05)
    });
  }
}

/* ===========================
   CORAZONES CAYENDO
=========================== */
function spawnFallingHeart(){
  fallingHearts.push({
    x: rand(20, canvas.width*0.55),
    y: groundY - 10,
    size: rand(6, 12),
    speedY: rand(1, 3),
    speedX: rand(-0.3, 0.3),
    alpha: 1,
    color: randomHeartColor()
  });
}

/* ===========================
   ANIMACIÓN PRINCIPAL
=========================== */
let startTime = Date.now();
let dotY = groundY - 260;

function animate(){
  ctx.clearRect(0,0,canvas.width,canvas.height);

  drawGround();

  // stage 0: punto rojo
  if(stage === 0){
    drawHeart(centerX, dotY, 8, "#d1002d", 1);

    if(Date.now() - startTime > 1200){
      stage = 1;
      generateTreeHearts();
    }
  }

  // stage 1: árbol con corazones apareciendo
  if(stage === 1){
    drawTree();

    hearts.forEach(h=>{
      h.alpha += h.appearSpeed;
      if(h.alpha > 1) h.alpha = 1;
      drawHeart(h.x, h.y, h.size, h.color, h.alpha);
    });

    if(Date.now() - startTime > 3200){
      stage = 2;
      generateBigHeart();
    }
  }

  // stage 2: corazón gigante
  if(stage === 2){
    drawTree();

    hearts.forEach(h=>{
      h.alpha += h.appearSpeed;
      if(h.alpha > 1) h.alpha = 1;
      drawHeart(h.x, h.y, h.size, h.color, h.alpha);
    });

    if(Date.now() - startTime > 5200){
      stage = 3;
      document.getElementById("dedicatoria").style.display = "block";
      document.getElementById("contador").style.display = "block";
      document.getElementById("nombre").innerText = NOMBRE;
    }
  }

  // stage 3: todo + corazones cayendo
  if(stage === 3){
    drawTree();

    hearts.forEach(h=>{
      drawHeart(h.x, h.y, h.size, h.color, 1);
    });

    if(Math.random() < 0.35){
      spawnFallingHeart();
    }

    fallingHearts.forEach((h,idx)=>{
      h.x += h.speedX;
      h.y += h.speedY;
      h.alpha -= 0.008;

      drawHeart(h.x, h.y, h.size, h.color, h.alpha);

      if(h.alpha <= 0){
        fallingHearts.splice(idx,1);
      }
    });
  }

  requestAnimationFrame(animate);
}

animate();

/* ===========================
   CONTADOR TIEMPO REAL
=========================== */
const startDate = new Date(FECHA_INICIO).getTime();
const timeText = document.getElementById("timeText");

function updateCounter(){
  const now = Date.now();
  let diff = now - startDate;
  if(diff < 0) diff = 0;

  const seconds = Math.floor(diff/1000);
  const days = Math.floor(seconds/86400);
  const hours = Math.floor((seconds%86400)/3600);
  const minutes = Math.floor((seconds%3600)/60);
  const secs = seconds%60;

  timeText.innerText = `${days} días ${hours} horas ${minutes} minutos ${secs} segundos`;
}

setInterval(updateCounter,1000);
updateCounter();

</script>
</body>
</html>



