# 📊 Previsão de Estoque Inteligente na AWS com SageMaker Canvas

Este projeto foi desenvolvido como parte do **Lab: Previsão de Estoque Inteligente na AWS com SageMaker Canvas** da **DIO (Digital Innovation One)**. 

O objetivo foi criar um modelo de Machine Learning *no-code* capaz de prever a demanda de produtos, considerando fatores complexos como **feriados nacionais, promoções e sazonalidade de estoque**.

## 🚀 Diferenciais do Projeto

Ao invés de utilizar um dataset estático simples, optei por **desenvolver um script em Python** para gerar um dataset sintético robusto (~7.000 linhas), simulando um cenário real de varejo brasileiro com as seguintes lógicas de negócio:

* **Sazonalidade de Feriados:** O script utiliza a biblioteca `holidays` para mapear feriados no Brasil.
* **Comportamento do Consumidor:**
    * **Feriados Prolongados (Seg/Sex):** Queda nas vendas no dia do feriado, mas pico de vendas nos dias que antecedem.
    * **Feriados "Meio de Semana":** Leve aumento nas vendas (efeito folga curta).
* **Promoções:** Impacto direto na elasticidade do preço (preço cai 15% -> vendas sobem até 2.5x).

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

> *Nota: Tive o cuidado de converter os IDs para String (`P-101`) para garantir a compatibilidade correta com o SageMaker Canvas.*

## 🧠 Construção do Modelo (Passo a Passo)

### 1. Geração dos Dados
Utilizei um script Python (disponível na pasta `/src` deste repositório) para criar 10 produtos fictícios com histórico de vendas de 2023 a 2024.

### 2. Configuração no SageMaker Canvas
* **Dataset Importado:** `dataset_estoque_v3.csv`
* **Model Type:** Time Series Forecasting (Previsão de Série Temporal).
* **Target Column:** `QUANTIDADE_VENDIDA`.
* **Item ID:** `ID_PRODUTO`.
* **Configuração de Feriados:** Ativei a opção **"Holiday Schedule"** para o **Brasil**. Isso permitiu que o AWS SageMaker cruzasse meus dados com o calendário nacional automaticamente.

### 3. Análise dos Resultados
Após o treinamento (Standard Build), o modelo apresentou métricas interessantes:

* **Avg. wQL:** 0.417
* **MAPE:** 1.452
* **MASE:** 0.860 (Indica que o modelo é capaz de fazer previsões melhores que uma média simples).

#### Impacto das Variáveis
O gráfico de impacto das colunas revelou que a estratégia de dados funcionou perfeitamente:

![Impacto das Colunas](caminho/para/seu/print_impacto.png)
*(Substitua isso pela sua imagem do Column Impact)*

* **`Holiday_BR` (47.25%):** O modelo identificou que os **feriados brasileiros** são o fator mais determinante para a variação de vendas, validando a lógica implementada no script Python.
* **`PRECO` (19.76%):** O segundo maior fator, confirmando a sensibilidade a preços e promoções.

## 📈 Conclusões

Este projeto demonstrou como a qualidade dos dados (Data Engineering) impacta diretamente no sucesso de um modelo de Machine Learning. Ao criar variáveis explicativas ricas (como flags de promoção e comportamento de feriado), o SageMaker Canvas conseguiu atingir uma alta assertividade sem que fosse necessário escrever código complexo de modelagem.

---
### 🔗 Links Úteis
* [Documentação do AWS SageMaker Canvas](https://docs.aws.amazon.com/sagemaker/latest/dg/canvas.html)
