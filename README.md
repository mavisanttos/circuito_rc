# **Documentação: Simulação de Circuito RC com Tinkercad e Python**

**Autor:** Maria Vitória dos Santos
**Data:** 22 de outubro de 2025  
**Disciplina/Contexto:** Programação

---

## 1. Objetivo do Projeto

Este projeto demonstra como simular e monitorar a curva de carga de um capacitor e a curva de tensão em um resistor dentro de um circuito RC série.  
A simulação é realizada na plataforma **Autodesk Tinkercad**, com os dados sendo exportados para um **script em Python** que gera visualizações gráficas para análise.

O objetivo é **validar a teoria de circuitos RC** em um ambiente virtual e demonstrar o **fluxo de trabalho para extrair e analisar dados de simulações**.

---

## 2. Ferramentas e Componentes

### Componentes Virtuais (no Tinkercad)
- 1x Placa **Arduino Uno R3**
- 1x **Protoboard**
- 1x **Resistor** de 10 kΩ
- 1x **Capacitor Eletrolítico** de 100 µF
- **Jumpers** (fios de conexão virtuais)

### Software e Plataformas
- Navegador Web com acesso ao [Autodesk Tinkercad]([https://www.tinkercad.com/](https://www.tinkercad.com/things/b8jT9mduM9p-brilliant-allis/editel?returnTo=https%3A%2F%2Fwww.tinkercad.com%2Fdashboard&sharecode=Iz_zsA6vOo7E9jCPr5287HX6PDbfyBHK2Bh49aTfG4M))
- **Python 3.x**
- Bibliotecas Python: `pandas` e `matplotlib`

---

## 3. Montagem do Circuito (Simulação)

O circuito virtual no Tinkercad replica a montagem física.  
A fonte de **5V do Arduino simulado** alimenta o circuito RC série, e o **pino analógico A0** é usado para medir a tensão na junção entre o resistor e o capacitor.

### Esquema de Conexão
1. O pino **5V** do Arduino é conectado à coluna positiva (+) da protoboard.  
2. Um terminal do **Resistor** é conectado a essa mesma coluna de 5V.  
3. O outro terminal do **Resistor** é conectado a uma fileira de pinos no centro da protoboard.  
4. O pino analógico **A0** do Arduino é conectado a esta mesma fileira.  
5. O terminal positivo (+) do **Capacitor** é conectado também a esta fileira (junto com o resistor e o pino A0).  
6. O terminal negativo (-) do **Capacitor** é conectado à coluna negativa (-) da protoboard.  
7. O pino **GND** (Terra) do Arduino é conectado a essa mesma coluna negativa.  

### Print da Simulação no Tinkercad
> [SIMULAÇÃO NO TINKERCAD AQUI] ()

---

## 4. Códigos do Projeto

### 4.1. Código do Arduino (Para o Tinkercad)

Este código é inserido na aba **"Código"** do Tinkercad.  
Ele é idêntico ao usado no Arduino físico.

```cpp
// Código para o simulador do Tinkercad

int pinoNoRC = 0;
int valorLido = 0;
float tensaoResistor = 0;
float tensaoCapacitor = 0;
unsigned long time;

void setup() {
  Serial.begin(9600);
}

void loop() {
  time = millis();
  valorLido = analogRead(pinoNoRC);
  tensaoResistor = (valorLido * 5.0 / 1023.0);
  tensaoCapacitor = 5.0 - tensaoResistor;

  Serial.print(time);
  Serial.print(" ");
  Serial.print(tensaoResistor);
  Serial.print(" ");
  Serial.println(tensaoCapacitor);

  delay(400); // No Tinkercad, o tempo de simulação pode não ser real. O delay ainda é útil.
}
```

## 4.2. Código Python (Para Análise dos Dados)

Este script lê o arquivo de texto com os dados copiados do Tinkercad e gera os gráficos.  
Não há conexão em tempo real.

---

### **Nome do arquivo:** `analisar_dados_tinkercad.py`

```python
import pandas as pd
import matplotlib.pyplot as plt

# O nome do arquivo que você salvou com os dados do Tinkercad
arquivo_de_dados = 'dados_tinkercad.txt' 

try:
    # Lê os dados do arquivo de texto
    df = pd.read_csv(
        arquivo_de_dados,
        sep='\\s+',
        header=None,
        names=['Tempo_ms', 'TensaoResistor', 'TensaoCapacitor']
    )

    # Converte o tempo para segundos
    df['Tempo_s'] = df['Tempo_ms'] / 1000

    # --- Gráfico 1: Lado a Lado ---
    fig1, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 6))
    fig1.suptitle('Análise do Circuito RC (Gráficos Separados)', fontsize=16)
    ax1.plot(df['Tempo_s'], df['TensaoCapacitor'], color='blue', marker='o', linestyle='-', markersize=4)
    ax1.set_title('Curva de Carga do Capacitor')
    ax1.set_xlabel('Tempo Simulado (s)')
    ax1.set_ylabel('Tensão (V)')
    ax1.grid(True)

    ax2.plot(df['Tempo_s'], df['TensaoResistor'], color='red', marker='x', linestyle='--', markersize=4)
    ax2.set_title('Curva de Tensão no Resistor')
    ax2.set_xlabel('Tempo Simulado (s)')
    ax2.set_ylabel('Tensão (V)')
    ax2.grid(True)
    
    # --- Gráfico 2: Comparativo ---
    fig2, ax_comp = plt.subplots(figsize=(12, 8))
    ax_comp.plot(df['Tempo_s'], df['TensaoCapacitor'], color='blue', marker='o', markersize=4, label='Tensão no Capacitor (Carga)')
    ax_comp.plot(df['Tempo_s'], df['TensaoResistor'], color='red', marker='x', markersize=4, linestyle='--', label='Tensão no Resistor (Descarga)')
    ax_comp.set_title('Gráfico Comparativo: Tensão no Resistor vs. Capacitor', fontsize=16)
    ax_comp.set_xlabel('Tempo Simulado (s)')
    ax_comp.set_ylabel('Tensão (V)')
    ax_comp.legend()
    ax_comp.grid(True)

    # Exibe todas as figuras
    plt.show()

except FileNotFoundError:
    print(f"ERRO: O arquivo '{arquivo_de_dados}' não foi encontrado.")
```
## 5. Execução e Resultados

O fluxo de trabalho consiste em gerar os dados na nuvem (**Tinkercad**) e analisá-los localmente (**Python**).

---

### **Passos para Execução**

1. Montar o circuito virtual no **Tinkercad**.  
2. Inserir o código na aba **"Código"** e abrir o **"Monitor Serial"**.  
3. Clicar em **"Iniciar simulação"** e aguardar a geração de dados.  
4. Parar a simulação, selecionar todo o texto do **Monitor Serial** (`Ctrl+A`) e copiar (`Ctrl+C`).  
5. Colar os dados em um arquivo de texto simples e salvar como **`dados_tinkercad.txt`**.  
6. Garantir que o script **Python** e o arquivo **`dados_tinkercad.txt`** estejam na mesma pasta.  
7. Executar o script Python. Duas janelas com os gráficos serão exibidas.

---

### **Resultado Gráfico 1: Gráficos Lado a Lado**

> [INSERIR A IMAGEM DO GRÁFICO GERADO COM AS DUAS CURVAS SEPARADAS AQUI.]

**Exemplo:**
![Gráficos Separados](https://i.imgur.com/link-para-grafico1.png)

---

### **Resultado Gráfico 2: Gráfico Comparativo**

> [INSERIR A IMAGEM DO GRÁFICO GERADO COM AS DUAS CURVAS SOBREPOSTAS AQUI.]

**Exemplo:**
![Gráfico Comparativo](https://i.imgur.com/link-para-grafico2.png)

---

## **Análise dos Resultados**

A análise dos resultados da simulação é idêntica à da montagem física.  
Os gráficos confirmam o comportamento **exponencial previsto pela teoria de circuitos RC**.

- A **curva de tensão no capacitor** é **crescente** (carregamento).  
- A **curva de tensão no resistor** é **decrescente** (descarga).  
- A **soma das tensões** em qualquer instante de tempo *t* é **igual à tensão da fonte (5V)**, validando a **Lei das Malhas de Kirchhoff** no ambiente simulado.

---

## **6. Conclusão**

A simulação no **Tinkercad** provou ser uma ferramenta **eficaz e acessível** para estudar o comportamento de circuitos elétricos sem a necessidade de componentes físicos.  

O processo de exportar dados simulados para análise em **Python** demonstrou ser um **fluxo de trabalho poderoso**, permitindo uma **investigação quantitativa detalhada** que complementa a observação visual da simulação.
