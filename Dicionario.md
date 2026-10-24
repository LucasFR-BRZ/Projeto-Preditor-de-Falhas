
# Dicionário de Dados — Preditor de Falhas de Rede (v0.4)

## 1. Objetivo

Este documento descreve a origem dos dados, as medições selecionadas, os campos utilizados e as abreviações presentes nos registros coletados para o projeto de predição de falhas de rede. Também documenta as métricas calculadas, os rótulos heurísticos e as entradas utilizadas pelo modelo final.

## 2. Fonte dos dados

Os dados são obtidos por meio do [RIPE Atlas](https://atlas.ripe.net/), uma plataforma que realiza medições de conectividade e desempenho da internet por meio de sondas distribuídas geograficamente.

As medições selecionadas utilizam testes do tipo `ping`, baseados no protocolo ICMP, para registrar os tempos de resposta entre sondas e endereços IP de destino.

## 3. Medições selecionadas

| ID da medição (`msm_id`) | Origem informada | Destino informado | IP de destino |
|---|---|---|---|
| `11688943` | Finlândia | Porto Alegre, Brasil | `200.132.1.32` |
| `203894195` | Finlândia | Paris, França | `82.66.27.208` |
| `192385276` | Finlândia | Zug, Suíça | `193.134.29.16` |
| `146202339` | França | Düsseldorf, Alemanha | `91.207.13.233` |
| `209654486` | Finlândia | Umm ash Sha'um, Bahrein | `37.131.15.98` |
| `112718717` | Finlândia | Buenos Aires, Argentina | `200.9.157.207` |
| `100423378` | Finlândia | Santa Cruz, Costa Rica | `200.5.63.109` |
| `160208095` | Finlândia | Ostend, Alemanha* | `80.243.198.243` |

**Nota:** as localidades de destino foram registradas conforme as informações fornecidas e ainda precisam de confirmação independente. Ostend é conhecida como uma cidade da Bélgica; por isso, a associação apresentada na última linha deve ser revisada antes de ser considerada definitiva.

## 4. Dicionário dos campos das medições

| Campo | Descrição |
|---|---|
| `msm_id` | Identificador da medição no RIPE Atlas. |
| `prb_id` | Identificador da sonda que realizou a medição. |
| `src_addr` | Endereço IP de origem da sonda. |
| `dst_addr` | Endereço IP do destino testado. |
| `dst_name` | Nome ou endereço associado ao destino. |
| `proto` | Protocolo utilizado na medição, como ICMP. |
| `type` | Tipo de medição, como `ping`. |
| `timestamp` | Data e hora do registro em formato Unix timestamp. |
| `avg` | Tempo médio de resposta, em milissegundos. |
| `min` | Menor tempo de resposta registrado, em milissegundos. |
| `max` | Maior tempo de resposta registrado, em milissegundos. |
| `sent` | Quantidade de pacotes enviados. |
| `rcvd` | Quantidade de pacotes recebidos. |
| `result` | Lista de resultados individuais, que pode conter os tempos de resposta (`rtt`). |
| `ttl` | Campo do pacote que limita seu tempo de permanência na rede por meio de saltos. |
| `size` | Tamanho do pacote, em bytes. |
| `dup` | Quantidade de respostas duplicadas registradas. |
| `step` | Intervalo programado entre as medições, em segundos. |
| `group_id` | Identificador do grupo associado à medição, quando fornecido. |
| `stored_timestamp` | Data e hora em que o resultado foi armazenado, em formato Unix timestamp. |

## 5. Dicionário dos metadados das sondas

| Campo | Descrição |
|---|---|
| `address_v4` | Endereço IPv4 cadastrado para a sonda. |
| `address_v6` | Endereço IPv6 cadastrado para a sonda, quando disponível. |
| `asn_v4` | Número do Sistema Autônomo associado à conexão IPv4. |
| `asn_v6` | Número do Sistema Autônomo associado à conexão IPv6, quando disponível. |
| `country_code` | Código de duas letras do país associado à sonda. |
| `description` | Descrição da rede ou organização associada à sonda. |
| `firmware_version` | Versão do firmware da sonda. |
| `first_connected` | Data do primeiro registro de conexão da sonda, em formato Unix timestamp. |
| `geometry` | Estrutura que contém as coordenadas geográficas cadastradas da sonda. |

### 5.1. Coordenadas geográficas

O campo `geometry` pode conter as coordenadas no formato:

`[longitude, latitude]`

A ordem é importante: o primeiro valor representa a longitude e o segundo representa a latitude.

As coordenadas correspondem à localização cadastrada da sonda e não garantem a localização física exata do equipamento.

## 6. Abreviações e termos técnicos

| Termo | Significado |
|---|---|
| IP | *Internet Protocol*: protocolo responsável pelo endereçamento e encaminhamento de pacotes IP. |
| IPv4 | Quarta versão do protocolo IP. |
| IPv6 | Sexta versão do protocolo IP. |
| ICMP | *Internet Control Message Protocol*: protocolo utilizado, entre outras funções, em testes de ping. |
| RTT | *Round-Trip Time*: tempo de ida de uma mensagem até o destino e de retorno da resposta. |
| ASN | *Autonomous System Number*: identificador numérico de um Sistema Autônomo na internet. |
| TTL | *Time To Live*: campo utilizado para limitar a quantidade de saltos de um pacote. |
| JSON | Formato estruturado de dados utilizado para armazenamento e transmissão de informações. |
| Unix timestamp | Representação numérica de data e hora a partir de 1º de janeiro de 1970, em UTC. |
| Sonda (*probe*) | Equipamento ou agente que executa medições de rede na plataforma RIPE Atlas. |
| Latência | Tempo necessário para que uma mensagem percorra a rede até o destino e a resposta retorne, conforme a medição utilizada. |
| Perda de pacotes | Situação em que pacotes enviados não recebem resposta ou não chegam ao destino esperado. |

## 7. Organização geográfica

Para comparar as medições por localização, podem ser associados os seguintes campos a cada registro:

- País de origem.
- Continente de origem.
- País de destino.
- Continente de destino.
- Endereço IP de origem.
- Endereço IP de destino.

A localização da origem pode ser obtida por meio dos metadados da sonda no RIPE Atlas. A localização do destino deve ser verificada por fontes adequadas, como informações de rede e serviços de geolocalização de IP.

A localização geográfica aproximada de um IP não comprova, por si só, o caminho percorrido pelos pacotes. Para investigar os roteadores intermediários, podem ser necessárias medições adicionais, como `traceroute`.

**Importante para o modelo:** informações geográficas, endereços IP e identificadores de rota não são utilizados como entradas da árvore de decisão final. O modelo oficial utiliza as oito métricas listadas na Seção 14.

## 8. Finalidade dos dados no projeto

Os dados coletados pelo RIPE Atlas são utilizados para analisar o comportamento das conexões de rede ao longo do tempo e identificar sinais compatíveis com degradação ou falha, segundo os critérios definidos no projeto.

A análise utiliza os resultados individuais de RTT, os tempos de resposta, a quantidade de pacotes enviados e recebidos e os horários das medições. Os identificadores da sonda e do endereço IP de destino são utilizados para agrupar as medições por fluxo.

Para comparar o comportamento das conexões, os dados foram organizados em dois períodos:

- **Período A:** utilizado para calcular o baseline de cada fluxo, que serve como referência do comportamento observado.
- **Período B:** utilizado para calcular métricas em relação ao baseline, atribuir rótulos heurísticos e preparar os conjuntos de treino, validação e teste.

Entre as métricas calculadas estão o desvio robusto do RTT, o aumento percentual do tempo de resposta, o jitter, o percentual de perda de pacotes e a ocorrência de timeouts.

Com base nessas métricas e em critérios definidos por regras heurísticas, as medições do Período B são classificadas como `OK`, `RISCO` ou `FALHA`. Essas classificações representam os critérios adotados pelo projeto e não comprovam, isoladamente, a ocorrência de uma falha real na infraestrutura de rede.

A localização geográfica pode ser utilizada para agrupar e comparar medições entre diferentes regiões. Entretanto, a distância geográfica, por si só, não determina a latência ou a ocorrência de falhas, pois o desempenho também depende de fatores como roteamento, congestionamento e infraestrutura de rede.

Os dados foram preparados e divididos cronologicamente por fluxo. A árvore de decisão foi treinada com o conjunto de treino, os parâmetros foram selecionados com base na validação e o desempenho final foi medido no conjunto de teste.

## 9. Observações sobre a qualidade dos dados

1. Os identificadores `msm_id` e `prb_id` representam entidades diferentes: o primeiro identifica a medição e o segundo identifica a sonda.
2. Uma mesma medição pode apresentar resultados de várias sondas.
3. Os metadados de uma sonda podem se repetir em diversos registros, pois a mesma sonda pode executar várias medições.
4. As localidades dos destinos devem ser verificadas antes de serem consideradas definitivas.
5. Os dados originais devem ser preservados, mantendo separadas as informações coletadas e as informações geográficas acrescentadas posteriormente.
6. Os rótulos `OK`, `RISCO` e `FALHA` são definidos por regras heurísticas construídas a partir das métricas. A avaliação do modelo mede sua capacidade de reproduzir esses rótulos e não constitui, por si só, validação independente de falhas reais.
7. O conjunto de medições é proveniente de sondas do RIPE Atlas e não representa necessariamente o comportamento de uma rede local específica, como a rede de um campus.

## 10. Identificação dos fluxos e baseline

Um fluxo é identificado pela combinação de `prb_id` e `dst_addr`, representada por `prb_id|dst_addr`.

O baseline é calculado com os dados do Período A e funciona como referência para avaliar as medições do Período B.

| Campo | Descrição |
|---|---|
| `fluxo_id` | Identificador composto da sonda e do IP de destino. |
| `n_rtt_validos_A` | Quantidade de RTTs válidos do fluxo no Período A. |
| `mediana` | Mediana dos RTTs válidos do Período A, em milissegundos. |
| `MAD` | Mediana dos desvios absolutos em relação à mediana do baseline. |
| `status_baseline` | Indica se o fluxo possui amostras suficientes para análise. |

Na preparação dos conjuntos utilizados pelo modelo, foi adotado provisoriamente o mínimo de **20 RTTs válidos por fluxo**, em substituição ao requisito inicial de 1.500, devido à quantidade disponível na coleta.

Na verificação de generalização para um fluxo não visto, a Tarefa 5 exigiu pelo menos **1.500 RTTs válidos no Período A**, além de o fluxo não estar presente no treino e estar presente no teste. Nenhum fluxo atendeu simultaneamente a esses critérios; portanto, essa verificação não pôde ser realizada.

## 11. Métricas calculadas no Período B

| Campo | Descrição |
|---|---|
| `rtt_ms` | RTT utilizado na análise da medição atual, em milissegundos. |
| `z_robusto` | Desvio do RTT em relação ao baseline, normalizado pela dispersão robusta. |
| `aumento_pct` | Variação percentual do RTT atual em relação à mediana do baseline. |
| `jitter_atual` | Diferença absoluta entre RTTs válidos consecutivos do mesmo fluxo, respeitando o intervalo temporal definido. |
| `jitter_relativo` | Relação entre o jitter atual e o jitter típico do baseline. |
| `perda_pct` | Percentual de perda calculado a partir dos pacotes enviados e recebidos. |
| `timeout_atual` | Indica ausência de RTT válido ou perda calculada de 100%. |
| `n5_timeout` | Quantidade de timeouts nas últimas cinco medições do fluxo, incluindo a atual. |
| `n5_aumento80` | Quantidade de aumentos de RTT superiores a 80% nas últimas cinco medições. |
| `n5_risco` | Quantidade de medições nas últimas cinco que atendem a pelo menos um critério individual de risco. |

### 11.1. Cálculos e cuidados

- O percentual de perda é calculado a partir dos pacotes enviados e recebidos, quando existe quantidade de pacotes enviados maior que zero.
- O jitter atual é calculado como a diferença absoluta entre RTTs válidos consecutivos do mesmo fluxo. O cálculo respeita o intervalo temporal definido e não atravessa timeouts ou resultados sem RTT válido.
- O desvio robusto utiliza a mediana e a dispersão do baseline. Quando a MAD é zero, é necessário utilizar o tratamento alternativo definido no código de preparação para evitar divisão por zero.
- As métricas de janela, como `n5_timeout`, `n5_aumento80` e `n5_risco`, consideram as cinco medições pertinentes do mesmo fluxo, incluindo a medição atual.

**Nota de reprodução:** o código do notebook é a referência para os detalhes exatos de implementação dos cálculos e do tratamento de valores ausentes.

## 12. Classificação das medições

As medições do Período B recebem um dos seguintes rótulos:

- `OK`: nenhum critério de risco ou falha foi atendido.
- `RISCO`: a medição atende a pelo menos um critério individual de risco e à condição de recorrência definida.
- `FALHA`: pelo menos um critério de falha foi atendido.

As regras são aplicadas na seguinte ordem:

1. `FALHA` se `perda_pct >= 10`.
2. `FALHA` se `n5_timeout >= 3`.
3. `FALHA` se `z_robusto >= 3.5`, havendo RTT válido.
4. `FALHA` se `n5_aumento80 >= 2`.
5. `RISCO` se pelo menos um critério individual de risco for atendido e `n5_risco >= 2`.
6. `OK` nos demais casos.

Os critérios individuais de risco são:

- `2 <= z_robusto < 3.5`;
- `30 <= aumento_pct <= 80`;
- `jitter_relativo >= 3`.

Esses rótulos são definidos por regras heurísticas e não representam, por si só, uma confirmação independente de falha real na rede. Como o modelo é treinado para classificar esses mesmos rótulos, métricas elevadas indicam principalmente que ele reproduz bem as regras usadas para gerar os rótulos.

## 13. Divisão temporal dos dados

O Período B foi dividido cronologicamente dentro de cada fluxo:

- **Treino:** aproximadamente 50% das medições iniciais.
- **Validação:** aproximadamente 20% das medições seguintes.
- **Teste:** aproximadamente 30% das medições finais.

O conjunto de treino foi utilizado para ajustar o modelo. O conjunto de validação foi utilizado para comparar configurações e selecionar os parâmetros finais. O conjunto de teste foi reservado para a avaliação final e foi utilizado uma única vez para essa avaliação; seus resultados não devem ser utilizados para reajustar os parâmetros.

A divisão temporal busca preservar a ordem das medições de cada fluxo, evitando misturar aleatoriamente medições anteriores e posteriores do mesmo fluxo entre os conjuntos.

## 14. Modelo final — árvore de decisão

### 14.1. Entradas do modelo

A árvore de decisão final utiliza as seguintes oito colunas como entradas:

| Coluna | Significado |
|---|---|
| `z_robusto` | Desvio robusto do RTT atual em relação ao baseline. |
| `aumento_pct` | Aumento percentual do RTT atual em relação à mediana do baseline. |
| `jitter_relativo` | Jitter atual relativo ao comportamento de referência. |
| `perda_pct` | Percentual de pacotes enviados que não receberam resposta. |
| `timeout_atual` | Indicador de ausência de RTT válido ou de perda total. |
| `n5_timeout` | Quantidade de timeouts na janela das últimas cinco medições. |
| `n5_aumento80` | Quantidade de aumentos superiores a 80% na janela das últimas cinco medições. |
| `n5_risco` | Quantidade de medições com pelo menos um critério individual de risco na janela das últimas cinco medições. |

A coluna `fluxo_id` é utilizada para agrupar e organizar as medições, mas não é uma entrada da árvore. O modelo oficial também não utiliza IP, país, rota ou RTT absoluto (`rtt_ms`) como entradas.

### 14.2. Parâmetros do modelo

| Parâmetro | Valor final |
|---|---|
| Algoritmo | `DecisionTreeClassifier` |
| Critério de divisão | `entropy` |
| Profundidade máxima | `7` |
| Mínimo de amostras por folha | `10` |
| Semente aleatória | `42` |
| Profundidade real da árvore treinada | `7` |
| Número de folhas | `22` |

### 14.3. Avaliação final no conjunto de teste

A avaliação final foi realizada no conjunto de teste, separado cronologicamente durante a preparação dos dados.

| Métrica | Resultado |
|---|---:|
| Total de registros de teste | 66.552 |
| Registros classificados corretamente | 66.539 |
| Erros de classificação | 13 |
| F1 macro | 0,999611 |
| Recall da classe `FALHA` | 0,998949 |

Matriz de confusão — linhas representam a classe real e colunas representam a classe prevista:

| Classe real \ Classe prevista | `OK` | `RISCO` | `FALHA` |
|---|---:|---:|---:|
| `OK` | 49.768 | 0 | 0 |
| `RISCO` | 0 | 6.320 | 2 |
| `FALHA` | 7 | 4 | 10.451 |

Os resultados demonstram alta concordância entre as previsões e os rótulos heurísticos no conjunto de teste. Entretanto, como os rótulos foram produzidos com regras baseadas em métricas semelhantes às utilizadas pelo modelo, esses números não comprovam, isoladamente, que o sistema consiga prever falhas reais futuras. A avaliação realizada mede a classificação do estado rotulado em cada medição, não uma previsão futura validada.

### 14.4. Limitações

- Os rótulos de referência são heurísticos e não foram confirmados de forma independente por registros reais de incidentes.
- A avaliação de teste mede a reprodução desses rótulos, não a previsão comprovada de uma falha futura.
- A verificação em fluxo não visto com o requisito de 1.500 RTTs válidos não pôde ser realizada, pois nenhum fluxo atendeu a todos os critérios definidos.
- O baseline pode deixar de representar adequadamente uma conexão se o comportamento da rota mudar ao longo do tempo.
- Janelas curtas podem fornecer uma estimativa limitada de jitter e recorrência.
- As medições do RIPE Atlas não representam necessariamente o comportamento de uma rede local específica.

## 15. Histórico de versões

- **v0.1:** documentação inicial da fonte dos dados, campos originais, metadados das sondas e informações geográficas.
- **v0.2:** inclusão da identificação dos fluxos, baseline, métricas calculadas, regras de classificação e divisão temporal dos dados.
- **v0.4:** atualização da documentação para refletir o treinamento e a avaliação da árvore de decisão final, suas entradas e parâmetros, os resultados do conjunto de teste e as limitações da verificação de fluxo não visto.
