# Detec-o-de-anomalias-em-transic-o-com-Python

# Deteção de Fraude em Cartões de Crédito

## O Problema e o Desbalanceamento
A deteção de fraudes financeiras é um problema clássico de anomalia, onde a classe de interesse (fraude) é extremamente rara em comparação com as transações legítimas (99,8% normais vs 0,17% fraude). Devido a este cenário, a métrica de *Acurácia* é enganadora: um modelo que preveja que "nada é fraude" terá uma acurácia de 99,8%, mas falhará completamente no seu propósito. Por isso, a avaliação deste projeto focou-se no **Recall** (capacidade de encontrar todas as fraudes), na **Precision** (evitar falsos positivos que bloqueiam cartões indevidamente) e no **F1-Score**.

## Preparação dos Dados
* O *dataset* contém as *features* transformadas por PCA (V1 a V28) para garantir a privacidade, além do `Time` e do `Amount`.
* Foi criada uma nova variável aplicando o logaritmo ao valor da transação (`Log_Amount`), de modo a normalizar a distribuição de valores extremos.
* Os dados foram divididos em conjuntos de treino e teste utilizando a técnica `stratify` para garantir que a proporção de 0,17% de fraudes se mantivesse em ambos os conjuntos.
* Aplicou-se o `StandardScaler` aos dados para colocar todas as variáveis na mesma escala, o que é essencial para modelos como a Regressão Logística.

## Comparação de Modelos
Foram treinados três modelos ajustados para lidar com o desbalanceamento através da ponderação de classes (`class_weight` e `scale_pos_weight`):
1. **Regressão Logística (Baseline):** Apresentou um bom *Recall*, mas com muitos falsos positivos (baixa *Precision*).
2. **Random Forest:** Equilibrou bem a *Precision* e o *Recall*, reduzindo consideravelmente os alarmes falsos.
3. **XGBoost:** Apresentou o melhor desempenho geral, maximizando o *Recall* e mantendo uma excelente taxa de *Precision*, sendo o modelo final escolhido.

## Limiar de Decisão e Interpretabilidade (SHAP)
Optámos por manter o limiar de decisão flexível dependendo do apetite ao risco da instituição financeira, mas focámos a explicação nas decisões do modelo XGBoost utilizando valores SHAP. O gráfico de resumo do **SHAP** (`summary_plot`) revelou que as variáveis originais transformadas por PCA (destacando-se a V14 e a V17) são os indicadores mais fortes, demonstrando o que mais pesa para marcar uma transação como fraudulenta.

## O que mudou em relação à base
Além de construir o *pipeline* do zero, introduzimos o modelo **XGBoost**, que frequentemente supera a Regressão Logística e a Random Forest em cenários tubulares desbalanceados. Expandimos a análise de erro introduzindo diretamente as Curvas Precision-Recall, que oferecem uma melhor perceção da performance da classe minoritária face à curva ROC tradicional.
