let atoms = [];
let springs = [];
let deformationMode = "Elastic";
let maxForce = 0.5;
let breakLength = 50;

function setup() {
  createCanvas(800, 600);
  textSize(16);
  
  // Создание сетки атомов
  let spacing = 30;
  for (let y = 0; y < 6; y++) {
    for (let x = 0; x < 8; x++) {
      let atom = new Atom(
        x * spacing + 200,
        y * spacing + 200
      );
      atoms.push(atom);
    }
  }

  // Создание связей между соседями
  for (let a of atoms) {
    for (let b of atoms) {
      if (a !== b && dist(a.pos.x, a.pos.y, b.pos.x, b.pos.y) < spacing * 1.1) {
        let existing = springs.find(s => 
          (s.a === a && s.b === b) || (s.a === b && s.b === a)
        );
        if (!existing) {
          springs.push(new Spring(a, b));
        }
      }
    }
  }
}

function draw() {
  background(240);
  
  // Обновление и отрисовка связей
  for (let i = springs.length - 1; i >= 0; i--) {
    let s = springs[i];
    s.update();
    s.show();
    if (s.checkBreak()) {
      springs.splice(i, 1);
      deformationMode = "Plastic";
    }
  }

  // Обновление и отрисовка атомов
  for (let a of atoms) {
    a.update();
    a.show();
  }

  // Текстовые пояснения
  fill(0);
  text("Deformation Mode: " + deformationMode, 20, 30);
  text("Click and drag to apply force", 20, 50);
  text("Break threshold: " + breakLength, 20, 70);
}

function mouseDragged() {
  for (let a of atoms) {
    let d = dist(mouseX, mouseY, a.pos.x, a.pos.y);
    if (d < 20) {
      let force = createVector(mouseX - pmouseX, mouseY - pmouseY);
      force.mult(maxForce);
      a.applyForce(force);
    }
  }
}

class Atom {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = createVector(0, 0);
    this.acc = createVector(0, 0);
    this.mass = 1;
  }

  applyForce(f) {
    let acc = p5.Vector.div(f, this.mass);
    this.acc.add(acc);
  }

  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.vel.mult(0.95);
  }

  show() {
    fill(100, 100, 200);
    ellipse(this.pos.x, this.pos.y, 10);
  }
}

class Spring {
  constructor(a, b) {
    this.a = a;
    this.b = b;
    this.len = dist(a.pos.x, a.pos.y, b.pos.x, b.pos.y);
    this.k = 0.2;
  }

  update() {
    let force = p5.Vector.sub(this.b.pos, this.a.pos);
    let stretch = force.mag() - this.len;
    force.normalize();
    force.mult(-this.k * stretch);
    
    this.a.applyForce(force);
    this.b.applyForce(force.mult(-1));
  }

  show() {
    let stretch = dist(this.a.pos.x, this.a.pos.y, this.b.pos.x, this.b.pos.y) - this.len;
    let col = map(abs(stretch), 0, breakLength/2, 0, 255);
    stroke(255 - col, col, 0);
    line(this.a.pos.x, this.a.pos.y, this.b.pos.x, this.b.pos.y);
  }

  checkBreak() {
    let currentLen = dist(this.a.pos.x, this.a.pos.y, this.b.pos.x, this.b.pos.y);
    return currentLen > this.len + breakLength;
  }
}
