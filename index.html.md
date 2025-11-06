<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>도형 심리 테스트</title>
  <style>
    body {
      font-family: "Pretendard", sans-serif;
      text-align: center;
      background: #f9f6f2;
    }
    h1 {
      color: #2a2a80;
    }
    #canvas-container {
      position: relative;
      margin: 0 auto;
      width: 400px;
      height: 400px;
      border: 2px solid #c48a5a;
      background: white;
    }
    canvas {
      border: 1px solid #d8bca7;
      cursor: crosshair;
    }
    .btns {
      margin-top: 10px;
    }
    button {
      margin: 3px;
      padding: 6px 10px;
      font-size: 14px;
      border-radius: 5px;
      border: none;
      background-color: #2a2a80;
      color: white;
      cursor: pointer;
    }
    #result {
      margin-top: 20px;
      padding: 10px;
      border-top: 2px solid #ccc;
      font-size: 15px;
      line-height: 1.6;
      color: #333;
      width: 90%;
      margin-left: auto;
      margin-right: auto;
    }
  </style>
</head>
<body>
  <h1>○△□S 도형 심리 테스트</h1>
  <p>도형을 선택해 그림판에 직접 배치하고 크기를 조절해보세요.</p>

  <div class="btns">
    <button onclick="setShape('circle')">○ 동그라미</button>
    <button onclick="setShape('triangle')">△ 세모</button>
    <button onclick="setShape('square')">□ 네모</button>
    <button onclick="setShape('wave')">S 물결</button>
    <button onclick="undo()">⟲ 되돌리기</button>
    <button onclick="analyze()">결과 보기</button>
  </div>

  <div id="canvas-container">
    <canvas id="draw" width="400" height="400"></canvas>
  </div>

  <div id="result"></div>

  <script>
    const canvas = document.getElementById("draw");
    const ctx = canvas.getContext("2d");
    let shapes = [];
    let currentShape = "circle";
    let isDrawing = false;
    let startX, startY;

    function setShape(shape) {
      currentShape = shape;
    }

    canvas.addEventListener("mousedown", (e) => {
      isDrawing = true;
      startX = e.offsetX;
      startY = e.offsetY;
    });

    canvas.addEventListener("mouseup", (e) => {
      if (!isDrawing) return;
      const endX = e.offsetX;
      const endY = e.offsetY;
      const size = Math.abs(endX - startX);
      shapes.push({ type: currentShape, x: startX, y: startY, size });
      drawShapes();
      isDrawing = false;
    });

    function drawShapes() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      shapes.forEach((s) => {
        ctx.beginPath();
        switch (s.type) {
          case "circle":
            ctx.arc(s.x, s.y, s.size / 2, 0, Math.PI * 2);
            break;
          case "triangle":
            ctx.moveTo(s.x, s.y - s.size / 2);
            ctx.lineTo(s.x - s.size / 2, s.y + s.size / 2);
            ctx.lineTo(s.x + s.size / 2, s.y + s.size / 2);
            ctx.closePath();
            break;
          case "square":
            ctx.rect(s.x - s.size / 2, s.y - s.size / 2, s.size, s.size);
            break;
          case "wave":
            for (let i = 0; i < s.size; i += 10) {
              ctx.moveTo(s.x - s.size / 2 + i, s.y);
              ctx.quadraticCurveTo(
                s.x - s.size / 2 + i + 5,
                s.y - 5,
                s.x - s.size / 2 + i + 10,
                s.y
              );
            }
            break;
        }
        ctx.strokeStyle = "#333";
        ctx.stroke();
      });
    }

    function undo() {
      shapes.pop();
      drawShapes();
    }

    function analyze() {
      let resultText = "";
      if (shapes.length === 0) {
        resultText = "아직 도형이 없습니다. 먼저 그림을 그려주세요.";
      } else {
        const first = shapes[0].type;
        const count = shapes.length;
        const big = shapes.reduce((max, s) => (s.size > max ? s.size : max), 0);
        let personality = "";

        switch (first) {
          case "circle":
            personality =
              "감정이 따뜻하고 관계를 중시하는 편입니다. 사람 사이의 조화를 중요하게 생각하고 타인의 감정에 공감합니다.";
            break;
          case "triangle":
            personality =
              "목표 지향적이고 추진력이 강합니다. 결단력이 있으며 리더십이 두드러집니다.";
            break;
          case "square":
            personality =
              "신중하고 체계적인 성향입니다. 안정감을 중요시하고 계획적으로 행동합니다.";
            break;
          case "wave":
            personality =
              "유연하고 창의적인 사고를 가진 사람입니다. 새로운 시도나 감각적 경험을 즐깁니다.";
            break;
        }

        resultText = `🌀 ${count}개의 도형을 배치했습니다.<br>
가장 먼저 그린 도형은 <b>${first}</b>이며, 이는 당신의 현재 중심적인 성향을 나타냅니다.<br>
가장 큰 도형은 자신감과 에너지가 높은 부분을 상징합니다.<br>
${personality}<br>
도형의 위치에 따라 과거, 현재, 미래의 에너지 흐름을 파악할 수 있습니다.`;
      }
      document.getElementById("result").innerHTML = resultText;
    }
  </script>
</body>
</html>