# 🪐 Aurora Siger - Fase 3

## A Aurora Estabelece os Primeiros Sistemas da Colonia

---

## Descricao

Este projeto implementa o sistema computacional inteligente da colonia **Aurora Siger** em Marte para a fase de **operacao continua**.

Enquanto nas fases anteriores o foco foi pousar e sobreviver, agora o desafio e manter a colonia funcionando de forma autonoma, integrando estruturas de dados, logica de decisao, modelagem matematica e previsao para apoiar decisoes operacionais.

A entrega esta em um **notebook executavel** que organiza sensores da colonia, aplica regras de decisao, modela geracao e consumo, e usa regressao linear simples para antecipar comportamentos.

---

## 🎯 Objetivo da Atividade

- Organizar os dados da colonia em estruturas eficientes (listas, dicionarios e hierarquias).
- Construir regras logicas para decisoes automaticas baseadas em condicoes.
- Representar fenomenos da colonia por meio de funcoes matematicas.
- Aplicar regressao linear simples para prever comportamentos futuros.
- Analisar geracao, consumo e reservas e gerar recomendacoes de eficiencia energetica.
- Evoluir o sistema de uma resposta reativa para uma resposta preditiva.

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Finalidade |
|---|---|
| Python 3 | Linguagem principal |
| Google Colab / Jupyter Notebook | Ambiente interativo |
| **NumPy** | **Arrays 1D/2D, vetorizacao e broadcasting** |
| pandas | Tabelas de dados e simulacoes |
| matplotlib | Visualizacao dos modelos |

> Conforme orientacao do enunciado, nao foram utilizadas bibliotecas avancadas como `scikit-learn`. A regressao linear foi implementada manualmente usando apenas operacoes vetorizadas do NumPy (`np.sum`, `np.mean`, multiplicacao elemento a elemento).

### NumPy aplicado

- **Series historicas** sao `np.ndarray` 1D (`shape (12,)`, `dtype float64`).
- **Modelos matematicos** (`geracao_solar`, `geracao_eolica`, `consumo_estimado`) aceitam tanto escalares quanto arrays via broadcasting, usando `np.sin`, `np.exp`, `np.maximum` e `np.where`.
- **Regressao linear** calcula coeficientes com somas vetorizadas (sem laco Python).
- **Cenarios de eficiencia** sao representados como matriz 2D e os saldos sao calculados em uma unica operacao vetorial.
- **Avaliacao do modelo** usa `np.sum((y - y_pred)**2)` para o R^2 e estatisticas dos residuos.

---

## Como o Sistema Funciona

O fluxo segue o ciclo classico: **entrada -> processamento -> saida**.

```text
sensores -> estruturas de dados -> regras de decisao -> previsao -> recomendacao
```

### Etapa 1 - Estruturacao dos dados

Os dados sao organizados em tres niveis:

- **Hierarquia** dos subsistemas (energetico -> solar / eolico, ambiental, operacional).
- **Dicionario (chave-valor)** com a leitura atual dos sensores.
- **Listas (serie historica)** com leituras recentes para analise e previsao.

### Etapa 2 - Logica de decisao

Sao aplicadas regras com `if / elif / else` que combinam multiplos criterios:

```text
se consumo > geracao E reserva < 30  -> "ALERTA: reduzir consumo"
se geracao > consumo * 1.3           -> "SUGESTAO: armazenar energia excedente"
se reserva < 20 E saldo < 0          -> MODO CRITICO (desliga nao essenciais)
```

A funcao `priorizar_modulos` mantem o **suporte a vida** ligado e desliga modulos nao essenciais (laboratorio, estufa) em modos restritivos.

### Etapa 3 - Modelagem matematica

Funcoes matematicas representam fenomenos da colonia:

- Geracao solar: curva senoidal entre o nascer e o por do sol.
- Geracao eolica: relacao linear com a velocidade do vento.
- Consumo: distribuicao parabolica com pico no horario de atividade.

### Etapa 4 - Previsao com regressao linear

Implementacao manual de minimos quadrados para `y = a*x + b`:

```text
a = (n*Sxy - Sx*Sy) / (n*Sxx - Sx^2)
b = (Sy - a*Sx) / n
```

O modelo aprende com a serie historica e gera previsoes como:

- vento = 11 m/s -> energia eolica prevista
- hora = 12     -> consumo previsto

### Etapa 5 - Sistema integrado

A funcao `executar_sistema` recebe o estado da colonia, aplica previsao, decide o modo de operacao e devolve a recomendacao operacional para aquele instante.

---

## Regras de Decisao Principais

| Condicao | Acao |
|---|---|
| `consumo > geracao` AND `reserva < 30%` | ALERTA: reduzir consumo |
| `consumo > geracao` AND `reserva >= 30%` | ATENCAO: usar reserva |
| `geracao > consumo * 1.3` | SUGESTAO: armazenar excedente |
| `reserva < 20%` AND `saldo < 0` AND `previsao saldo < 0` | MODO CRITICO |
| `reserva < 50%` AND `saldo < 0` | MODO ECONOMIA |
| `saldo > 0` AND `reserva > 80%` | MODO EXCEDENTE |

---

## Estrutura do Projeto

```text
aurora-siger-fase-3/
|-- Aurora_Siger_Fase3_Colab.ipynb
|-- README.md
```

---

## ▶️ Como Executar

### Opcao 1 - Google Colab

1. Faca upload do arquivo `Aurora_Siger_Fase3_Colab.ipynb` no Colab.
2. Execute as celulas em ordem.
3. Analise as tabelas, modelos e recomendacoes geradas.

### Opcao 2 - Jupyter Notebook Local

1. Clone o repositorio:

```bash
git clone https://github.com/rodrigogmdias/aurora-siger-fase-3.git
cd aurora-siger-fase-3
```

2. Abra o notebook:

```bash
jupyter notebook Aurora_Siger_Fase3_Colab.ipynb
```

3. Execute todas as celulas.

> A primeira celula instala automaticamente `numpy`, `pandas` e `matplotlib` caso nao estejam disponiveis.

---

## Exemplos de Entrada e Saida

### Exemplo 1 - Risco energetico

```text
Entrada:
  geracao = 40 kW
  consumo = 70 kW
  reserva = 25 %

Saida:
  "ALERTA: consumo maior que geracao e reserva baixa"
```

### Exemplo 2 - Excedente

```text
Entrada:
  geracao = 80 kW
  consumo = 30 kW
  reserva = 90 %

Saida:
  "SUGESTAO: armazenar energia excedente"
```

### Exemplo 3 - Previsao eolica

```text
Entrada:
  vento = 11 m/s

Saida:
  modelo  : energia = 2.24 * vento + (-0.76)
  R^2     : 0.995  (ajuste excelente)
  residuos: media = 0.000 kW, desvio = 0.293 kW
  previsao: aprox. 23.9 kW
  (minimos quadrados vetorizados com NumPy sobre a serie historica)
```

### Exemplo 4 - Sistema integrado (hora = 9)

```text
Entrada (sensores atuais):
  solar = 32 kW, eolico = 18 kW
  consumo = 45 kW, reserva = 64 %
  vento = 9.2 m/s

Saida:
  saldo_atual_kw   : 5.0
  previsao_eolica  : ~ 20.0 kW
  modo_operacao    : MODO NORMAL
  recomendacao     : "OK: carregar baterias com excedente"
  modulos          : todos LIGADOS (suporte_vida, habitacao, comunicacao,
                                    laboratorio, estufa)
```

---

## Conceitos Aplicados

- Estruturas de dados: listas, dicionarios (chave-valor), hierarquias e **arrays NumPy** (1D e 2D).
- Estruturas condicionais: `if`, `elif`, `else` com condicoes combinadas (AND / OR).
- Modelagem matematica vetorizada: `np.sin`, `np.exp`, `np.maximum`, `np.where` (broadcasting).
- Regressao linear simples implementada manualmente com **somas vetorizadas** (`np.sum`, `np.mean`).
- Avaliacao do modelo: coeficiente de determinacao R^2 e analise de residuos.
- Analise de eficiencia: matriz de cenarios (array 2D) com operacoes vetoriais.
- Fluxo entrada -> processamento -> saida.

---

## 🔋 Como o Sistema Apoia a Eficiencia Energetica

- Detecta quando o consumo ultrapassa a geracao e aciona modo economia.
- Antecipa quedas de geracao usando previsao linear sobre dados historicos.
- Prioriza modulos essenciais (suporte a vida, habitacao, comunicacao).
- Sugere armazenar excedente quando ha sobra de energia renovavel.
- Mostra de forma visual (graficos) o saldo energetico ao longo do dia e o modo de operacao recomendado.

---

## 👤 Autor

Desenvolvido como projeto academico para a FIAP - Fase 3 da missao Aurora Siger.

- Rodrigo Gomes Dias (RM 569142) - rodrigogmdias@gmail.com
