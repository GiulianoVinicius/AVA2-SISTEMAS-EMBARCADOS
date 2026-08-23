# AVA2-SISTEMAS-EMBARCADOS
AVA2 DE SISTEMAS EMBARCADOS

## Explicação do código (arquivo: Código)

Resumo: este sketch para uma placa STM32 lê um sensor de luminosidade (LDR) na entrada analógica A0 e acende um LED verde, amarelo ou vermelho conforme o nível de luz.

Detalhamento por partes:

- #define LR PB7
- #define LY PB6
- #define LG PA10
  - Estas linhas associam nomes legíveis (LR = LED vermelho, LY = LED amarelo, LG = LED verde) aos pinos físicos da placa (PB7, PB6, PA10). São macros que o compilador substitui antes da compilação.

- void setup()
  - Serial.begin(115200); inicia a porta serial a 115200 bps para debug.
  - Serial.println("Hello, STM32!"); imprime uma mensagem ao iniciar.
  - pinMode(..., OUTPUT); configura os pinos dos LEDs como saída.

- void loop()
  - int lux = analogRead(0); lê o valor analógico do canal A0 e guarda em `lux`.
    - Observação: o valor retornado depende da resolução do ADC (por exemplo, 0–4095 em ADC 12-bit ou 0–1023 em 10-bit). Os limiares a seguir (350 e 700) foram escolhidos assumindo uma faixa específica; ajuste conforme seu hardware.
  - if (lux < 350) { ... } else if (lux >= 350 and lux <= 700) { ... } else { ... }
    - Quando o valor é pequeno (pouca luz) acende o LED verde e apaga os outros.
    - Quando o valor está na faixa intermédia acende o LED amarelo.
    - Quando o valor é alto (muita luz) acende o LED vermelho.
    - Nota: o código atual usa `and` (token alternativo em C++); o mais comum é usar `&&`.
  - Serial.println(analogRead(0)); imprime o valor lido — aqui o código faz uma segunda leitura em vez de imprimir `lux` (melhor usar `Serial.println(lux);` para evitar leituras duplicadas).
  - delay(1000); espera 1 segundo antes da próxima leitura.

Observações e possíveis problemas:
- Não é necessário chamar pinMode para a entrada analógica; analogRead cuida disso.
- Chamar analogRead(0) duas vezes (na linha que atribui `lux` e na linha que imprime) pode retornar valores ligeiramente diferentes; reutilize `lux`.
- Os nomes dos pinos (PB7, PA10...) dependem do core/placa; confirme se esses mapeamentos estão corretos para sua placa STM32.

Código original:

```cpp
#define LR PB7
#define LY PB6
#define LG PA10

void setup() {  
  Serial.begin(115200);
  Serial.println("Hello, STM32!");
  pinMode(LR,OUTPUT);
  pinMode(LG,OUTPUT);
  pinMode(LY,OUTPUT);
}

void loop() {
  int lux=analogRead(0);
  if(lux<350)
    {
      digitalWrite(LG,1);
      digitalWrite(LR,0);
      digitalWrite(LY,0);
    }
  else if(lux>=350 and lux<=700)
    {
      digitalWrite(LG,0);
      digitalWrite(LR,0);
      digitalWrite(LY,1);
    }  
  else
    {
      digitalWrite(LG,0);
      digitalWrite(LR,1);
      digitalWrite(LY,0);
    }
  Serial.println(analogRead(0));
  delay(1000); 
}
```

Sugestões de melhoria (exemplo de versão limpa):

- Use nomes de constantes claros e um pino explícito para o LDR.
- Reutilize a leitura salva em `lux` em vez de chamar analogRead duas vezes.
- Use `&&` para condicional composta (mais comum em C/C++).

```cpp
#define LR PB7
#define LY PB6
#define LG PA10
#define LDR_PIN A0

void setup() {
  Serial.begin(115200);
  Serial.println("Hello, STM32!");
  pinMode(LR, OUTPUT);
  pinMode(LG, OUTPUT);
  pinMode(LY, OUTPUT);
}

void loop() {
  int lux = analogRead(LDR_PIN);

  if (lux < 350) {
    digitalWrite(LG, HIGH);
    digitalWrite(LR, LOW);
    digitalWrite(LY, LOW);
  } else if (lux >= 350 && lux <= 700) {
    digitalWrite(LG, LOW);
    digitalWrite(LR, LOW);
    digitalWrite(LY, HIGH);
  } else {
    digitalWrite(LG, LOW);
    digitalWrite(LR, HIGH);
    digitalWrite(LY, LOW);
  }

  Serial.println(lux);
  delay(1000);
}
```

Se quiser, posso:
- Ajustar os limiares (350/700) com base em medições reais da sua LDR;
- Mapear os pinos usando `const int` em vez de `#define`;
- Incluir calibração e filtragem (média móvel) para leituras mais estáveis.
