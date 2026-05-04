# Final-Project
##Fengkai Liu
```
let rings = [];
let t = 0;

let camZ = 0;
let targetCamZ = 0;

let texts = [
  "",
  "You move through the dark underwater cave, \n your flashlight a fragile beam cutting the void.",
  "You have already lost your sense of direction,\n knowing only that you are searching for an exit.",
"The silence around you is suffocating,\n making your own breathing sound too loud.",
"The numbers on your oxygen gauge keep dropping,\n and you try not to look at them.",
"You attempt to recall the path you took,\n but your memory feels unreliable.",
"The rock presses close,\n scraping against your gear as you move.",
"Your breathing grows faster,\n and you realize panic is setting in.",
"You force yourself to stop \nand watch the faint movement in the water.",
"Maybe the current can guide you out,\n you tell yourself.",
"But it is so weak \nthat you are not even sure it is real.",
"Your oxygen continues to fall,\n and your chest tightens.",
"A thought settles in: \n you might not make it out.",
"Strangely, as that thought takes hold,\n your fear begins to fade.",
"You stop struggling \nand let your body drift in the water.",
"If this is the end,\n then so be it.",
"You close your eyes,\n listening to your heavy breathing.",
"When you open them again,\n you sweep your flashlight forward.",
"In the distance,\n something looks different in the darkness.",
"You freeze,\n thinking it might be a hallucination.",
"You move closer,\n and the faint light does not disappear.",
"It seeps through a narrow crack in the rock.",
"Your heart surges back to life,\n and you almost forget to breathe.",
"You stare at the light,\n knowing it is the way out.",
];

let index = 0;
let shaking = false;

function setup() {
  createCanvas(windowWidth, windowHeight);
  noStroke();
  textFont("monospace");
//number of rings, z is deepth
  for (let i = 0; i < 70; i++) {
    rings.push({
      z: i * 55
    });
  }
}

function draw() {
  background(0);
//water effect strongth
  t += 0.015;

  camZ = lerp(camZ, targetCamZ, 0.06);

  // tunnel motion
  push();
  translate(
    noise(t) * 6 - 3,
    noise(t + 1000) * 6 - 3
  );

  drawTunnel();
  pop();

  // text
  drawText();
}

/* ---------------------------
   TUNNEL
----------------------------*/
function drawTunnel() {
  translate(width / 2, height / 2);

  let maxRadius = dist(0, 0, width, height) * 1.05;
//draw each rings
  for (let i = 0; i < rings.length; i++) {
    let r = rings[i];
//distance from you and ring
    let dz = r.z - camZ;
    if (dz < -200) continue;
//perspective of rings
    let perspective = 300 / (dz + 300);
    perspective = constrain(perspective, 0.07, 2.6);

    // Color the ring
    let depthNorm = 1 - constrain(dz / 1500, 0, 1);
    depthNorm = pow(depthNorm, 2.2);

    let rCol = lerp(0, 10, depthNorm);
    let gCol = lerp(0, 60, depthNorm);
    let bCol = lerp(5, 140, depthNorm);

    fill(rCol, gCol, bCol);

    push();
    scale(perspective);

    drawOrganicRing(maxRadius * 0.8, i);

    pop();
  }
}

/* ---------------------------
   ORGANIC SHAPE (strong deformation)
----------------------------*/
function drawOrganicRing(baseR, seed) {
  beginShape();

  let detail = 160;

  for (let i = 0; i < TWO_PI; i += TWO_PI / detail) {
    let ang = i;

    let nx = cos(ang) * 0.5 + 0.5;
    let ny = sin(ang) * 0.5 + 0.5;

    let n1 = noise(nx * 2, ny * 2, seed * 0.08 + t * 0.6);
    let n2 = noise(nx * 5, ny * 5, seed * 0.12 - t * 0.5);
    let n3 = noise(nx * 10, ny * 10, seed * 0.2 + t * 0.3);

    let deform = n1 * 0.5 + n2 * 0.3 + n3 * 0.2;

    let r = baseR * (0.55 + deform * 0.5);

    vertex(cos(ang) * r, sin(ang) * r);
  }

  endShape(CLOSE);
}

/* ---------------------------
   TEXT (FIXED CENTER, WHITE)
----------------------------*/
function drawText() {
  fill(255);
  textAlign(CENTER, CENTER);
  textSize(50);

  let x = width / 2;
  let y = height / 2;

  if (shaking) {
    x += random(-5, 5);
    y += random(-5, 5);
  }

  text(texts[index], x, y);
}

/* ---------------------------
   INTERACTION
----------------------------*/
function mousePressed() {
  targetCamZ += 160;

  rings.unshift({
    z: camZ + 80
  });

  triggerText();
}

/* ---------------------------
   TEXT FLOW
----------------------------*/
function triggerText() {
  shaking = true;

  setTimeout(() => {
    shaking = false;
    nextText();
  }, 250);
}

function nextText() {
  index++;
  if (index >= texts.length) index = texts.length - 1;
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}


```
### The scene is a first-person view inside an underwater tunnel. The tunnel slightly distorts and deforms, with a wavy water-like flow. Text appears in the center of the screen. When the player clicks anywhere on the screen, the text distorts and slowly disappears, and new text appears.
### Basically, the cave tunnel is constructed from a series of ring-shaped structures. Each ring has a depth value, which represents its position along the z-axis. At the same time, I define a camera position that represents the player’s viewpoint.
The entire system is driven by the relationship between these two values. Each ring calculates its distance to the camera, and based on that distance, its scale and color are adjusted. The farther a ring is, the smaller and darker it becomes. When the player clicks, the camera moves forward, and all the rings update their appearance based on the new distance. This creates a simple 3D perspective effect in a 2D environment.
Link:https://editor.p5js.org/supperred1/full/irVhZy8zf
