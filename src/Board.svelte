<script>
  import { onMount, afterUpdate } from "svelte";

  const [width, height] = [640, 480];
  let canvas;

  let pointA = { x: 100, y: height / 4, color: "#ffb6b9" };
  let pointB = { x: 540, y: height * (3 / 4), color: "#61c0bf" };

  let pointX = { x: 0, y: height / 2, color: "#1b1b2f" };

  let v1 = 299792;
  let v2 = 225000;

  const backgroundColor1 = "#fae3d9";
  const backgroundColor2 = "#bbded6";

  const clear = () => {
    const ctx = canvas.getContext("2d");
    ctx.fillStyle = backgroundColor1;
    ctx.fillRect(0, 0, canvas.width, pointX.y);
    ctx.fillStyle = backgroundColor2;
    ctx.fillRect(0, pointX.y, canvas.width, canvas.height);
  };

  const drawPoint = ({ x, y, color }) => {
    const ctx = canvas.getContext("2d");
    ctx.beginPath();
    ctx.arc(x, y, 7, 0, 2 * Math.PI);
    ctx.fillStyle = color;
    ctx.fill();
    ctx.stroke();
  };

  const distance = (p1, p2) => {
    const dx = p1.x - p2.x;
    const dy = p1.y - p2.y;
    return Math.sqrt(Math.pow(dx, 2) + Math.pow(dy, 2));
  };

  const totalTime = x => {
    const temp = { x, y: pointX.y };
    const l1 = distance(pointA, temp);
    const l2 = distance(pointB, temp);

    return l1 / v1 + l2 / v2;
  };

  const findMinimum = () => {
    let minX = 0;
    let minValue = Infinity;

    for (let t = 0; t < width; t++) {
      const value = totalTime(t);
      if (value < minValue) {
        minValue = value;
        minX = t;
      }
    }
    return minX;
  };
  const drawBoard = () => {
    clear();

    pointX.x = findMinimum();
    [[pointA, pointX], [pointX, pointB]].forEach(([p1, p2]) =>
      drawLine(p1, p2)
    );
    [pointA, pointB].forEach(drawPoint);
  };
  onMount(drawBoard);
  afterUpdate(drawBoard);

  const drawLine = (point1, point2) => {
    const ctx = canvas.getContext("2d");
    ctx.beginPath();
    ctx.moveTo(point1.x, point1.y);
    ctx.lineTo(point2.x, point2.y);
    ctx.stroke();
  };
</script>

<style>
  .bg {
    background-color: #fae3d9;
    min-height: 100vh;
  }
  canvas {
    margin: 10px;
    border-style: solid;
    border-width: 1px;
  }
  .slidecontainer {
    width: 640px;
    margin: 25px auto;
  }

  .slider {
    -webkit-appearance: none;
    width: 100%;
    height: 25px;
    background: #d3d3d3;
    outline: none;
    opacity: 0.7;
    -webkit-transition: 0.2s;
    transition: opacity 0.2s;
  }

  .slider:hover {
    opacity: 1;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 25px;
    height: 25px;
    background: #61c0bf;
    cursor: pointer;
  }

  .slider::-moz-range-thumb {
    width: 25px;
    height: 25px;
    background: #61c0bf;
    cursor: pointer;
  }
</style>

<div class="bg">
  <div style="text-align:center;">
    <canvas bind:this={canvas} {width} {height} />

  </div>
  <div class="slidecontainer">
    <label>x1: {pointA.x}</label>
    <input
      type="range"
      min={1}
      max={width - 1}
      class="slider"
      bind:value={pointA.x} />

    <label>x2 : {pointB.x}</label>
    <input
      type="range"
      min={1}
      max={width - 1}
      class="slider"
      bind:value={pointB.x} />

    <label>v1: {v1} km/s</label>
    <input type="range" min={1} max={300000} class="slider" bind:value={v1} />
    <label>v2: {v2} km/s</label>
    <input type="range" min={1} max={300000} class="slider" bind:value={v2} />

    <div>
      <p>
        Zasada Fermata - Promień świetlny poruszający się od punktu A do punktu
        B przebywa najkrótszą możliwie drogę optyczną, czyli taką, na której
        przebycie potrzebuje minimalnego czasu.
      </p>
      <p>
        W powyższej symulacji można kontrolować położenie na osi X punktów A i
        B, oraz prędkość światła w ośrodkach. Światło "stara się" przybyć jak
        najdłuższą drogę w ośrodku, w którym prędkość światła jest większa.
      </p>

    </div>
  </div>

</div>
