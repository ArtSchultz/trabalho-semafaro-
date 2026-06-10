// ====================================
// SEMAFORO INTELIGENTE COM EMERGENCIA
// ====================================

// Via A
#define VA_VERDE 2
#define VA_AMARELO 3
#define VA_VERMELHO 4

// Via B
#define VB_VERDE 5
#define VB_AMARELO 6
#define VB_VERMELHO 7

// Botoes
#define BTN_PED 10
#define BTN_AMB 11

// ====================================
// PROTOTIPOS
// ====================================

void apagarTudo();

void estadoViaA();
void estadoAmareloA();

void estadoViaB();
void estadoAmareloB();

void estadoPedestre();
void estadoEmergencia();

bool verificarEventos(unsigned long tempo);

// ====================================
// SETUP
// ====================================

void setup() {

  Serial.begin(9600);

  pinMode(VA_VERDE, OUTPUT);
  pinMode(VA_AMARELO, OUTPUT);
  pinMode(VA_VERMELHO, OUTPUT);

  pinMode(VB_VERDE, OUTPUT);
  pinMode(VB_AMARELO, OUTPUT);
  pinMode(VB_VERMELHO, OUTPUT);

  pinMode(BTN_PED, INPUT_PULLUP);
  pinMode(BTN_AMB, INPUT_PULLUP);

  apagarTudo();
}

// ====================================
// LOOP PRINCIPAL
// ====================================

void loop() {

  // Via A Verde
  estadoViaA();

  if (verificarEventos(10000))
    return;

  // Via A Amarelo
  estadoAmareloA();

  if (verificarEventos(10000))
    return;

  // Via B Verde
  estadoViaB();

  if (verificarEventos(10000))
    return;

  // Via B Amarelo
  estadoAmareloB();

  verificarEventos(10000);
}

// ====================================
// ESTADOS NORMAIS
// ====================================

void estadoViaA() {

  apagarTudo();

  digitalWrite(VA_VERDE, HIGH);
  digitalWrite(VB_VERMELHO, HIGH);

  Serial.println("S0 - Via A Verde");
}

void estadoAmareloA() {

  apagarTudo();

  digitalWrite(VA_AMARELO, HIGH);
  digitalWrite(VB_VERMELHO, HIGH);

  Serial.println("S2A - Via A Amarelo");
}

void estadoViaB() {

  apagarTudo();

  digitalWrite(VA_VERMELHO, HIGH);
  digitalWrite(VB_VERDE, HIGH);

  Serial.println("S1 - Via B Verde");
}

void estadoAmareloB() {

  apagarTudo();

  digitalWrite(VA_VERMELHO, HIGH);
  digitalWrite(VB_AMARELO, HIGH);

  Serial.println("S2B - Via B Amarelo");
}

// ====================================
// ESTADO PEDESTRE
// ====================================

void estadoPedestre() {

  Serial.println("Pedestre acionado");

  apagarTudo();

  digitalWrite(VA_AMARELO, HIGH);
  digitalWrite(VB_AMARELO, HIGH);

  delay(10000);

  apagarTudo();

  digitalWrite(VA_VERMELHO, HIGH);
  digitalWrite(VB_VERMELHO, HIGH);

  Serial.println("S3 - Travessia");

  delay(10000);
}

// ====================================
// ESTADO EMERGENCIA
// ====================================

void estadoEmergencia() {

  Serial.println("Emergencia acionada");

  apagarTudo();

  digitalWrite(VA_AMARELO, HIGH);
  digitalWrite(VB_AMARELO, HIGH);

  delay(1000);

  apagarTudo();

  digitalWrite(VA_VERDE, HIGH);
  digitalWrite(VB_VERMELHO, HIGH);

  Serial.println("S4 - Emergencia");

  delay(10000);
}

// ====================================
// FUNCOES AUXILIARES
// ====================================

void apagarTudo() {

  digitalWrite(VA_VERDE, LOW);
  digitalWrite(VA_AMARELO, LOW);
  digitalWrite(VA_VERMELHO, LOW);

  digitalWrite(VB_VERDE, LOW);
  digitalWrite(VB_AMARELO, LOW);
  digitalWrite(VB_VERMELHO, LOW);
}

bool verificarEventos(unsigned long tempo) {

  unsigned long inicio = millis();

  while (millis() - inicio < tempo) {

    if (digitalRead(BTN_AMB) == LOW) {

      estadoEmergencia();
      return true;
    }

    if (digitalRead(BTN_PED) == LOW) {

      estadoPedestre();
      return true;
    }
  }

  return false;
}