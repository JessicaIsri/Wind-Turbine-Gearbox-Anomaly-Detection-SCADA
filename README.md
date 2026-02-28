# Wind Turbine Gearbox: Anomaly Detection & Predictive Maintenance

Este projeto apresenta uma metodologia para monitoramento de condição e manutenção preditiva de caixas de engrenagens (gearboxes) em turbinas eólicas, utilizando dados SCADA coletados ao longo de um período de 5 anos. O foco central reside na detecção de padrões precursores de falhas e no desenvolvimento de modelos capazes de antecipar anomalias operacionais.

## Contexto do Problema

Falhas mecânicas em caixas de engrenagens representam custos elevados e períodos prolongados de inatividade. Através da análise de sensores de temperatura, vibração e lubrificação, busca-se a transição de uma estratégia de manutenção reativa para uma abordagem proativa baseada em dados.

**Nota sobre os dados**: Os dados utilizados possuem caráter estritamente acadêmico/estudo. Embora baseados em registros de sensores, os resultados aqui apresentados refletem um cenário controlado de experimentação e não devem ser interpretados como métricas de performance em ambiente de produção real (chão de fábrica), onde variáveis externas não mapeadas podem influenciar o desempenho.

## Análise Exploratória e Estatística

A aplicação da correlação de Pearson permitiu a identificação dos principais indicadores de falha no sistema:

- Vibrações (X, Y, Z): Apresentam forte correlação positiva (0.72) com a ocorrência de anomalias.

- Pressão do Óleo: Exibe correlação negativa (-0.57), caracterizada por quedas de pressão críticas precedendo eventos de falha.

- Temperatura do Rolamento: Correlação moderada (0.31), atuando como um indicador relevante, porém com resposta temporal mais lenta.

A análise de distribuição revelou uma bimodalidade distinta nos níveis de vibração. Durante a operação normal, os sinais mantêm-se estáveis e em baixa amplitude. Na ocorrência de anomalias, o Eixo Z demonstra as maiores elevações, sugerindo problemas de alinhamento ou sobrecarga axial.

##  Lead Time Analysis (Análise de Antecedência)

A partir do critério de 3 Desvios Padrão ($3\sigma$) sobre uma linha de base operacional estável, foram calculados os tempos de antecedência com que cada sensor desvia do comportamento normal antes do registro oficial da falha:

| Sensor | Passos de Antecedência (Média) | Lead Time Estimado |
|:-------:|:-------:|--------|
| Pressão do Óleo | 18.0 | ~3 Horas |
| Vibração Z | 8.3 | ~1.4 Horas |
| Temp. Rolamento | 0 | Reativo (Pós-evento) |

Nota: Estimativa baseada em intervalos de amostragem de 10 minutos

## Modelo Preditivo: Random Forest
Foi desenvolvido um modelo de classificação baseado em Random Forest para a previsão de anomalias em um horizonte de 1 hora.

Engenharia de Atributos (Feature Engineering)

- Janelas Móveis (Rolling Windows): Utilização de médias e desvios padrão móveis para capturar a volatilidade e tendências dos sinais.

- Deslocamento do Alvo (Target Shifting): Ajuste da variável dependente para refletir a probabilidade de falha futura em relação ao estado atual dos sensores.

- Importância das Variáveis: Verificou-se que a estabilidade da pressão do óleo e a variância da vibração são os preditores com maior peso estatístico no modelo.

## Desempenho do Modelo (Dados de Teste)

Os resultados abaixo referem-se ao conjunto de teste do dataset de estudo:

| Classe | Precision | Recall | F1-Score |
|:-------:|:-------:|--------:|--------|
| 0.0 (Normal) | 0.94 | 1.00 | 0.97 |
| 1.0 (Anomalia) | 1.00 | 0.17 | 0.29 |
| Acurácia |  |  | 0.94 |

**Análise Crítica:**
Observa-se que o modelo apresenta alta confiabilidade nas predições positivas (Precision de 1.00), porém demonstra um comportamento conservador. Como se trata de um estudo, esses valores servem para validar a sensibilidade do algoritmo aos sensores de pressão e vibração, destacando a necessidade de calibração do limiar de decisão (threshold) para futuras aplicações.
