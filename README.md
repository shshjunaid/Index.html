<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>3D Spinning Name Wheel</title>
  <style>
    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background: linear-gradient(135deg, #43cea2, #185a9d);
      height: 100vh;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
    }
    h1 {
      color: #fff;
      margin-bottom: 10px;
    }
    .wheel-container {
      position: relative;
      width: 300px;
      height: 300px;
      perspective: 1000px;
    }
    canvas {
      width: 100%;
      height: 100%;
      border-radius: 50%;
      background: #fff;
      box-shadow: 0 0 20px rgba(0,0,0,0.3);
    }
    .controls {
      margin-top: 20px;
      text-align: center;
    }
    input[type="text"] {
      padding: 10px;
      width: 250px;
      border-radius: 25px;
      border: 2px solid #ffffff;
      outline: none;
      font-size: 16px;
      margin-bottom: 10px;
      background: rgba(255,255,255,0.2);
      color: #fff;
    }
    button {
      padding: 10px 20px;
      margin: 5px;
      font-size: 18px;
      border-radius: 25px;
      border: 2px solid #ffffff;
      background: transparent;
      color: #fff;
      cursor: pointer;
      transition: all 0.3s ease;
    }
    button:hover {
      background: #ffffff;
      color: #185a9d;
    }
    button:active {
      transform: scale(0.95);
    }
    #winner {
      margin-top: 20px;
      font-size: 22px;
      color: #fff;
      font-weight: bold;
      background: rgba(0,0,0,0.3);
      padding: 10px 20px;
      border-radius: 25px;
      display: none;
    }
  </style>
</head>
<body>

<h1>3D Spinning Name Wheel</h1>

<div class="wheel-container">
  <canvas id="wheel" width="300" height="300"></canvas>
</div>

<div class="controls">
  <input type="text" id="nameInput" placeholder="Enter a name">
  <br>
  <button onclick="addName()">Add Name</button>
  <button onclick="spin()">Spin</button>
</div>

<div id="winner"></div>

<script>
  const canvas = document.getElementById('wheel');
  const ctx = canvas.getContext('2d');
  const names = ['Start'];
  let angle = 0;
  let spinning = false;
  let spinSpeed = 0;
  let targetAngle = 0;

  function drawWheel() {
    const segments = names.length;
    const arcSize = (2 * Math.PI) / segments;

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    names.forEach((name, i) => {
      const startAngle = i * arcSize;
      const endAngle = startAngle + arcSize;

      ctx.beginPath();
      ctx.fillStyle = `hsl(${i * (360 / segments)}, 70%, 60%)`;
      ctx.moveTo(150, 150);
      ctx.arc(150, 150, 140, startAngle, endAngle);
      ctx.fill();

      ctx.save();
      ctx.translate(150, 150);
      ctx.rotate(startAngle + arcSize/2);
      ctx.textAlign = "right";
      ctx.fillStyle = "#000";
      ctx.font = "bold 14px Poppins";
      ctx.fillText(name, 130, 5);
      ctx.restore();
    });
  }

  function rotateWheel() {
    if (!spinning) return;
    angle += spinSpeed;
    spinSpeed *= 0.98; // Easing effect (slow down)

    if (spinSpeed < 0.002) {
      spinning = false;
      const winnerIndex = Math.floor(((2 * Math.PI - (angle % (2 * Math.PI))) / (2 * Math.PI)) * names.length) % names.length;
      document.getElementById('winner').innerText = `Winner: ${names[winnerIndex]}`;
      document.getElementById('winner').style.display = 'inline-block';
    } else {
      requestAnimationFrame(rotateWheel);
    }

    ctx.save();
    ctx.translate(150, 150);
    ctx.rotate(angle);
    ctx.translate(-150, -150);
    drawWheel();
    ctx.restore();
  }

  function addName() {
    const input = document.getElementById('nameInput');
    const name = input.value.trim();
    if (name) {
      if (names.length === 1 && names[0] === 'Start') names.pop();
      names.push(name);
      input.value = '';
      drawWheel();
    }
  }

  function spin() {
    if (spinning || names.length <= 1) return;
    document.getElementById('winner').style.display = 'none';
    spinSpeed = Math.random() * 0.3 + 0.3; // Random initial speed
    spinning = true;
    rotateWheel();
  }

  drawWheel();
</script>

</body>
</html>
