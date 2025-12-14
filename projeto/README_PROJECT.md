# 📊 Previsão de Estoque Inteligente na AWS com SageMaker Canvas

Este projeto foi desenvolvido como parte do **Lab: Previsão de Estoque Inteligente na AWS com SageMaker Canvas** da **DIO (Digital Innovation One)**. 

O objetivo foi criar um modelo de Machine Learning *no-code* capaz de prever a demanda de produtos, considerando fatores complexos como **feriados nacionais, promoções e sazonalidade de estoque**.

## 🚀 Diferenciais do Projeto: Engenharia de Dados

Ao invés de utilizar um dataset estático simples, optei por **desenvolver um script em Python** para gerar um dataset sintético robusto, simulando um cenário real de varejo brasileiro com as seguintes lógicas de negócio:

* **Sazonalidade de Feriados:** O script utiliza a biblioteca `holidays` para mapear feriados no Brasil.
* **Comportamento do Consumidor:**
    * **Feriados Prolongados (Seg/Sex):** Queda nas vendas no dia do feriado, mas pico de vendas nos dias que antecedem.
    * **Feriados "Meio de Semana":** Leve aumento nas vendas (efeito folga curta).
* **Promoções:** Impacto direto na elasticidade do preço (preço cai 15% -> vendas sobem significativamente).

## 🛠 Tecnologias Utilizadas

* **Python:** Para geração e processamento dos dados (`pandas`, `numpy`, `holidays`).
* **AWS SageMaker Canvas:** Para construção, treinamento e análise do modelo de Machine Learning sem código.
* **Git/GitHub:** Versionamento e documentação.

## 📂 Estrutura do Dataset

O dataset gerado possui as seguintes colunas (features):

| Coluna | Tipo | Descrição |
| :--- | :--- | :--- |
| `ID_PRODUTO` | String | Identificador único do produto (ex: P-101). |
| `DATA_EVENTO` | Data | Data da venda. |
| `PRECO` | Numérico | Preço unitário do produto naquele dia. |
| `FLAG_PROMOCAO` | Binário | 1 se o produto estava em promoção, 0 caso contrário. |
| `QUANTIDADE_ESTOQUE` | Numérico | Nível de inventário disponível. |
| `QUANTIDADE_VENDIDA` | Numérico | **Variável Alvo (Target)**. Quanto foi vendido. |

> *Nota: Realizei o tratamento dos IDs para o formato String (`P-101`) para garantir a compatibilidade correta com o SageMaker Canvas.*

## 🧠 Construção do Modelo (Passo a Passo)

### 1. Geração dos Dados
Utilizei o script `gerar_dataset.py` (disponível na pasta `/src` deste repositório) para criar simulações de vendas de 2023 a 2024.

### 2. Configuração no SageMaker Canvas
* **Dataset Importado:** `dataset_estoque_v3.csv`
* **Model Type:** Time Series Forecasting (Previsão de Série Temporal).
* **Target Column:** `QUANTIDADE_VENDIDA`.
* **Item ID:** `ID_PRODUTO`.
* **Configuração de Feriados:** Ativei a opção **"Holiday Schedule"** para o **Brasil**. Isso permitiu que o AWS SageMaker cruzasse meus dados com o calendário nacional automaticamente.

### 3. Análise dos Resultados
O modelo treinado apresentou as seguintes métricas de performance:

* **Avg. wQL:** 0.417
* **MAPE:** 1.452
* **MASE:** 0.860 (Indica que o modelo faz previsões melhores que uma média simples).
* **RMSE:** 4.740

#### Impacto das Variáveis
O SageMaker identificou quais fatores mais influenciaram a previsão de estoque:

![Impacto das Colunas](img/column_impact.png)
*(Gráfico gerado pelo SageMaker Canvas)*

* **`Holiday_BR` (47.25%):** O modelo validou a hipótese de negócio, identificando que os **feriados brasileiros** são o fator mais determinante para a variação de vendas.
* **`PRECO` (19.76%):** Segundo maior fator, confirmando a sensibilidade do cliente a preços e promoções.
* **`QUANTIDADE_ESTOQUE` (14.69%):** A disponibilidade do produto também afetou o volume de vendas.

## 📈 Conclusões

Este projeto demonstrou como a qualidade dos dados (Data Engineering) impacta diretamente no sucesso de um modelo de Machine Learning. Ao criar variáveis explicativas ricas (como flags de promoção e comportamento de feriado), o SageMaker Canvas conseguiu atingir uma alta assertividade e capturar padrões sazonais complexos sem a necessidade de codificação manual do algoritmo.

---
### 🔗 Links Úteis
* [Documentação do AWS SageMaker Canvas](https://docs.aws.amazon.com/sagemaker/latest/dg/canvas.html)

## 📊 Resultados e Previsões

O modelo gerou previsões para os próximos dias, permitindo um planejamento de estoque mais assertivo.

### Gráfico de Previsão (Item 105)
Abaixo, vemos a projeção de vendas. As linhas coloridas representam os cenários de incerteza (P10, P50, P90):

![Gráfico de Previsão](img/grafico_previsao_futura.png)

* **Linha Verde (P50):** O cenário mais provável de vendas.
* **Linha Amarela (P90):** Cenário otimista (preparação para alta demanda).
* **Linha Rosa (P10):** Cenário pessimista (demanda mínima esperada).

### 💾 Dados Brutos
Caso queira explorar os dados gerados pelo modelo, o arquivo CSV completo está disponível:
<img width="1801" height="515" alt="single_prediction_results (2)" src="https://github.com/user-attachments/assets/4e42630c-85ce-4afd-b86c-a1da22671bfe" />
<img width="1801" height="515" alt="single_prediction_results (1)" src="https://github.com/user-attachments/assets/19168d85-9387-4f5e-a17b-128a88675353" />


