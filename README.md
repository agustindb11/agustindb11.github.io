<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Te Amo Matrix Limpio</title>
  <style>
    html, body {
      margin: 0; padding: 0;
      background: black;
      overflow: hidden;
      height: 100%;
      font-family: monospace;
      user-select: none;
    }
    canvas {
      display: block;
      position: absolute;
      top: 0; left: 0;
      z-index: 0;
    }
  </style>
</head>
<body>
  <canvas id="matrixCanvas"></canvas>

  <script>
    const canvas = document.getElementById('matrixCanvas');
    const ctx = canvas.getContext('2d');
    let width = canvas.width = window.innerWidth;
    let height = canvas.height = window.innerHeight;

    const matrixWord = "Te amo";
    const fontSize = 48;
    const columns = Math.floor(width / fontSize);
    const drops = new Array(columns).fill(0);

    const messages = [];

    class TeAmoMessage {
      constructor(x, y) {
        this.x = x;
        this.y = y;
        this.opacity = 1;
        this.lifetime = 100;
        this.size = 48 + Math.random() * 12;
      }
      update() {
        this.y -= 0.5;
        this.opacity -= 1 / this.lifetime;
      }
      draw() {
        ctx.save();
        ctx.globalAlpha = this.opacity;
        ctx.font = `${this.size}px sans-serif`;
        ctx.textAlign = "center";
        ctx.fillStyle = "red";
        ctx.shadowColor = "red";
        ctx.shadowBlur = 20;
        ctx.fillText("Te amo", this.x, this.y);
        ctx.restore();
      }
      isDead() {
        return this.opacity <= 0;
      }
    }

    function drawMatrix() {
      ctx.fillStyle = "rgba(0, 0, 0, 0.15)";
      ctx.fillRect(0, 0, width, height);

      ctx.fillStyle = "#ff3399";
      ctx.font = `bold ${fontSize}px monospace`;
      ctx.textBaseline = "top";

      for(let i = 0; i < columns; i++) {
        const x = i * fontSize;
        const y = drops[i] * fontSize;

        ctx.shadowColor = "black";
        ctx.shadowBlur = 4;
        ctx.fillText(matrixWord, x, y);

        if(y > height && Math.random() > 0.98) {
          drops[i] = 0;
        } else {
          drops[i] += 0.1;
        }
      }
    }

    function drawMessages() {
      for(let i = messages.length - 1; i >= 0; i--) {
        messages[i].update();
        messages[i].draw();
        if(messages[i].isDead()) messages.splice(i, 1);
      }
    }

    function animate() {
      drawMatrix();
      drawMessages();
      requestAnimationFrame(animate);
    }

    function showLove(x, y) {
      messages.push(new TeAmoMessage(x, y));
    }

    document.addEventListener("click", e => {
      showLove(e.clientX, e.clientY);
    });

    document.addEventListener("touchstart", e => {
      for(const touch of e.touches) {
        showLove(touch.clientX, touch.clientY);
      }
    });

    window.addEventListener("resize", () => {
      width = canvas.width = window.innerWidth;
      height = canvas.height = window.innerHeight;
    });

    animate();
  </script>
</body>
</html>

