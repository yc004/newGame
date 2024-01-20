<script setup>
import * as PIXI from 'pixi.js';
import {Ticker} from 'pixi.js';

const app = new PIXI.Application({
  resizeTo: window,
  background: '#000000',
  resolution: window.devicePixelRatio || 1,
  antialias: true,
});

// noinspection JSCheckFunctionSignatures
document.body.appendChild(app.view);


class firework {
  explodeParticles = [];
  exploded = false;

  constructor(x, y, color, speed, radius) {
    this.mainBody = new Particle(x, y, 0, speed, color, radius);
    this.color = color;
    this.speed = speed;
    this.move();
  }

  move() {
    app.ticker.add((delta) => {
      if (!this.exploded) {
        this.mainBody.move(delta)
        this.explode();
      }
      // console.log(this.mainBody.circle.y);
      let alpha = 1;
      for (const explodeParticle of this.explodeParticles) {
        explodeParticle.move(delta);
        //   垂直方向对重力进行模拟
        explodeParticle.vy += 0.98 * delta * 0.5;

        explodeParticle.circle.alpha -= 0.03;
      }
    });
  }

  explode() {
    let count = 100;
    if (this.mainBody.y <= randomBetween(300, 600)) {
      this.exploded = true;
      app.stage.removeChild(this.mainBody.circle);
      let speed = this.speed * 1.2;
      while (speed <= 0) {
        for (let i = 0; i < count; i++) {
          // 为粒子增加偏移量 提高真实性
          let offset = randomBetween(50, 100);
          this.explodeParticles.push(new Particle(this.mainBody.x + offset, this.mainBody.y + offset, Math.cos(Math.PI * 2 / count * i) * speed, Math.sin(Math.PI * 2 / count * i) * speed, this.color, 3));
        }
        speed += 0.3;
      }
    }
  }
}

class Particle {
  // 初始化粒子,将粒子绘制在(x, y)的位置
  constructor(x, y, vx, vy, color, radius, alpha = 1) {
    this.x = x;
    this.y = y;
    this.circle = new PIXI.Graphics();
    this.circle.beginFill(color, alpha);
    this.circle.arc(x, y, radius, 0, Math.PI * 2);
    this.circle.pivot.set(0.5);
    app.stage.addChild(this.circle);
    this.vx = vx;
    this.vy = vy;
  }

  move(delta) {
    this.circle.x += this.vx * delta;
    this.circle.y += this.vy * delta;
    this.x += this.vx * delta;
    this.y += this.vy * delta;
  }
}

const colors = ['#F4A460',
  '#8B008B',
  '#FFD700',
  '#00FFFF',
  '#FF1493',
  '#7FFF00',
  '#FF4500',
  '#0000CD',
  '#FFFF00',
  '#C71585'];


function randomBetween(min, max) {
  return Math.floor(Math.random() * (max - min) + min);
}

app.ticker.add(() => {
  if (Math.random() <= 0.015) {
    new firework(randomBetween(100, window.innerWidth - 100), window.innerHeight, colors[randomBetween(0, colors.length)], randomBetween(-20, -10), 10);
  }
});

// 绘制辅助线
const line = new PIXI.Graphics();
line.lineStyle(3, 'white');
line.moveTo(0, 300);
line.lineTo(window.innerWidth, 300);
app.stage.addChild(line);


</script>
<style scoped>
* {
  margin: 0;
  width: 100vw;
  height: 100vh;
}

canvas {
  width: 100vw;
  height: 100vh;
}
</style>
