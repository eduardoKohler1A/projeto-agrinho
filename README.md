# projeto-agrinho
let plataforma;
let circulos = [];
let pontuacao = 0;
let verdesPerdidos = 0;
let jogoAcabou = false;

function setup() {
  createCanvas(400, 600);
  plataforma = new Plataforma();
}

function draw() {
  background(120);

  if (!jogoAcabou) {
    plataforma.mover();
    plataforma.exibir();

    // Criar novos círculos
    if (frameCount % 60 === 0) {
      circulos.push(new Circulo());
    }

    // Atualizar e desenhar círculos
    for (let i = circulos.length - 1; i >= 0; i--) {
      let c = circulos[i];
      c.atualizar();
      c.exibir();

        if (c.colidiuCom(plataforma)) {
  if (c.cor === 'green') {  // Se o círculo for verde, adiciona ponto
    pontuacao++;
  } else if (c.cor === 'black') {
    jogoAcabou = true;  // Se o círculo for preto, o jogo acaba
  }
  circulos.splice(i, 1); // remove o círculo após colisão
      } else if (c.y - c.raio > height) {
        // Círculo saiu da tela
        if (c.cor === 'green') {
          verdesPerdidos++;
          if (verdesPerdidos >= 3) {
            jogoAcabou = true;
          }
        }
        circulos.splice(i, 1);
      }
    }

    fill(255);
    textSize(20);
    textAlign(LEFT, TOP);
    text(`Pontos: ${pontuacao}`, 10, 10);
    text(`Verdes Perdidos: ${verdesPerdidos}/3`, 10, 40);
  } else {
    fill('red');
    textSize(40);
    textAlign(CENTER, CENTER);
    text("Game Over", width / 2, height / 2);
    textSize(20);
    text("Pressione R para reiniciar", width / 2, height / 2 + 40);
  }
}

function keyPressed() {
  if (jogoAcabou && (key === 'r' || key === 'R')) {
    reiniciarJogo();
  }
}

function reiniciarJogo() {
  pontuacao = 0;
  verdesPerdidos = 0;
  circulos = [];
  jogoAcabou = false;
  plataforma = new Plataforma();
}

// ===== CLASSES =====

class Plataforma {
  constructor() {
    this.largura = 100;
    this.altura = 15;
    this.x = width / 2 - this.largura / 2;
    this.y = height - 40;
    this.velocidade = 7;
  }

  mover() {
    if (keyIsDown(LEFT_ARROW)) this.x -= this.velocidade;
    if (keyIsDown(RIGHT_ARROW)) this.x += this.velocidade;
    this.x = constrain(this.x, 0, width - this.largura);
  }

  exibir() {
    fill(255);
    rect(this.x, this.y, this.largura, this.altura);
  }
}

class Circulo {
  constructor() {
    this.raio = 15;
    this.x = random(this.raio, width - this.raio);
    this.y = -this.raio;
    this.velocidade = 4;
    this.cor = random(1) < 0.7 ? 'green' : 'black'; // 70% chance de ser verde
  }

  atualizar() {
    this.y += this.velocidade;
  }

  exibir() {
    fill(this.cor);
    noStroke();
    ellipse(this.x, this.y, this.raio * 2);
  }

  colidiuCom(plataforma) {
    let dentroX = this.x > plataforma.x && this.x < plataforma.x + plataforma.largura;
    let dentroY = this.y + this.raio > plataforma.y && this.y - this.raio < plataforma.y + plataforma.altura;
    return dentroX && dentroY;
  }
}
