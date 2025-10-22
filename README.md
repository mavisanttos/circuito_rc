# Monitor de Circuito RC com Arduino e Python  

**Autor:** Maria Vitória dos Santos
**Data:** 22 de outubro de 2025  
**Disciplina/Contexto:** Programação

---

## 1. Objetivo do Projeto  

Este projeto demonstra como monitorar a curva de carga de um capacitor e a curva de tensão em um resistor dentro de um circuito RC série.  
Os dados são coletados em tempo real por uma placa Arduino e visualizados dinamicamente através de um script em Python, que plota os gráficos de tensão versus tempo.  

O objetivo é observar experimentalmente o comportamento exponencial característico desses componentes e validar a **Lei das Malhas de Kirchhoff**.  

---

## 2. Materiais Utilizados  

### **Hardware**  
- 1x Placa Arduino (Uno ou similar)  
- 1x Protoboard (Matriz de Contatos)  
- 1x Resistor de **10 kΩ** (ou outro valor, anote o seu)  
- 1x Capacitor Eletrolítico de **100 µF** (ou outro valor, anote o seu)  
- Jumpers (fios de conexão)  
- 1x Cabo USB para conectar o Arduino ao computador  

### **Software**  
- Arduino IDE  
- Python 3.x  
- Bibliotecas Python: `pyserial` e `matplotlib`  

---

## 3. Montagem do Circuito  

O circuito consiste em uma fonte de 5V (fornecida pelo Arduino) alimentando um resistor e um capacitor em série.  
O pino analógico **A0** do Arduino é utilizado como um voltímetro para medir a tensão no ponto de junção entre o resistor e o capacitor.  

### **Esquema de Conexão**  
1. O pino **5V** do Arduino é conectado a uma trilha da protoboard.  
2. Um terminal do **Resistor (10 kΩ)** é conectado a essa mesma trilha de 5V.  
3. O outro terminal do Resistor é conectado a outra trilha.  
4. O pino **A0** do Arduino é conectado a esta trilha (segundo terminal do resistor).  
5. O terminal **positivo (+)** do Capacitor (100 µF) é conectado também a esta trilha.  
6. O terminal **negativo (-)** do Capacitor é conectado à trilha de **referência (terra)**.  
7. O pino **GND** do Arduino é conectado a essa mesma trilha de referência.  

---

### **Foto da Montagem Física**  
[CLIQUE AQUI PARA INSERIR UMA IMAGEM NÍTIDA DA SUA MONTAGEM FÍSICA. MOSTRE AS CONEXÕES CLARAMENTE.]  

Exemplo de como a imagem aparecerá:  

---

## 4. Códigos do Projeto  

### **4.1. Código do Arduino (Sketch)**  

Este código é responsável por medir a tensão no pino A0, calcular as tensões no resistor e no capacitor, e enviar esses dados pela porta serial.  

```cpp
int pinoNoRC=0;
int valorLido = 0;
float tensaoCapacitor = 0, tensaoResistor;
unsigned long time;
void setup(){
Serial.begin(9600);
}
void loop() {
time=millis();
valorLido=analogRead(pinoNoRC);
tensaoResistor=(valorLido*5.0/1023); // 5.0V / 1023 degraus = 0.0048876
tensaoCapacitor = abs(5.0-tensaoResistor);
Serial.print(time); //imprime o conteúdo de time no MONITOR SERIAL
Serial.print(" ");
Serial.print(tensaoResistor);
Serial.print(" ");
Serial.println(tensaoCapacitor);
delay(400);
}
```
### 4.2. Código Python (Script de Visualização)

Este script se conecta à porta serial do Arduino, lê os dados enviados e plota os gráficos de tensão em tempo real.

```py
import serial
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation

# --- CONFIGURAÇÃO OBRIGATÓRIA ---
# Altere 'COM3' para a porta serial correta do seu Arduino!
PORTA_SERIAL = 'COM3'  # <--- MUDE AQUI
BAUD_RATE = 9600

# Listas para armazenar os dados lidos
tempos = []
tensoes_r = []
tensoes_c = []

# --- Conexão com o Arduino ---
try:
    arduino = serial.Serial(PORTA_SERIAL, BAUD_RATE, timeout=1)
    print(f"Conectado com sucesso na porta {PORTA_SERIAL}.")
except serial.SerialException as e:
    print(f"Erro ao conectar na porta {PORTA_SERIAL}: {e}")
    exit()

# --- Configuração do Gráfico Dinâmico ---
fig, ax = plt.subplots(figsize=(12, 7))

def atualizar_grafico(frame):
    try:
        linha_de_dados = arduino.readline().decode('utf-8').strip()
        if linha_de_dados:
            dados = linha_de_dados.split()
            if len(dados) == 3:
                tempos.append(int(dados[0]) / 1000)
                tensoes_r.append(float(dados[1]))
                tensoes_c.append(float(dados[2]))

                ax.clear()
                ax.plot(tempos, tensoes_c, 'b-', label='Tensão no Capacitor (Vc)')
                ax.plot(tempos, tensoes_r, 'r-', label='Tensão no Resistor (Vr)')

                ax.set_title('Análise em Tempo Real do Circuito RC')
                ax.set_xlabel('Tempo (s)')
                ax.set_ylabel('Tensão (V)')
                ax.grid(True)
                ax.legend()
                ax.set_ylim(-0.2, 5.2)
    except (ValueError, IndexError):
        pass

ani = FuncAnimation(fig, atualizar_grafico, interval=100, cache_frame_data=False)
plt.show()

arduino.close()
print("Comunicação serial encerrada.")
```

## 5. Execução e Resultados

Passos para Execução

- Montar o circuito na protoboard conforme o esquema.

- Conectar o Arduino ao computador via USB.

- Abrir o arquivo .ino na Arduino IDE, selecionar a placa e a porta corretas, e fazer o upload do código.

- Fechar o Monitor Serial da IDE do Arduino (ele não pode ser usado ao mesmo tempo que o script Python).

- Abrir o script .py em um editor (ex: VS Code) e alterar a variável PORTA_SERIAL para a porta correta.

- Executar o script Python a partir do terminal.

- Uma janela com o gráfico dinâmico será exibida.

## Vídeo do Gráfico em Ação

<video src="[https://i.imgur.com/your-video-url.mp4](https://drive.google.com/file/d/1L8-8pmf_uZAJWx6g42AJWNYg_LwBW4Fj/view?usp=sharing)" width="600" controls></video>

## Análise dos Resultados  

Os gráficos gerados confirmam a teoria de circuitos RC:  

- A **tensão no capacitor (azul)** exibe uma **curva de carga exponencial crescente**, partindo de **0V** e tendendo assintoticamente a **5V**.  
- A **tensão no resistor (vermelha)** mostra uma **curva exponencial decrescente**, partindo de **5V** e tendendo a **0V**.  

A soma das tensões é aproximadamente constante e igual à tensão da fonte:  

$$V_R(t) + V_C(t) \approx 5V$$  

Esse resultado valida experimentalmente a **Lei das Malhas de Kirchhoff**.  

---

## Conclusão  

O projeto foi bem-sucedido em criar um sistema de **aquisição e visualização de dados** para um circuito RC.  
A integração entre o **hardware (Arduino)** e o **software (Python)** permitiu uma análise dinâmica do comportamento elétrico do circuito, evidenciando o **caráter exponencial das tensões envolvidas** e reforçando **conceitos fundamentais da teoria dos circuitos elétricos**.  
